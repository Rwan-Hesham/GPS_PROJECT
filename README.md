# GPS_PROJECT
void UART5Init() {

	// 1st give it clk

	SYSCTL_RCGCUART_R |= SYSCTL_RCGCUART_R5; // INITIALIZE CLK OF UART

	SYSCTL_RCGCGPIO_R |= SYSCTL_RCGCGPIO_R4; // INITIALIZE CLK OF PORT

	// NO NEED TO DELAY

	// disable uart

	UART5_CTL_R &= ~UART_CTL_UARTEN; // 0x01

	// set Baud rate divider, BR = 9600 bits/sec

	// BR = 16*10^6/(16*9600) = 104.16667

	UART5_IBRD_R = 104;

	// 0.16667=>

	UART5_FBRD_R = 11;

	//UART0_CC_R = UART_CC_CS_SYSCLK;

	UART5_LCRH_R = (UART_LCRH_WLEN_8 | UART_LCRH_FEN);

	UART5_CTL_R |= (UART_CTL_UARTEN | UART_CTL_RXE | UART_CTL_TXE);

	GPIO_PORTE_AFSEL_R |= 0x30;

	GPIO_PORTE_PCTL_R = (GPIO_PORTE_PCTL_R & ~0x00ff0000) | (GPIO_PCTL_PE4_U5RX | GPIO_PCTL_PE5_U5TX);

	GPIO_PORTE_DEN_R |= 0x30;

GPIO_PORTE_AMSEL_R = 0;

}

uint8_t UART5_ReadAvailable(void) {

	return ((UART5_FR_R & UART_FR_RXFE) == UART_FR_RXFE) ? 0:1;

}

//func read data from uart

char UART5_read() {

	while((UART5_ReadAvailable()) != 1);

	return UART5_DR_R & 0xFF;

}

void UART5_write(char c) {

	while((UART5_FR_R & UART_FR_TXFF) != 0);

	UART5_DR_R = c;

}
