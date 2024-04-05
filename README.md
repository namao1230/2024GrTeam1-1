# 2024 그래픽스 	지형 만들기 및 작품 과제
//컴퓨터공학과 20210817 신현진

let cols, rows;
let scl = 20;
let w = 1400;
let h = 1400;
let flying = 0;
let terrain = [];

let posX = 0;
let posY = 0;
let posZ = 0;
let velX = 0;
let velY = 0;
let velZ = 0;
let acc = 1; // 가속도
let damping = 0.95; // 감속
let moving = {
  up: false,
  down: false,
  left: false,
  right: false,
  forward: false,
  backward: false
};

function setup() {
  createCanvas(1000, 1400, WEBGL);
  cols = w / scl;
  rows = h / scl;

  for (let x = 0; x < cols; x++) {
    terrain[x] = [];
    for (let y = 0; y < rows; y++) {
      terrain[x][y] = 0; //specify a default value for now
    }
  }
  noStroke();
}

function draw() {
  // 지형 생성
  let angle = map(mouseX, 0, width, -PI / 6, PI / 6); // 마우스 X 위치에 따라 회전 각도 조정
  angle = constrain(angle, -PI / 6, PI / 6); // 회전 각도를 일정 범위 내로 제한
  rotateY(angle);

  flying -= 0.1;
  let yoff = flying;
  for (let y = 0; y < rows; y++) {
    var xoff = 0;
    for (let x = 0; x < cols; x++) {
      terrain[x][y] = map(noise(xoff, yoff), 0, 1, -100, 100);
      xoff += 0.2;
    }
    yoff += 0.2;
  }
  
  ambientLight(60);

  // add point light to showcase specular material
  let locX = mouseX - width / 2;
  let locY = mouseY - height / 2;
  pointLight(255, 255, 255, locX, locY, 50);

  specularMaterial(250);
  shininess(50);
  
  // 배경 및 지형 그리기
  if (mouseY < height / 2) {
    background(0, 128, 255); // 마우스 Y축이 낮을 때 회색 배경
  } else {
    background(128); // 마우스 Y축이 높을 때 하늘색 배경
  }
  rotateX(PI / 3);
  
  fill(164, 164, 164, 196);
  translate(-w / 2, -h / 2);
  for (let y = 0; y < rows - 1; y++) {
    beginShape(TRIANGLE_STRIP);
    for (let x = 0; x < cols; x++) {
      let v = terrain[x][y];
      fill(v + 0, 80, 0, v + 200);
      vertex(x * scl, y * scl, terrain[x][y]);
      vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);
    }
    endShape();
  }

  
  // 비행기 이동
  if (moving.forward) {
    velZ -= acc;
  }
  if (moving.backward) {
    velZ += acc;
  }
  if (moving.left) {
    velX -= acc;
  }
  if (moving.right) {
    velX += acc;
  }
  if (moving.up) {
    velY -= acc;
  }
  if (moving.down) {
    velY += acc;
  }

  posX += velX;
  posY += velY;
  posZ += velZ;

  // 속도 감소
  velX *= damping;
  velY *= damping;
  velZ *= damping;

  push();
  translate(posX, posY, posZ);

  // 비행기 그리기
    push();
    translate(700,400, 150);
    drawAirplane();
    pop();
  pop();
}

function drawAirplane() {
  // 바디
  push();
  fill("#00BFFF");
  ellipsoid(30, 80, 30);
  pop();

  // 뒷부분 꼬리날개
  push();
  fill("#0404B4");
  translate(30, 50);
  rotateZ(-PI / 2);
  cone(30, 65, 10, 16);
  pop();

  // 뒷부분 꼬리날개
  push();
  fill("#0404B4");
  translate(-30, 50);
  rotateZ(PI / 2);
  cone(30, 65, 10, 16);
  pop();

  // 뒷부분 꼬리날개 윗부분
  push();
  fill("#0404B4");
  translate(0, 60, 40);
  rotateX(PI / 2);
  cone(10, 40, 14, 16);
  pop();

  // 왼쪽 엔진
  push();
  fill("#81DAF5");
  translate(-70, -10);
  rotateZ(PI / 2);
  cone(30, 120, 10, 16);
  pop();

  // 오른쪽 엔진
  push();
  fill("#58D3F7");
  translate(70, -10);
  rotateZ(-PI / 2);
  cone(30, 120, 10, 16);
  pop();
}

function keyPressed() {
  if (keyCode === 83) { // w 키
    moving.forward = true;
  } else if (keyCode === 87) { // s 키
    moving.backward = true;
  } else if (keyCode === 65) { // a 키
    moving.left = true;
  } else if (keyCode === 68) { // d 키
    moving.right = true;
  } else if (keyCode === 81) { // q 키
    moving.up = true;
  } else if (keyCode === 69) { // e 키
    moving.down = true;
  }
}

function keyReleased() {
  if (keyCode === 83) { // w 키
    moving.forward = false;
  } else if (keyCode === 87) { // s 키
    moving.backward = false;
  } else if (keyCode === 65) { // a 키
    moving.left = false;
  } else if (keyCode === 68) { // d 키
    moving.right = false;
  } else if (keyCode === 81) { // q 키
    moving.up = false;
  } else if (keyCode === 69) { // e 키
    moving.down = false;
  }
}

