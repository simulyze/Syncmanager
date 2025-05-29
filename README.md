PlayerSyncManager Sync Tuner - README

========================================
OVERVIEW

This sync tuner system lets you tweak all important multiplayer network player smoothing, correction, and prediction variables in your Unity game. There are three UI panels: Low-Level 1, Low-Level 2, and High-Level. All variables are live and interact with each other.

========================================
VARIABLES - WHAT THEY DO & WHAT THEY AFFECT

Low-Level Panel 1 Variables:
----------------------------
updateInterval              
- How often (seconds) to send player updates. Lower = smoother, but uses more bandwidth. Affects all sync timing and smoothing. Interacts with inputSmoothing, baseInterpTime, baseJitterBufferDelay.
- 
positionTolerance
- How far a player can drift before correction. Affects correction frequency. Interacts with teleportThreshold, maxCorrectionDistance, desyncToleranceMultiplier.
- 
rotationTolerance
 - How much rotation error allowed before snapping/correcting. Affects correction frequency. Interacts with correctionAggressiveness.
 - 
teleportThreshold
 - Distance before a full teleport correction. Handles major desync/jitter. Interacts with positionTolerance, maxCorrectionDistance.
 - 
initialMoveDelay
  - Delay before first move update after spawn. Affects spawn movement.
  - 
initialCorrectGracePeriod
  - Time after spawn before corrections can happen. Affects initial smoothness.
  - 
movementThreshold
  - Minimum movement before sending an update. Affects update rate. Interacts with minMoveDistance.
  - 
minMoveDistance
  - Smallest movement to send/correct. Affects correction sensitivity. Interacts with movementThreshold.
  - 
minRotationAngle
   - Smallest rotation to send/correct. Affects update/correction sensitivity. Interacts with rotationTolerance.
   - 
inputSmoothing
 - Smoothing factor for local input. Affects local movement feel. Interacts with updateInterval, baseInterpTime.
 - 
baseInterpTime              
- Base time for interpolating between remote updates. Affects smoothing, lag feel. Interacts with updateInterval, minInterpTime, basePredictionFactor.
- 
minInterpTime
  - Minimum interpolation time. Sets lower smoothing bound. Interacts with baseInterpTime.
  - 
rotationDamping
 - Damping for smoothing rotations. Affects how soft/hard rotations feel. Interacts with rotationTolerance, baseInterpTime.
 - 
basePredictionFactor
- Base prediction/extrapolation factor. Affects how much the system predicts. Interacts with baseInterpTime, extrapolationLimiter, maxExtrapolationTime.
- 
baseVelocitySmoothing
 - Smoothness of velocity blending for remotes. Affects remote movement sharpness. Interacts with velocityPredictionDamping.
 - 
velocityPredictionDamping
 - Damping when blending predicted/actual velocity. Affects smoothness. Interacts with baseVelocitySmoothing.
 - 
extrapolationLimiter
 - Limits maximum extrapolation. Controls how wild predictions can get. Interacts with basePredictionFactor, maxExtrapolationTime.
 - 
baseCorrectionSmoothing
  - How fast corrections are applied. Higher is slower/smoother. Interacts with correctionAggressiveness, correctionDamping.
  - 
desyncCorrectionTime
  - How long before major desync triggers correction. Affects delay of corrections. Interacts with reCorrectionDelay, maxCorrectionDistance.
  - 
baseJitterBufferDelay
   - How much time to buffer network packets to smooth out jitter. Affects lag smoothing. Interacts with updateInterval, minStateBufferSize, maxStateBufferSize.
   - 
minStateBufferSize
  - Minimum number of state buffers for interpolation. Affects smoothing. Interacts with maxStateBufferSize, baseJitterBufferDelay.
  - 
maxStateBufferSize
 - Maximum number of state buffers. Affects smoothing/lag handling. Interacts with minStateBufferSize, baseJitterBufferDelay.
 - 

Low-Level Panel 2 Variables:
----------------------------
maxCorrectionSpeed          
- Max speed to correct a desynced player. Controls snap speed. Interacts with baseCorrectionSmoothing, correctionAggressiveness.
- 
correctionDamping
 - Damping for correction. Smooths out large corrections. Interacts with baseCorrectionSmoothing, correctionAggressiveness.
 - 
reCorrectionDelay
 - Time before allowing another forced correction. Prevents over-correction. Interacts with desyncCorrectionTime.
 - 
maxExtrapolationTime
- Maximum allowed prediction time. Controls how far prediction can go. Interacts with basePredictionFactor, extrapolationLimiter.
- 

High-Level Panel Variables:
---------------------------
maxCorrectionDistance       
- Maximum distance to allow correction before teleporting. Affects major corrections. Interacts with teleportThreshold, positionTolerance.
- 
overCorrectionThreshold
 - Threshold for what counts as an "over-correction." Used in diagnostics. Interacts with maxCorrectionDistance.
 - 
reCorrectionThreshold
 - Minimum distance to trigger a forced recorrection. Affects snap corrections. Interacts with desyncCorrectionTime, reCorrectionDelay.
 - 
correctionAggressiveness
 - Multiplier for how aggressive corrections are. Affects correction smoothing. Interacts with baseCorrectionSmoothing, correctionDamping.
 - 
predictionAdjustmentFactor
  - Factor for prediction blending. Affects how much prediction is applied. Interacts with basePredictionFactor, extrapolationLimiter.
  - 
desyncToleranceMultiplier
  - Multiplies all tolerance thresholds. Affects all correction/teleport triggers. Interacts with positionTolerance, teleportThreshold.
  - 

========================================
INTERDEPENDENCIES

- updateInterval affects all time-based smoothing and prediction.
- baseInterpTime, basePredictionFactor, extrapolationLimiter, maxExtrapolationTime all work together for prediction/interpolation.
- baseCorrectionSmoothing, correctionAggressiveness, correctionDamping, maxCorrectionSpeed combine to control how corrections are applied.
- Buffer settings (baseJitterBufferDelay, minStateBufferSize, maxStateBufferSize) work together for smoothing and lag handling.
- Tolerance and thresholds (positionTolerance, rotationTolerance, teleportThreshold, desyncToleranceMultiplier, maxCorrectionDistance) dictate when corrections occur.
- reCorrectionDelay, desyncCorrectionTime, reCorrectionThreshold control how often corrections are triggered.

========================================
REQUIRED SCRIPTS

PlayerSyncManager.cs   (this script, controls all sync logic)
GameNetworkManager.cs  (handles actual network comms and server time)
DotShooter.cs          (handles boost/shot logic if used)
PlayerManagerClient.cs (tracks local player in scene)
OtherPlayersManagerClient.cs (spawns and manages other players, health bars, etc)

========================================
REQUIRED GAMEOBJECTS AND UI SETUP

- A GameObject (empty is fine) with PlayerSyncManager.cs attached (should use DontDestroyOnLoad).
- UI panels for Low-Level 1, Low-Level 2, High-Level settings.
  - Each panel needs a content parent for variable entries, a variable entry prefab (with NameField, ValueField, PlusButton, MinusButton), and toggle/reset buttons.
- Diagnostics panel (optional but recommended), with a prefab that has a Text field for displaying live diagnostics.
- Hz toggle button to switch between 60Hz and 120Hz mode.
- Player GameObjects (tagged as "Player"), should have a PlayerMovement script.
- OtherPlayers spawned by OtherPlayersManagerClient, each with a unique ID.
