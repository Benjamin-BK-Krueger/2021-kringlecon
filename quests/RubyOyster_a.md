The objective already contains a very well written step-by-step documentation. The solutions for each step are:

<u>3 - Getting started</u>

```assembly
; Enter a return statement below and hit Execute to see what happens!
ret
```

<u>4 - Returning a value</u>

```assembly
; Set rax to 1337
mov rax, 1337
; Return, just like we did last time
ret
```

<u>5 - System calls</u>

See https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/

```assembly
; Find the syscall number for sys_exit and put it in rax
mov rax, 60
; Put the exit_code we want (99) in rdi
mov rdi, 99
; Perform the actual syscall
syscall
```

<u>6 - Calling into the void</u>

```assembly
; Push this value to the stack
push 0x12345678
; Try to return
ret
```

<u>7 - Gettting RIP</u>

```assembly
; Remember, this call pushes the return address to the stack
call place_below_the_nop
; This is where the function *thinks* it is supposed to return
nop
; This is a 'label' - as far as the call knows, this is the start of a function
place_below_the_nop:
; Pop the top of the stack into rax
pop rax
; Return from our code, as in previous levels
ret
```

<u>8 - Hello World</u>

```assembly
; This would be a good place for a call
call afterhello
; This is the literal string 'Hello World', null terminated, as code. Except
; it'll crash if it actually tries to run, so we'd better jump over it!
db 'Hello World',0
; This would be a good place for a label and a pop
afterhello:
pop rax
; This would be a good place for a re... oh wait, it's already here. Hooray!
ret
```

<u>9 - Hello, World!!</u>

```assembly
; Get a reference to this string into the correct register
call afterhello
db 'Hello World!',0
afterhello:
pop rbx
; Set up a call to sys_write
; Set rax to the correct syscall number for sys_write
mov rax, 1
; Set rdi to the first argument (the file descriptor, 1)
mov rdi, 1
; Set rsi to the second argument (buf - this is the "Hello World" string)
mov rsi, rbx
; Set rdx to the third argument (length of the string, in bytes)
mov rdx, 12
; Perform the syscall
syscall
; Return cleanly
ret
```

<u>10 - Opening a File</u>

```assembly
; Get a reference to this string into the correct register
call afterhello
db '/etc/passwd',0
afterhello:
pop rbx
; Set up a call to sys_open
; Set rax to the correct syscall number
mov rax, 2
; Set rdi to the first argument (the filename)
mov rdi, rbx
; Set rsi to the second argument (flags - 0 is fine)
mov rsi, 0
; Set rdx to the third argument (mode - 0 is also fine)
mov rdx, 0
; Perform the syscall
syscall
; syscall sets rax to the file handle, so to return the file handle we don't
; need to do anything else!
ret
```

<u>11 - Reading a file</u>

```assembly
; Get a reference to this
call afterhello
db '/var/northpolesecrets.txt',0
afterhello:
pop rbx
; Call sys_open
mov rax, 2
mov rdi, rbx
mov rsi, 0
mov rdx, 0
syscall
; Call sys_read on the file handle and read it into rsp
mov rdi, rax
mov rax, 0
mov rsi, rsp
mov rdx, 200
syscall
; Call sys_write to write the contents from rsp to stdout (1)
mov rdx, rax
mov rax, 1
mov rdi, 1
mov rsi, rsp
syscall
; Call sys_exit
mov rax, 60
mov rdi, 99
syscall
```

The result is:

Secret to KringleCon success: all of our speakers and organizers, providing the gift of cyber security knowledge, free to the community. 
