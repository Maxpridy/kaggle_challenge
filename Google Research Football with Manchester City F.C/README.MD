# Google Research Football with Manchester City F.C.

늦게 참가해서 시간이 없는것이 정말 아쉽다. 시도는 여러가지로 해봤는데 쉽지않다.  

일단 내가 생각해본것들을 정리해보자


## 대회에 대한 파악

일단 대회에 대해 파악해야했다. 첫 인상은 무진장 어려워보인다는 느낌이였는데 지금은 손쓸 방법을 모르겠다에 가까운것 같다. 

일단 대회가 어렵느냐. 꽤 어려운편에 속한다. 에피소드 길이는 꽤 긴데(여기선 3000) 그 안에서 짧은구간에 훌륭한 컨트롤을 수행해야 하는 상황에서 학습량이 부족하면 잘 학습하지 못한다. 이건 당연하기도 하고 그냥 어려운것이다. 사람도 마찬가지로 골키퍼를 마주쳤을 때 A-B-C의 시퀀스를 적절하게 입력해서 골을 넣는것은 숙련된 게이머나 할 수 있는것이다. 심지어 이 env는 액션이 잘... 무시당한다(흔히 게임용어로 씹힌다 라고 많이 부른다). 사람은 십히는걸 보고 다시 누르는 반면 모델이 이런걸 학습하는데에 무엇이 필요한지는 생각하기가 참 어렵더라.

일단 남은 시간은 별로 없고 캐글에서 제공하는 컴퓨팅 파워의 성능이 압도적으로 좋은것도 아니며 환경의 SPS도 빠르지않다. 쉽게 말하면 '강화학습 하기 좋은 환경이 아니다'라는 것이다. 그럼 어떻게 하는게 좋을까?


## 대회 끝나가는 상황에서 다른 노트북들 살펴보기

일단 메모리 패턴이라고 공개되어있는걸 살펴봤다. 여기서 살펴보면서 느낀건데, offence에 비해 defence 행동이 굉장히 빈약하더라. 그 이유가 뭔가 하니 일반적으로 축구는 수비들의 유기적인 움직임이 중요한데 그건 내가 컨트롤하는것도 아니고 그냥 게임 환경이 알아서 한다. 그러니까 내가 할 수 있는게 별로 없다는것이다. 내가 축구 감독처럼 '상대방이 공격할때 이런 진형으로 방어해라~' 라고 전체적인 명령을 내릴 수 있는게 아니다.  

물론 답은 있긴 할것이다. 상대방이 드리블할때 스프린트를 하면서 공에 플레이어가 다가가고 게임 내에 구현되어있는 어떤 규칙에 따라서 공을 뺏을 수 있을것이다(내가 알기론 공을 뺏은 직후에는 뺏을 수 없고 이런식으로 구현되어있는걸로 알고있다.). 태클의 경우에도 (일반적으로 스프린트의 속도는 x이기 때문에 공의 그 속도는 y이므로 시간차를 고려하여 z 위치로 태클을 건다) 등의 완벽한 계산이 있으면 할 수는 있다. 이런 일반적인 규칙들에 기반해서 아주 섬세한 계산으로 해답에 가까워질 수 있을것이다. 하지만 그 규칙을 알아내기란 어려운 일이다. 그러니까 보통은 할 수 있는게 공을 향해 달린다는게 거의 유일한 선택지가 된다.

이걸 다시말하면 수비는 어렵고(선택의 여지가 별로 없고) 공격은 쉽다(스스로 무언가 만들어낼 수 있는 가능성이 높다)라고도 볼 수 있다. 공격은 골이 잘 들어가는 골키퍼와의 거리를 유지한 채 정확한 각도에서 사이드로 약간 무빙한뒤에 슛을 쏘는 정확한 메카닉 등이 있다면 수월하게 골을 넣을 수 있다. 특히 룰베이스로 하기 쉽기도 하다.


## rule base가 더 좋을까?

한정된 학습자원이라면 룰베이스가 어쩔 수 없이 강화학습보단 강할 수 밖에 없다. 아마도 잘은 모르겠지만 이 환경이 어떻게 되어있는지 잘 해킹하는쪽이 난 더 성능이 높을거같다. 학습으로 따라잡기엔 여러모로 어려운점이 많아보인다. 아마 상위권들도 룰베이스가 꽤 많을것같다. 정확히는 모르겠지만 플레이를 지켜보면 룰베이스 같다는 생각이 들긴한다.

