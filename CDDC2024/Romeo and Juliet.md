# Romeo and Juliet

Let's inspect the PCAP file's HTTP requests before moving on to SMTP.

​![image](assets/image-20240527192132-tg8446s.png)​

From this, we can see an image **Dear_Juliet.jpg**. Let's follow the HTTP stream for  **/Juliet wedding ring** as well.

​![image](assets/image-20240527192215-v8prvvt.png)​

Looks like a directory listing page (Apache?) containing a link to the file **wedding.txt**. Hence, let's export all objects, specifically **Dear_Juliet.jpg** and **wedding.txt**.

​![image](assets/image-20240527192333-t7ua6w6.png)​

​![image](assets/image-20240527192352-cnfn35o.png)​

Flag: CDDC2024{M4rry_M3_Ju1iet}
