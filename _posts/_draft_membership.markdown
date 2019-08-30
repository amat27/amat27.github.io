Crash-stop model

FLP impossibility- 
 "there does not exist a (deterministic) algorithm for the consensus problem in an asynchronous system subject to failures, even if messages can never be lost, at most one process may fail, and it can only fail by crashing (stopping executing)".

Complete list
virtual synchrony

Almost-complete list (weakly consistent)
Gossip-style, SWIM

Partial-random list
SCAMP, T-MAN, Cyclon

Two parts:
- Falure detection
- Dissemination

Two properties:
- Completeness: Each failure is detected
- Accuracy: There is no mistaken detection

Gossip-Style Failure Detection:

Each process saving a membership list with
 - Address: the process id
 - Heartbeat Counter: the sequence number of the heartbeat
 - Time: local time of last receiving a heartbeat

Each process gossips their membership list with a randomly chosen node
On recipient, the local membership list is updated row by row:
 - if the heartbeat seq is higher then local record, update the heartbeat counter, and set the time to now 

 If the heartbeat has not increased for more than T_fail seconds, the member is considered failed
 After T_cleanup seconds, it will delete the member from the list