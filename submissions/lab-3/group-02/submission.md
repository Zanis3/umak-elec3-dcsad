# Lab 3 Submission

## About us
- Group number: `2`
- Section: IV-DCSAD
- Client: A barangay health center records system
- VPC CIDR: `192.168.16.0/20`

## Task 1. Equal subnets

New prefix: `22`

|  #  |        CIDR       |     First      |      Last        | Usable  |  First usable  |   Last usable    |
| --- |        ---        |      ---       |       ---        |   ---   |      ---       |       ---        |
|  1  | `192.168.16.0/22` | `192.168.16.0` | `192.168.19.255` | `1,019` | `192.168.16.4` | `192.168.19.254` |
|  2  | `192.168.20.0/22` | `192.168.20.0` | `192.168.23.255` | `1,019` | `192.168.20.4` | `192.168.23.254` |
|  3  | `192.168.24.0/22` | `192.168.24.0` | `192.168.27.255` | `1,019` | `192.168.24.4` | `192.168.27.254` |
|  4  | `192.168.28.0/22` | `192.168.28.0` | `192.168.31.255` | `1,019` | `192.168.28.4` | `192.168.31.254` |

Reserved addresses of subnet 1:

1. `192.168.16.0` — Network address
2. `192.168.16.1` — Reserved for the VPC router
3. `192.168.16.2` — Reserved for DNS
4. `192.168.16.3` — Reserved for future use
5. `192.168.19.255` — Reserved by AWS


Working for subnet 1:

```
1. The original VPC prefix is /20 and the required number of subnets is 4.
2. Because we need to divide the original VPC into 4 equal subnets, we need to calculate the additional bits needed: 2^2 = 4 subnets.
3. We add the 2 additional bits to the original prefix of 20 to get the new prefix: 20 + 2 = /22. We add 2 because 2 bits allow us to create 4 equal subnets (2² = 4).
4. Then, we use the formula 2^(32-prefix) to get the total addresses per subnet: 2^(32-22) = 2^10 = 1,024 addresses.
5. And then for the usable addresses, we subtract 5 from the total addreesses per subnet: 1,024 - 5 = 1,019 usable addresses.
6. For the subnet ranges, a /22 subset advances by 4 in the third octet:
    Subnet 1: 192.168.16.0 – 192.168.19.255
    Subnet 2: 192.168.20.0 – 192.168.23.255
    Subnet 3: 192.168.24.0 – 192.168.27.255
    Subnet 4: 192.168.28.0 – 192.168.31.255
7. And then we determine the usable range of subnet 1:
    First usable address: 192.168.16.4
    Last usable address: 192.168.19.254
```

## Task 2. Subnets of a given size

- 2a. There are 16 subnets of size `/24` that fit inside the VPC.
- 2b. Each `/24` subnet has 251 usable addresses.
- 2c. CIDR: `192.168.25.0/24`. Usable range: `192.168.25.4` – `192.168.25.254`.

Working for 2c:

```
1. The original VPC CIDR is 192.168.16.0/20, and the required subnet size is /24.

2. To determine how many subnets fit inside the VPC, we subtract the original prefix from the required subnet prefix:
    24 - 20 = 4 additional bits.

3. We then calculate the number of subnets using 2^4:
   2^4 = 16 subnets.

4. Since subnet number 1 starts at 192.168.16.0/24, each next /24 subnet increases the third octet by 1.

5. To find subnet number 10, we subtract 1 from the subnet number because subnet numbering starts at 1:
   10 - 1 = 9.

6. We add 9 to the third octet of the starting address:
   16 + 9 = 25.

7. Therefore, subnet number 10 is:
   192.168.25.0/24.

8. A /24 subnet contains:
   2^(32 - 24) = 2^8 = 256 total addresses.

9. We subtract the 5 AWS-reserved addresses to calculate the usable addresses:
   256 - 5 = 251 usable addresses.

10. The first usable address is 4 addresses after the subnet's starting address, while the last usable address is 1 address before the subnet's ending address.

    First usable address: 192.168.25.4
    Last usable address: 192.168.25.254

11. Therefore, the usable range is:
    192.168.25.4 – 192.168.25.254.
```

- 2d. The last subnet is `192.168.31.0/24`

## Task 3. Subnets from host counts

|  Subnet   | Prefix |         CIDR        | Availability Zone |  Usable  |
|   ---     |  ---   |         ---         |        ---        |    ---   |
| private-a |  /24   | `192.168.16.0/24`   |  ap-southeast-1a  |   251    |
| private-b |  /24   | `192.168.17.0/24`   |  ap-southeast-1b  |   251    |
| public-a  |  /25   | `192.168.18.0/25`   |  ap-southeast-1a  |   123    |
| public-b  |  /25   | `192.168.18.128/25` |  ap-southeast-1b  |   123    |

## Task 4. Proposed subnets

|  #  |       Proposal      | Valid or not valid |                                                              Why                                                                                 |
| --- |         ---         |         ---        |                                                              ---                                                                                 |
| P1  | `192.168.16.128/25` | Not Valid          | The subnet is inside the VPC and starts on a valid `/25` boundary, but it overlaps with private-a (`192.168.16.0/24`).                           |
| P2  | `192.168.32.0/22`   | Not Valid          | The subnet is outside the VPC. The VPC ends at `192.168.31.255`, but this subnet starts at `192.168.32.0`.                                       |
| P3  | `192.168.29.0/25`   | Valid              | The subnet is inside the VPC, starts on a valid `/25` boundary, uses an allowed prefix, and does not overlap with any subnet in Task 3.          |
| P4  | `192.168.28.128/29` | Not Valid          | The subnet is inside the VPC and does not overlap with the Task 3 subnets, but `/29` is outside the allowed prefix range of `/16` through `/28`. |
