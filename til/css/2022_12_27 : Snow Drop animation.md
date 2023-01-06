> 어제 내 기준에 맞춘 눈내리기 애니메이션을 완성 했고, 그 내용을 한번 정리해 본다. 누군가 혹시 이걸보고 만들어보고 싶다는 생각이 들었다면 유튜브에 노마드코더를 검색해 영상을 보는것 또한 추천한다. 간단하고 재미있게 설명해준다.

![](https://velog.velcdn.com/images/cksgml1914/post/8ea21f20-bfe8-4fa9-b866-bdcc3afaf437/image.gif)

## css part
 * 우선 페이지 안에서 내릴 동그란 눈을 만들어 주고 @keyframes를 설정해 준다.
 ```css
 /*우선 안과 밖의 공백을 없애주고*/
 *{
   margin: 0;
   padding: 0;
 }
 /*배경의 크기를 꽉채워주고, 눈이 보이도록 검은색으로 바꿔준다.
 배경을 어둡게 해주고 싶다면 background에 linear-gradient()를 사용하거나 :after를 사용해 볼 수도 있다.*/
 body {
   width: 100vw;
   height: 100vh;
   background-color: black;
 }
 /*눈을 작고 동그랗고 하얗게 만들어준다.
 다른 모양이 탐난다면 구글과 함께 열심히 만들어보자 눈 결정은 결국 못만들었다..*/
 .snowflake {
   width: 8px;
   height: 8px;
   border-radius: 50%;
   background-color: white;
   /*만들어진 눈덩이는 내려오기 전에는 보이지 않아야 하므로 화면 위로 옮겨준다.*/
   position: absolute;
   top: -8px;
 }
 /*이제 눈의 목적지를 정해준다.*/
 @keyframes fall {
 from { /*눈의 시작 높이는 이미 정해놨으니 비워둔다.*/
 }
 to {
   transform: translate(0, 100vh); /*해당 요소의 x축 y축 값을 지정*/
   opacity: 0;
 }
 /*눈이 좌우로 흔들리면서 내려오길 원한다면 진행%별로 눈의 중간 경로를 지정해준다.*/
 @keyframes fall {
 from {
 }
 20% {
   transform: translate(-20px, 20vh)
 }
 40% {
   transform: translate(20px, 40vh)
 }
 60% {
   transform: translate(-20px, 60vh)
 }
 80% {
   transform: translate(20px, 80vh)
 }
 to {
   transform: translate(0, 100vh);
   opacity: 0;
 }
 }
 ```
 
## javascript part
 * 눈의 시작 위치(x축)와 투명도 내려오는 속도를 랜덤으로 지정하고 눈div 여러개를 html로 찍어내기 귀찮으니 반복문을 사용해 자동생성 해보자
 ```javascript
 // 눈덩이를 집어넣을 body를 가져온다
 const body = document.querySelector('body');
 
 function makeSnowflake() {
     const snowflake = document.createElement("div") // 눈을 만들 div를 생성하고
     const delay = Math.random() * 10;
     const initialOpacity = Math.random();
     const duration = Math.random() * 10; // 애니메이션 지속시간을 랜덤으로 만들어 줒마다 내리는 속도를 다르게 한다.
 
     snowflake.classList.add("snowflake") // div에 snowflake란 이름을 달아주고
     snowflake.style.left = `${Math.random() * window.screen.width}px`; //눈의 시작 x축을 랜덤으로 만들어준다.
     snowflake.style.animationDelay = `${delay}s`; //눈이 동시에 내리지 않게 애니메이션 시작 시간도 다르게 해주고
     snowflake.style.opacity = initialOpacity; //눈마다 투명도를 다르게 해 더 투명한게 멀리 있는 것처럼 보이게 해준다.
     snowflake.style.animation = `fall ${duration}s linear`;
 
     body.appendChild(snowflake); // snowflake를 body안에 넣어준다.
 
     setTimeout(()=> {
         body.removeChild(snowflake); 
         makeSnowflake()
     }, (duration+delay)*1000); // 눈이 다 내려왔을 쯤에 눈을 브라우저에서 지워주고 새로운 눈을 다시 만들어 준다.
}
 
 
 for (let index=0; index < 50; index++) {
    setTimeout(makeSnowflake, 500 * index); //처음 눈을 만드는 타이밍을 틀어 자연스럽게 만들어 준다.
 }
 
 ```