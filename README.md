# Bash-scripting
Click the flower to see the code and explanation about my code
<!-- 
<details><summary>:blossom: </summary>
<p>

```bash
   
```
</p>
</details>
-->
<!-- TO DO: add more details later -->

<details><summary>:blossom: Check System Health all hosts in the network  </summary>
<p>

```bash
  ip_list=("172.16.0.11" "172.16.0.12" "172.16.0.13" "172.16.0.14")

for ip_address in ${ip_list[@]}; do
	echo "$ip_address"
	status=$(bash is_active.sh "$ip_address")
	if [[ "$status" == "is active" ]]; then 

		mem=$(bash memory_freeIP.sh "$ip_address") #172.16.0.12
		
		if (( $mem < 20 )); then
			echo "send email --warning--memory is getting low, it is : $mem%"
		fi

		disk=$(bash disk_freeIp.sh "$ip_address")
		if (( $disk < 20 )); then 
			echo "send email --warning-- disk getting low, it is: $disk%"
		fi

		bad_user=$(bash check_usersHealth.sh "$ip_address")
		
		if [[ $bad_user == "yes" ]]; then
			echo "all good"
		else 
			echo "send email--warning --Bad user detected"
		fi
	else
		echo "the ip address $ip_address is not active"
	fi
	echo 
		
done
```
is_active.sh
```bash
received=$(ping -c 4 $1 | grep "received")
IFS=" "
rev=($received)
unset IFS

if (( ${rev[3]} > 2 )); then
	
	echo "is active"
else
	echo "not active"

fi
```
memory_freeIP.sh
```bash
mem_free=$(ssh "student@$1" "free | grep Mem")
IFS=" "
mem_percent=($mem_free)
unset IFS

total=${mem_percent[1]}
free=${mem_percent[3]}

mem_free=$(( ($free * 100) / $total  ))

echo "$mem_free"
```
disk_freeIP.sh
```bash
disk_free=$(ssh "student@$1" "df --total | grep total")
IFS=" "
diskF=($disk_free)
unset IFS
free=${diskF[4]}
free=${free:0:-1}

echo "$free"
```
check_usersHealth.sh
```bash
ssh "student@$1" "who" > user_login.txt
login_user=()
while read line ; do

	IFS=" "
	line=($line)
	unset IFS
	login_user+=(${line[0]})

		
done < user_login.txt


good_user=()
while read good_user ; do

	IFS=" "
	good_user=($good_user)
	unset IFS
	good_user+=(${good_user[0]})
		
done < good_users.txt

all_good="yes"

for user in "${login_user[@]}"; do
	found_match="no"
	for good in "${good_user[@]}"; do
		if [[ $user == "$good" ]]; then 
			found_match="yes"
	
		fi
	done	
	
	if [[ $found_match == "no"  ]]; then
		
		all_good="no"

	fi
done

echo "$all_good"
```
</p>
</details>

<details><summary>:blossom:Check user IP address </summary>
<p>

```bash
  echo "enter IP address: "
read ip_address

ssh student@$ip_address "who" > user_login.txt
#$1 from system health.sh $ip_address

#ssh student@$1 "who" > user_login.txt
login_user=()
while read line ; do

	IFS=" "
	line=($line)
	unset IFS
	login_user+=(${line[0]})

		
done < user_login.txt

echo
good_user=()
while read good_user ; do

	IFS=" "
	good_user=($good_user)
	unset IFS
	good_user+=(${good_user[0]})
		
done < good_users.txt

all_good="yes"

for user in "${login_user[@]}"; do
	found_match="no"
	for good in "${good_user[@]}"; do
		if [[ $user == "$good" ]]; then 
			found_match="yes"
	
		fi
	done	
	
	if [[ $found_match == "no"  ]]; then
		
		all_good="no"

	fi
done
if [[ $all_good == "yes" ]]; then
	
	echo "All good"
else 
	echo "Bad user detected"
fi

 
```
</p>
</details>

<details><summary>:blossom: subscript (call script from another script) </summary>
<p>

```bash
echo "Hello World"
echo "Something Else"
echo "$1" the 1st argument from another script 
```
call subscript from main_script
```
#bash sub_script.sh
bash sub_script.sh "Good bye"
```
</p>
</details>

