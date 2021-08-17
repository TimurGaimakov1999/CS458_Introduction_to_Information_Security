##### <center>Introduction to Information Security - CS 458 - Summer 2021</center>
##### <center>Lab 3 - MD5 Collision Attack Lab^1</center>
##### <center>Due: Blackboard Wednesday, June 23rd, 2021 by 11:59pm</center>

#### 1 Introduction

A secure one-way hash function needs to satisfy two properties: the one-way property and the collision resistance property. The one-way property ensures that given a hash value h, it is computationally infeasible to find an input <mark style="background-color: #e6e6e6">M</mark>, such that <mark style="background-color: #e6e6e6">hash(M) = h</mark>. The collision-resistance property ensures that it is computationally infeasible to find two different inputs <mark style="background-color: #e6e6e6">M1</mark> and <mark style="background-color: #e6e6e6">M2</mark> , such that <mark style="background-color: #e6e6e6">hash(M1) = hash(M2)</mark>.

Several widely-used one-way hash functions have trouble maintaining the collision-resistance property. At the rump session of CRYPTO 2004, Xiaoyun Wang and co-authors demonstrated a collision attack against <mark style="background-color: #e6e6e6">MD5</mark>^2. In February <mark style="background-color: #e6e6e6">2017</mark>, CWI Amsterdam and Google Research announced the <mark style="background-color: #e6e6e6">SHAttered attack</mark>, which breaks the collision-resistance property of <mark style="background-color: #e6e6e6">SHA-1</mark>^3.

While many students do not have trouble understanding the importance of the one-way property, they cannot easily grasp why the collision-resistance property is necessary, and what impact these attacks can cause.

The learning objective of this lab is for students to really understand the impact of collision attacks, and see in first hand what damages can be caused if a widely-used one-way hash function’s collision-resistance property is broken. To achieve this goal, students need to launch actual collision attacks against the MD hash function. Using the attacks, students should be able to create two different programs that share the same <mark style="background-color: #e6e6e6">MD5</mark> hash but have completely different behaviors. This lab covers a number of topics described in the following:

- One-way hash function, <mark style="background-color: #e6e6e6">MD5</mark>
- The collision-resistance property
- Collision attacks

#### Lab Environment.

The lab uses a tool called “Fast MD5 Collision Generation”, which was written by Marc Stevens; the name of the binary is called <mark style="background-color: #cceacc">md5collgen</mark> in our VMs. <mark style="background-color: #cceacc">md5collgen</mark> has already been installed inside <mark style="background-color: #e6e6e6">/home/seed/bin</mark>

---

(^1) Credit: Wenliang Du, Syracuse University
(^2) John Black, Martin Cochran, and Trevor Highland. A study of the md5 attacks: Insights and improvements
(^3) Marc Stevens, Elie Bursztein, Pierre Karpman, Ange Albertini, and Yarik Markov. The first collision for full SHA-

---

#### 2 Lab Tasks

##### 2.1 Task 1: Generating Two Different Files with the Same MD5 Hash

In this task, we will generate two different files with the same <mark style="background-color: #e6e6e6">MD5</mark> hash values. The beginning parts of these two files need to be the same, i.e., they share the same prefix. We can achieve this using the <mark style="background-color: #cceacc">md5collgen</mark> program, which allows us to provide a prefix file with any arbitrary content. The way how the program works is illustrated in Figure 1.
The following command generates two output files, <mark style="background-color: #e6e6e6">out1.bin</mark> and <mark style="background-color: #e6e6e6">out2.bin</mark>, for a given a prefix file <mark style="background-color: #e6e6e6">prefix.txt</mark>:
```
$ md5collgen -p prefix.txt -o out1.bin out2.bin
```

<center>Figure 1: MD5 collision generation from a prefix</center>

We can check whether the output files are distinct or not using the <mark style="background-color: #cceacc">diff</mark> command. We can also use the <mark style="background-color: #cceacc">md5sum</mark> command to check the <mark style="background-color: #e6e6e6">MD5</mark> hash of each output file. See the following commands.
```
$ diff out1.bin out2.bin
$ md5sum out1.bin
$ md5sum out2.bin
```
Since <mark style="background-color: #e6e6e6">out1.bin</mark> and <mark style="background-color: #e6e6e6">out2.bin</mark> are binary, we cannot view them using a text-viewer program, such as <mark style="background-color: #cceacc">cat</mark> or <mark style="background-color: #cceacc">more</mark>; we need to use a binary editor to view (and edit) them. We have already installed a hex editor software called <mark style="background-color: #cceacc">bless</mark> in our VM. Please use such an editor to view these two output files, and describe your observations. In addition, you should answer the following questions:

- **Question 1.** If the length of your prefix file is not multiple of <mark style="background-color: #e6e6e6">64</mark>, what is going to happen?
- **Question 2.** Create a prefix file with exactly <mark style="background-color: #e6e6e6">64</mark> bytes, and run the collision tool again, and see what happens.
- **Question 3.** Are the data (<mark style="background-color: #cceacc">128</mark> bytes) generated by <mark style="background-color: #cceacc">md5collgen</mark> completely different for the two output files? Please identify all the bytes that are different.

##### 2.2 Task 2: Understanding MD5’s Property

In this task, we will try to understand some of the properties of the <mark style="background-color: #e6e6e6">MD5</mark> algorithm. These properties are important for us to conduct further tasks in this lab. <mark style="background-color: #e6e6e6">MD5</mark> is a quite complicated algorithm, but from very high level, it is not so complicated. As Figure 2 shows, <mark style="background-color: #e6e6e6">MD5</mark> divides the input data into blocks of <mark style="background-color: #e6e6e6">64</mark> bytes, and then computes the hash iteratively on these blocks. The core of the <mark style="background-color: #e6e6e6">MD5</mark> algorithm is a compression function, which takes two inputs, a <mark style="background-color: #e6e6e6">64-byte</mark> data block and the outcome of the previous iteration. The compression function produces a <mark style="background-color: #e6e6e6">128-bit</mark> <mark style="background-color: #e6e6e6">IHV</mark>, which stands for “Intermediate Hash Value”; this output is then fed into the next iteration. If the current iteration is the last one, the IHV will be the final hash value. The <mark style="background-color: #e6e6e6">IHV</mark> input for the first iteration (<mark style="background-color: #e6e6e6">IHV 0</mark>) is a fixed value.

Based on how <mark style="background-color: #e6e6e6">MD5</mark> works, we can derive the following property of the <mark style="background-color: #e6e6e6">MD5</mark> algorithm:

- Given two inputs <mark style="background-color: #e6e6e6">M</mark> and <mark style="background-color: #e6e6e6">N</mark>, if <mark style="background-color: #e6e6e6">MD5(M) = MD5(N)</mark>, i.e., the <mark style="background-color: #e6e6e6">MD5</mark> hashes of <mark style="background-color: #e6e6e6">M</mark> and <mark style="background-color: #e6e6e6">N</mark> are the same, then for any input <mark style="background-color: #e6e6e6">T</mark>, <mark style="background-color: #e6e6e6">MD5(M || T) = MD5(N || T)</mark>, where <mark style="background-color: #e6e6e6">||</mark> represents concatenation.
- That is, if inputs <mark style="background-color: #e6e6e6">M</mark> and <mark style="background-color: #e6e6e6">N</mark> have the same hash, adding the same suffix <mark style="background-color: #e6e6e6">T</mark> to them will result in two outputs that have the same hash value.

<center>Figure 2: How the MD5 algorithm works</center>

This property holds not only for the <mark style="background-color: #e6e6e6">MD5</mark> hash algorithm, but also for many other hash algorithms.

Your job in this task is to design an experiment to demonstrates that this property holds for <mark style="background-color: #e6e6e6">MD5.</mark>

You can use the <mark style="background-color: #cceacc">cat</mark> command to concatenate two files (binary or text files) into one. The following command concatenates the contents of <mark style="background-color: #e6e6e6">file2</mark> to the contents of <mark style="background-color: #e6e6e6">file1</mark>, and places the result in <mark style="background-color: #e6e6e6">file3</mark>.

```
$ cat file1 file2 > file
```
##### 2.3 Task 3: Generating Two Executable Files with the Same MD5 Hash

In this task, you are given the following <mark style="background-color: #e6e6e6">C program</mark>. Your job is to create two different versions of this program, such that the contents of their <mark style="background-color: #e6e6e6">xyz</mark> arrays are different, but the hash values of the executables are the same.

```
#include <stdio.h>
unsigned char xyz[200] = {
  /* The actual contents of this array are up to you */
};

int main()
{
  int i;
  for (i=0; i<200; i++){
    printf("%x", xyz[i]);
  }
  printf("\n");
}
```
You may choose to work at the source code level, i.e., generating two versions of the above <mark style="background-color: #e6e6e6">C program</mark>, such that after compilation, their corresponding executable files have the same <mark style="background-color: #e6e6e6">MD5 hash value</mark>. However, it may be easier to directly work on the binary level. You can put some random values in the <mark style="background-color: #e6e6e6">xyz</mark> array, compile the above code to binary. Then you can use a hex editor tool to modify the content of the <mark style="background-color: #e6e6e6">xyz</mark> array directly in the binary file.