그리고 룰베이스를 강하게 만들어주는것 중 하나는 'ball_owned_team'이라는 명확한 지표가 이 상황에서 무슨 행동을 해야할지를 나타내준다. 조건문으로 볼을 가지고 있지 않을때에는 방어를, 볼을 가지고 있을때에는 공격을 하면 된다. 반면 머신러닝 에이전트는 좀 더 학습할게 늘어날뿐이다. 어떻게해야 공수를 깔끔하게 번갈아가면서 학습할 수 있을까 고민해봤지만 좀처럼 쉽지 않았다.


## reward function 추가

dense한 reward fuction을 두가지 추가했는데, 하나는 볼 점유율이고 하나는 공의 위치에 대한 reward이다. 볼을 더 많이 가지고 있을수록, 공이 상대방 진영에 더 오래 있을수록 리워드를 받도록 했다. 이런식으로 최적성과 효율 사이의 trade off를 주는것은 당장의 상황에서 어쩔 수 없이 필요하다고 본다. 중간중간 이상한 방향으로 학습되기도 하기 때문에 참 까다로운 문제이다. 학습의 중간다리 역할을 할 때가 정말 많기때문에 필요하다고 생각되어 넣었다.


## 공격에 집중

기가막힌 수비 방법이 있을 수 있다. 하지만 나는 아무리 생각해봐도 수비를 잘하는법은 모르겠더라. 그래서 수비에 대한 액션은 전부 포기했다. 내가 공을 가지고 있지 않을때에는 공을 향해 달리는 액션 뿐이다.

공격에 집중하겠다고 했지만 공격을 학습시키는것도 어려운 일인데 골키퍼를 파훼하는것이 간단하지 않기 때문이다. 사람은 대략적으로 (골키퍼와 일직선이 아닌 상태 -> 슛을 쏨) 이라던가 (각도가 좋지 않음 -> 센터쪽으로 패스함 -> 슛을 쏨) 이라던가의 일반적인 더 좋은 슈팅에 대한 개념을 알고있지만 에이전트에게 그런걸 기대하기는 어려운일이다. 그래서 공격도 어렵다.

여기까지 이야기하면 이제 할일은 하나만 남았다. action을 결정하는것이다.


## 액션 결정

액션은 두개이다. 하나는 "적 골대앞으로 돌진", 나머지 하나는 "숏패스" 이렇게 두개로 정했다. 강화학습으로 성능을 높이려면 행동 조합의 자유가 필요하다. optimal한 에이전트를 생각해본다면 액션이 기본단위일수록 더 좋은성능을 내겠지만 여기서도 최적과 계산량의 tradeoff가 필요하다. 행동을 정말로 필요한것들로만 설정해서 더 높은 성능을 얻어내는것이 목적이다.

위에서 말했듯이 볼을 잡고있지 않을때에는 공한테 돌진하는게 끝이고 슈팅은 좋아보이는걸(메모리패턴) 가져와서 일정 조건을 만족하면 무조건 슈팅 루틴을 따르도록 설정했다. 한마디로 수비와 슈팅은 불변하는 요소로 박아놓고 나머지를 학습시키겠다는 전략이다.

이 나머지를 말로 표현하면 "어떻게해야 볼을 더 오래 점유하고 그 볼을 효과적으로 적진으로 가져갈 수 있을까"이고 이를 중점적으로 reward shaping했다. 

결론적으로 말하면 대충 1000점짜리 제약을 많이준 강화학습치곤 꽤 하는 모델이라고 평가할 수 있겠다.



## 두려운 사실 : 20.11.26

지금부터 이야기하는건... 그냥 최상위권 이야기이다. 내가 뭐 최상위권에 갈거라는 근거없는 자신감이 있는건 아니고, 강화학습으로 그 성능까지 가기도 힘들다는걸 알고있긴 하다. 이전 대회들의 탑들이 거의 룰베이스였으니까.

난 캐글을 해본지 딱 며칠 되었다. 가장 좋은건 이전에는 어떻게 해왔는가를 살펴보는것이다. 몇달 전에 끝난 halite 대회를 좀 살펴봤다. 1등은 룰베이스를 기반으로 한 학습, 2등은 룰베이스더라. 1등은 룰베이스로 11000줄짜리 코드를 기반으로 학습을 한것같다. 더 무서운점은 그 사람이 지금 이 대회의 4등이라는것이다... 도대체 집념의 만 천줄짜리 룰베이스 에이전트를 만들 수 있는 사람이 4등이라니 그 위에있는 사람들은 어떤식으로 구현한건지 알수가 없다.

