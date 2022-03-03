# <span id='top'>02-11 Logging</span>

[[Logging]](#Logging)  
[[]](#)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

├──
│ㅤㅤ├──
│ㅤㅤ└──
├──
│ㅤㅤ├──
│ㅤㅤ└──
├──
├──
│  
├──
└──

## <span id='Logging'>Logging </span>

[[Top]](#top)

👈

<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Check `logs`.

        $ kubectl logs forest-1

            [2022-03-03 23:32:39,662] INFO in event-simulator: USER3 logged out
            [2022-03-03 23:32:40,663] INFO in event-simulator: USER1 is viewing page1
            [2022-03-03 23:32:41,665] INFO in event-simulator: USER3 is viewing page3
            [2022-03-03 23:32:42,666] INFO in event-simulator: USER2 logged out
            [2022-03-03 23:32:43,667] INFO in event-simulator: USER2 is viewing page1
            [2022-03-03 23:32:44,669] WARNING in event-simulator: USER5 👈 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.

Or, alternatively, `grep` only the lines you want to see.

        $ kubectl logs forest-1 | grep -i user5
            [2022-03-03 23:32:44,669] WARNING in event-simulator: USER5 👈 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
            [2022-03-03 23:32:49,674] WARNING in event-simulator: USER5 👈 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.

2.  By checking logs, figure out the cause of user complaint (cannot place a purchase order).

        $ kubectl logs forest-2 birch-garden

        Login as the account is locked due to MANY FAILED ATTEMPTS.
        [2022-03-03 23:45:15,356] WARNING in event-simulator: USER30 Order failed 👈 as the item is OUT OF STOCK.
        [2022-03-03 23:45:20,362] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
        [2022-03-03 23:45:23,365] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK. 👈
        [2022-03-03 23:45:25,368] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.

### `Logs` example

        [2022-03-03 23:44:45,287] INFO in event-simulator: USER3 is viewing page3 👈
        [2022-03-03 23:44:46,288] INFO in event-simulator: USER4 is viewing page2
        [2022-03-03 23:44:47,290] INFO in event-simulator: USER4 logged in 👈
        [2022-03-03 23:44:47,290] INFO in event-simulator: USER4 logged out 👈
        [2022-03-03 23:44:48,291] INFO in event-simulator: USER3 is viewing page3
        [2022-03-03 23:44:49,293] INFO in event-simulator: USER3 is viewing page3
        [2022-03-03 23:44:50,293] WARNING in event-simulator: USER5 Failed 👈 to Login as the account is locked due to MANY FAILED ATTEMPTS.

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
