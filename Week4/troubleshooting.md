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
