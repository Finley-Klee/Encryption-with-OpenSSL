# Encryption-with-OpenSSL

<h2>Description</h2>
As part of the final course in the Google IT Support Professional certificate, I did this lab exploring encryption using OpenSSL. 

<h2>Utilities Used</h2>

- <b>OpenSSL</b> 

<h2>Environments Used </h2>

- <b>Linux</b>

<h2>Project walk-through:</h2>

- <b>Generating Keys</b>
<p>In this first section of the lab I used OpenSSL to generate an asymmetric key pair from the command line. </p>
<br>
<p align="center">To start, I generated the private key by using the openssl utility with the genrsa command, which indicates that I want to create a new RSA key. Using the -out flag I told it the name of the file I'd like the key saved as, in this case private_key.pem, and the last parameter 2048 tells it how many bits I want the key to be.<br/>
  <img src="https://github.com/user-attachments/assets/07a1af01-960e-40be-8553-bd802b46af29" height="80%" width="80%" alt="linux terminal with black background and white text. Top line reads openssl genrsa -out private_key.pem 2048. The next line reads generating RSA private key, 2048 bit long modulus (2 primes) and then there's a line break followed by the private key which is redacted by a red rectangle filled in which black and white text that reads private key here."/>
  <br />
  <br />
 Now that I had a private key, I used it to generate a corresponding public key. I did this by using the private_key.pem file as an input to the openssl utility. The command was openssl rsa -in private_key.pem -outform PEM -pubout -out public_key.pem<br />
  <img src="https://github.com/user-attachments/assets/47a21f44-93f8-420a-b4d3-ab959f93d8c6" height="80%" width="80%" alt="linux terminal with black background and white text. The top line reads openssl rsa -in private_key.pem -outform PEM -pubout -out public_key.pem. The next line reads writing RSA key. Then there is a command cat public_key.pem followed by a block which starts with begin public key and ends with end public key and in the middle is the key value."/>
</p>
<br />
<br />
- <b>Encrypting and Decrypting</b>
<p>Once I generated the key pair, I was ready to try actually using them to encrypt and decrypt some data.</p>
<br>
<p align="center">First I created a text file using the echo command and the phrase "This is a secret message, for authorized parties only" followed by the > to direct the output to a location, in this case secret.txt. Then I went back to the openssl utility, this time using the rsautl command, which is an RSA utility for signing, verification, encryption, and decryption. I used the encrypt flag and the to indicate that I want to encrypt a message, and the pubin flag to use the public key. I fed the utility the public key I want to use with the inkey flag, then gave it the argument public_key.pem. Next I need to tell it what file I want to encrypt with the in flag and the argument secret.txt. Lastly, once it's encrypted we need to specify where to save it, so using the out flag I told it to save the encrypted version to secret.enc.<br/>
  <img src="https://github.com/user-attachments/assets/8256c454-e097-43e6-b5c3-51a7006dd8f7" width="80%" alt="linux terminal with black background and white text. The top line reads echo this is a secret message, for authorized parties only > secret.txt. The next line reads openssl rsautl -encrypt -pubin -inkey public_key.pem -in secret.txt -out secret.enc"/>
  <br />
  <br />
  I opened secret.enc, the encrypted version of my message, to see what it looks like, and it looks like gibberish, which is the desired effect.<br />
  <img src="https://github.com/user-attachments/assets/cae3f5be-eddb-4e00-ad85-9800b0d8faaf" width="80%" alt="a nano text editor window with the file path at the top and the control prompts at the bottom, a black background and white text. The text file has three lines with random letters, numbers, and symbols."/>
  <br />
  <br />
So, I checked that it's really illegible in it's encrypted form, so it would be safe to send over unsecured channels. Now to read it I would need to decrypt it, so using the RSA utility again, I used the private key to decrypt the message. The command was openssl rsautl -decrypt -inkey private_key.pem -in secret.enc<br />
  <img src="https://github.com/user-attachments/assets/3d0a6516-9155-4510-84d2-9131beb4b3bb" width="80%" alt="linux terminal with a black background and white text. The top line reads openssl rsautl -decrypt -inkey private_key.pem -in secret.enc and the second line reads This is a secret message, for authorized parties only."/>
   <br />
  <br />
As one last step, I practiced creating a hash digest of the message to verify that it was unchanged during transport. I did this using the dgst command in openssl. The command was openssl dgst -sha256 -sign private_key.pem -out secret.txt.sha256 secret.txt the sha256 flag indicates the hashing algorithm to use, and the sign flag indicates which key to use. Since it's a message digest we would sign it with the private key, so that the public key can be used for nonrepudiation, since only the sender would have the private key that corresponds to their public key. With the out flag I indicated what to save the digest as, and then the final argument in the command is the file that I want to create the hash digest of. When someone receives that message, they can verify the integrity of the message using the command openssl dgst -sha256 -verify public_key.pem -signature secret.txt.sha256 secret.txt. You can see that by using the same hashing algorithm we can generate an identical hash, and we can use the public key that corresponds to the private key to verify the sender.<br />
  <img src="https://github.com/user-attachments/assets/473ac056-49d6-43ff-bb35-7991bad5956d" width="80%" alt="linux terminal with a black background and white text. the top line reads openssl dgst -sha256 -sign private_key.pem -out secret.txt.sha256 secret.txt, the second line reads openssl dgst -sha256 -verify public_key.pem -signature secret.txt.sha256 secret.txt, and the last line reads verified OK."/>
</p>