Finding where the contents of the array are stored in the binary is not easy. However, if we fill the array with some fixed values, we can easily find them in the binary. For example, the following code fills the array with <mark style="background-color: #e6e6e6">0x41</mark>, which is the <mark style="background-color: #e6e6e6">ASCII</mark> value for letter <mark style="background-color: #e6e6e6">A</mark>. It will not be difficult to locate <mark style="background-color: #e6e6e6">200 A’s</mark> in the binary.

```
unsigned char xyz[200] = {
        0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41,
        0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41,
        0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41,
        ... (omitted) ...
        0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41, 0x41
}
```

**Guidelines.** From inside the array, we can find two locations, from where we can divide the executable file into three parts: a <mark style="background-color: #e6e6e6">prefix</mark>, a <mark style="background-color: #e6e6e6">128-byte</mark> region, and a <mark style="background-color: #e6e6e6">suffix</mark>. The length of the prefix needs to be multiple of <mark style="background-color: #e6e6e6">64</mark> bytes. See Figure 3 for an illustration of how the file is divided.

<center>Figure 3: Break the executable file into three pieces.</center>

We can run <mark style="background-color: #cceacc">md5collgen</mark> on the <mark style="background-color: #e6e6e6">prefix</mark> to generate two outputs that have the same <mark style="background-color: #e6e6e6">MD5 hash value</mark>.
Let us use <mark style="background-color: #e6e6e6">P</mark> and <mark style="background-color: #e6e6e6">Q</mark> to represent the second part (each having <mark style="background-color: #e6e6e6">128</mark> bytes) of these outputs (i.e., the part after the <mark style="background-color: #e6e6e6">prefix</mark>). Therefore, we have the following:
```
MD5 (prefix || P) = MD5 (prefix || Q)
```
Based on the property of <mark style="background-color: #e6e6e6">MD5</mark>, we know that if we append the same <mark style="background-color: #e6e6e6">suffix</mark> to the above two outputs, the resultant data will also have the same hash value. Basically, the following is true for any <mark style="background-color: #e6e6e6">suffix</mark>:
```
MD5 (prefix || P || suffix) = MD5 (prefix || Q || suffix)
```
Therefore, we just need to use <mark style="background-color: #e6e6e6">P</mark> and <mark style="background-color: #e6e6e6">Q</mark> to replace <mark style="background-color: #e6e6e6">128</mark> bytes of the array (between the two dividing points), and we will be able to create two binary programs that have the same hash value. Their outcomes are different, because they each print out their own arrays, which have different contents.

**Tools.** You can use <mark style="background-color: #cceacc">bless</mark> to view the binary executable file and find the location for the array. For dividing a binary file, there are some tools that we can use to divide a file from a particular location. The <mark style="background-color: #cceacc">head</mark> and <mark style="background-color: #cceacc">tail</mark> commands are such useful tools. You can look at their manuals to learn how to use them.
We give three examples in the following:
```
$ head -c 3200 a.out > prefix
$ tail -c 100 a.out > suffix
$ tail -c +3300 a.out > suffix
```
- The first command above saves the first <mark style="background-color: #e6e6e6">3200</mark> bytes of <mark style="background-color: #e6e6e6">a.out</mark> to <mark style="background-color: #e6e6e6">prefix</mark>.
- The second command saves the last <mark style="background-color: #e6e6e6">100</mark> bytes of <mark style="background-color: #e6e6e6">a.out</mark> to <mark style="background-color: #e6e6e6">suffix</mark>.
- The third command saves the data from the <mark style="background-color: #e6e6e6">3300th</mark> byte to the end of the file <mark style="background-color: #e6e6e6">a.out</mark> to <mark style="background-color: #e6e6e6">suffix</mark>.
- With these two commands, we can divide a binary file into pieces from any location. If we need to glue some pieces together, we can use the <mark style="background-color: #cceacc">cat</mark> command.

If you use <mark style="background-color: #cceacc">bless</mark> to copy-and-paste a block of data from one binary file to another file, the menu item “<mark style="background-color: #e6e6e6">Edit -> Select Range</mark>” is quite handy, because you can select a block of data using a starting point and a range, instead of manually counting how many bytes are selected.


##### 2.4 Task 4: Making the Two Programs Behave Differently

In the previous task, we have successfully created two programs that have the same <mark style="background-color: #e6e6e6">MD5 hash</mark>, but their behaviors are different. However, their differences are only in the data they print out; they still execute the same sequence of instructions. In this task, we would like to achieve something more significant and more meaningful.

