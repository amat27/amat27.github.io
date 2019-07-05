Casual Order

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