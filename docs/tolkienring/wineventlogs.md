# Windows Event Logs

Y'know, I can't help but get some `Slumdog Millionaire` vibes from this challenge. Like someone keeps asking me questions and I need to go back in the past to find out how I could answer that question from my own life experiences.

Hm.

Oh well, anyway!

!!! info "Challenge Introduction"
	Grinchum successfully downloaded his keylogger and has gathered the admin credentials! 
	We think he used PowerShell to find the Lembanh recipe and steal our secret ingredient. 
	Luckily, we enabled PowerShell auditing and have exported the Windows PowerShell logs to a flat text file.

	Please help me analyze this file and answer my questions.
	Ready to begin? 


!!! question "Question 1"
	What month/day/year did the attack take place? For example, 09/05/2021.

!!! success ""
	Answer: `12/24/2022`

!!! question "Question 2"
	An attacker got a secret from a file. What was the original file's name?

!!! success ""
	Answer: `Recipe.txt`

Searched for `Add-Content` and found `Recipe.txt` and `recipe_updated.txt`.
![Add-Content and Recipe.txt](/img/tolkienring/wineventlogs1.png)

!!! question "Question 3"
	The contents of the previous file were retrieved, changed, and stored to a variable by the attacker. This was done multiple times. Submit the last full PowerShell line that performed only these actions.

!!! success ""
	Answer: `$foo = Get-Content .\Recipe| % {$_ -replace 'honey', 'fish oil'}`

![honey and fish oil](/img/tolkienring/wineventlogs2.png)

!!! question "Question 4"
	After storing the altered file contents into the variable, the attacker used the variable to run a separate command that wrote the modified data to a file. This was done multiple times. Submit the last full PowerShell line that performed only this action.

Didn't quite understand this, there were several accounts of similar commands. But this was what worked apparently.

!!! success ""
	Answer: `$foo | Add-Content -Path 'Recipe'`

![Foo](/img/tolkienring/wineventlogs3.png)

!!! question "Question 5"
	The attacker ran the previous command against a file multiple times. What is the name of this file?

!!! success ""
	Answer: `Recipe.txt`

!!! question "Question 6"
	Were any files deleted? (Yes/No)

!!! success ""
	Answer: `yes`

![Yup](/img/tolkienring/wineventlogs4.png)

!!! question "Question 7"
	Was the original file (from question 2) deleted? (Yes/No)

!!! success ""
	Answer: (`no`, see screenshot, `recipe_updated.txt` was deleted)

!!! question "Question 8"
	What is the Event ID of the log that shows the actual command line used to delete the file?

!!! success ""
	Answer: `4104`

![4104](/img/tolkienring/wineventlogs5.png)

!!! question "Question 9"
	Is the secret ingredient compromised (Yes/No)?

!!! success ""
	Answer: yes

![yes](/img/tolkienring/wineventlogs6.png)

!!! question "Question 10"
	What is the secret ingredient?

!!! success ""
	Answer: `honey`