Assume that you have created a software which does good things. You send the software to a trusted authority to get certified. The authority conducts a comprehensive testing of your software, and concludes that your software is indeed doing good things. The authority will present you with a certificate, stating that your program is good. To prevent you from changing your program after getting the certificate, the <mark style="background-color: #e6e6e6">MD5 hash value</mark> of your program is also included in the certificate; the certificate is signed by the authority, so you cannot change anything on the certificate or your program without rendering the signature invalid.

You would like to get your malicious software certified by the authority, but you have zero chance to achieve that goal if you simply send your malicious software to the authority. However, you have noticed that the authority uses <mark style="background-color: #e6e6e6">MD5</mark> to generate the hash value. You got an idea:

- You plan to prepare two different programs. One program will always execute benign instructions and do good things, while the other program will execute malicious instructions and cause damages. You find a way to get these two programs to share the same <mark style="background-color: #e6e6e6">MD5 hash value</mark>.
- You then send the benign version to the authority for certification. Since this version does good things, it will pass the certification, and you will get a certificate that contains the hash value of your benign program. Because your malicious program has the same hash value, this certificate is also valid for your malicious program. Therefore, you have successfully obtained a valid certificate for your malicious program. If other people trusted the certificate issued by the authority, they will download your malicious program.

The objective of this task is to launch the attack described above. Namely, you need to create two programs that share the same <mark style="background-color: #e6e6e6">MD5 hash</mark>. However, one program will always execute benign instructions, while the other program will execute malicious instructions. In your work, what benign/malicious instructions are executed is not important; it is sufficient to demonstrate that the instructions executed by these two programs are different.

**Guidelines.** Creating two completely different programs that produce the same <mark style="background-color: #e6e6e6">MD5 hash value</mark> is quite hard. The two hash-colliding programs produced by <mark style="background-color: #cceacc">md5collgen</mark> need to share the same <mark style="background-color: #e6e6e6">prefix</mark>; moreover, as we can see from the previous task, if we need to add some meaningful <mark style="background-color: #e6e6e6">suffix</mark> to the outputs produced by <mark style="background-color: #e6e6e6">md5collgen</mark>, the <mark style="background-color: #e6e6e6">suffix</mark> added to both programs also needs to be the same. These are the limitations of the <mark style="background-color: #e6e6e6">MD5 collision generation program</mark> that we use. Although there are other more complicated and more advanced tools that can lift some of the limitations, such as accepting two different prefixes^4 , they demand much more computing power, so they are out of the scope for this lab. We need to find a way to generate two different programs within the limitations.
There are many ways to achieve the above goal. We provide one approach as a reference, but students are encouraged to come up their own ideas.

---

(^4) Marc Stevens. On collisions for md5. Master’s thesis, Eindhoven University of Technology

---

In our approach, we create two arrays <mark style="background-color: #e6e6e6">X</mark> and <mark style="background-color: #e6e6e6">Y</mark>. We compare the contents of these two arrays; if they are the same, the benign code is executed; otherwise, the malicious code is executed. See the following pseudo-code:
```
Array X;
Array Y;
main()
{
  if(X's contents and Y's contents are the same)
    run benign code;
  else
    run malicious code;
  return;
}
```
We can initialize the arrays <mark style="background-color: #e6e6e6">X</mark> and <mark style="background-color: #e6e6e6">Y</mark> with some values that can help us find their locations in the executable binary file. Our job is to change the contents of these two arrays, so we can generate two different versions that have the same <mark style="background-color: #e6e6e6">MD5 hash</mark>. In one version, the contents of <mark style="background-color: #e6e6e6">X</mark> and <mark style="background-color: #e6e6e6">Y</mark> are the same, so the benign code is executed; in the other version, the contents of <mark style="background-color: #e6e6e6">X</mark> and <mark style="background-color: #e6e6e6">Y</mark> are different, so the malicious code is executed. We can achieve this goal using a technique similar to the one used in **Task 3.** Figure 4 illustrates what the two versions of the program look like.

<center>Figure 4: An approach to generate two hash-colliding programs with different behaviors.</center>

From Figure 4, we know that these two binary files have the same <mark style="background-color: #e6e6e6">MD5 hash value</mark>, as long as <mark style="background-color: #e6e6e6">P</mark> and <mark style="background-color: #e6e6e6">Q</mark> are generated accordingly. In the first version, we make the contents of arrays <mark style="background-color: #e6e6e6">X</mark> and <mark style="background-color: #e6e6e6">Y</mark> the same, while in the second version, we make their contents different. Therefore, the only thing we need to change is the contents of these two arrays, and there is no need to change the logic of the programs.

#### 3 Submission

You need to submit a detailed lab report, with screenshots, to describe what you have done and what you have observed. You also need to provide explanation to the observations that are interesting or surprising. Please also list the important code snippets followed by explanation. Simply attaching code without any explanation will not receive credits.