<details><summary>:blossom: SSH to others linux</summary>
<p>
to automate the SSH, we will use SSH-keygen and SSH-copy-id
```bash
 ssh student@172.16.0.12
mkdir I_connected
echo "I Connected"
exit  
```
</p>
</details>


<details><summary>:blossom: check instruder to network </summary>
<p>

```bash
   who > user_login.txt
login_user=()
while read line ; do

	IFS=" "
	line=($line)
	unset IFS
	login_user+=(${line[0]})

		
done < user_login.txt

echo
good_user=()
while read good_user ; do

	IFS=" "
	good_user=($good_user)
	unset IFS
	good_user+=(${good_user[0]})
		
done < good_users.txt

all_good="yes"

for user in "${login_user[@]}"; do
	found_match="no"
	for good in "${good_user[@]}"; do
		if [[ $user == "$good" ]]; then 
			found_match="yes"
	
		fi
	done	
	
	if [[ $found_match == "no"  ]]; then
		
		all_good="no"

	fi
done
if [[ $all_good == "yes" ]]; then
	
	echo "All good"
else 
	echo "Bad user detected"
fi


```
</p>
</details>

<details><summary>:blossom: Redirection</summary>
<p>

```bash
   #echo "Hello Wolrd" > output.txt
#echo "Something Else" >> output.txt

#ping -c 1 172.16.0.12 > output.txt
#ping -c 1 linux2 > output.txt

#ping -c 1 linux2 2> errors.txt
#ping -c 1 linux2 > output.txt 2> errors.txt
#ping -c 1 linux2 &> stuff.txt

> output.txt # clears the file
for (( i=0; i<5; i++ )); do
	echo "$i" >> output.txt
done


for (( i = 5; i < 10; i++ )); do
	echo "$i"
done > output.txt


read num < output.txt
echo "I got $num"

read next_num < output.txt
echo "I also got $next_num"

numbers=()
while read line ; do
	
	numbers+=("$line")
done < output.txt

while read line ; do
	IFS=","
	line=($line)
	unset IFS
		echo "${line[2]}"
	
done < read_me.txt


```
</p>
</details>


<details><summary>:blossom: check network connection</summary>
<p>

```bash
   #echo "enter ip address: "
#read enter_ip


received=$(ping -c 4 $1 | grep "received")
IFS=" "
rev=($received)
unset IFS

if (( ${rev[3]} > 2 )); then
	
	echo "is active"
else
	echo "not active"

fi
```
</p>
</details>



<details><summary>:blossom: Check disk free</summary>
<p>

```bash
disk_free=$(df --total | grep "total")
IFS=" "
diskF=($disk_free)
unset IFS
free=${diskF[4]}
free=${free:0:-1}
echo " disk free: $(( 100 - $free ))%"
   
```
</p>
</details>


<details><summary>:blossom:check memory free </summary>
<p>

```bash
mem_free=$(free | grep "Mem")
IFS=" "
mem_percent=($mem_free)
unset IFS

total=${mem_percent[1]}
free=${mem_percent[3]}

echo "Mem percent is :$(( ($free * 100) / $total  ))"   
```
</p>
</details>

<details><summary>:blossom: substring</summary>
<p>

```bash
 str="Hello World"

echo "${str^^}"
echo "${str,,}"

echo "${str//'l'/'p'}" # Heppo worpd
echo "${str/'o'/'i'}" #Helli world

echo "${str:0:2}" #He

echo "${str:6:3}" #Wor
echo "${str:3:5}" #lo Wo

echo "${str::5}" 
echo "${str:6}"
echo "${str:(-9):3}"
echo "${str:(-4)}" # orld
echo "${str:6:-1}" #Worl
echo " ${str:(-10):-4}" # ello W
#splitting
IFS=" "
words=($str)
unset IFS

echo "${words[1]}"
echo "${words[0]}"

test_str="Foo,bar,Apple,Banana"
IFS=","
words=($test_str)
unset IFS
echo "${words[2]}"

  
```
</p>
</details>

<details><summary>:blossom: Function add</summary>
<p>
create function : add
```bash
   add(){
	#total=$(($1 +$2))
	#echo $total
	echo $1
	echo $2
}
#add 7 2
#add 10 20
#add 3 4 5 # just 7 
add 7
do_something(){
	echo "Hello"
	echo "hi"
	return 42
}
do_something
echo $?
```
</p>
</details>