이 축구 대회의 상위권도 내가 관찰해본바론 룰베이스같다. 1등 2등 둘다 잘하긴 하는데 사람의 눈으로 보기엔 약간 아쉬운 플레이들이 보인다. 1200점 정도로 내려가면 가끔가다가 그냥 대놓고 못할때도 많이 있다. 뭐 물론 사람이 플레이하는걸 기준으로 했을때니까 당연히 내 에이전트들보단 잘한다. 

뭐 사실 이기는게 정답이긴 하다. 강화학습을 쓰세요! 하고 강요하는건 좋은 방식이 아니다. 그런식으로 강요를 하고싶다면 대회를 그런식으로 구성하는게 맞는것이다. 모든 시도는 평가 지표로만 평가받는것이 올바르다고 생각한다.

아무튼 11000줄짜리 룰베이스는 참고해볼만 할것같다. 언제곤 꼭 봐야겠다.


## 뭐..? : 20.11.27

새로운 시도를 시작했다. 그런데  

제출 에러가 나는데 아무런 메세지가 나오지가 않더라...  
결국 시간을 들여 알아낸것이 np.array([1])[0]은 numpy.int32타입이고 1은 int타입이다.  
파이썬에서는 두 타입이 다르다. 당연히 알고는 있다. 깊게는 아니여도 함수형 프로그래밍을 공부해보았고 엄격한 타입이 무엇인지 맛보긴 했다. 그래도 파이썬에서 이런 문제가 자주 발생하지 않는것도 사실이라 별 생각 없었는데 이게 문제의 원인이라는게 놀랍다. 파이썬 내부에서 이게 문제가 될수 있나 싶은데 정확히 어디서 문제가 발생하는지 궁금하다. 뭐 자바스크립트와의 통신단에서 문제가 생긴다던가 그런거 아닐까?

어쨋든 새로운 시도가 잘 되었으면 좋겠다. 일단 시도하는것부터 해보고나서..


## 1등(WeKick)의 뛰어난 전략 : 20.11.29

흔히 우리나라에선 센터링이라고 불렸고 요즘엔 크로스라고 부르는게 옳다고 하는 그 전략  
마치 코너킥마냥 차서 골대와 가까운 사람에게 패스하는 전략이다. 그게 1등의 전략이다. 사실 이 게임에 가장 어울리는 전략이기도 한것같다. 수비가 공을 잡은사람 중심으로 모이기 때문이다. 크로스를 하는순간 수비가 텅텅빈다.  
뭐 보다보면 그렇구나 싶은데 액션을 뽑아보니까 생각이 달라진다. 19개 액션중 18개를 다 사용한다. 그리고 특이하게도 볼 컨트롤로 사용하는지 드리블은 안하는데 릴리즈 드리블은 엄청 많이한다. 18개의 액션을 사용한다는건 룰베이스로 그만큼 골고루 액션을 하기란 쉽지가 않다. 그렇다면 뭐지?



## 이 환경에서 imitation learning을 방해하는 요소는 무엇일까?

나는 상위권의 데이터를 얻을 수 있다는것을 알고서 SL을 시도했다. 하지만 썩 잘하진 않았다. 아마 예측하기로는 환경의 stochastic함이 결과 모델의 inference를 방해하지 않나 싶다.  
오히려 쓸데없는 정보를 제거하면 차라리 낫지 않을까 하는 생각이 들었다. 아무것도 모르는 상태로 크로스를 올린다 해도 거기에 무언가 아군이 있을것이기 때문이고 환경의 법칙상 방향만 맞으면 대충 패스가 될것이기 때문이다. 시도할 시간이 없는게 참 아쉽다.  
어쨋든 state가 생각보다 변화무쌍하다는것이 가장 SL을 방해하는 요소가 아닌가 싶다. 적절한 state를 잘 선택한다면 충분히 더 좋은 imitation이 가능하지 않을까 싶다.



## 결론 : 20.12.01

일주일정도의 시간동안 나름 성능을 내보려고 이것저것 해봤는데, 역시 쉽지가 않다.  
뭔가 더 해보고싶은데 아쉽다. 플레이그라운드로 가고 상위권들의 솔루션이 나오고 나서도 더 해봐야겠다.



## 이후 솔루션과 노트북 읽어보기 : 20.12.02-20.12.07

