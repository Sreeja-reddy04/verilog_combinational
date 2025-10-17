# combinational circuits
- Ripple carry adder
- 4x1 MUx
- 4x1 using 2x1
- 4x1 (using 2:4 decoder & tristate buffer)
- Operators
- 3x8 Decoder
- 8x3 priority encoder
- ALU (8-bit)
  # operators
## [RTL]
```bash

```
## [Test bench]
```bash

```
# Ripple carry adder
## [RTL]
```bash
module ripple_carry(a0,a1,a2,a3,b0,b1,b2,b3,c,sum0,sum1,sum2,sum3,carry);
input a0,a1,a2,a3;
input b0,b1,b2,b3;
input c;
output sum0,sum1,sum2,sum3;
output carry;
wire y0,y1,y2;
fulladder FA1(.a(a0),.b(b0),.c(c),.sum(sum0),.carry(y0));
fulladder FA2(.a(a1),.b(b1),.c(y0),.sum(sum1),.carry(y1));
fulladder FA3(.a(a2),.b(b2),.c(y1),.sum(sum2),.carry(y2));
fulladder FA4(.a(a3),.b(b3),.c(y2),.sum(sum3),.carry(carry));
endmodule
module fulladder(a,b,c,sum,carry);
input a,b,c;
output sum,carry;
assign sum=(a^b^c);
assign carry=(c&(a^b)|a&b);
endmodule
```
## [Test bench]
```bash
module ripple_carry_tb;
	// Inputs
	reg a0;
	reg a1;
	reg a2;
	reg a3;
	reg b0;
	reg b1;
	reg b2;
	reg b3;
	reg c;
	wire sum0;
	wire sum1;
	wire sum2;
	wire sum3;
	wire carry;
   integer i,j;
	// Instantiate the Unit Under Test (UUT)
	ripple_carry uut (
		.a0(a0), 
		.a1(a1), 
		.a2(a2), 
		.a3(a3), 
		.b0(b0), 
		.b1(b1), 
		.b2(b2), 
		.b3(b3), 
		.c(c), 
		.sum0(sum0), 
		.sum1(sum1), 
		.sum2(sum2), 
		.sum3(sum3), 
		.carry(carry)
	);

	initial begin
		a0 = 0;
		a1 = 0;
		a2 = 0;
		a3 = 0;
		b0 = 0;
		b1 = 0;
		b2 = 0;
		b3 = 0;
		c = 0;
		#100;
     for (i=0;i<16;i=i+1)
	    begin
	       {a0,a1,a2,a3}=i;
	       #10;
	    end 
     for (j=0;j<16;j=j+1)
	    begin
	       {b0,b1,b2,b3}=j;
	       #10;
	    end		
	end
      initial $monitor(" Input a0 = %b,a1 = %b,a2 = %b,a3 =%b,b0 =%b,b1 =%b,b2 =%b,b3 =%b,c =%b, output sum0 = %b,sum1 = %b,sum2 = %b,sum3 =%b,carry=%b",a0,a1,a2,a3,b0,b1,b2,b3,c,sum0,sum1,sum2,sum3,carry);
	initial begin #300 $finish;
 end
endmodule
```
## 4x1 Mux
### [RTL]
```bash
module foutto1(i,s,y);
input wire[3:0]i;
input wire [1:0]s;
output reg y;
always@(*)
 begin
  case({s})
   2'b00:y=i[0];
	2'b01:y=i[1];
	2'b10:y=i[2];
	2'b11:y=i[3];
	default :y=0;
  endcase
 end
endmodule
```
### [Test bench]
```bash
module fourto1_tb;
	// Inputs
	reg [3:0] i;
	reg [1:0] s;
	// Outputs
	wire y;
   integer j,k;
	// Instantiate the Unit Under Test (UUT)
	foutto1 uut (
		.i(i), 
		.s(s), 
		.y(y)
	);
   task inti;
	begin
	  i=4'b0000;
	  s=2'b00;
	end
	endtask
	task in(input [3:0]j, input [1:0]k);
	 begin
	  i=j;
	  s=k;
	 end
	endtask
	initial begin
      inti;
		#100;
		for(j=0;j<16;j=j+1)
		begin
		for(k=0;k<4;k=k+1)
		begin
		 in(j,k);
		 #10;
		end
		end
	end
	initial 
	  begin
     $monitor("i=%b,s=%b,y=%b",i,s,y);
	  end
   initial 
     begin 
		#1000 $finish;	
     end 
endmodule
```
# 4x1 Mux 2x1
## [RTL]
```bash
module four_one_to_two_one_MUX(i0,i1,i2,i3,s1,s0,y);
input s1,s0;
input i0,i1,i2,i3;
output y;
wire y1,y2;
 mux m1(.i0(i0),.i1(i1),.s0(s0),.y(y1));
 mux m2(.i0(i2),.i1(i3),.s0(s0),.y(y2));
 mux m3(.i0(y1),.i1(y2),.s0(s1),.y(y));

endmodule
module mux(y,i0,i1,s0);
input i0,i1;
input s0;
output y;
assign y=(~s0&i0)|(s0&i1);
endmodule

```
## [Test bench]
```bash
module four_one_to_two_one_MUX_tb();
	reg i0;
	reg i1;
	reg i2;
	reg i3;
	reg s1;
	reg s0;
	wire y;
   integer i,j;
	four_one_to_two_one_MUX Dut (.i0(i0), .i1(i1),.i2(i2),.i3(i3),.s1(s1),
	.s0(s0),.y(y));
	initial begin
		// Initialize Inputs
		i0 = 0;
		i1 = 0;
		i2 = 0;
		i3 = 0;
		s1 = 0;
		s0 = 0;
		#100;
		  for (i=0;i<16;i=i+1) 
		 begin
		  {i0,i1,i2,i3}=i;
		  for (j=0;j<4;j=j+1)
		     begin
		  {s1,s0}=j;
		  #10;
		     end
     	end
	end
      initial 
      $monitor("Input i0=%b, i1=%b, i2=%b, i3=%b, s1 =%b, s0=%b, output y=%b",i0,i1,i2,i3,s1,s0,y);					
   initial
	#1000 $finish;	
endmodule
```
 # 4x1 (using 2:4 decoder & tristate buffer)
