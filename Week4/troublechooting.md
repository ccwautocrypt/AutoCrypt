문제 상황.

power_on --> reset --> send_command --> await asyncio.wait_for(self.pending_response, timeout=response_timeout)

리셋 이후 위의 함수 흐름에서 무기한으로 로딩이 걸림. 