재미있게도 지금까지 올라온 솔루션들 대부분이 WeKick과 SaltyFish를 imitation learning 한것이다. 나도 솔직히말하면 의심의 여지없이 이쪽으로 시도해보고 싶었지만 그러기엔 강화학습을 해보고 싶다는 마음이 컸던것같다. 3등의 솔루션을 보면 SL + RL(kl)을 쓰는데 이제는 정석과도 같아진 느낌이다. 3등의 솔루션은 꽤나 인상깊으면서 강화학습으로 할 수 있는 최대한을 시도해본것 같다. 대단하다.

여기 적힌 등수는 최종 등수가 아니다.

3등(Raw Beast)의 솔루션
- 먼저 잡설부터 하자면 왠지 이 팀이 최종 2등을 할것같다. SaltyFish는 약간 불안정한 모습을 보이는듯하다. 경기를 보다보면 WeKick이 좀 더 robust한 느낌이 있다. 아마 WeKick을 잘 배운 원시 야수가 이기지 않을까?
- feature중에 특정 액션을 한 이후의 시간이 있는데 굉장히 좋은 피쳐같다. 자주 하면 안되는 액션같은 경우는 이런 피쳐가 굉장히 의미있을것이다.
- NLE에서 사용한 페이스북의 IMPALA 구현체 torchbeast를 사용했더라(지금생각해보니 팀명에 beast가 들어간 이유가 이거인듯). 그것도 병렬 버전인 polybeast를 사용한듯 하다. 이 환경은 SPS가 상당히 낮기때문에 이런 시도는 굉장히 훌륭하지 않나 싶다. 솔루션에 "환경 설치(쉽지 않음)"이라고 적힌게 재미있었다. 사실 나도 로컬에 설치하려다가 결국 도커에다 설치하고 겨우 실행했다. 내 경우는 아마 pygame 버전문제였던걸로 기억한다. 이 gfootball 환경이 좀 문제가 많은것같다.
- 캐글 뉴비라 신경망 제출방법이 번거롭다고 생각했는데 그냥 인코딩해서 코드에 넣어버리는 방법을 쓰더라. 난 컴퓨터가 캐글 노트북보다 성능이 대단치 않아서 잘 모르겠지만 외부에서 학습해서 가져온다면 좋은 방법인것 같다. 나는 모델을 같이 압축한다음 실행중에 로드하는 방식을 사용했었다. 사실 대단한 차이는 아니지만 노트북에 데이터로 넣고 save&commit하고 불러오고 어쩌구 하는거보다 파일 하나로 깔끔하게 제출할 수 있다는게 좋아보인다. 게다가 환경에 불러올때도 파일 하나만 불러오면 알아서 되니까 에이전트의 weight를 따로 관리할 필요가 없는점이 좋지 않나싶다. 
- 승은님의 질문으로 알 수 있는건 최종 모델은 115000에피소드정도 훈련한듯 하다. 모델은 700kb정도라는데 unit수가 64인걸 보면 확실히 작아보이긴 한다.