## [RTL]
```bash
module four_one_tristate(s0,s1,i0,i1,i2,i3,y);
input i0,i1,i2,i3;
input s0,s1;
output y;
wire a0,a1,a2,a3;
wire b0,b1,b2,b3;
decoder DEC(.a(s0),.b(s1),.y0(a0),.y1(a1),.y2(a2),.y3(a3));
tristate T1(.in(i0),.en(a0),.y(b0));
tristate T2(.in(i1),.en(a1),.y(b1));
tristate T3(.in(i2),.en(a2),.y(b2));
tristate T4(.in(i3),.en(a3),.y(b3));
assign y=b0;
assign y=b1;
assign y=b2;
assign y=b3;
endmodule

module decoder(input a,b,output y0,y1,y2,y3);
assign y0=(~a&~b);
assign y1=(~a&b);
assign y2=(a&~b);
assign y3=(a&b);
endmodule

module tristate(input in,en,output y);
assign y=en?in:1'bz;
endmodule
```
## [Test bench]
```bash
module four_one_tristate_tb;
	reg s0;
	reg s1;
	reg i0;
	reg i1;
	reg i2;
	reg i3;
	wire y;
   integer i,j;
	four_one_tristate uut (
		.s0(s0), 
		.s1(s1), 
		.i0(i0), 
		.i1(i1), 
		.i2(i2), 
		.i3(i3), 
		.y(y));
	initial begin
		// Initialize Inputs
		s0 = 0;
		s1 = 0;
		i0 = 0;
		i1 = 0;
		i2 = 0;
		i3 = 0;
		#100;
		for(i=0;i<16;i=i+1)
		begin
		{i0,i1,i2,i3}=i;
		for(j=0;j<4;j=j+1)
		begin
		{s0,s1}=j;
       #10;
end
end		 
	end
      initial begin
     $monitor(" s0=%b, s1=%b,i0=%b,i1=%b,i2=%b,i3=%b, y=%b", s0,s1,i0,i1,i2,i3,y);// Add stimulus here
	 end
      initial begin
	#1000 $finish;
	end
endmodule
```
# operators
## [RTL]
```bash
module four_one_to_two_one_MUX(i0,i1,i2,i3,s1,s0,y);
input s1,s0;
input i0,i1,i2,i3;
output y;
wire y1,y2;
 mux m1(.i0(i0),.i1(i1),.s0(s0),.y(y1));
 mux m2(.i0(i2),.i1(i3),.s0(s0),.y(y2));
 mux m3(.i0(y1),.i1(y2),.s0(s1),.y(y));
endmodule
module mux(y,i0,i1,s0);
input i0,i1;
input s0;
output y;
assign y=(~s0&i0)|(s0&i1);
endmodule
```
## [Test bench]
```bash
module four_one_to_two_one_MUX_tb();
	reg i0;
	reg i1;
	reg i2;
	reg i3;
	reg s1;
	reg s0;
	wire y;
   integer i,j;
	four_one_to_two_one_MUX Dut (.i0(i0), .i1(i1),.i2(i2),.i3(i3),.s1(s1),
	.s0(s0),.y(y));
	initial begin
		i0 = 0;
		i1 = 0;
		i2 = 0;
		i3 = 0;
		s1 = 0;
		s0 = 0;
		#100;
		  for (i=0;i<16;i=i+1) 
		 begin
		  {i0,i1,i2,i3}=i;
		  for (j=0;j<4;j=j+1)
		     begin
		  {s1,s0}=j;
		  #10;
		     end
     	end
      initial 
      $monitor("Input i0=%b, i1=%b, i2=%b, i3=%b, s1 =%b, s0=%b, output y=%b",i0,i1,i2,i3,s1,s0,y);
   initial
	#1000 $finish;	
endmodule
```
# Operators
## [RTL]
```bash
module operators(a,b,sum,diff,mul,quot,rem,
log_shift,arth_shift,reduct,bit_or,bit_neg,conc,relational);
input [3:0]a,b;
output reg [3:0]sum,diff,quot,rem,log_shift,arth_shift,reduct,bit_or,bit_neg,relational;
output reg [8:0]conc,mul;
always@(*)
 begin
  sum=a+b;
  diff=a-b;
  mul=a*b;
  quot=a/b;
  rem=a%b;
  log_shift=a>>1;
  arth_shift=b<<<1;
  reduct=&b;
  bit_or=a|b;
  bit_neg=~a;
  conc={2{a}};
  relational=a>b;
 end
endmodule
```
## [Test bench]
```bash
module operators_tb;
	reg [3:0] a;
	reg [3:0] b;
	wire [3:0]sum,diff,quot,rem,log_shift,arth_shift,reduct,bit_or,bit_neg,relational; 
   wire [8:0]mul,conc;	
	integer i,j;
	// Instantiate the Unit Under Test (UUT)
	operators uut (
		.a(a), 
		.b(b), 
		.sum(sum),.diff(diff),.mul(mul),.quot(quot),.rem(rem),
		.log_shift(log_shift),.arth_shift(arth_shift),.reduct(reduct),.bit_or(bit_or),.bit_neg(bit_neg),.conc(conc),.relational(relational)	);

	initial begin
		// Initialize Inputs
		a = 0;
		b = 0;
		#100;
		for(i=0;i<16;i=i+1)
		begin
		  a=i;
		  #10;
		 for(j=0;j<16;j=j+1)
		 begin
		 b=j;
		 #10;
		 end// Add stimulus here
      end
	end
	initial begin
        $monitor("Time=%0t | a=%b |b=%b| sum=%b | diff=%b | mul=%b | quot=%b|rem=%b log_shift=%b,arth_shift=%b,reduct=%b,bit_or=%b,bit_neg=%b,conc=%b relational=%b", $time, a,b,sum,diff,mul,quot,rem,log_shift,arth_shift,reduct,bit_or,bit_neg,conc,relational);
    end
	 initial begin #2000 $finish;
end	 
endmodule
```
# 3x8 Decoder
## [RTL]
```bash
module threeto8_decoder(i,en,y);
input [2:0]i;
input en;
output reg [7:0]y;
always@(*)
begin 
if (en==0)
begin
y=8'b1111_1111;
end
 else 
  begin
case(i)
  3'b000: y=8'b0000_0001;
  3'b001: y=8'b0000_0010;
  3'b010: y=8'b0000_0100; 
  3'b011: y=8'b0000_1000;
  3'b100: y=8'b0001_0000;
  3'b101: y=8'b0010_0000;
  3'b110: y=8'b0100_0000;
  3'b111: y=8'b1000_0000;
 default y=8'b0000_0000;
endcase
   end
end
endmodule
```
## [Test bench]
```bash
module threeto8_decoder_tb;
	// Inputs
	reg [2:0] i;
	reg en;
	// Outputs
	wire [7:0] y;
   integer j,n;
	// Instantiate the Unit Under Test (UUT)
	threeto8_decoder uut (.i(i),.en(en),.y(y));
	task inti;
	 begin
	  i=0;
	  en=0;
	 end
	endtask
	task delay;
	 begin
	  #10;
	 end
	endtask
	task inp(input [2:0]j,input n);
	 begin
	  i=j;
	  en=n;
	 end
	endtask
	initial begin
	inti;
	for(j=0;j<8;j=j+1)
	 begin
	  for(n=0;n<2;n=n+1)
	 begin
	  inp(j,n);
	  delay;
	 end
	 end
	end
	/*initial begin
		// Initialize Inputs
		i = 0;
      en=1'b0;
		// Wait 100 ns for global reset to finish
		#100;
        en=1'b1;
        i = 3'b000; #10;
        i = 3'b001; #10;
        i = 3'b010; #10;
        i = 3'b011; #10;
        i = 3'b100; #10;
        i = 3'b101; #10;
        i = 3'b110; #10;
        i = 3'b111; 
		  #10;
		en=1'b0;
      #10;		 
    end*/
    initial begin
        $monitor("Time=%0t | i=%b |en=%b| y=%b", $time, i,en, y);
    end
	 initial begin #1000 $finish;
end	 
endmodule
```
# 8x3 priority encoder
## [RTL]
```bash
module priority_encoder(y0,y1,y2,IDLE,d0,d1,d2,d3,d4,d5,d6,d7,en);
input d0,d1,d2,d3,d4,d5,d6,d7,en;
output y0,y1,y2,IDLE;
wire h0,h1,h2,h3,h4,h5,h6,h7y_0,y_1,Y_2;
priority_ckt c1(.IDLE(IDLE),.h0(h0),.h1(h1),.h2(h2),.h3(h3),.h4(h4),.h5(h5),.h6(h6),.h7(h7),.d0(d0),.d1(d1),.d2(d2),.d3(d3),.d4(d4),.d5(d5),.d6(d6),.d7(d7));
binary_decoder b1(.y0(y_0),.y1(y_1),.y2(y_2),.d0(h0),.d1(h1),.d2(h2),.d3(h3),.d4(h4),.d5(h5),.d6(h6),.d7(h7));
assign y0   = en ? y_0 : 1'b0;
assign y1   = en ? y_1 : 1'b0;
assign y2   = en ? y_2 : 1'b0;
endmodule

module priority_ckt(IDLE,h0,h1,h2,h3,h4,h5,h6,h7,d0,d1,d2,d3,d4,d5,d6,d7);
input d0,d1,d2,d3,d4,d5,d6,d7;
//output y0,y1,y2,y3,y4,y5,y6,y7;
output IDLE,h0,h1,h2,h3,h4,h5,h6,h7;
assign h7 = d7;
assign h6 = ~d7 & d6;
assign h5 = ~d7 & ~d6 & d5;
assign h4 = ~d7 & ~d6 & ~d5 & d4;
assign h3 = ~d7 & ~d6 & ~d5 & ~d4 & d3;
assign h2 = ~d7 & ~d6 & ~d5 & ~d4 & ~d3 & d2;
assign h1 = ~d7 & ~d6 & ~d5 & ~d4 & ~d3 & ~d2 & d1;
assign h0 = ~d7 & ~d6 & ~d5 & ~d4 & ~d3 & ~d2 & ~d1 & d0;
assign IDLE =  ~d7 & ~d6 & ~d5 & ~d4 & ~d3 & ~d2 & ~d1 & ~d0;

endmodule
module binary_decoder (y0,y1,y2,d0,d1,d2,d3,d4,d5,d6,d7);
input d0,d1,d2,d3,d4,d5,d6,d7;
wire h0,h1,h2,h3,h4,h5,h6,h7;
output y0,y1,y2;
assign y0=(d7|d5|d3|d1);
assign y1=(d7|d6|d3|d2);
assign y2=(d7|d6|d5|d4);
//assign valid=(d7|d6|d5|d4|d3|d2|d1|d0);
endmodule

//behavioural model
module priority_encoder(
    input  d0, d1, d2, d3, d4, d5, d6, d7,en,
    output reg y0, y1, y2,
    output reg IDLE);
always @(*) begin
    IDLE = 0;
    y0 = 0;
    y1 = 0;
    y2 = 0;
	 if(en==0)
	 begin
	  {y2,y1,y0}=3'b111;
	 end
	  else
	     begin
    //  from highest to lowest priority
    casez ({d7,d6,d5,d4,d3,d2,d1,d0}) //casez
        8'b1???????: begin y2=1; y1=1; y0=1; IDLE=0; 
		  end 
        8'b01??????: begin y2=1; y1=1; y0=0; IDLE=0; 
		  end
        8'b001?????: begin y2=1; y1=0; y0=1; IDLE=0; 
		  end 
        8'b0001????: begin y2=1; y1=0; y0=0; IDLE=0; 
		  end 
        8'b00001???: begin y2=0; y1=1; y0=1; IDLE=0; 
		  end 
        8'b000001??: begin y2=0; y1=1; y0=0; IDLE=0; 
		  end 
        8'b0000001?: begin y2=0; y1=0; y0=1; IDLE=0; 
		  end 
        8'b00000001: begin y2=0; y1=0; y0=0; IDLE=0; 
		  end 
        default: begin
            y2=0; y1=0; y0=0; IDLE=1;
		 end
    endcase
	 end
end
endmodule
```
## [Test bench]
```bash
module priority_encoder_tb;
	// Inputs
	reg d0;
	reg d1;
	reg d2;
	reg d3;
	reg d4;
	reg d5;
	reg d6;
	reg d7;
	reg en;
	// Outputs
	wire y0;
	wire y1;
	wire y2;
	wire IDLE;
	//wire valid;
   integer i,j;
	// Instantiate the Unit Under Test (UUT)
	priority_encoder uut (
		.y0(y0), 
		.y1(y1), 
		.y2(y2), 
      .IDLE(IDLE),		
		.d0(d0), 
		.d1(d1), 
		.d2(d2), 
		.d3(d3), 
		.d4(d4), 
		.d5(d5), 
		.d6(d6), 
		.d7(d7),
		.en(en)
	);
	initial begin
		d0 = 0;
		d1 = 0;
		d2 = 0;
		d3 = 0;
		d4 = 0;
		d5 = 0;
		d6 = 0;
		d7 = 0;
		en = 0;
		#100; 
		  for(i=0;i<256;i=i+1)
		  begin 
		  {d7,d6,d5,d4,d3,d2,d1,d0}=i;
		  for(j=0;j<2;j=j+1)
		  begin 
		  en=j;
		  #10;// Add stimulus here
        end
		  end
	end
	initial begin 
	#10000 $finish;
	end
	initial begin 
	$monitor("|d7=%b,d6=%b,d5=%b,d4=%b,d3=%b,d2=%b,d1=%b,d0=%b,en=%b,y0=%b y1=%b,y2=%b,IDLE=%b",d7,d6,d5,d4,d3,d2,d1,d0,en,y0,y1,y2,IDLE);
	end
endmodule

//structural model
module priority_encoder_tb;
	// Inputs
	reg d0;
	reg d1;
	reg d2;
	reg d3;
	reg d4;
	reg d5;
	reg d6;
	reg d7;
	reg en;
	// Outputs
	wire y0;
	wire y1;
	wire y2;
	wire y_0;
	wire y_1;
	wire y_2;
	wire IDLE;
	//wire valid;
   integer i,j;
	// Instantiate the Unit Under Test (UUT)
	priority_encoder uut (
		.y0(y_0), 
		.y1(y_1), 
		.y2(y_2), 
    .IDLE(IDLE),		
		.d0(d0), 
		.d1(d1), 
		.d2(d2), 
		.d3(d3), 
		.d4(d4), 
		.d5(d5), 
		.d6(d6), 
		.d7(d7),
		.en(en)
	);

	initial begin
		d0 = 0;
		d1 = 0;
		d2 = 0;
		d3 = 0;
		d4 = 0;
		d5 = 0;
		d6 = 0;
		d7 = 0;
		en = 0;
		#100;
		  for(i=0;i<256;i=i+1)
		  begin 
		  {d7,d6,d5,d4,d3,d2,d1,d0}=i;
		  for(j=0;j<2;j=j+1)
		  begin 
		  en=j;
		  #10;// Add stimulus here
        end
		  end  
	end
	assign y0   = en ? y_0 : 1'b0;
   assign y1   = en ? y_1 : 1'b0;
   assign y2   = en ? y_2 : 1'b0;
	initial begin 
	#10000 $finish;
	end
	initial begin 
	$monitor("|d7=%b,d6=%b,d5=%b,d4=%b,d3=%b,d2=%b,d1=%b,d0=%b,en=%b,y0=%b y1=%b,y2=%b,IDLE=%b",d7,d6,d5,d4,d3,d2,d1,d0,en,y0,y1,y2,IDLE);
	end 
endmodule
```
# ALU 8-bit
## [RTL]
```bash
module alu(input [7:0]a_in,b_in,
           input [3:0]command_in,
	   input oe,
	   output [15:0]d_out);
   parameter 	 ADD  = 4'b0000, // Add two 8 bit numbers a and b.
		 INC  = 4'b0001, // Increment a by 1. 
		 SUB  = 4'b0010, // Subtracts b from a.
		 DEC  = 4'b0011, // Decrement a by 1.
		 MUL  = 4'b0100, // Multiply 4 bit numbers a and b.
		 DIV  = 4'b0101, // Divide a by b.
		 SHL  = 4'b0110, // Shift a to left side by 1 bit.
		 SHR  = 4'b0111, // Shift a to right by 1 bit.
		 AND  = 4'b1000, // Logical AND operation
	    OR   = 4'b1001, // Logical OR operation
		 INV  = 4'b1010, // Logical Negation
		 NAND = 4'b1011, // Bitwise NAND
		 NOR  = 4'b1100, // Bitwise NOR
		 XOR  = 4'b1101, // Bitwise XOR
		 XNOR = 4'b1110, // Bitwise XNOR
		 BUF  = 4'b1111; // BUF
   reg  [15:0]out;
   always@(command_in)
      begin
	 case(command_in)
           4'b0000 : out =a_in+b_in;
	  4'b0001 : out =a_in+1;
      4'b0010 : out =b_in-a_in; 
      4'b0011 : out =a_in-1;
	  4'b0100 : out =a_in*b_in;
	  4'b0101 : out =(b_in != 0) ? a_in / b_in : 16'hFFFF;
      4'b0110 : out =a_in<<1;
	  4'b0111 : out =a_in>>1;
	  4'b1000 : out =a_in&b_in;
	  4'b1001 : out =a_in|b_in;
	  4'b1010 : out =~(a_in);
	  4'b1011 : out =~(a_in&b_in);
      4'b1100 : out =~(a_in+b_in);
	  4'b1101 : out =a_in^b_in;
	  4'b1110 : out =~(a_in^b_in);
	  4'b1111 : out =a_in;
	   default :out=16'h0000;
	 endcase
      end
   assign d_out = (oe) ? out : 16'hzzzz;
endmodule
```
## [Test bench]
```bash
//Testbench global variables
   reg [7:0]a,b;
   reg [3:0]command;
   reg enable;
   wire [15:0]out;
   //Variables for iteration of the loops 
   integer m,n,o;
   //Parameter constants used for displaying the strings during operation
   parameter ADD  = 4'b0000, // Add two 8 bit numbers a and b.
  	     INC  = 4'b0001, // Increment a by 1. 
	     SUB  = 4'b0010, // Subtracts b from a.
	     DEC  = 4'b0011, // Decrement a by 1.
	     MUL  = 4'b0100, // Multiply 4 bit numbers a and b.
	     DIV  = 4'b0101, // Divide a by b.
	     SHL  = 4'b0110, // Shift a to left side by 1 bit.
	     SHR  = 4'b0111, // Shift a to right by 1 bit.
	     AND  = 4'b1000, // Logical AND operation
	     OR   = 4'b1001, // Logical OR operation
	     INV  = 4'b1010, // Logical Negation
	     NAND = 4'b1011, // Bitwise NAND
	     NOR  = 4'b1100, // Bitwise NOR
	     XOR  = 4'b1101, // Bitwise XOR
	     XNOR = 4'b1110, // Bitwise XNOR
	     BUF  = 4'b1111; // BUF
   //Internal register for storing the string values
   reg [4*8:0]string_cmd;
   //Step1 : Instantiate the design ALU
	alu DUT (a, b,command,enable,out);
 //Step2 : Write a task named "initialize" to initialize the inputs of DUT
   task initialize;
      begin
         {a,b,command,enable}=0;
      end
   endtask
    task en_oe(input i);
      begin
         enable=i;
      end
   endtask
  task inputs(input [7:0]j,k);
      begin
	 a=j;
	 b=k;
      end
   endtask
   task cmd (input [3:0]l);
      begin
         command=l;
      end
   endtask

   task delay();
      begin
	 #10;
      end
   endtask
   //Process to hold the string values as per the commands.
   always@(command)
      begin
         case (command)
	    ADD    :  string_cmd = "ADD";
	    INC    :  string_cmd = "INC";
	    SUB    :  string_cmd = "SUB";
	    DEC    :  string_cmd = "DEC";
	    MUL    :  string_cmd = "MUL";
	    DIV    :  string_cmd = "DIV";
	    SHL    :  string_cmd = "SHL";
	    SHR    :  string_cmd = "SHR";
	    INV    :  string_cmd = "INV";
	    AND    :  string_cmd = "AND";
	    OR     :  string_cmd = "OR";
	    NAND   :  string_cmd = "NAND";
	    NOR    :  string_cmd = "NOR";
	    XOR    :  string_cmd = "XOR";
	    XNOR   :  string_cmd = "XNOR";
	    BUF    :  string_cmd = "BUF";
	 endcase
      end
   //Process used for generating stimulus by calling tasks & passing values
   initial
      begin
	     initialize;
	     en_oe(1'b1);
	     for(m=0;m<16;m=m+1)
	      begin
	       for(n=0;n<16;n=n+1)
	          begin
	             inputs(m,n);
		          for(o=0;o<16;o=o+1)
		         	begin
			          command=o;
			          delay;
			         end 
	          end
         end 
      //Manual test cases				
		   en_oe(0);
         inputs(8'd20,8'd10);
         cmd(ADD);
         delay;
         en_oe(1); //enable output
         inputs(8'd25,8'd17);
         cmd(ADD);
         delay;  
         $finish;
      end
   initial 
      $monitor("Input oe=%b, a=%b, b=%b, command=%s, Output out=%b",enable,a,b,string_cmd,out);
endmodule
```




