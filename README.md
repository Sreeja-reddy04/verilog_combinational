# combinational circuits

- 4x1 MUx
- Operators
- 8x3 Decoder
- 8x3 priority encoder 
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
# operators
## [RTL]
```bash

```
## [Test bench]
```bash

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
# 8X3 Decoder
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