5등(liveinparis)의 솔루션
- NC에 계시는 노승은님이 참가한 팀이다. 사실 대회 전에 한국인 팀이 있는건 알았는데 노승은 님인건 끝나고 알았다. 띠용... 최근에 내신 책도 재미있게 읽었다.
- 짧게 요약하고 그것에 대해 길게 이야기해보자. 1. 순수 강화학습 2. 역시 그정도로 도달하려면 필요한 370시간, 13억 스탭정도의 45만 에피소드 3. reward shaping 4. self-play
- 1. 순수 강화학습. 어찌보면 내가 위에 했던 자그마한 시도와 비슷하다. 나는 내가 사용할 컴퓨팅 자원의 상황(남은시간 6일, 컴퓨팅자원은 오직 캐글 노트북뿐)을 알고있었기 때문에 나는 해봤자 10000 에피소드도 가기 힘들었고, 액션을 좀 심각할정도로 우겨넣고 우겨넣어 2개로 만들었고 리워드도 shaping을 최대한 하려고 했다. 이 팀도 컴퓨팅 자원에 맞는 시도를 적절하게 한 것 같다. 사실 안봐도 비디오인 부분은 초반에 얼마나 못했을지가 보인다. 그걸 15일동안 우직하게 학습시키는건 참 어려운 일이다. 학습 도중에 '이런 시도를 했으면 더 좋지 않았을까' 하는 생각이 많이 들기도 하는게 사실이고... 사실 나만 그렇게 느끼는진 모르지만 강화학습만으로 문제를 해결하는것은 굉장한 로망이 아닐까 싶다. 어찌보면 순수 강화학습으로 이정도 성능을 냈다는건 대단한 일이라고 생각한다. 역시 강화학습을 실제 게임에 적용하시는 전문가분들 답다는 생각이 든다.
- 2. 1500점대에 도달하기 위해 action space를 줄이고 goal에 유도하기 위해 reward shaping까지 했는데도 13억 스탭의 성능이 약간은 아쉽다는 생각이 들수도 있다. 그만큼 이 문제가 쉽지는 않다. 문제의 여러가지 특징이 있지만 은근히 sequential한 연속 액션을 필요로 한다는점이나 액션이 씹히거나 하는 여러 상황이 이 환경에서의 학습을 참 어렵게 만드는것 같다.
- 3. reward shaping. 나는 볼 점유율과 공의 위치에 따라 dense한 reward를 줬는데 이 팀에서는 공의 위치와 옐로우카드에 대해 추가적인 리워드를 줬다. 옐로우카드... 이 대회의 베이스라인은 적이 공을 가지고있으면 무조건 태클을 거는데 생각보다 경기 시간이 짧아서 퇴장을 전부 당하지는 않더라. 부정적으로 보면 상대방의 공을 막다가 옐로우카드를 받아 -1 reward를 마주치면 더 적극적으로 뺏으려는 움직임을 하지 않을수 있다는 생각이 들기도 하고, 오히려 상대방의 옐로우카드를 유도해서 상대방에게 압박을 줄 수도 있다는 생각이 들기도 한다. 내가 준 볼 점유율 reward도 어찌보면 나쁜 영향을 충분히 미칠 수 있다. 슛을 안쏘고 맴돌면 무조건 reward를 더받는데 슛을 잘쏘는것을 굳이 배울까? 결론적으로 부분적인 reward가 어떤 영향을 미칠지는 예측은 해볼 수 있어도 정확히 알수가 없다. 여담인데 3등의 솔루션에서 reward는 심플할수록 좋다라는 식의 말이 있는데 알고는 있지만 참 그게 어려운것 같다(물론 여기는 SL 후에 RL을 하기 때문에 이런식으로 reward를 구성해도 자주 reward를 마주칠 수 있다). 어떤 리워드가 어떤 영향을 미치고 어떻게 학습이 될지에 대해서 완벽히 이해하는것은 거의 불가능에 가깝다. 만약 5등이 정말 목적에 맞는 심플한 reward로만 학습시켰다면 13억 스탭으로 어느정도 성능이 되었을까? 분명한건 더 많은 샘플과 연산을 필요로 할것이라는 것이다.
- 4. self-play. 노승은님과 NC RL팀에서 수행한 블소로 단련된 self-play를 게임에 적용하는 능력이 발휘된게 아닌가 싶다. 사실 난 unity ml-agents에서도 self-play 환경을 구축해봤지만 썩 self-play를 잘하진 않았다. 그만큼 어떤 세심한 조절이 필요한 작업이라고 생각한다. 지금까지 프로레벨의 인간을 뛰어넘을 정도로 대단한 성능을 낸 강화학습 학습방법은 거의 self-play가 포함되어 있다. 중요한 토픽이지만 사실 적용하는게 무엇이냐에 따라 적용이 불가능하기도 하기 때문에 간과되는감이 있지만 중요하다고 생각한다. self-play를 학습시키는 엔지니어링이 이 에이전트를 훈련시키는데 정말 중요한부분이 아니였을까 한다. 나는 이런 경험이 부족하기 때문에 공개된 코드를 열심히 봐야겠다.
- 신경망은 3위와 5위 둘다 conv1d를 많이 사용했고 학습대비 성능이 좋았다고 한다. 강화학습은 모델의 영향이 다른 분야보다 크진 않은것 같지만 상위권들이 다 채택한데에는 이유가 있을듯하다. 개인적으로도 NLE같은곳에서 실험을 해보고 싶다. 
- action blocking도 discrete action space에서 상당히 중요하게 활용할 수 있는것 같다. ball_owned_team이라는 매우 중요한 지표를 아주 잘 활용한것같다.
- CPU를 다른 팀에 비해 적게 사용했음에도 불구하고 높은 성능을 얻어낸듯하다. 높은 예술점수를 드리고싶다.

확고한 1등 : WeKick의 솔루션

