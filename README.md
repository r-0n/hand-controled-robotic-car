"# hand-controled-robotic-car" 

**CONCEPT**
How would someone virtually learn how complicated it is to drive a car? Would teaching someone how to drive a car virtually save a lot of money and decrease potential accidents associated with driving? These questions inspired our project, which is to create a remote-controlled car that can be controlled using hand gestures (that imitate the driving steering wheel movements), specifically by tracking the user's hand position and a foot pedal. The foot pedal will be used to control the acceleration, braking, and reversing. We will achieve all these by integrating a P5JS tracking system into the car, which will interpret the user's hand gestures and translate them into commands that control the car's movements. The hand gestures and pedal control will be synced together via two serial ports that will communicate with the microcontroller of the car.

**Experience**
The entire concept is not based only on a driving experience. We introduce a racing experience by creating a race circuit. The idea is for a user to complete a lap in the fastest time possible. Before you begin the experience, you can view the leaderboard. After your time has been recorded, a pop-up appears for you to input your name to be added to the leaderboard. For this, we created a new user interface on a separate laptop. This laptop powers an Arduino circuit connection which features an ultrasonic sensor. The ultrasonic sensor checks when the car has crossed the start line and begins a timer, and detects when the user ends the circuit. After this, it records the time it took a user to complete the track and sends this data to the leaderboard.

This piece of code is how we're able to load and show the leaderboard.

function loadScores() {
  let storedScores = getItem("leaderboard");
  if (storedScores) {
    highscores = storedScores;
    console.log("Highscores loaded:", highscores);
  } else {
    console.log("No highscores found.");
  }
}

function saveScores() {
  // make changes to the highscores array here...
  storeItem("leaderboard", highscores);
  console.log("Highscores saved:", highscores);
}






**IMPLEMENTATION(The Car & Foot Pedal)
**

We first built the remote-controlled car using an Arduino Uno board, a servo motor, a Motor Shield 4 Channel L293D, an ultrasonic sensor, 4 DC motors, and other peripheral components. Using the Motor Shield 4 Channel L293D decreased numerous wired connections and allowed us space on the board on which we mounted all other components. After, we created a new Arduino circuit connection to use the foot pedal. 

The foot pedal sends signals to the car by rotating a potentiometer whenever the pedal is engaged. The potentiometer value is converted into forward/backward movement before it reaches p5.js via serial communication.

P5/Arduino Communication

At first, a handshake is established to ensure communication exists before proceeding with the program:

//////////////VarCar/////
// Define Serial port
let serial;
let keyVal;
//////////////////////////
const HANDTRACKW = 432;
const HANDTRACKH = 34;

const VIDEOW = 320;
const VIDEOH = 240;

const XINC = 5;
const CLR = "rgba(200, 63, 84, 0.5)";

let smooth = false;
let recentXs = [];
let numXs = 0;

// Posenet variables
let video;
let poseNet;

// Variables to hold poses
let myPose = {};
let myRHand;

let movement;
////////////////////////

let Acceleration = 0;
let Brake = 0;
let data1 = 0;
let data2 = 0;

let s2_comp=false;

