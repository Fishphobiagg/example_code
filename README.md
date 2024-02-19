# example_code
```Java
// 1 프레임당 1000ms가 소요된다고 가정했을 때, 프레임당 처리 시간을 전역 변수로 관리(enum, db, 환경변수 등 관리 형태는 자유)

@Service
@RequiredArgsConstructor
public class frameService{

		private final RecordRepository recordRepository;
		private final static int FRAMES_PER_SECOND = 1000; // 딥러닝 모델의 처리 성능, 1000ms 당 1 프레임을 의미
		private final static int MAXIMUM_ACCEPTABLE_ANALYSIS_DURATION = 300000; // 사용자에게 전달해야 하는 최대 처리 시간 (ms)
		
		public void sendRequestToAiServer(final int recordId){
				// DB에서 영상 총 길이(프레임) 호출하여 요청할 프레임 단위 + 요청 개수 계산 - JPA
				Record record = this.recordRepository.findById(recordId);
				FrameRequestDto dto = this.getFramesForRequest(record.getTotalFrame());
				
				// AI 서버로 요청하는 로직
				for(int requestCount = 0; requestCount < dto.getFramesPerRequest(); requestCount++;){
								// 영상의 총 프레임을 요청 개수로 나누어 시작 지점, 종료 지점을 계산하여 요청 전송하는 로직 작성
				}

		}

		// 요청할 프레임 단위 + 요청의 개수를 구하는 함수
		private FrameRequestDto getFramesForRequest(final int totalFrames) {

    // 하나의 요청에서 처리할 수 있는 최대 프레임 수(1000은 1초를 의미)
    int maxFramesWithinDuration = (MAXIMUM_ACCEPTABLE_ANALYSIS_DURATION / 1000) * FRAMES_PER_SECOND;
    
    // 분석해야 할 총 프레임 수에 따라, 한 번에 요청할 프레임 수를 결정
    // 여기서는 모든 프레임을 최대 처리 시간 내에 분석할 수 있도록 요청당 프레임 수를 조정
    int framesPerRequest;
    int totalRequestCount;
    
    if (totalFrames <= maxFramesWithinDuration) {
        // 전체 프레임 수가 최대 처리 시간 내에 분석 가능한 프레임 수 이하인 경우
        framesPerRequest = totalFrames;
        totalRequestCount = 1;
    } else {
        // 전체 프레임 수가 최대 처리 시간을 초과하는 경우
        framesPerRequest = maxFramesWithinDuration;
        totalRequestCount = (int) Math.ceil((double) totalFrames / framesPerRequest);
    }

    return FrameRequestDto.builder()
            .totalRequestCount(totalRequestCount)
            .framesPerRequest(framesPerRequest)
            .build();
		}
}

@Getter
public class FrameRequestDto{
		private int totalRequestCount;
		private int framesPerRequest;
}
```
		
		@Builder
		public FrameRequestDto(int count, int frame){
				this.totalRequestCount = count;
				this.framesPerRequest = frame;
		}
}