<details><summary>:blossom: Function is_positive</summary>
<p>
create function called is_positive 
```bash
   is_positive(){
	
	if (( $1 > 0 )); then
		echo " positive"
	elif (( $1 < 0 )); then
		echo " negative"
	else
		echo "zero"
	fi
}

#num=$(is_positive 10)
echo "Your number is $(is_positive 10)"
```
</p>
</details>

<details><summary>:blossom: subshell</summary>
<p>

```bash
#var=$( command )
output=$( ping -c 1 172.16.0.12 )
#echo $output
#echo "$output"

#foobar=$(mkdir "foobar")
#echo "$foobar"
say_hello(){
	echo "Hello"
}
#say_hello
a=$(say_hello)   
```
</p>
</details>

<details><summary>:blossom: Exit code</summary>
<p>

```bash
  ping -c 1 172.16.0.15
code=$?
echo
echo $code

ping -c 1 172.16.0.15
if [[ $? -ne 0 ]]; then
	echo $?
fi 
```
</p>
</details>

<details><summary>:blossom: Guessing game </summary>
<p>

```bash
 number=0
count=0
while true; do
	
	(( count++ ))
	echo " You have 5 times to guess,  guess no: $count , Enter a number: 0 - 50 "
	read number

	if (( $number == 21 )); then
		echo  "You Win"
		break
	elif (( $count == 5 )); then
		echo "You lose !"
		break
	elif (( $number > 50 )); then
		echo " you did not enter the correct option number, try again"	
	elif (( $number > 21 )); then
		echo " your guess is $number , too big, try again"
	elif (( $number < 0 )); then
		echo " you did not enter the correct option number, try again"	
	elif (( $number < 21 )); then
		echo " your guess is $number , too small, try again"
	fi
done  
```
</p>
</details>


<details><summary>:blossom: Loop </summary>
<p>

```bash
 animals=("dog" "cat" "fish" "whale shark")
for animal in "${animals[@]}"; do
	echo "$animal"
done
echo

for animal in ${animals[@]}; do
	echo "$animal"
done


numbers=(3 -2 4 -1 5)
#print out only the positive numbers

for  number in "${numbers[@]}" ; do 
	if (( $number > 0 )) ; then
		echo "$number"
	fi
done


for (( i=0; i<11; i=i+1 )); do
	echo $i
done

for (( i=10; i>-1; i=i-1 ));do
	echo $i
done


declare -A people=(["ml"]="Matt" ["bh"]="Bill" ["tm"]="Tri")
	
for key in "${!people[@]}"; do
	#echo "$key"
	echo "${people[$key]}"

done 


a=0
while (( a < 11 )); do
	echo $a
	((a++))
done

while true; do
	echo "Enter a number"
	read num
	if [[ $num = "Done" ]]; then 
		break
	fi
done
  
```
</p>
</details>

<details><summary>:blossom: Associative Array </summary>
<p>

```bash
#people={"hn":"Harrison", "jd":"John"}
declare -A people=(["hn"]="Harrison" ["jd"]="John")
#print(people["hn"])
echo "${people[hn]}"
echo "${people[@]}"
echo "${!people[@]}"
#changing a value
people["jd"]="Jeremy"

echo "${people[@]}"

 #adding items
people+=(["ml"]="Matt" ["tm"]="Tri" ["bh"]="Bill")
echo "${people[@]}"
unset people["jd"]
echo "${people[@]}"
```
</p>
</details>

<details><summary>:blossom: Array practices </summary>
<p>

```bash
#A array of colors

colors=("Red" "Green" "Blue" "Black" "White" "Yellow")

echo "${colors[@]}"
echo


echo "${colors[0]}"
echo "${colors[4]}"
echo "${colors[2]}"

echo

colors[3]="Purple"
colors[4]="Orange"

echo "${colors[@]}"
echo

colors+=("Black" "White")
echo "${colors[@]}"
echo

unset colors[0]
unset colors[2]
unset colors[6]

echo "${colors[@]}"
echo "${colors[4]}"
echo
colors=("${colors[@]}")
echo "${colors[2]}"
```
</p>
</details>


