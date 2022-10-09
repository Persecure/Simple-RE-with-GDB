<!-- wp:heading {"level":3} -->
<h3>Cracking a simple program with&nbsp;GDB</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>It’s likely that you are honing your skills on beginner-friendly CTFs (Capture the Flags) if you are just getting started in information security. There may be categories in reverse engineering or binary exploits if you encounter certain intermediate to advanced level challenges. While we can first try to avoid them, eventually we must accept them. In my previous article I wrote about creating our own programs and breaking into them to learn fundamental concepts and skills. Slowly but surely.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>I created a simple C language password program which you can download here. Let’s use a debugger to dissect the program and see how we can bypass the password.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>What is a debugger? A tool that aids in finding and fixing problems in a program. For this practice I’m going to start out with the Linux based debugger, GDB.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>You can install this program in your Linux machine with the following commands:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong><em>sudo apt-get update<br>sudo apt-get install gdb</em></strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Assembly has two flavors AT&amp;T and Intel. Why? I wish can explain it but I have no idea. (yet)</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>You can download the the practice file <a href="https://github.com/Persecure/Simple-RE-with-GDB" rel="noreferrer noopener" target="_blank">here</a>.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>First let’s view the source code.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*qZzHC9YJsBb40ljcef2maw.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>I drafted out a rudimentary flow chart to have a clearer understanding of the program.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*zQMl2w14qGzzfBEJjn6fkQ.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Now we that we have a gist of how the program functions we can start up GDB. use<strong><em> gdb &lt;FILE&gt;&nbsp;</em></strong></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*FJU0UTrTzjxJ5JazhR1fxA.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>First let’s look at the functions available for us to debug. Enter <strong><em>info functions</em></strong></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*YgJIr0_fykjgI1Kg1m-ryg.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>We can see some familiar inbuilt functions like puts(printf)&nbsp;, scanf and strcmp. For most programs we will start with the main function.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Enter <strong><em>disassemble main</em></strong> to dissect the main function into Assembly language.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*M6Fq-C6lGXlJN-ULQmR7Sg.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>You did not make a mistake it’s suppose to look like this. By default it comes with AT&amp;T syntax. Use <strong><em>set disassembly-flavor</em></strong> intel to change it to the intel syntax. <em>*Most tutorials are done with Intel syntax so I’m going to use it too.</em></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*qssnl6ltvCmtcOIcCEHiDA.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Much better I guess?</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Let’s see if I can categorize the functions and explain the code.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*kqgwZnmVq8jmDo0pHpa-lQ.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>When the <strong><em>strcmp</em></strong> function is called the<strong><em> test</em></strong> instruction is invoked to compare the <strong><em>user input</em></strong> and the <strong><em>password</em></strong>. The <strong><em>jne</em></strong> flag means “jump not equal” if the test result is 0 it continues with the next instruction till it reaches the “Correct” message (blue box) if not, it will jump to the &lt;main+141&gt; address that leads to the “Wrong message” (red box).</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>One of the most crucial debugging methods in your toolkit is the use of breakpoints. Anywhere you wish to pause the debugger, you place breakpoints.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Place a breakpoint at the start of the main function with <strong><em>break *main </em></strong>and run the program with<strong><em> “r”.&nbsp;</em></strong></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*gxsnVw_7jyhRSIW_lVjcdw.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>There will be an arrow pointing to where the breakpoint stops.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Add another breakpoint ofthe address of the test instruction. <strong>break *&lt;ADDRESS&gt;</strong> and continue the program with <strong>“c”</strong></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*SCsZ4zf_G8f_45LiOdA90w.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Enter a random password and the breakpoint 2 will stop at the test instruction.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>We can see this with arrow pointing to the test instruction.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*9_w9ykrXbux7uZ0107A9tA.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>If the test instruction results in<strong> 0</strong> it means that our password is correct. However we do not know the password and chances are it is a wrong one. It will result in<strong> 1</strong>. Which will activate the<strong> jne</strong> command and prints us the wrong message.&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>To bypass this we can change the <strong>eax</strong> value of the test instruction to 0. We can use <strong><em>set $eax=0</em></strong>&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*fCRmFQiqVsUswblMThzFgw.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p><strong><em>info registers</em></strong> is a command we can use to check the values of the registers in that current address.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Now when we continue to run our program we bypassed the jne and got the correct message.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*_idB_aHqa8ussfFnIlqDYw.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Another method we can use is to set the instruction pointer of the program to the address after the jne instruction. This will bypass everything. Use <strong><em>set $rip=&lt;ADDRESS AFTER JNE&gt;</em></strong>&nbsp;</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*lMJw5AidMyV0wnZ_Djz51g.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Now when we run the program we have the correct message.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*wBZF_UaLGYbnVwiB_UFp-w.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Another option to find the password in plaintext is to look for the address that has stored the password. GDB has an option to view the address in string format. From my experience in doing basic crackmes this is only possible if the password is hardcoded.</p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*hXNYCVhdTY3ebrVIbI1i3A.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>Use <strong><em>x/s &lt;ADDRESS OF STORED PASSWORD&gt;</em></strong> *<em>strings might be various order*</em></p>
<!-- /wp:paragraph -->

<!-- wp:image -->
<figure class="wp-block-image"><img src="https://cdn-images-1.medium.com/max/800/1*vGp8hLduV5MzQuRs9srBgA.png" alt=""/></figure>
<!-- /wp:image -->

<!-- wp:heading {"level":3} -->
<h3>Final thoughts</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>There are indeed many tools and easier methods to crack simple crackmes but I wanted to experience using a debugger like GDB. Reverse engineering is not just about crackmes&nbsp;, It’s an integral part of malware analysis and research, but it’s also one of the most advanced skills a researcher can possess.</p>
<!-- /wp:paragraph -->
