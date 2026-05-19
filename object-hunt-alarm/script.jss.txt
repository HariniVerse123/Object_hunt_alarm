// ============================================================
// OBJECT HUNT ALARM SYSTEM
// ============================================================
// FEATURES:
// ------------------------------------------------------------
// 1. Alarm Clock System
// 2. Random Object Generator
// 3. AI Object Detection
// 4. Webcam Activation
// 5. Continuous Alarm Ringing
// 6. Voice Assistant
// 7. Detection Confidence System
// 8. Futuristic System Logs
// 9. Automatic Alarm Stop
// 10. Real-Time Object Recognition
// ============================================================

// ============================================================
// TEACHABLE MACHINE MODEL URL
// ============================================================

const URL =
"https://teachablemachine.withgoogle.com/models/tMp8YZJlX/";

// ============================================================
// GLOBAL VARIABLES
// ============================================================

// AI MODEL

let model;
let webcam;
let maxPredictions;

// ALARM VARIABLES

let alarmTime = null;
let alarmStarted = false;
let alarmCurrentlyPlaying = false;

// AI DETECTION VARIABLES

let selectedObject = "";
let detectionCounter = 0;
let predictionLoopRunning = false;
let successfulDetectionFrames = 25;

// OBJECT GENERATION VARIABLES

let randomIndex = 0;

// SYSTEM VARIABLES

let currentPrediction = "";
let highestPrediction = 0;

// WEBCAM VARIABLES

let webcamStarted = false;

// AUDIO VARIABLES

let alarmAudio =
document.getElementById("alarmSound");

// ============================================================
// OBJECTS
// IMPORTANT:
// THESE MUST MATCH YOUR TEACHABLE MACHINE
// CLASS NAMES EXACTLY
// ============================================================

const objects = [

    "Lock",
    "Water Bottle",
    "Pen",
    "Watch",
    "Spoon",
    "Drinking Cup"
];

// ============================================================
// SYSTEM STARTUP
// ============================================================

window.onload = () => {

    console.log(
        "====================================="
    );

    console.log(
        "OBJECT HUNT ALARM INITIALIZED"
    );

    console.log(
        "====================================="
    );

    updateStatus(
        "SYSTEM READY"
    );

    animateStatus();
};

// ============================================================
// STATUS SYSTEM
// ============================================================

function updateStatus(message){

    document.getElementById(
        "status"
    ).innerText = message;

    console.log(
        "STATUS:",
        message
    );
}

// ============================================================
// TARGET OBJECT DISPLAY
// ============================================================

function updateTargetObject(message){

    document.getElementById(
        "targetObject"
    ).innerText = message;
}

// ============================================================
// TEXT TO SPEECH SYSTEM
// ============================================================

function speak(text){

    window.speechSynthesis.cancel();

    const speech =
    new SpeechSynthesisUtterance(text);

    speech.lang = "en-US";

    speech.volume = 1;

    speech.rate = 1;

    speech.pitch = 1;

    window.speechSynthesis.speak(
        speech
    );
}

// ============================================================
// LOAD AI MODEL
// ============================================================

async function loadModel(){

    try{

        updateStatus(
            "LOADING AI MODEL..."
        );

        console.log(
            "Loading Teachable Machine Model..."
        );

        const modelURL =
        URL + "model.json";

        const metadataURL =
        URL + "metadata.json";

        model =
        await tmImage.load(
            modelURL,
            metadataURL
        );

        maxPredictions =
        model.getTotalClasses();

        console.log(
            "MODEL LOADED SUCCESSFULLY"
        );

        console.log(
            "TOTAL CLASSES:",
            maxPredictions
        );

        updateStatus(
            "AI MODEL ONLINE"
        );

        return true;
    }

    catch(error){

        console.error(
            "MODEL LOADING FAILED:",
            error
        );

        updateStatus(
            "MODEL LOAD FAILED"
        );

        alert(
            "Failed to load AI model."
        );

        return false;
    }
}

// ============================================================
// RANDOM OBJECT GENERATOR
// ============================================================

function generateRandomObject(){

    randomIndex =
    Math.floor(
        Math.random() * objects.length
    );

    selectedObject =
    objects[randomIndex];

    console.log(
        "====================================="
    );

    console.log(
        "RANDOM OBJECT GENERATED"
    );

    console.log(
        "TARGET OBJECT:",
        selectedObject
    );

    console.log(
        "====================================="
    );

    return selectedObject;
}

