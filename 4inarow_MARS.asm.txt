.data
	str1:	.asciiz	" | "
	curpiece:	.asciiz	" | Player's piece: "
	cantun:	.asciiz	"\nYou can undo no more! Agree? "
	asku:	.asciiz	"\nDo you want to undo? ('y': Yes): "
	turnof:	.asciiz	"\nTurn of player "
	pcol:	.asciiz	"\nChoose a collumn from 1-7: "
	vio1:	.asciiz 	"Violations of player 1: "
	vio2:	.asciiz	"Violations of player 2: "
	ud1:	.asciiz	"\nUndoings of player 1: "
	ud2:	.asciiz	"Undoings of player 2: "
	invalid:	.asciiz	"\nINVALID INPUT. Rules violation counted"
	filled:	.asciiz	"\nTable is filled! Game result is draw\n"
	colfill:	.asciiz	"\nCollumn filled!"
	pick:	.asciiz	"\nPick your preferred piece (Input 'x' or 'o'): "
	plwin:	.asciiz	"\nPLAYER "
	win:	.asciiz	" WIN!!!\n"
	rowindex:	.word	'1','2','3','4','5','6','7'
	row1:	.word	'-':7
	row2:	.word	'-':7
	row3:	.word	'-':7
	row4:	.word	'-':7
	row5:	.word	'-':7
	row6:	.word	'-':7
	last:	.word	0 0
	counter:	.word	0:6 #5+6 is player's symbol memory
	