function setup() {
 // Create a canvas
  //createCanvas(400, 400);

  // // Open Serial port
  // serial = new p5.SerialPort();
  // serial.open("COM3"); // Replace with the correct port for your Arduino board
  // serial.on("open", serialReady);

  ///////////////////////////
  // Create p5 canvas
 




The Hand Gestures

Two resources that helped detect the user's hand position were PoseNet and Teachable Machine. We used these two resources to create a camera tracking system which was then programmed to interpret specific hand gestures, such as moving the hand right or left to move the car in those directions. This aspect of our code handles the hand tracking and gestures.

if (myPose) {
    try {
      // Get right hand from pose
      myRHand = getHand(myPose, false);
      myRHand = mapHand(myRHand);

      const rangeLeft2 = [0, 0.2 * HANDTRACKW];
      const rangeLeft1 = [0.2 * HANDTRACKW, 0.4 * HANDTRACKW];
      const rangeCenter = [0.4 * HANDTRACKW, 0.6 * HANDTRACKW];
      const rangeRight1 = [0.6 * HANDTRACKW, 0.8 * HANDTRACKW];
      const rangeRight2 = [0.8 * HANDTRACKW, HANDTRACKW];

      // Check which range the hand is in and print out the corresponding data
      if (myRHand.x >= rangeLeft2[0] && myRHand.x < rangeLeft2[1]) {
        print("LEFT2");
        movement = -1;
      } else if (myRHand.x >= rangeLeft1[0] && myRHand.x < rangeLeft1[1]) {
        print("LEFT1");
        movement = -0.5;
      } else if (myRHand.x >= rangeCenter[0] && myRHand.x < rangeCenter[1]) {
        print("CENTER");
        movement = 0;
      } else if (myRHand.x >= rangeRight1[0] && myRHand.x < rangeRight1[1]) {
        print("RIGHT1");
        movement = 0.5;
      } else if (myRHand.x >= rangeRight2[0] && myRHand.x <= rangeRight2[1]) {
        print("RIGHT2");
        movement = 1;
      }
      // Draw hand
      push();
      const offsetX = (width - HANDTRACKW) / 2;
      const offsetY = (height - HANDTRACKH) / 2;
      translate(offsetX, offsetY);
      noStroke();
      fill(CLR);
      ellipse(myRHand.x, HANDTRACKH / 2, 50);
      pop();
    } catch (err) {
      print("Right Hand not Detected");
    }
    print(keyVal)




The Final Result & Car Control

The final result was an integrated system consisting of the car, the pedal, and gesture control in P5.JS. When the code is run in p5.js, the camera detects a user’s hand position and translates it into movement commands for the car.

The entire code for controlling the car.

//////////////VarCar/////
// Define Serial port
let serial;
let keyVal;
//////////////////////////
const HANDTRACKW = 432;
const HANDTRACKH = 34;

const VIDEOW = 320;
const VIDEOH = 240;

const XINC = 5;
const CLR = "rgba(200, 63, 84, 0.5)";

let smooth = false;
let recentXs = [];
let numXs = 0;

// Posenet variables
let video;
let poseNet;

// Variables to hold poses
let myPose = {};
let myRHand;

let movement;
////////////////////////

let Acceleration = 0;
let Brake = 0;
let data1 = 0;
let data2 = 0;

let s2_comp=false;

function setup() {
 // Create a canvas
  //createCanvas(400, 400);

  // // Open Serial port
  // serial = new p5.SerialPort();
  // serial.open("COM3"); // Replace with the correct port for your Arduino board
  // serial.on("open", serialReady);

  ///////////////////////////
  // Create p5 canvas
  createCanvas(600, 600);
  rectMode(CENTER);

  // Create webcam capture for posenet
  video = createCapture(VIDEO);
  video.size(VIDEOW, VIDEOH);
  // Hide the webcam element, and just show the canvas
  video.hide();

  // Posenet option to make posenet mirror user
  const options = {
    flipHorizontal: true,
  };

  // Create poseNet to run on webcam and call 'modelReady' when model loaded
  poseNet = ml5.poseNet(video, options, modelReady);

  // Everytime we get a pose from posenet, call "getPose"
  // and pass in the results
  poseNet.on("pose", (results) => getPose(results));
}

function draw() {
  // one value from Arduino controls the background's red color
  //background(0, 255, 255);
  
  /////////////////CAR///////
  background(0);

  strokeWeight(2);
  stroke(100, 100, 0);
  line(0.2 * HANDTRACKW, 0, 0.2 * HANDTRACKW, height);
  line(0.4 * HANDTRACKW, 0, 0.4 * HANDTRACKW, height);
  line(0.6 * HANDTRACKW, 0, 0.6 * HANDTRACKW, height);
  line(0.8 * HANDTRACKW, 0, 0.8 * HANDTRACKW, height);
  line(HANDTRACKW, 0, HANDTRACKW, height);
  line(1.2 * HANDTRACKW, 0, 1.2 * HANDTRACKW, height);

  if (myPose) {
    try {
      // Get right hand from pose
      myRHand = getHand(myPose, false);
      myRHand = mapHand(myRHand);

      const rangeLeft2 = [0, 0.2 * HANDTRACKW];
      const rangeLeft1 = [0.2 * HANDTRACKW, 0.4 * HANDTRACKW];
      const rangeCenter = [0.4 * HANDTRACKW, 0.6 * HANDTRACKW];
      const rangeRight1 = [0.6 * HANDTRACKW, 0.8 * HANDTRACKW];
      const rangeRight2 = [0.8 * HANDTRACKW, HANDTRACKW];

      // Check which range the hand is in and print out the corresponding data
      if (myRHand.x >= rangeLeft2[0] && myRHand.x < rangeLeft2[1]) {
        print("LEFT2");
        movement = -1;
      } else if (myRHand.x >= rangeLeft1[0] && myRHand.x < rangeLeft1[1]) {
        print("LEFT1");
        movement = -0.5;
      } else if (myRHand.x >= rangeCenter[0] && myRHand.x < rangeCenter[1]) {
        print("CENTER");
        movement = 0;
      } else if (myRHand.x >= rangeRight1[0] && myRHand.x < rangeRight1[1]) {
        print("RIGHT1");
        movement = 0.5;
      } else if (myRHand.x >= rangeRight2[0] && myRHand.x <= rangeRight2[1]) {
        print("RIGHT2");
        movement = 1;
      }
      // Draw hand
      push();
      const offsetX = (width - HANDTRACKW) / 2;
      const offsetY = (height - HANDTRACKH) / 2;
      translate(offsetX, offsetY);
      noStroke();
      fill(CLR);
      ellipse(myRHand.x, HANDTRACKH / 2, 50);
      pop();
    } catch (err) {
      print("Right Hand not Detected");
    }
    print(keyVal)
    
    

    //print(movement);
   // print("here")
    //print(writers);
  }
  //////////////////////////

  if (!serialActive1 && !serialActive2) {
    text("Press Space Bar to select Serial Port", 20, 30);
  } else if (serialActive1 && serialActive2) {
    text("Connected", 20, 30);

    // Print the current values
    text("Acceleration = " + str(Acceleration), 20, 50);
    text("Brake = " + str(Brake), 20, 70);
    mover();
  }

}

function keyPressed() {
  if (key == " ") {
    // important to have in order to start the serial connection!!
    setUpSerial1();
  } else if (key == "x") {
    // important to have in order to start the serial connection!!
    setUpSerial2();
    s2_comp=true
  }
}

/////////////CAR/////////
function serialReady() {
  // Send initial command to stop the car
  serial.write("S",0);
  print("serialrdy");
}

function mover() {
    print("mover");

  // Send commands to the car based wwon keyboard input
  if (Acceleration==1) {
    writeSerial('S',0);
   
    //print(typeof msg1)
    
  }else if (Brake==1) {
    writeSerial('W',0);
  }else if ( movement < 0) {
    print("left")
    writeSerial('A',0);
  }else if ( movement > 0) {
        print("right")

    writeSerial('D',0);
  }else if (movement== 0) {
    print("stop");
    writeSerial('B',0);
  }
}

// When posenet model is ready, let us know!
function modelReady() {
  console.log("Model Loaded");
}

// Function to get and send pose from posenet
function getPose(poses) {
  // We're using single detection so we'll only have one pose
  // which will be at [0] in the array
  myPose = poses[0];
}

// Function to get hand out of the pose
function getHand(pose, mirror) {
  // Return the wrist
  return pose.pose.rightWrist;
}

// function mapHand(hand) {
//   let tempHand = {};
//   tempHand.x = map(hand.x, 0, VIDEOW, 0, HANDTRACKW);
//   tempHand.y = map(hand.y, 0, VIDEOH, 0, HANDTRACKH);

//   if (smooth) tempHand.x = averageX(tempHand.x);

//   return tempHand;
// }
function mapHand(hand) {
  let tempHand = {};
  // Only add hand.x to recentXs if the confidence score is greater than 0.5
  if (hand.confidence > 0.2) {
    tempHand.x = map(hand.x, 0, VIDEOW, 0, HANDTRACKW);

    if (smooth) tempHand.x = averageX(tempHand.x);
  }

  tempHand.y = map(hand.y, 0, VIDEOH, 0, HANDTRACKH);

  return tempHand;
}

function averageX(x) {
  // the first time this runs we add the current x to the array n number of times
  if (recentXs.length < 1) {
    console.log("this should only run once");
    for (let i = 0; i < numXs; i++) {
      recentXs.push(x);
    }
    // if the number of frames to average is increased, add more to the array
  } else if (recentXs.length < numXs) {
    console.log("adding more xs");
    const moreXs = numXs - recentXs.length;
    for (let i = 0; i < moreXs; i++) {
      recentXs.push(x);
    }
    // otherwise update only the most recent number
  } else {
    recentXs.shift(); // removes first item from array
    recentXs.push(x); // adds new x to end of array
  }

  let sum = 0;
  for (let i = 0; i < recentXs.length; i++) {
    sum += recentXs[i];
  }

  // return the average x value
  return sum / recentXs.length;
}

////////////////////////

// This function will be called by the web-serial library
// with each new *line* of data. The serial library reads
// the data until the newline and then gives it to us through
// this callback function
function readSerial(data) {
  ////////////////////////////////////
  //READ FROM ARDUINO HERE
  ////////////////////////////////////

  if (data != null) {
   // print(data.value);
    let fromArduino = data.value.split(",");
    if (fromArduino.length == 2) {
      //print(int(fromArduino[0]));
      //print(int(fromArduino[1]));
      Acceleration = int(fromArduino[0]);
      Brake  = int(fromArduino[1])
      
    }

    //////////////////////////////////
    //SEND TO ARDUINO HERE (handshake)
    //////////////////////////////////
    if(s2_comp){
    let sendToArduino = Acceleration + "," + Brake + "\n";
      // mover()
    //print("output:");
    //print(sendToArduino);
    //writeSerial(sendToArduino, 0);
    }
  }
}







The car moves forward/backward when a user engages the foot pedals, and steers left/right when the user moves his/her hand left/right. The components of the car system are able to communicate via serial communication in p5.js. To enable this, we created 2 serial ports(one for the car and the other for the foot pedal). 




CHALLENGES

One challenge we may face during the implementation is accurately interpreting the user's hand gestures. The camera tracking system required a lot of experimentation and programming adjustments to ensure that it interprets the user's hand movements while also being light and responsive. Originally the camera was tracking the X and Y axis, but it caused p5 to be very slow and laggy because of the number of variables that it needs to keep track of. The solution was to simply remove one of the axes, this improved the responsiveness of the program drastically.




The initial plan was to operate the car wirelessly, however, this was not possible due to many factors, such as using the wrong type of Bluetooth board. With limited time, we resorted to working with two serial ports for communication between the car, the pedals, and the hand gesture control. This introduced a new problem- freely moving the car. However, we solved the issue by using an Arduino USB extension cable for the car to be able to move freely.




Another major roadblock was the serial ports in p5js. Since the project uses both a pedal and the car, there was the need to use 2 separate Arduino Uno boards to control both systems. This necessitated the use of 2 serial ports in p5js. The original starter code for connecting p5 to Arduino was only for 1 serial port. A lot of time was spent adjusting the existing code to function with 2 serial ports. 




Lessons learned, especially with regard to robots, is be grouped into the following points: 

Planning is key: The project can quickly become overwhelming without proper planning. It's important to define the project goals, select appropriate devices and how to sync the code to those devices and create a detailed project plan. 

Test as often as you can before the showcase date: Testing is crucial in robotics projects, especially when dealing with multiple hardware components and sensors. This one was no exception. It's important to test each component and module separately before combining them into the final project.

Future steps needed to take our project to the next level.

Expand functionality: While the current design allows for movement in various directions, there are other features that could be added to make the car more versatile. We plan on adding cameras and other sensors(LiDar) to detect obstacles to create a mapping of an environment while providing visual feedback to a user.
Optimize hardware and software: We also plan on optimizing the hardware and software components used. This would involve changing the motors to more efficient or powerful motors, using more accurate sensors (not using the ultrasonic sensor), or exploring other microcontrollers that can better handle the project's requirements. Additionally, optimizing the software code can improve the car's responsiveness and performance. For example, our software code can detect obstacles but cannot detect the end of a path.  Regardless, we believe we can engineer a reverse obstacle-sensing algorithm to create an algorithm that could detect cliffs and pot-holes, and dangerous empty spaces on roads to ultimately reduce road accidents.
