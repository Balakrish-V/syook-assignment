//header file inclusion
#include<stdio.h>
#include<stdlib.h>

// for easy understanding - typedef
typedef unsigned int u32;
typedef unsigned short int u16;
typedef unsigned char u8;

// Packet
typedef struct beacon_acc
{
	u8 data_length;
	u8 flag_data_type;
	u8 flag_data;
	u8 UUIDs_services;
	u16 UUID_data;
	u8 service_data;
	u8 frame_type;
	u8 version_number;
	u8 battery_level;
	u16 x_axis;
	u16 y_axis;
	u16 z_axis;
	u8 *mac;
}ACCELERATOR;

u8 **q;
// this is like a global iterator for traversing till END OF STRING

//function which extracts the information from the raw data packet
u32 extract_beacon_packets(u8 **p);

//for modularity, another subroutine has been used here
u8* extract_byte(u32 n);

//execution from in main()
int main()
{

	unsigned char *p="0x0201060303E1FF1216E1FFA10364FFF4000FFF003772A33F23AC";

	extract_beacon_packets(&p);
}

u32 extract_beacon_packets(u8 **t)
{
	q=t;  //Global iterator in initialized here

	*q+=2; 
	/* to omit the sign 0x that represents the hexa decimal notation*/ 

	ACCELERATOR packet_1;

	packet_1.data_length=*(extract_byte(1))-48;
	/* -48 is for conversion from char to int */	
	packet_1.flag_data_type=*(extract_byte(1))-48;

	packet_1.flag_data=*(extract_byte(1))-48;

	packet_1.data_length=*(extract_byte(1))-48;

	packet_1.UUIDs_services=*(extract_byte(1))-48;

	packet_1.UUID_data=*(extract_byte(2));

	packet_1.battery_level=*(extract_byte(1)-48);
	
	printf("Battery_level = %d %%\n",((packet_1.battery_level)/64)*100);
	packet_1.x_axis=*(extract_byte(2));

	packet_1.x_axis=*(extract_byte(2));
	
	packet_1.x_axis=*(extract_byte(2));
	
	int is_moving = packet_1.x_axis != 0 || packet_1.y_axis != 0 || packet_1.z_axis != 0;

	printf("Beacon Status: %s\n", is_moving ? "Moving" : "Not Moving");

	packet_1.mac=extract_byte(6);

}


u8* extract_byte(u32 n)  /* n -> number of bytes to be extracted */
{
	u8* p=(u8*)malloc(sizeof(u8)*n);
	int i;
	if(n==1)
	{	
		if(*q!=0) /* to avoid the program crash - segmentation fault */
		{
			// here byte ordering is happening
			p[i]=(**q);
			p[i]<<=4;
			*q+=1;
			p[i]=p[i]|(**q);
			*q+=1;
		}
	}
	else if(n==2)
	{
		char temp1;
		for(i=0;n--;i++)
		{
			if(*q!=0)
			{
				// here 2 bytes are fetched and the nibbles are being byte ordered
				p[i]=(**q);
				p[i]-=48;
				p[i]<<=4;
				*q+=1;
				p[i]=p[i]|(**q);
				temp1=p[i]>>4;
				temp1-=48;
				p[i]|=temp1;
				*q+=1;
			}
		}
	}
	else if(n==6)
	{	
		p=*q;
		/* because already the global iterator moved till the last and we are only extracting the remaining last 6 bytes of data*/
	}
	return p;
}