.text
	j	main
				##### GENERAL PURPOSE #####
	get_adrs:	# row&collumn index from s0,s1 | return adrs in s2
		addi	$sp, $sp, -20
		sw	$t0, 12($sp)
		sw	$t1, 16($sp)
		sw	$s1, 0($sp)
		sw	$s0, 8($sp)
		sw	$ra, 4($sp)
		
		addi	$t0, $0, 0
		addi	$t0, $t0, 1
		la	$t1, row1
		beq	$t0, $s0, done2
		addi	$t0, $t0, 1
		la	$t1, row2
		beq	$t0, $s0, done2
		addi	$t0, $t0, 1
		la	$t1, row3
		beq	$t0, $s0, done2
		addi	$t0, $t0, 1
		la	$t1, row4
		beq	$t0, $s0, done2
		addi	$t0, $t0, 1
		la	$t1, row5
		beq	$t0, $s0, done2
		addi	$t0, $t0, 1
		la	$t1, row6
		beq	$t0, $s0, done2
		
	done2.0:	addi	$s2, $0, 0
		bne	$t0, $s0, done2.1
			
	done2:	addi 	$t0, $s1, -8
		slti	$t0, $t0, 0
		beq	$t0, $0, done2.0
		addi 	$s1, $s1, -1
		sll	$s1, $s1, 2
		add	$s2, $t1, $s1
			
	done2.1:	lw	$s1, 0($sp)
		lw	$s0, 8($sp)
		lw	$ra, 4($sp)
		lw	$t0, 12($sp)
		lw	$t1, 16($sp)
		addi	$sp, $sp, 20
		jr 	$ra
				##### PRINT CURRENT STATE #####
	printScreen:
		addi	$sp, $sp, -4
		sw	$ra, 0($sp)
		jal	print1
		jal	printOthers
	# print turn of 
	la	$4, turnof
	li	$2, 4
	syscall
	addi	$4, $s4, 1
	li	$2, 1
	syscall	
	la	$4, curpiece
	li	$2, 4
	syscall
	move	$4, $s5
	li	$2, 11
	syscall
		lw	$ra, 0($sp)
		addi	$sp, $sp, 4
		jr	$ra
	printOthers:
		addi	$sp, $sp, -4
		sw	$t0, 0($sp)
		la	$t0, counter
		la	$4, vio1
		li	$2, 4
		syscall
		lw	$4, 0($t0)
		li	$2, 1
		syscall
		la	$4, str1
		li	$2, 4
		syscall
		la	$4, vio2
		li	$2, 4
		syscall
		lw	$4, 8($t0)
		li	$2, 1
		syscall
		
		la	$4, ud1
		li	$2, 4
		syscall
		lw	$4, 4($t0)
		li	$2, 1
		syscall
		la	$4, str1
		li	$2, 4
		syscall
		la	$4, ud2
		li	$2, 4
		syscall
		lw	$4, 12($t0)
		li	$2, 1
		syscall
		lw	$t0, 0($sp)
		addi	$sp, $sp, 4
		jr	$ra
		
	print1:	#print 7 collunms
		addi	$sp, $sp,-8
		sw	$ra, 0($sp)
		sw	$s7,4($sp)
		
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		syscall
		syscall
		syscall
		la	$s7, rowindex
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row1
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row2
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row3
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row4
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row5
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		la	$s7, row6
		jal 	print
		addi	$4, $0, '\n'
		li	$2, 11
		syscall
		
		lw	$ra, 0($sp)
		lw	$s7,4($sp)
		addi	$sp, $sp,8
		jr 	$ra
	
	print:	#s7 = adrs, s6 = size
		addi	$sp, $sp,-16
		sw	$s7, 0($sp)
		sw 	$s6, 4($sp)
		sw 	$s5, 8($sp)
		sw	$ra, 12($sp)
		
		la	$s5, str1
		addi	$s6,$0,7
		sll	$s6,$s6,2
		add 	$s6, $s7,$s6
	l1:
		addi 	$4, $s5, 0
		li	$2, 4
		syscall
		lw	$4,0($s7)
		li	$2, 11
		syscall
		addi	$s7, $s7, 4
		bne	$s7,$s6,l1
		
		addi 	$4, $s5, 0
		li	$2, 4
		syscall
		
		lw	$s7, 0($sp)
		lw 	$s6, 4($sp)
		lw 	$s5, 8($sp)
		lw	$ra, 12($sp)
		addi	$sp, $sp,16
		jr	$ra
				##### INSERTION #####
	available_row: #return s6 as number of row that is available, takes s7 as input collumn 
		addi	$sp, $sp,-20
		sw	$s0, 4($sp)
		sw	$s1, 8($sp)
		sw	$ra, 0($sp)
		sw	$t1, 12($sp)
		sw	$t0, 16($sp)
		
		addi	$s1, $s7, 0
		addi	$s0, $0, 6
		addi 	$t0, $0, '-'
	l2:
		jal	get_adrs
		lw	$t1, 0($s2)
		addi	$s0, $s0, -1
		beq	$s0, $0, done1
		bne	$t1, $t0, l2
		
	done1:	addi	$s6, $s0, 1
		lw	$t1, 12($sp)
		lw	$t0, 16($sp)
		lw	$ra, 0($sp)
		lw	$s0, 4($sp)
		lw	$s1, 8($sp)
		addi	$sp, $sp,20
		jr 	$ra
		
	insert_at_s7: #s7 as number of collumn to be inserted, s5 as what to be inserted | return s0 as inserted row
		addi	$sp, $sp, -24
		sw 	$s1, 4($sp)
		sw 	$s2, 8($sp)
		sw 	$ra, 12($sp)
		sw 	$s6, 16($sp)
		sw 	$s5, 20($sp)
		
		addi	$s1, $s7, 0
		jal	available_row
		addi 	$s0, $s6, 0
		jal	get_adrs
		sw	$s5, 0($s2)
		
		la	$s5, last
		sw	$s0, 0($s5)
		sw	$s1, 4($s5)
		
		lw 	$s1, 4($sp)
		lw 	$s2, 8($sp)
		lw 	$ra, 12($sp)
		lw 	$s6, 16($sp)
		lw 	$s5, 20($sp)
		addi	$sp, $sp, 24
		jr	$ra
				##### CONDITION #####
	check_horizontal: # s0,s1 as index of need checking slot, s2 as of symbol checking | return true, false in s3
		addi 	$sp, $sp, -20
		sw	$ra, 0($sp)
		sw	$s1, 4($sp)
		sw	$t1, 12($sp)
		sw	$t2, 8($sp)
		sw	$s2, 16($sp)
		
		addi	$t1, $0, 0
	l3_1:
		addi	$s1, $s1, 1 
		jal	get_adrs
		beq	$s2, $0, l3_2
		lw	$t2, 0($s2)
		lw 	$s2, 16($sp)
		bne	$t2, $s2, l3_2
		addi	$t1, $t1, 1
		beq	$t2, $s2, l3_1
		
	l3_2:	lw	$s1, 4($sp)
	l3_3:
		add	$s1, $s1, -1
		jal	get_adrs
		beq	$s2, $0, l3_4
		lw	$t2, 0($s2)
		lw	$s2, 16($sp)
		bne	$t2, $s2, l3_4
		addi	$t1, $t1, 1
		beq	$t2, $s2, l3_3
	
	l3_4:	addi	$t1, $t1, -3
		slti	$t2, $t1, 0
		lw	$s2, 16($sp)
		lw	$ra, 0($sp)
		lw	$s1, 4($sp)
		lw	$t1, 12($sp)
		
		beq	$t2, $0, l3_5
		addi 	$s3, $0, 0
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 20
		jr 	$ra

	l3_5:	addi	$s3, $0, 1
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 20
		jr	$ra
	
	check_vertical: # s0,s1 as index of need checking slot, s2 as of symbol checking | return true, false in s3
		addi 	$sp, $sp, -20
		sw	$ra, 0($sp)
		sw	$s0, 4($sp)
		sw	$t1, 12($sp)
		sw	$t2, 8($sp)
		sw	$s2, 16($sp)
		
		addi	$t1, $0, 0
	l4_1:	addi	$s0, $s0, 1 
		jal	get_adrs
		beq	$s2, $0, l4_2
		lw	$t2, 0($s2)
		lw 	$s2, 16($sp)
		bne	$t2, $s2, l4_2
		addi	$t1, $t1, 1
		beq	$t2, $s2, l4_1
		
	l4_2:	lw	$s0, 4($sp)
	l4_3:
		addi	$s0, $s0, -1
		jal	get_adrs
		beq	$s2, $0, l4_4
		lw	$t2, 0($s2)
		lw	$s2, 16($sp)
		bne	$t2, $s2, l4_4
		addi	$t1, $t1, 1
		beq	$t2, $s2, l4_3
	
	l4_4:	addi	$t1, $t1, -3
		slti	$t2, $t1, 0
		lw	$s2, 16($sp)
		lw	$ra, 0($sp)
		lw	$s0, 4($sp)
		lw	$t1, 12($sp)
		
		beq	$t2, $0, l4_5
		addi 	$s3, $0, 0
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 20
		jr 	$ra

	l4_5:	addi	$s3, $0, 1
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 20
		jr	$ra
		
	check_diagonal1:# s0,s1 as index of need checking slot, s2 as of symbol checking | return true, false in s3
		addi 	$sp, $sp, -24
		sw	$ra, 0($sp)
		sw	$s1, 20($sp)
		sw	$s0, 4($sp)
		sw	$t1, 12($sp)
		sw	$t2, 8($sp)
		sw	$s2, 16($sp)
		
		addi	$t1, $0, 0
	l5_1:	addi	$s0, $s0, 1
		addi	$s1, $s1, 1 
		jal	get_adrs
		beq	$s2, $0, l5_2
		lw	$t2, 0($s2)
		lw 	$s2, 16($sp)
		bne	$t2, $s2, l5_2
		addi	$t1, $t1, 1
		beq	$t2, $s2, l5_1
		
	l5_2:	lw	$s0, 4($sp)
		lw	$s1, 20($sp)
	l5_3:
		addi	$s0, $s0, -1
		addi	$s1, $s1,-1
		jal	get_adrs
		beq	$s2, $0, l5_4
		lw	$t2, 0($s2)
		lw	$s2, 16($sp)
		bne	$t2, $s2, l5_4
		addi	$t1, $t1, 1
		beq	$t2, $s2, l5_3
	
	l5_4:	addi	$t1, $t1, -3
		slti	$t2, $t1, 0
		lw	$s2, 16($sp)
		lw	$ra, 0($sp)
		lw	$s0, 4($sp)
		lw	$t1, 12($sp)
		lw	$s1, 20($sp)
		
		beq	$t2, $0, l5_5
		addi 	$s3, $0, 0
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 24
		jr 	$ra

	l5_5:	addi	$s3, $0, 1
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 24
		jr	$ra
	check_diagonal2:# s0,s1 as index of need checking slot, s2 as of symbol checking | return true, false in s3
		addi 	$sp, $sp, -24
		sw	$ra, 0($sp)
		sw	$s1, 20($sp)
		sw	$s0, 4($sp)
		sw	$t1, 12($sp)
		sw	$t2, 8($sp)
		sw	$s2, 16($sp)
		
		addi	$t1, $0, 0
	l6_1:	addi	$s0, $s0, -1
		addi	$s1, $s1, 1 
		jal	get_adrs
		beq	$s2, $0, l6_2
		lw	$t2, 0($s2)
		lw 	$s2, 16($sp)
		bne	$t2, $s2, l6_2
		addi	$t1, $t1, 1
		beq	$t2, $s2, l6_1
		
	l6_2:	lw	$s0, 4($sp)
		lw	$s1, 20($sp)
	l6_3:
		addi	$s0, $s0, 1
		addi	$s1, $s1,-1
		jal	get_adrs
		beq	$s2, $0, l6_4
		lw	$t2, 0($s2)
		lw	$s2, 16($sp)
		bne	$t2, $s2, l6_4
		addi	$t1, $t1, 1
		beq	$t2, $s2, l6_3
	
	l6_4:	addi	$t1, $t1, -3
		slti	$t2, $t1, 0
		lw	$s2, 16($sp)
		lw	$ra, 0($sp)
		lw	$s0, 4($sp)
		lw	$t1, 12($sp)
		lw	$s1, 20($sp)
		
		beq	$t2, $0, l6_5
		addi 	$s3, $0, 0
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 24
		jr 	$ra

	l6_5:	addi	$s3, $0, 1
		lw	$t2, 8($sp)
		addi 	$sp, $sp, 24
		jr	$ra
	win.check:	#s0, s1 as input index; s2 as current piece | return s7 (0: not yet, 12: won)
		addi	$sp, $sp, -4
		sw	$ra, 0($sp)
		
		addi	$s7, $0 ,12
		jal	check_vertical
		bne	$s3, $0, l.wc.1	
		jal	check_horizontal
		bne	$s3, $0, l.wc.1
		jal	check_diagonal1
		bne	$s3, $0, l.wc.1
		jal	check_diagonal2
		bne	$s3, $0, l.wc.1
		addi	$s7, $0, 0
	l.wc.1:	
		lw	$ra, 0($sp)
		addi	$sp, $sp, 4
		jr	$ra
		
				##### EXCEPTION #####	
	check.input.char: #input s1 | return s7 (0 = valid, 11 = invalid)
		addi	$sp, $sp, -16
		sw	$ra, 0($sp)
		sw	$t0, 4($sp)
		sw	$t1, 8($sp)
		sw	$s1, 12($sp)
		
		addi	$s7, $0, 0
		addi	$t0, $0, 'x'
		addi	$t1, $0, 'o'
		beq	$s1, $t0, l.cic.1
		beq	$s1, $t1, l.cic.1
		addi	$s7, $0, 11
		la	$4, invalid
		li	$2, 4
		syscall
	l.cic.1:	
		lw	$ra, 0($sp)
		lw	$t0, 4($sp)
		lw	$t1, 8($sp)
		lw	$s1, 12($sp)
		addi	$sp, $sp, 16	
		jr	$ra
		
	check.input.num: #input s1 | return s7 (0 = valid, 11 = invalid input
		addi	$sp, $sp, -12
		sw	$ra, 0($sp)
		sw	$t0, 4($sp)
		sw	$t1, 8($sp)
		
		li	$t1, 0
		li	$t0, '1'
		li	$s7, 0
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '2'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '3'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '4'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '5'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '6'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$t0, '7'
		addi	$t1, $t1, 1
		beq	$s1, $t0, l.cin1
		li	$s7, 11
		la	$4, invalid
		li	$2, 4
		syscall
	l.cin1:	move	$s1, $t1
		lw	$ra, 0($sp)
		lw	$t0, 4($sp)
		lw	$t1, 8($sp)
		addi	$sp, $sp, 12
		jr	$ra
		
	check.draw: #input s1 | return exception code in s7(0 = valid, 10 = board filled)
		addi 	$sp, $sp, -24
		sw	$ra, 0($sp)
		sw	$s1, 4($sp)
		sw	$s0, 8($sp)
		sw	$t1, 12($sp)
		sw	$t0, 16($sp)
		sw	$s2, 20($sp)
		
		li	$s1, 0
		li	$s0, 1
		li	$s7, 10
	l.ca.1:	
		addi	$s1, $s1, 1
		jal	get_adrs
		beq	$s2, $0, l.ca.2
		lw	$t0, 0($s2)
		li	$t1, '-'
		bne	$t0, $t1, l.ca.1
		li	$s7, 0
		j	l.ca.3
	l.ca.2:	
		la	$4, filled
		li	$2, 4
		syscall
		j	end	
	l.ca.3:
		lw	$ra, 0($sp)
		lw	$s1, 4($sp)
		lw	$s0, 8($sp)
		lw	$t1, 12($sp)
		lw	$t0, 16($sp)
		lw	$s2, 20($sp)
		addi	$sp, $sp, 24		
		jr	$ra
				##### UNDO MECHANIC #####
	askUndo:	#call after one move , current player code in s4
		#s7 (0 = dont undo, 8 = undoed)
		addi	$sp, $sp, -28
		sw	$ra, 0($sp)
		sw	$4, 4($sp)
		sw	$t0, 8($sp)
		sw	$t1, 12($sp)
		sw	$s3, 16($sp)
		sw	$s1, 20($sp)
		sw	$s0, 24($sp)
		#check if undo-able
	# inspect counter
		li	$s7, 0
		move	$a2, $s4 
		li	$a3, 1
		jal	countdraw
		lw	$t0, 0($s2) 
	# player.s4's adrs's undoing in s2 
		slti	$t0, $t0, 3 
		bne	$t0, $0, u.able
		#can't undo no more
		la	$4, cantun
		li	$2, 4
		syscall
		li	$2, 12
		syscall
		j	l.au.1
	u.able:	#ask if undo wanted
		li	$2, 4
		la	$4, asku
		syscall	
		li	$2, 12
		syscall	
		addi	$t0, $2, -121
		bne	$t0, $0, l.au.1
		#undo wanted
	# undo
		li	$s7, 8
		la	$t0, last
		lw	$s0, 0($t0)
		lw	$s1, 4($t0)
		jal	get_adrs
		addi	$t0, $s2, 0
		addi	$t1, $0, '-'
		sw	$t1, 0($t0)
	# inspect counter
		move	$a2, $s4 
		li	$a3, 1
		jal	countdraw
		lw	$t0, 0($s2)
	# increasing undoing
		addi	$t0, $t0, 1
		sw	$t0, 0($s2) 	
		
	l.au.1:	lw	$ra, 0($sp)
		lw	$4, 4($sp)
		lw	$t0, 8($sp)
		lw	$t1,12($sp)
		lw	$s3, 16($sp)
		lw	$s1, 20($sp)
		lw	$s0, 24($sp)
		addi	$sp, $sp, 28
		jr	$ra
	countdraw: #a2 as player code , a3 as category | return s2 (adrs seeking)
		#category: 0 to  player violation, 'else' to  player undoings
		#a2 as player code (0 as player 1, 'else' as player 2)
		addi	$sp, $sp, -20
		sw	$ra, 0($sp)
		sw	$a3, 4($sp)
		sw	$a2, 8($sp)
		sw	$t0, 12($sp)
		sw	$t1, 16($sp)
		
		la	$t0, counter
		bne	$a2, $0, l.ch.1
		bne	$a3, $0, l.ch.2
		j	l.ch.3  
	l.ch.2:	addi	$t0, $t0, 4
		j	l.ch.3
	l.ch.1:	bne	$a3, $0, l.ch.4
		addi	$t0, $t0, 8
		j	l.ch.3
	l.ch.4:	addi	$t0, $t0, 12
	l.ch.3:	addi	$s2, $t0 ,0
		
		lw	$ra, 0($sp)
		lw	$a3, 4($sp)
		lw	$a2, 8($sp)
		lw	$t0, 12($sp)
		lw	$t1, 16($sp)
		addi	$sp, $sp, 20
		jr	$ra
				##### GAMEFLOW #####