<details><summary>:blossom: Array</summary>
<p>

```bash
   animals=("dog" "cat" "fish")
echo ${animals[1]}
#echo ${animals}
#echo $animal

echo "${animals[@]}"

#change fish to llama:
animals[2]="llama"
echo "${animals[@]}"
animals+=("hedgehog")
echo "${animals[@]}"

animals+=("lizard" "whale shark")
echo "${animals[@]}"

unset animals[1]
echo "${animals[@]}"

echo "${animals[1]}"

animals=("${animals[@]}")
echo "${animals[1]}"

animals=("dog" "cat" "fish")

echo "${animals[@]}"
unset animals[1]
echo "${animals[@]}"

echo "${!animals[@]}"

```
</p>
</details>

<details><summary>:blossom: If statement  more practices</summary>
<p>

```bash
   echo "Which one your fav language"
read language
if [[ $language = "Bash" ]]; then
	echo "Yay!"
elif [[ $language = "Python" ]]; then
	echo "Also Yay!"
elif [[ $language = "Java" ]]; then
	echo "Another Yay!"
else
	echo "Boo"

fi

echo "Enter a number"
read number
if (( $number < 0 )); then
	echo " negative number"
elif (( $number > 0 )); then 
	echo "positive number"
else 
	echo " neither positive or negative number"

fi

echo "enter your year of birth"
year=2022
read birth_year
echo "your age is $(( year - birth_year)) years old"


echo "enter a number"
read first
echo "enter a second number"
read second

if (( first > second )); then 
	echo "your bigger number is $first"
else 
	echo "your bigger number is $second"
fi

echo "what is your grade number"
read grade
if (( $grade >=100 )); then 
	echo "You get an A+"
elif (( $grade >= 90 ));then 
	echo " you get an A"
elif (( $grade >= 80 )); then
	echo "You get a B"
elif (( $grade >=70 )); then
	echo "You get a C"
elif (( $grade >= 60 )); then 
	echo "you get a D"
else
	echo "you get a F"
fi

```
</p>
</details>


<details><summary>:blossom: If statement</summary>
<p>

```bash
  
light="green"

if [[ $light = "red" ]];then 
	echo "Stop"
elif [[ $light = "yellow" ]];then 
	echo "Slow down"
elif [[ $light = "green" ]];then
	echo "Go"
else 
	echo "???"
fi

#a=7
if (( $a > 10 )); then
	echo "Bigger than 10"
else 
	echo "Less than 10"
fi

light="RED"
if [[ $light = "red" || $light = "RED" ]]; then
	echo "Stop"

fi

a=7
if (( 0 < a)) && (( a < 10 )); then
	echo " a is a single digit"
fi
```
</p>
</details>

<details><summary>:blossom: Numbers </summary>
<p>

```bash
a=7
b=2
#c=5+3
c=$((5+3))
#d=$a+$b
d=$(($a+$b))
echo $a #7
echo $b #2
echo $c #8
echo $d #9
#echo $((3.2+1.4)) #it will give an error. There is no built in float in Bash
echo
echo $((a*b)) #14
echo $((a/b)) #3
echo $((99/100))

echo "Enter 1st number"
read first_number

echo "Enter 2nd number"
read second_number

echo "Your total is $((first_number + second_number))"
```
</p>
</details>


<details><summary>:blossom: User input </summary>
<p>

```bash
#first_name="Tri"
#last_name="McNichols"
#echo "Hi. my name is $first_name $last_name "
 
echo "What is your first name "
read first_name

echo "What is your last name"
read last_name
echo "Hello $first_name $last_name"

```
</p>
</details>

<details><summary>:blossom: Variables</summary>
<p>

```bash
#animal = "dog"
animal="dog"
echo $animal
echo $aminal
echo "Something"
echo "I have a $animal"

phrase="Hello        World"
echo $phrase
echo "$phrase"
echo '$phrase'

count='6'
fruit='apple'

echo "I have $count $fruit""s"
```
</p>
</details>


<details><summary>:blossom: Hello World </summary>
<p>

```bash
   #echo "Hello World"
: 'mkdir test_me
cd test_me
touch FileA FileB FileC
'
echo "Hello        World"

echo Hello       World
```
</p>
</details>
