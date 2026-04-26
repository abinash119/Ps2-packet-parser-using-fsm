# Ps2-packet-parser-using-fsm
Verilog based ps2 packet parser using FSM, implemented and tested using vivado.

The PS/2 mouse protocol sends messages that are three bytes long. However, within a continuous byte stream, it's not obvious where messages start and end. The only indication is that the first byte of each three byte message always has bit[3]=1 (but bit[3] of the other two bytes may be 1 or 0 depending on data).
We want a finite state machine that will search for message boundaries when given an input byte stream. The algorithm we'll use is to discard bytes until we see one with bit[3]=1. We then assume that this is byte 1 of a message, and signal the receipt of a message once all 3 bytes have been received (done).
The FSM should signal done in the cycle immediately after the third byte of each message was successfully received.

The design impements a 4 state synchronous Moore FSM with states A, B, C, D. The input condition is in[3] and the output done depends only on the current state.

State behaviour:

STATE=A(idle/start state):
1.reset initializes the FSM to A
2.if in[3]==0 then A if in[3]==1 transition to B

STATE=B:
Unconditional transition to next state C

STATE=C:
Unconditional transition to next state D

STATE=D(done state):
1.Output signal done is asserted as soon as the state==D
2.if in[3]==0 then A(return to idle) if in[3]==1 transition to B(restart sequence)

The model architecture contains 3 blocks
1.Next state logic(combinational block)- It computes the next state from current state and in[3]
Implements the state transition rules
A to B or A (depending on in[3])
B to C (unconditional)
C to D (unconditional)
D to B or A (depending on in[3])

2.State Register(Sequential)- It stores the current state
It gets updated on every posedge of the clock signal
On reset its state is set to A

3.Output logic(Combitional)- It generates output drom current state only, the done signal is asserted only when state==D
