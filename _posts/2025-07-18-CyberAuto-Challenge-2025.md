# Introduction
As a graduate student, it was an honor to attend the CyberAuto Challenge from July 14th to July 18th, 2025, in Detroit, Michigan. This event has been running annually since its establishment in 2012, thanks to the industry's Titanium Sponsors Ford, Toyota, and Toyota Tsusho Systems US. Due to the mission statement of this event - inspiring and training the next generation workforce - it is no surprise that it is still running today and will continue in the future. The success of this event comes from the involvement of industry, government, and security researchers in aiding to mentor and nurture the younger generation within the automotive Cybersecurity space. 

# Day-to-Day
To start, each student provides their areas of interest/expertise to aid the staff in pairing them up with the right team. These areas encompass Internal Combustion Engine (ICE), and Electrical Vehicles (EV), Radio Frequency (RF), Forensics, and Reverse Engineering. For my selection, I informed staff that I was interested in pursuing the reverse engineering and hardware side, as my interests and expertise reside within this area. After supplying this information, students are promptly informed of their team and their requirement to sign an NDA; so apologies for the lack of specifics in this post. 

Some students may find the first day of the event challenging, as many are coming from diverse areas, including countries and time zones. It is taxing because the event kicks off at 8:00 AM and runs until 8:00 PM, and continues this way until Thursday. Besides going through the logistical discussions of orientation, legal, and introductions, the other topics covered were Controller Area Network (CAN) & Networks, Diagnostics over Internet Protocol (DoIP), and Software-Defined Radio (SDR)/Wireless. 

 
Figure 1 – Day 1 DOIP Instruction

In my opinion, I found the CAN & Networks and the SDR/Wireless sections to be exceptional. Before this event, I lacked familiarity with how automotive components interfaced and communicated with one another. “For those interested, the SDR/Wireless section offers an introduction to - or refresher on - key electrical engineering concepts related to the electromagnetic spectrum (e.g., radio frequencies). Students also learn wireless car hacking from DEFCON's Car Hacking Village Chief, Justin Montalbano. To end the day, students were selected to give talks on their current areas of research. One presentation caught my eye as it focused on the extraction and detection of Personally Identifiable Information (PII) within infotainment systems.

 
Figure 2 – Wireless Hacking with the Car Hacking Village

Next, on the second day of the CyberAuto Challenge, students are introduced to topics surrounding Cellular Networks, Phone Application Attacks, and the Forensics of Automotive Systems. As I have the privilege of testing phone applications from time to time, the Phone Application Attacks piece was of high interest. This section involved intercepting requests and responses via the phone application through Burp Suite's proxy feature. It was intriguing to conduct an analysis and exploitation of the various APIs. These APIs conducted actions on the vehicle through the phone application, such as locking and unlocking the car. Due to the progressive advancement of car technology, newer features would result in additional potential affects and attack surfaces. Another interesting topic, Forensics, was taught by industry professionals from Berla Corporation and the Michigan State Police. They emphasized the importance of tying vehicle data to the implications of a crime. A technical component of the course involved removing chips from circuit boards (chip-off) and soldering wires to extract data or firmware via SWD. The data/firmware extraction process involved a multimeter to test the continuity between chip pins and the corresponding debug ports on the back of the board. Once the soldering process was complete, students conducted data extraction via serial connection. 

The third day, taught by the Idaho National Laboratory (INL), involved Electrical Infrastructure for Vehicles, Electrical Vehicles, and an E-CTF, followed by the Assessment ROE and Mission Planning. 

 
Figure 3 – Day 3 of Instruction

Although a foreign topic, the intricacies of EV infrastructure were fascinating regarding the interaction between the charging station and vehicle components. Students participated in a hands-on observation of HomePlug Green PHY's Signal Level Attenuation Characteristic (SLAC) request/response protocol between the Electrical Vehicle Supply Equipment (EVSE) and Plug-in Electric Vehicle (PEV). 

 
Figure 4 – PVE and EVSE Lab

 
Figure 5 – Level 1 Charger Testing

For students interested in the E-CTF, there were stations with unique challenges that involved CAN manipulation, data extraction, and reverse engineering, followed by learning concepts like EV infrastructure. The team with the highest score at the end of the day is awarded a prize, for instance, a Flipper Zero.

The last day was a 24-hour hackathon where a majority of students and their teams tapped out around 2:00 AM. According to the CyberAuto Challenge staff, this is a recurring trend. During breakfast, students discuss their areas of interest for testing their system, which naturally divides the team into groups. These groups focus on the topics covered during the beginning of the week. As this is only a 24-hour event, students carefully plan out how to effectively manage their time during the development and execution of test cases. One of the groups I was a part of focused on the system's hardware. However, it was a marathon; the team as a whole learned a great deal about the chipsets in use, the firmware extraction process, and the firmware. For the final hours of our assessment, the groups gathered and formed their presentations pertaining to their findings and or developed frameworks. As a part of the hardware group, our presentation involved a framework for conducting product security testing within the automotive industry. Overall, this was a rewarding experience, and I would highly advocate that those within the academic field pursue this opportunity.
