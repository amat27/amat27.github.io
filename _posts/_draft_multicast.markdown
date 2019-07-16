Snapshot

Starter:
- P_i records start
- P_i create [marker]
- P_i multicast [marker] (N-1) copies
- Start recording the incoming messages

Others:
- P_i recieve [marker] from C_ki
    - if first time
        - P_i records its state
        - P_i marks C_ki empty
        - P_i multicast [marker] (N-1) copies
        - P_i records incoming messages on (N-2) channels
    - else
        - Mark C_ki's state as all the messages after recoding started

FIFO Order Multicast

P_i [1..N], where
- P_i[j] is the last message P_i recieved from j
- P_i[i] is the last message P_i sent out

- When P_i sends a multicast message
    - P_i[i]++
    - Sends P_i[i] with the message

- When P_i recieve from P_j with sequence S
    - if S == P_i[j] + 1
        - deliever the message, set P_i[j] = S
    - else
        buffer message


Causal Order Multicast

P_i[1..N]
P_i[j] is the latest sequence P_i received from P_j

- When P_i sends a multicast message
    - P_i[j] += 1
    - multicast P_i[1..N] along with the message
- When P_i recieves a message from P_j
    - if P_j[j] = P_i[j] + 1
        - for every k != j, P_j[k] <= P_i[k]
        - Delever the messsage
    - else delay the message