// ============================================================
// SET ALARM
// ============================================================

function setAlarm(){

    const input =
    document.getElementById(
        "alarmTime"
    ).value;

    if(input === ""){

        alert(
            "Please select alarm time."
        );

        return;
    }

    alarmTime = input;

    console.log(
        "ALARM SET FOR:",
        alarmTime
    );

    updateStatus(
        "ALARM SET FOR " + alarmTime
    );

    speak(
        "Alarm set successfully"
    );
}

// ============================================================
// ALARM TIME MONITOR
// ============================================================

setInterval(() => {

    if(alarmTime === null){

        return;
    }

    if(alarmStarted){

        return;
    }

    const now =
    new Date();

    const hours =
    String(
        now.getHours()
    ).padStart(2,"0");

    const minutes =
    String(
        now.getMinutes()
    ).padStart(2,"0");

    const currentTime =
    hours + ":" + minutes;

    if(currentTime === alarmTime){

        console.log(
            "ALARM TIME REACHED"
        );

        startAlarmSequence();
    }

},1000);

// ============================================================
// MAIN ALARM SEQUENCE
// ============================================================

async function startAlarmSequence(){

    try{

        alarmStarted = true;

        console.log(
            "====================================="
        );

        console.log(
            "STARTING ALARM SEQUENCE"
        );

        console.log(
            "====================================="
        );

        updateStatus(
            "INITIALIZING SYSTEM..."
        );

        // ----------------------------------------------------
        // STEP 1:
        // LOAD AI MODEL
        // ----------------------------------------------------

        const modelLoaded =
        await loadModel();

        if(!modelLoaded){

            return;
        }

        // ----------------------------------------------------
        // STEP 2:
        // GENERATE RANDOM OBJECT
        // ----------------------------------------------------

        generateRandomObject();

        updateTargetObject(
            "FIND: " + selectedObject
        );

        // ----------------------------------------------------
        // STEP 3:
        // VOICE ASSISTANT
        // ----------------------------------------------------

        speak(
            "Wake up. Find a " +
            selectedObject
        );

        // ----------------------------------------------------
        // STEP 4:
        // START ALARM SOUND
        // ----------------------------------------------------

        startAlarmAudio();

        // ----------------------------------------------------
        // STEP 5:
        // START WEBCAM
        // ----------------------------------------------------

        await startWebcam();

        // ----------------------------------------------------
        // STEP 6:
        // START AI DETECTION
        // ----------------------------------------------------

        predictionLoopRunning = true;

        window.requestAnimationFrame(
            predictionLoop
        );

        updateStatus(
            "AI OBJECT SEARCH ACTIVE"
        );
    }

    catch(error){

        console.error(
            "ALARM SEQUENCE ERROR:",
            error
        );
    }
}

// ============================================================
// START ALARM AUDIO
// ============================================================

function startAlarmAudio(){

    try{

        alarmAudio.volume = 1;

        alarmAudio.loop = true;

        alarmAudio.play();

        alarmCurrentlyPlaying = true;

        console.log(
            "ALARM AUDIO STARTED"
        );
    }

    catch(error){

        console.error(
            "AUDIO PLAYBACK ERROR:",
            error
        );
    }
}

// ============================================================
// STOP ALARM AUDIO
// ============================================================

function stopAlarmAudio(){

    alarmAudio.pause();

    alarmAudio.currentTime = 0;

    alarmCurrentlyPlaying = false;

    console.log(
        "ALARM AUDIO STOPPED"
    );
}

// ============================================================
// START WEBCAM
// ============================================================

async function startWebcam(){

    try{

        console.log(
            "INITIALIZING WEBCAM..."
        );

        const flip = true;

        webcam =
        new tmImage.Webcam(
            500,
            500,
            flip
        );

        await webcam.setup();

        await webcam.play();

        webcamStarted = true;

        console.log(
            "WEBCAM STARTED"
        );

        const webcamContainer =
        document.getElementById(
            "webcam-container"
        );

        webcamContainer.innerHTML = "";

        webcamContainer.appendChild(
            webcam.canvas
        );
    }

    catch(error){

        console.error(
            "WEBCAM ERROR:",
            error
        );

        alert(
            "Failed to access webcam."
        );
    }
}

// ============================================================
// MAIN AI PREDICTION LOOP
// ============================================================

async function predictionLoop(){

    if(!predictionLoopRunning){

        return;
    }

    webcam.update();

    await detectObject();

    window.requestAnimationFrame(
        predictionLoop
    );
}

