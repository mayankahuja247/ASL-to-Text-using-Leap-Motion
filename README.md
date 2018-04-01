

In[1]:= Needs["JLink`"];

In[2]:= With[
  {lmlib = 
    "C:\\Users\\mayan\\Downloads\\LeapDeveloperKit_3.2.1_win\\\
LeapDeveloperKit_3.2.1+45911_win\\LeapSDK\\lib\\"},
  AddToClassPath[lmlib, FileNameJoin[{lmlib, "x64"}]]
  ];

In[3]:= InstallJava[]

Out[3]= LinkObject["\"C:\\Program Files\\Wolfram Research\\Wolfram \
Desktop\\11.3\\SystemFiles\\Java\\Windows-x86-64\\bin\\javaw.exe\" -classpath \
\"C:\\Program Files\\Wolfram Research\\Wolfram \
Desktop\\11.3\\SystemFiles\\Links\\JLink\\JLink.jar\" -Xmx512m  \
-Djava.system.class.loader=com.wolfram.jlink.JLinkSystemClassLoader \
com.wolfram.jlink.Install -init \
\"C:\\Users\\mayan\\AppData\\Local\\Temp\\m-78d22ed9-d37f-416a-9240-\
72b46b129547\"", 80, 4]

controller = LoadJavaClass["com.leapmotion.leap.Controller"]

Out[4]= JLink`JavaClass["com.leapmotion.leap.Controller", 3, {
JLink`JVM["vm1"]}, 4, "com`leapmotion`leap`Controller`", False, True]

Methods[controller];

In[6]:= controller = JavaNew["com.leapmotion.leap.Controller"]


Out[6]= JLink`Objects`vm1`JavaObject15611175379140609

In[33]:= Methods[controller];

In[8]:= controller@frame[]@fingers[]@get[1]

Out[8]= JLink`Objects`vm1`JavaObject35896312612257793

In[147]:= fgrs@count[]

Out[147]= 5





In[250]:= fr = controller@frame[];

fgrs = fr@fingers[];

posOfFingers = Map[
   Function[
    {number},
    f1 = fgrs@get[number];
    pos = f1@tipPosition[];
    {pos@getX[], pos@getY[], pos@getZ[]}
    ],
   Range@fgrs@count[]
   ];

(*do same for hand*)
hds = fr@hands[];

posOfPalms = Map[
   Function[
    {number},
    hd = hds@get[number];
    pos = hd@palmPosition[];
    {pos@getX[], pos@getY[], pos@getZ[]}
    ],
   Range@hds@count[]
   ];

Dataset@<|"hand" -> posOfPalms, "fingers" -> posOfFingers|>

Out[255]= Dataset[ <> ]

In[258]:= Methods[fr];

In[123]:= fr = controller@frame[];
hds = fr@hands[];
hd = hds@get[1];
pmpos = hd@palmPosition[];
pmpos@getZ[]


Out[127]= 0.

getFrameData = Module[
   {getFrame, getFingerList, getVector, makeRule, fun}, 
   getFrame[controller_] := controller@frame[];
   
   getFingerList[frame_] := 
    With[{f = frame@fingers[]}, 
     Table[f@get[finger], {finger, 0, f@count[] - 1}]];
   
   getVector[v_] := {v@getX[], v@getY[], v@getZ[]};
   
   makeRule[finger_, map_, method_] := method -> map[finger@method[]];
   
   fun[controller_] := With[
     {fr = getFrame[controller]},
     {
      makeRule[fr, #/1000000. &, timestamp],
      fingers -> ({
           makeRule[#, Identity, length],
           makeRule[#, getVector, direction],
           makeRule[#, getVector, tipPosition],
           makeRule[#, getVector, tipVelocity],
           makeRule[#, getVector, stabilizedTipPosition]
           } & /@ getFingerList[fr])}
     ];
   
   fun
   ];

In[35]:= getFrameData[controller]

Out[35]= {timestamp -> 32185.4, fingers -> {}}

Graphics3D[{Dynamic[
    Sphere[{1, -1, 1} #[[{1, 3, 2}]], 
       15] & /@ ((tipPosition /. #) & /@ (fingers /. 
         getFrameData[controller])), UpdateInterval -> 0.05], 
   PlotRange -> {{-150, 150}, {-150, 150}, {0, 300}}}
  {Dynamic[
    Sphere[{1, -1, 1} #[[{1, 3, 2}]], 
       15] & /@ ((palmPosition /. #) & /@ (palm /. getFrameData[controller])),
     UpdateInterval -> 0.05], 
   PlotRange -> {{-150, 150}, {-150, 150}, {0, 300}}}]

In[306]:= recording = Table[Pause[.1]; getFrameData[controller], 10];


In[305]:= Dimensions@recording

Out[305]= {10, 2}




In[307]:= ListAnimate@Map[Function[
   {frame},
   Graphics3D[{Dynamic[
       Sphere[{1, -1, 1} #[[{1, 3, 2}]], 
          15] & /@ ((tipPosition /. #) & /@ (fingers /. frame)), 
       UpdateInterval -> 0.05], 
      PlotRange -> {{-150, 150}, {-150, 150}, {0, 300}}}
     {Dynamic[
       Sphere[{1, -1, 1} #[[{1, 3, 2}]], 
          15] & /@ ((palmPosition /. #) & /@ (palm /. frame)), 
       UpdateInterval -> 0.05], 
      PlotRange -> {{-150, 150}, {-150, 150}, {0, 300}}}]
   ], recording]




In[28]:= Lookup[{recording[[5]], fingers}
  {recording[[5]], palm}](*This is how commenting works in wolfram*)

Out[28]= Lookup[{recording[[5]], fingers} {recording[[5]], palm}]

Part
ReplaceAll

import data

In[259]:= dir = "C:\\Users\\mayan\\OneDrive\\Documents\\letters";

In[262]:= imageFiles = FileNames["*", dir, {2}];



Out[265]= "F"

In[270]:= getLabeledData[file_] := Module[
  {label, image},
  label = FileNameTake[file, {-2}];
  image = Import[file];
  image -> label
  ]

In[272]:= getLabeledData[imageFiles[[34]]]



In[273]:= Length@imageFiles

Out[273]= 258

In[274]:= classifyMe = Map[getLabeledData, imageFiles];

In[275]:= c = Classify[classifyMe]

Out[275]= ClassifierFunction[\[Ellipsis]]

In[315]:= image = CurrentImage[]









In[316]:= c[image, "Probabilities"]

Out[316]= <|"F" -> 0., "L" -> 0., "O" -> 1., "W" -> 3.51637*10^-46|>



In[332]:= i = 0;
Dynamic[
 image = CurrentImage[];
 c[image]
 , UpdateInterval -> 2, TrackedSymbols :> {}]























