# Entry 5
##### 3/15/20

#### Progress
Over the mid-winter break, I met up with Shuquan to finish our basic idea of a door lock system that can be opened with registered Mastercard and tags using their UID. We were able to use the Arduino's Dump Info example to find out the UIDs of the Mastercard and one tag. Throughout this product, I referred to [Arduino Reference](https://www.arduino.cc/reference/en/) page for help so that I can understand what code's syntax, parameter, and return value.

Before I met up with Shuquan, I try to learn how to get the UID so I can use it for comparison. My first search was at [Stackoverflow](https://stackoverflow.com/questions/32839396/how-to-get-the-uid-of-rfid-in-arduino). The person, named Jinha Kim, asked the question also needed to be able to get the UID of their tags. In respond to Kim, the respondent Hans Kilian suggests to use:

```
if (mfrc522.uid.uidByte[0] == 0x84 &&
     mfrc522.uid.uidByte[1] == 0x90 &&
     mfrc522.uid.uidByte[2] == 0x6c &&
     mfrc522.uid.uidByte[3] == 0xa7) {
      // turn your LED on
     }
```
I understood that this would individually check through each separate of the UID that I would pass to it. However, I felt that there is a much more efficient way such as creating a for loop to loop through the indexes of `mfrc522.uid.uidByte[]`. I edited the code so that it loops through the indexes, but I remember that sometimes the UID can be 7 digits long so I again refer to online searches that I came across a person named saiyojeff on [Miguelbalboa's Github](https://github.com/miguelbalboa/rfid/issues/63) page asking a similar question. A respondent named omersiar said to try this:

```
byte readCard[]; // This is our byte array to store UID mind that there are 4 and 7 bytes long UID

Serial.println("Scanned PICC's UID:");
 for (int i = 0; i < mfrc522.uid.size; i++) {
    readCard[i] = mfrc522.uid.uidByte[i];
    Serial.print(readCard[i], HEX);
  }
  Serial.println("");
```
This did allow the code to be more efficient, but not expandable. By only storing one UID in the `readCard[]` array I can only have one tag since the for loop always starts at the zeroth index. However, I wanted to add more UIDs from other tags so that multiple tags can be used to open the door. It would be inconvenient to only have one card or tag. This leads me to take what I learned now to implement my idea. The code below shows how I can print the UID into Serial (for debugging if I need to) and compare the UID with the UID that I have already gotten from the Dump Info example.

```
  //Show UID on serial monitor
  Serial.print("\nUID: ");
  String content = "";
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "); //Print UID on the Serial
    Serial.print(mfrc522.uid.uidByte[i], HEX);
    content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ")); //set the UID into content for use
    content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  Serial.println();
  content.toUpperCase();
  if (content.substring(1) == "15 D2 D2 22" || content.substring(1) == "89 E8 06 BA") //1: Mastercard, 2: TAG: BA,
  {
    Serial.println("Access Granted. Opening...");
    delay(1000);

    //Servo Run
    val = digitalRead(potpin);            // reads the value of the potentiometer (value between 0 and 1023)
    val = map(val, 0, 1023, 0, 180);     // scale it to use it with the servo (value between 0 and 180)
    myservo.write(val);                  // sets the servo position according to the scaled value

    Serial.println("Closing...");
    delay(2000);                          // delay
    myservo.write(180);                   // closed degree:180
  }

  else {
    Serial.println("Access denied");
    delay(2000);
  }
  ```

The first half of the code was to let Arduino find the UID as soon as the cards are scanned on the MFRC522 by looping through the UID that the card/tag sends out. After Arduino found the UID, Arduino would print the UID on the Serial and also store it in the String variable called `content`. The second half uses an if-else statement to compare the UID stored in `content` with a UID that I copied into the if-else statement. If the UID from the card/tag matches, Arduino is programmed to say "Access Granted. Opening..." and open the door by turning the Servo (Shuquan and I decide to use a Servo instead of a Solenoid Lock); on the other hand, if it does not match then Arduino is programmed to say "Assess Denied".

#### Engineering Design Process
Over the break, we went through Step 6, tested and evaluated the prototype, Step 7, improve the design, and Step 8, communicate the results, of the engineering design process. Shuquan completed the Arduino build by attaching the Servo and I coded the Servo to spin only with the UID is matched. After we attached and coded, we tested the design by using multiple tags since each tag has different UID. At the end of testing, I was able to improve the project by adding a new tag and adding some delay so it is easier to read in the Serial and also allow the Arduino to read the tags more easily.

Nevertheless, we thought the project can be further improved, so we have gone back to Step 4 to plan a better solution. We thought about the problems that can arise with our projects. A problem that we need to solve was the inconvenience of tags and how it can be easily forgotten, stolen, and lost. Furthermore, we need to make the home more secure even if there are people at home. Since individuals, such as children, can be easily fooled by knocks on the door, we also want to find ways that the user can make sure who is outside.

#### Skill/Skills
In creating this prototype for home security, I used Google and did many online searches to be able to understand clearly what each function does and how I can use these functions properly in my project. Also, Google searches are so great because of the large community that the internet is. By taking advantage of sharing information worldwide, I can progress my knowledge by learning from people that are much more knowledgable.

It is also important to develop my ability to pay attention to details. Since coding involves the usage of keywords known as code, I need to make sure that they are using the correct code for the right situation. Moreover, coding needs the programmer to follow the rules of syntax. If any error happens, it would most likely be caused by syntax error or incorrect spelling. Furthermore, paying attention to details allows me to solve the issues that would arise from my project. I need to much put as much attention as possible to be able to solve new problems and find solutions to them.

#### Next Steps
Shuquan and I have decided to further improve it by adding more accessibility and function to this house safety project. For example, we are going to solve the inconvenience usage of cards and tags by using a Bluetooth module that users can connect to. By connecting to the Bluetooth module, the user can operate the tools that we have connected the Arduino to. Since the majority of people in modern society have phones, it will be much more practical to use Bluetooth. The Bluetooth module can act as a backup to open the door in case the card is lost.

Some more additions are Optical Character Recognition (OCR) or Facial Recognition using a camera. I would like to be able to take a picture, such as a certain text (ie. Name and Address) to be compared to a package with similar information that can be used to confirm a package. Alternatively, we can take a picture of a logo (ie. USPS logo) and compare it to a logo on a shirt of a delivery person to make sure that the person at the front door is a delivery person and not a stranger. Facial Recognition can also be used to grant access to a person through the usage of face tracking and scanning. By doing so, only authorized people can get access to the tools programmed, such as opening a door.

All in all, our next step is to implement the Bluetooth device to the Arduino and be able to access the Arduino through our phone and also further study the usage of OCR and Facial Recognition.

<br><br>
[Previous](entry04.md) | [Next](entry06.md)

[Home](../README.md)














