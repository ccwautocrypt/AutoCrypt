문제 상황.

power_on --> reset --> send_command --> await asyncio.wait_for(self.pending_response, timeout=response_timeout)

리셋 이후 위의 함수 흐름에서 무기한으로 로딩이 걸림. 

```
import asyncio
import sys
import os
import logging
import random
import struct

from bumble.core import AdvertisingData
from bumble.device import Device
from bumble.transport import open_transport_or_link
from bumble.profiles.battery_service import BatteryService


async def main() -> None:
    if len(sys.argv) != 3:
        print('Usage: python battery_server.py <device-config> <transport-spec>')
        print('example: python battery_server.py device1.json usb:0')
        return

    async with await open_transport_or_link(sys.argv[2]) as hci_transport:
        device = Device.from_config_file_with_hci(
            sys.argv[1], hci_transport.source, hci_transport.sink
        )

        # Add a Battery Service to the GATT sever
        battery_service = BatteryService(lambda _: random.randint(0, 100))
        device.add_service(battery_service)

        # Set the advertising data
        device.advertising_data = bytes(
            AdvertisingData(
                [
                    (
                        AdvertisingData.COMPLETE_LOCAL_NAME,
                        bytes('Bumble Battery', 'utf-8'),
                    ),
                    (
                        AdvertisingData.INCOMPLETE_LIST_OF_16_BIT_SERVICE_CLASS_UUIDS,
                        bytes(battery_service.uuid),
                    ),
                    (AdvertisingData.APPEARANCE, struct.pack('<H', 0x0340)),
                ]
            )
        )

        # Go!
        
        await device.power_on()
        await device.start_advertising(auto_restart=True)

        # Notify every 3 seconds
        while True:
            await asyncio.sleep(3.0)
            await device.notify_subscribers(
                battery_service.battery_level_characteristic
            )


logging.basicConfig(level=os.environ.get('BUMBLE_LOGLEVEL', 'DEBUG').upper())
asyncio.run(main())

```

```
# Copyright 2021-2022 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# -----------------------------------------------------------------------------
# Imports
# -----------------------------------------------------------------------------
import asyncio
import sys
import os
import logging
from bumble.colors import color
from bumble.device import Device
from bumble.hci import Address
from bumble.transport import open_transport
from bumble.profiles.battery_service import BatteryServiceProxy


# -----------------------------------------------------------------------------
async def main() -> None:
    if len(sys.argv) != 3:
        print('Usage: battery_client.py <transport-spec> <bluetooth-address>')
        print('example: battery_client.py usb:0 E1:CA:72:48:C4:E8')
        return

    print('<<< connecting to HCI...')
    async with await open_transport(sys.argv[1]) as hci_transport:
        print('<<< connected')

        # Create and start a device
        device = Device.with_hci(
            'Bumble',
            Address('F6:F7:F8:F9:FA:FB'),
            hci_transport.source,
            hci_transport.sink,
        )
        await device.power_on()

        # Connect to the peer
        target_address = sys.argv[2]
        print(f'=== Connecting to {target_address}...')
        async with device.connect_as_gatt(target_address) as peer:
            print(f'=== Connected to {peer}')
            battery_service = peer.create_service_proxy(BatteryServiceProxy)

            # Check that the service was found
            if not battery_service:
                print('!!! Service not found')
                return

            # Subscribe to and read the battery level
            if battery_service.battery_level:
                await battery_service.battery_level.subscribe(
                    lambda value: print(
                        f'{color("Battery Level Update:", "green")} {value}'
                    )
                )
                value = await battery_service.battery_level.read_value()
                print(f'{color("Initial Battery Level:", "green")} {value}')

            await peer.sustain()


# -----------------------------------------------------------------------------
logging.basicConfig(level=os.environ.get('BUMBLE_LOGLEVEL', 'DEBUG').upper())
asyncio.run(main())

```
