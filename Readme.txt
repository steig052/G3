Adafruit Feather board behaves like a bluetooth keyboard. Letters are sent to Unity
Each letter represents a specific signal

Meaning of letters 
	flex sensor
		q - pinky
		w - ring
		e - middle
		r - index
		t - thumb
	thumb force 
 		a - 0
 	 	s - 1
  		d - 2
  		f - 3
	palm force 
  		g - 0
  		h - 1
  		j - 2
  		k - 3
	other
		z - calibration button pressed
		x - next calibration scene

HID packets (3)
	Each packet cannot contain more than 6 keys. 
	class: hid_keyboard_report_t
	

	packet 1	key	inputKeycodes		Pin		Meaning
	[0] ------------ z ---------[0]----------------- 9  ----------- calibration button pressed
	[1] ------------ q ---------[1]----------------- A5 ----------- pinky
	[2] ------------ w ---------[2]----------------- 4  ----------- ring
	[3] ------------ e ---------[3]----------------- 3  ----------- middle
	[4] ------------ r ---------[4]----------------- 2  ----------- index
	[5] ------------ t ---------[5]----------------- 1  ----------- thumb
	packet 2
	[0] ------------ a ---------[6]----------------- 0  ----------- thumb force level 0
	[1] ------------ s ---------[7]----------------- 0  ----------- thumb force level 1
	[2] ------------ d ---------[8]----------------- 0  ----------- thumb force level 2
	[3] ------------ f ---------[9]----------------- 0  ----------- thumb force level 3
	[4] ------------ x ---------[14]----------------    ----------- next in calibration scene
	[5] --------- nothing ------[ ]-----------------    ----------- 
	packet 3
	[0] ------------ g ---------[10]---------------- A7 ----------- thumb force level 0
	[1] ------------ h ---------[11]---------------- A7 ----------- thumb force level 1
	[2] ------------ j ---------[12]---------------- A7 ----------- thumb force level 2
	[3] ------------ k ---------[13]---------------- A7 ----------- thumb force level 3
	[4] --------- nothing ------[ ]-----------------    ----------- 
	[5] --------- nothing ------[ ]-----------------    ----------- 


4 void functions
	sendKeycode(...)
		This function sends letter 'z' or letter 'x' 
		It sends only 1 letter each time it is called

	sendKeycodeFlex(...)
		This function sends letters that each represents a finger flex sensor
		If the flex sensor bent passes the threshold, the corresponding letter is sent once
		If the previously bent flex sensor is reversed to relax state, the same letter is sent once
		The flex sensor reading is highest in its relax state, lowest in its max bent state
		Threshold: 
			  for index: 	>= 80% max bent
			  for others: 	>= 70% max bent
		Example output:	
			if index flex sensor reading:
					             exceeds 80% index max bent, send letter 'r' once
			                             still >= 80% index max bent, nothing happens
						     becomes < 80% index max bent, send letter 'r' once
							 
	sendKeycodeForce(...)
		This function sends letters that each represents a force sensor strength level
		If the force sensor compression passes the threshold, the corresponding letter is sent once
		If the previously compressed forcesensor is reversed to relax state, the same letter is sent once
		The force sensor reading is highest in its relax state, lowest in its max compressed state
		The force sensor reading = 1023 - raw reading
		Threshold: 
			  level 3:	>= 90% max compression
			  level 2:	>= 80% max compression
			  level 1:	>= 70% max compression
		Example output:	
			if thumb force sensor reading:
					             exceeds 90% max compression, send letter 'f' once
			                             still >= 90% index max bent, nothing happens
						     becomes within 80 - 90% max compression, send letter 'd' once
						     becomes < 70% max compression, send letter 'a' once
						     still < 70% max compression, nothing happens
						     
	Calibration()
		This function is called when the calibration button is pushed
		It logs the readings of flex and force sensors in their relax and max state
		It sends letter 'z' once to signal Unity that Calibration process is initiated
		It sends letter 'x' once to signal Unity to switch to the next scene

	
	
