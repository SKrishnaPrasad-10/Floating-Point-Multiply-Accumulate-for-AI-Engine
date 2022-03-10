module multiplier(inp1,inp2,out,M,E,overflow,underflow,done);
input [15:0] inp1;
input [15:0] inp2;
output reg[15:0] out;
output overflow,underflow;
output reg done;
output reg [4:0]E;
reg [10:0] M1,M2,z_m1;
reg [9:0] M_1;
reg [4:0] E1,E2,z_e1;
reg [4:0] E_1;
reg S1,S2,S;
output reg [11:0] M;
 
reg [21:0]product;
reg guard,round_bit,sticky;
reg [15:0] z;
reg [15:0] last_inp1,last_inp2;


always @(inp1, inp2)
begin
      done=0;
//UNPACK
		if(inp1 == 0 || inp2 == 0) begin
			 out = 0;
			end
		else begin
		 M1 ={1'b1,inp1[9:0]};
		 E1 =inp1[14:10];
		 S1 =inp1[15];
		 M2 ={1'b1,inp2[9:0]};
		 E2 =inp2[14:10];
		 S2 =inp2[15];
		
		if ($signed(E1) == -31) begin
            	 E1 = -30;
          	end else begin
          	end
          	if ($signed(E2) == -31) begin
            	 E2 = -30;
          	end else begin
          	end

        		 S = S1 ^ S2;
        		 E = (E1 + E2)-4'd15;
        		 product = M1 * M2;
			 out = product;
 //NORMALISE AND ROUNDOFF    
		 M=product[21:10];
		 out = E;
		 guard = product[10];
		 round_bit = product[9];
        	 sticky = (product[8:0] != 0);
        	if (M[10] == 0) begin
		case(M)
			11'bx1xxxxxxxxx : begin z_m1=M<<1; z_e1=E-1; end
			11'bxx1xxxxxxxx : begin z_m1=M<<2;  z_e1=E-2; end
			11'bxxx1xxxxxxx : begin z_m1=M<<3;  z_e1=E-3; end
			11'bxxxx1xxxxxx : begin z_m1=M<<4;  z_e1=E-4; end
			11'bxxxxx1xxxxx : begin  z_m1=M<<5;  z_e1=E-5; end
			11'bxxxxxx1xxxx : begin  z_m1=M<<6;  z_e1=E-6; end
			11'bxxxxxxx1xxx : begin  z_m1=M<<7;  z_e1=E-7; end
			11'bxxxxxxxx1xx : begin  z_m1=M<<8;  z_e1=E-8; end
			11'bxxxxxxxxx1x : begin  z_m1=M<<9;  z_e1=E-9; end
			11'bxxxxxxxxxx1 : begin  z_m1=M<<10;  z_e1=E-10; end
		endcase
		 M=z_m1;
		 E=z_e1;		
        	end 
		else begin
        	end
//PACK
	 M_1=M[9:0];
	 E_1=E[4:0];
	 z={S,E_1,M_1};
      out = z;
    	   
end
done =1;
end
		
endmodule