// ============================================================
// OBJECT DETECTION SYSTEM
// ============================================================

async function detectObject(){

    const prediction =
    await model.predict(
        webcam.canvas
    );

    highestPrediction = 0;

    currentPrediction = "";

    for(let i = 0; i < prediction.length; i++){

        const className =
        prediction[i].className;

        const probability =
        prediction[i].probability;

        // ----------------------------------------------------
        // TRACK HIGHEST PREDICTION
        // ----------------------------------------------------

        if(probability > highestPrediction){

            highestPrediction =
            probability;

            currentPrediction =
            className;
        }

        // ----------------------------------------------------
        // DEBUG LOGS
        // ----------------------------------------------------

        console.log(
            className,
            probability.toFixed(2)
        );

        // ----------------------------------------------------
        // CORRECT OBJECT DETECTION
        // ----------------------------------------------------

        if(
            className === selectedObject &&
            probability > 0.50
        ){

            detectionCounter++;

            updateStatus(
                "OBJECT DETECTED " +
                detectionCounter +
                "/" +
                successfulDetectionFrames
            );

            console.log(
                "CORRECT OBJECT DETECTED"
            );

            console.log(
                "DETECTION COUNT:",
                detectionCounter
            );

            // ------------------------------------------------
            // REQUIRE MULTIPLE DETECTIONS
            // ------------------------------------------------

            if(
                detectionCounter >=
                successfulDetectionFrames
            ){

                objectFoundSequence();

                return;
            }
        }

        else{

            detectionCounter = 0;
        }
    }
}

// ============================================================
// OBJECT FOUND SEQUENCE
// ============================================================

function objectFoundSequence(){

    console.log(
        "====================================="
    );

    console.log(
        "OBJECT FOUND SUCCESSFULLY"
    );

    console.log(
        "STOPPING ALARM"
    );

    console.log(
        "====================================="
    );

    predictionLoopRunning = false;

    stopAlarmAudio();

    stopWebcam();

    updateStatus(
        "OBJECT FOUND • ALARM STOPPED"
    );

    updateTargetObject(
        "MISSION COMPLETE"
    );

    speak(
        "Object found. Alarm stopped."
    );

    resetSystem();
}

// ============================================================
// STOP WEBCAM
// ============================================================

function stopWebcam(){

    if(webcam){

        webcam.stop();

        webcamStarted = false;

        console.log(
            "WEBCAM STOPPED"
        );
    }
}

// ============================================================
// RESET SYSTEM
// ============================================================

function resetSystem(){

    console.log(
        "RESETTING SYSTEM..."
    );

    alarmStarted = false;

    alarmTime = null;

    detectionCounter = 0;

    selectedObject = "";

    currentPrediction = "";

    highestPrediction = 0;

    setTimeout(() => {

        updateTargetObject("");

        updateStatus(
            "SYSTEM READY"
        );

    },3000);
}

// ============================================================
// OPTIONAL TEST BUTTON
// ============================================================

async function testAlarm(){

    console.log(
        "RUNNING TEST MODE"
    );

    await startAlarmSequence();
}

// ============================================================
// FUTURISTIC STATUS ANIMATION
// ============================================================

function animateStatus(){

    const statuses = [

        "AI READY",
        "VISION ONLINE",
        "SYSTEM ACTIVE",
        "OBJECT DETECTION READY"
    ];

    let index = 0;

    setInterval(() => {

        if(alarmStarted){

            return;
        }

        updateStatus(
            statuses[index]
        );

        index++;

        if(index >= statuses.length){

            index = 0;
        }

    },4000);
}

// ============================================================
// KEYBOARD SHORTCUTS
// ============================================================

document.addEventListener(
    "keydown",
    (event) => {

        // TEST MODE

        if(event.key === "t"){

            testAlarm();
        }

        // STOP EVERYTHING

        if(event.key === "Escape"){

            stopAlarmAudio();

            stopWebcam();

            resetSystem();

            updateStatus(
                "SYSTEM MANUALLY STOPPED"
            );
        }
    }
);

// ============================================================
// CONSOLE STARTUP MESSAGE
// ============================================================

console.log(`
===========================================
 OBJECT HUNT ALARM SYSTEM
===========================================

AI STATUS       : ONLINE
VISION SYSTEM   : READY
WEBCAM SYSTEM   : READY
OBJECT DATABASE : LOADED

Press 'T' for Test Mode
Press 'ESC' to Force Stop

===========================================
`);