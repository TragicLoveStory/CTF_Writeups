# Lag and Crash 2023 write up
Hello, my name is Ashwyn. I go by the pseudonym "TragicLoveStory" and this is my writeup for Lag and Crash 2023. My team made it into the finals despite all of us being unprepared and my teammates being new to CTF competitions. I have learnt a lot and hope to share what I learnt with others through my writeup.
![Image of my team's score](/assets/image18.png)

## Table of Contents
- [Insecure Keyed Hash](#insecure-keyed-hash)
- [The Abandoned Computer](#the-abandoned-computer)
- [Time Travel](#time-travel)
- [Pennyworth](#pennyworth)

## Insecure Keyed Hash
"I have created a new keyed-hash algorithm! It's faster and better than HMAC! I'll be famous!"
Easy, Crypto
```
nc nc.lagncra.sh 8012
```

1. Analyze the source code **server.py** provided with the challenge.
    ```
    from string import ascii_lowercase
    from random import choices
    from hashlib import md5


    def sign(message, key):
        """ Our new keyed-hash message authentication algorithm! So much more simpler then HMAC! """
        return md5((key + message).encode()).hexdigest()

    def verify(signed, key):
        """ Verifies if message is signed by key """
        try:
            message, signature = signed.split(".")
            return sign(message, key) == signature
        except:
            return False


    key = "".join(choices(ascii_lowercase, k=5))
    PASSPHRASE = "Better than HMAC!"

    print(
        "Welcome to our new signing service!\n"
        "Our service is extremely secure, feel free to try and break it >:D"
    )

    while True:
        print(
            "\nOptions:\n"
            "1. Sign messages\n"
            "2. Verify messages\n"
            "3. Exit\n"
        )

        option = input(f"Enter option: ")

        if option == "1":
            message = input("Enter message: ")
            if message == PASSPHRASE:
                print("Indeed! Our new algorithm runs much faster than HMAC :)")
            elif message:
                signed = f"{message}.{sign(message, key)}"
                print(f"Here's your signed message: {signed}")
            else:
                print("No message to sign ¯\_(ツ)_/¯")

        elif option == "2":
            signed = input("Enter signed message: ")
            if verify(signed, key):
                print("Message verified!")
                try:
                    if signed.split(".")[0] == PASSPHRASE:
                        print("Checkpoint 1")
                        with open("flag.txt") as f:
                            flag = f.read()
                            print(flag)
                except:
                    pass
            else:
                print("Message verification failed!")

        elif option == "3":
            print("Thanks for using our service. Goodbye!")
            break

        else:
            print("Please choose an option from 1 to 3!")
    ```
2. As we are unable to sign the passphrase "Better than HMAC!" through the script ran via netcat, we will have to edit the source code provided so as to sign the passphrase. We will also have to take in account of the key generated for our specific netcat session. 
3. Therefore, we will create a script to brute force the key generated for our netcat session. We will then hard-code the brute-forced key into our edited **server.py** and sign the message "Better than HMAC!". The edited source code and created scripts are shown below:
    ```
    # Prints out the signed key instead of a static message
    if message == PASSPHRASE:
        signed = f"{message}.{sign(message, key)}"
        print(f"Here's your signed message: {signed}")

    ```
    ```
    from string import ascii_lowercase
    from random import choices
    from hashlib import md5

    # Signed messages come in the format *message.hash*
    # For example: test.e5075e3c2fab2ccd9e02d632ddea7507
    while True:
        message = "MESSAGE HERE"
        key = "".join(choices(ascii_lowercase, k=5))
        hashed = md5((key + message).encode()).hexdigest()
        if(hashed == "INSERT HASHED MESSAGE HERE"):
            print("Correct key is: ")
            print(key)
            break
    ```
4. The script above takes a signed message and uses the algorithm in **server.py** to brute-force the key. Sign a message and insert the message and hash into the script to brute-force the key.
    ![Image showing the message "test" being signed](/assets/image1.jpg)
    ![Image showing the key being brute-forced](/assets/image2.png)
5. Once we get the key, hard-code the key into the edited **server.py** and sign the message "Better than HMAC!" to get the flag through the script running via netcat.
    ![Image showing the flag provided through successful verification of the passphrase](/assets/image3.png)

## The Abandoned Computer
"You found an old abandoned house with a very old computer that is still working. Hmm... seems like there's nothing inside the computer except for some corrupted files. You managed to find one file that might contain a messsage for you about life before the apocolypse. Can you try accessing the file and uncover the secret message? (To run the unknown file, run the file in the Cygwin terminal.)" Easy, RE, C, asm, Crypto 

1. After running the .exe, we can deduce that credentials are required. As one of the categories for this challenge is RE, there is a chance to find clues regarding the credentials in the source code. Therefore, we will be using Ghidra for decompiling.
	![Image showing the executable file running](/assets/image4.png)
2. Once the executable file is analyzed and decompiled by Ghidra, we find two functions of interest **check_password** and **main**.
	![Image showing the function check_password](/assets/image5.png)
	![Image showing the function main](/assets/image6.png)
3. We can see the password *Z40nj$q9Ul* is hard-coded in *check_password* and found in the strcmp() function that compares the user input with the hard-coded password. We can also see the username *user10218012437* hard-coded in *main* and also found in the strcmp() functions comparing the user input with the hard-coded username.
4. We now use the credentials acquired to login once again. Upon successfully logging in, we are met with a long hexadecimal string which provides us the flag after decoding it from hexadecimal and base-64.
	![Image showing the long hexadecimal string](/assets/image7.png)
	![Image showing the base-64 encoded string](/assets/image8.png)
	![Image showing the string containing the flag](/assets/image9.png)
## Time Travel
"We found this program from the ancient 20th century. I really miss those days..." Easy, Reverse

1. As one of the challenge category is reverse engineering, we will be decompiling the executable file with Ghidra.
2. Once the executable file is analyzed and decompiled by Ghidra, we can find multiple functions of interest such as **sub_987654** and **sub_123456** but we will be focusing on **main**. In the function **main**, we see an if condition:
	```
	tVar1 = time((time_t *)0x0);
	if (tVar1 < 0x1b6e7) {
		sub_123456(uVar2,uVar3,uVar4,in_R9B,in_stack_ffffffffffffffe8);
	}
	else {
		puts("You're too old for this!");
	}
	```
3. As the executable file outputs "You're too old for this" when executed, we can deduce that **tVar1** is greater than or equal to **0x1b6e7**(equals to 112455 in decimal). 
	![Image showing the regular output of tt.exe](/assets/image13.png)
4. From investigating the assembly code, we can deduce that address 0x10040118c causes a jump to address 0x100401195 as **tVar1** >= **0x1b6e6** (JG, jump greater, and JNLE, jump not less/equal, are identical. This jump is **what causes** the else condition to execute.). Therefore, we will change JG(jump greater) to JL(jump less) at address 0x10040118c to allow the if condition to pass since **tVar1** >= **0x1b6e6**, allowing **sub_123456** to run which may provide us the flag.
	![Image showing the assembly code which was produced by Ghidra](/assets/image10.png)
5. The change can be done by highlighting the specific address one wants changes to be made, and pressing CTRL + Shift + G (you can also right click -> Patch instruction). Once the change from JG to JL was made, save the changes, export the modified tt.exe and run it to get the flag.
	![Image showing the modification from JG to JL at address 10040118c](/assets/image11.png)
	![Image showing the modified tt.exe providing us the flag when ran](/assets/image12.png)
## Pennyworth
"Alfred needs help accessing the bat computer. The only thing Alfred got was the Bat Family's image and the following text "The one that got away". It is said that the password can be found in the image. Can you crack the password to the bat computer?" Easy 
![Image provided with the challenge Family.png](/assets/image17.jpg)

1. As binwalk was unable to find any embedded files, there is a high chance that steganography was applied to the image. As stegseek was unable to brute-force the passphrase, the passphrase is most likely related to the image Family.jpg. 
	![Image showing stegseek failing to brute-force the passphrase](/assets/image14.png)
2. As I know nothing about marvel, I used chatGPT to determine the relationship between Batman and the phrase "The one that got away". ChatGPT was able to narrow down the love interests to Selina Kyle, Talia al Ghul, Vicki Vale, Rachel Dawes and Julie Madison. 
	![Screenshot of ChatGPT output](/assets/image19.png)
3. As steghide was unable to extract anything using all the love interests above as the passphrase, I decided to switch to JPHide and Seek as the steganography algorithm used for Family.jpg may not be supported by steghide. Using the passphrase "Selina Kyle" on JPHide and Seek will provide the flag.
	![Image showing steghide failing to extract data from the image](/assets/image15.png)
	![Image showing the hidden file successfully being extracted](/assets/image16.png) 