- 세상에... 놀랍게도 강화학습이였다.
- reward shaping이 상당히 합리적이고 좋다. 어느정도 내 생각이랑 비슷해서 기분이 좋다. 가장 인상적인건 골과 연관이 된 패스에 +0.1을 부여하는것. 그러니까 골로 연결되지 않는 패스는 결국 리워드로 받지 못한다. 정말 합리적인 요소이다. 근데 이런 reward를 설계할때마다 항상 최악의 경우를 생각해보곤 하는데, 패스를 여러번 하고 나서 골을 넣는게 그냥 솔로 드리블로 골넣는거보다 가치가 커진다. 꼭 패스를 하지 않아도 될때에 패스를 하지 않을까? 라는 생각을 해보지만 엄청난 성능을 자랑하므로 그런가보다 해야겠다.
- 솔직하게 말하면... 잘 모르겠다. 뭐 생각 자체는 이해가 가는데  결국 이 요소들로 이런 압도적인 학습이 되나 싶은? 결국 궁금한건 컴퓨팅 자원을 얼마나 썼는지가 궁금해진다. 
- 내가 상상만 하던 전략을 실제로 학습했더라. 점수를 리드하고 있을때 공을 가지고 튀는것이다. 여긴 스포츠맨십같은건 없다. 이기면 그만이니까 이 전략은 굉장히 유효한데... 이게 학습이 가능하긴 하구나 싶었다.
- 800 cpu cores를 사용했다고한다. 1분에 500에피소드를 실행한다고 하더라... 쓰레드리퍼 3990X(500만원정도)가 64코어던가? 대충 계산해보면 450000 에피소드도 15시간이면 충분하다. 역시 에이전트가 조금 학습된 모습이 아니다.
- 코드는 공개예정이라고 한다.
- 텐센트에서 근무하고계신듯

2등 SaltyFish의 솔루션

- 이쪽은 넷이즈 사람인가보다.
- 가장 먼저 눈이가는건 2500 cpu core... 음... 중요한건 아는것이다. 나에게 없는걸 어쩔순없는법..
- 

6등 s_toppo팀의 솔루션

- 난 이 솔루션이 정말 놀라웠다. 이게 된다고...? 역시 단순 BC를 무시할수가 없다. 빠르게 올라온 솔루션이고 좋은 피쳐를 많이 뽑았구나 라는 생각은 했지만 결국 이걸로 6등의 성능이 나오다니 정말 무시무시하다.
- feature_importance가 흥미로우면서도 당연하다 싶은데, 역시 ball_owned_team이라는 지표가 가장 중요하고 나머지 고개가 끄덕여지는 지표들이 상위권에 위치해있다.
- 어찌보면 내가 마지막에 하던 imitation learning과 비슷한데 catboost를 적절히 사용해서 높은 성능을 끌어낸듯하다. 총 데이터가 370개 정도 게임밖에 안되보이는데 정말 대단하다. 내가 뽑은 경기 숫자도 300개 정도였는데 난 3일남은 시점에 테스트한답시고 좀 적당히만 뽑아서 사용해서 그런지 높은 성능을 얻진 못했다.
- 다시 읽어보니 공개된 노트북으로 학습하면 1400점 정도로 조금 성능이 낮다고 한다. 하지만 내가 여러가지로 테스트 해본 결과 꽤 강하다. 내가 갖고있는 에이전트들이 약한건가..?


5등 TamakEri팀의 솔루션

- 일본 팀이다. base로 handyrl이라는 라이브러리를 사용하는데, ReNA? 라는 회사에서 공개한 코드같다. 아마 임팔라를 사용한것 같은데 느낌이 굉장히 좋다. 지금 코드를 돌려보고 있는데 도커로 깔끔하게 환경세팅을 해서 따라해보는데 문제가 없었다. 가장 인상적인건 worker를 추가했다 뺐다 할 수 있도록 구현되어 있는게 뭔가 편리하다는 느낌을 받았다. 여러 임팔라 구현체들을 참고해서 이쪽 구현에 대해 더 공부해보고싶다. 이 코드와 토치비스트를 참고하면 많은 도움이 될것이다. 어쨋든 따라해보고는 있지만 사실 이 환경을 학습시키는건 내 컴퓨팅 자원으로는 멀고도 험한 일이라서 구현에 대한걸 더 살펴보고 싶다.
- rule-base 에이전트가 A, B, C, E, F 다섯가지가 있는데 이거에다가 다른걸 섞어서 확률적으로 샘플링해서 상대하는듯. 이런 면에서는 노승은님 팀의 구현이 훨씬 뛰어난 느낌이 있다. 이 팀은 솔루션을 보면 컴퓨팅 자원을 꽤 많이 사용했기 때문에 개인적으로는 조금 예술점수면에서 감점을 주고싶다. 

