# Program: Project2

        .data			# data declaration section

        input: .space 1001	# input string space allocation

        unrecognized_input: .asciiz "Unrecognized Input" # prompt for string

	      comma: .asciiz ","

	
	
        .text			# assembly language instructions


main:				      # beginning of code section

	li $v0, 8		    # system call for printing strings
	la $a0, input		# prompt for the string you're printing (user input)
	li $a1, 1000
	syscall


	input_line:
	move $t3, $a0
	lb $s2, ($t3)


	input_values:		# going thru input values one by one to see where they fall/stand in ASCII
	addi $t0, $t0, 1
	bge $t5, 5, non_answer
	beq $s2, 9, blank_check
	beq $s2, 32, blank_check
	bge $s2, 98, lower_u	# seeing if there are any values between 'a' and 'v'
	bge $s2, 66, upper_u	# seeing if there are any values between 'A' and 'V'
	bge $s2, 48, numbers	# seeing if there are any values between '0' and '9'
	beq $s2, 10, calculate
	beq $s2, 0, calculate

	
	counter:		        # creating a counter for various loops
	addi $t3, $t3, 1
	addi $t5, $t5, 1
	lb $s2, ($t3)
	j input_values

	
	blank_counter:		# creating a counter for various loops
	addi $t3, $t3, 1
	lb $s2, ($t3)
	j input_values

	
	blank_check:
	beq $t1, $zero, blank_counter
	li $t2, 1
	beq $t2, 1, blank_counter
	li $t4, 1
	j counter
	

	lower_u:		        # for lowercase 'v'
	li $t0, 1
	beq $t2, 1, non_answer	
	bgt $s2, 118, non_answer
	beq $t1, 1, skip1
	li $t1, 1
	move $t6, $t3

	skip1:
	beq $t4, 1, non_answer
	j counter


	upper_u:		              # for uppercase 'V'
	li $t0, 1
	beq $t2, 1, non_answer	
	bgt $s2, 86, non_answer
	beq $t1, 1, skip2
	li $t1, 1
	move $t6, $t3

	skip2:
	beq $t4, 1, non_answer
	j counter


	numbers:		            # for number range
	li $t0, 1
	beq $t2, 1, non_answer	
	bgt $s2, 57, non_answer
	beq $t1, 1, skip3
	li $t1, 1
	move $t6, $t3

	skip3:
	beq $t4, 1, non_answer
	j counter

	
	calculate:
	addi $sp, $sp, -8
	sw $t5, 0($sp)
	sw $t6, 4($sp)
	move $a0, $t6
	move $a1, $t5
	jal decimal_num

	decimal_num:
	li $t0, 0
	loop:
	bge $t7, $a1, answer
	lb $s2, 0($a0)
	beq $t0, $a1, non_answer
	ble $s2, 58, sub_num
	ble $s2, 86, sub_upper
	ble $s2, 118, sub_lower

	sub_num:
	sub $s2, $s2, 48
	sw $a0, 0($sp)
	sw $a1, 4($sp)
	j power 
	
	sub_upper:
	sub $s2, $s2, 55
	sw $a0, 0($sp)
	sw $a1, 4($sp)
	j power
	
	sub_lower:
	sub $s2, $s2, 87
	sw $a0, 0($sp)
	sw $a1, 4($sp)
	j power
	

	multiply:
	mult $s2, $v0
	mflo $s0
	add $s3, $s3, $s0
	addi $t7, $t7, 1
	addi $a0, $a0, 1
	j loop


	power:				            # takes the base (31) in $s4 and exponent in $a1 to return result in $v0
	sub $t8, $a1, $t7
	sub $t8, $t8, 1
	li $s4, 32
	sub $s1, $t7, $t0
	sub $s1, $s1, 1
	addi $v0, $zero, 1
	add $t0, $zero, $zero
	
	powerloop:
	slt $t4, $t0, $t8
	beq $t4, $zero, endpowerloop
	mult $v0, $s4
	mflo $v0
	addi $t0, $t0, 1
	j powerloop

	endpowerloop:
	j multiply


	non_answer:			       # prompt for unrecognized input
	li $v0, 4
	la $a0, unrecognized_input
	syscall

	
	li $v0, 10		        # call to exit program 
	syscall			          # call to exit program


	
	answer:				        # prompt for answers
	li $v0, 1
	la $a0, ($t5)
	syscall

	li $v0, 4
	la $a0, comma
	syscall

	li $v0, 1		         # prints out second half of result
	move $a0, $s3
	syscall



	li $v0, 10		      # call to exit program 
	syscall			        # call to exit program
     
 