loadpiece:#input s4 as player's num
	addi	$sp, $sp, -8
	sw	$ra, 0($sp)
	sw	$t0, 4($sp)
	
	la	$t0, counter
	bne	$s4, 0, p2
	lw	$s5, 16($t0)
	addi	$s3, $t0, 4
	addi	$s6, $t0, 0
	j 	l.lp1
p2:	
	lw	$s5, 20($t0)
	addi	$s3, $t0, 12
	addi	$s6, $t0, 8	
l.lp1:
	lw	$ra, 0($sp)
	lw	$t0, 4($sp)
	addi	$sp, $sp, 8		
	jr	$ra

	

main:
	#randomly pick a player
	addi	$a1, $0, 2 
	li	$v0, 42
	syscall
	move	$s4, $a0 # player num got first move now in s4
random1:	la	$4, turnof
	li	$2, 4
	syscall
	addi	$4, $s4, 1
	li	$2, 1
	syscall	
	li 	$2, 4
	la	$4, pick
	syscall
	li	$2, 12
	syscall
	move	$s1, $2 # piece now in s1
	jal	check.input.char
	beq	$s7, $0, endrandom
	# check violation of player s4
	move	$a2, $s4
	li	$a3, 0
	jal	countdraw
	lw	$a2, 0($s2)
	slti	$a2, $a2, 2
	beq	$a2, $0, otherwin
	# add 1 to violation of player s4
	move	$a2, $s4
	li	$a3, 0
	jal	countdraw
	lw	$a2, 0($s2)
	addi	$a2, $a2, 1
	sw	$a2, 0($s2)
	j	random1
endrandom:
	#store pieces to memory
	la	$s2, counter
	#create the other piece for the other player
	addi	$s3, $0, 'x'
	bne	$s1, $s3, l.er2
	addi	$s3, $0, 'o'
	
l.er2:	bne	$s4, $0, l.er1
	sw	$s1, 16($s2)
	sw	$s3, 20($s2)
	j	play
l.er1:	sw	$s1, 20($s2)
	sw	$s3, 16($s2)
	j 	play

play:
	#	$s5 as turn's symbol
	#	$s4 as turn's number
	#	$s3 as turn's undo count adrs
	#	$s6 as turn's invalid count adrs
ask4i:	jal	loadpiece
	jal	printScreen
	# ask for input
	la	$4, pcol
	li	$2, 4
	syscall
	li	$2, 12
	syscall
	move	$s1, $2	
	jal	check.input.num	#col coverted to num in s1
	beq	$s7, $0, check.col.filled
vio:	# check violation of player s4
	move	$a2, $s4
	li	$a3, 0
	jal	countdraw
	lw	$a2, 0($s2)
	slti	$a2, $a2, 2
	beq	$a2, $0, otherwin
	# add 1 to violation of player s4
	move	$a2, $s4
	li	$a3, 0
	jal	countdraw
	lw	$a2, 0($s2)
	addi	$a2, $a2, 1
	sw	$a2, 0($s2)
	j	ask4i
check.col.filled: #input s1 | return s7 (0 = valid, 9 = invalid)
	li	$s0, 1
	jal	get_adrs
	lw	$a0, 0($s2)
	li	$a1, '-'
	beq	$a1, $a0, insert
	la	$4, colfill
	li	$2, 4
	syscall
	j	vio
insert:
	move	$s7, $s1
	jal	insert_at_s7
	jal	printScreen
check.win:
	move	$s2, $s5
	jal	win.check
	bne	$s7, $0, winning
check.full:
	jal	check.draw
askundo:
	jal	askUndo
	bne	$s7, $0, ask4i	
switchturn:
	beq	$s4, $0, switch
	li	$s4, 0			
	j	play
	switch:
	li	$s4, 1
	j	play
	
otherwin:	#change cur player piece and let them win
	beq	$s4, $0, change
	li	$s4, 0
	j	winning
	change:
	li	$s4, 1
	j 	winning
winning:	
	la	$4, plwin
	li	$2, 4
	syscall
	addi	$s4, $s4, 1
	move	$4, $s4
	li	$2, 1
	syscall
	la	$4, win
	li	$2, 4
	syscall	
end:	
	li 	$2, 10
	syscall
	
	
