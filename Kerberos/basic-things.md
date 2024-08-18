# Understand how kerberos work


## What is Kerberos:
Kerberos is a passwordless computer network security authentication protocol that was created by MIT to help solve network security problems

Instead of using traditional passwords, Kerberos employs strong, time-limited secret-key cryptography, multiple secret keys, and a third-party service to authenticate client-server applications and user identities

Kerberos is a complex process on the backend but offers the user an almost frictionless experience on the front end (check the example bellow)

## What It Looks Like to the USERS

### The problem
Today’s distributed workforce uses multiple devices (laptops, smartphones, tablets) 
and requires anytime-and-anywhere access to their organization’s systems and third-party apps. 
Since each resource requires users to verify their identity, 
it could be very time-consuming if employees had to sign into each one.

### Things solve when use kerberos
When Kerberos is used, employees are automatically authenticated to access all network resources and many third-party 
systems with SSO. This means that once they can sign onto a device, 
they can access multiple resources from the same device without authenticating again 
(unless their location or some other factor has changed)

### An example:
- Josh sits down at his desk exactly at 9 a.m. He’s concerned about being late for his 9 a.m. Zoom call, so he quickly logs onto his computer.

- Because his company uses Kerberos, he doesn’t have to sign into Zoom. He can just click on the Zoom link, and he is automatically authenticated and can enter the meeting.

- During the meeting, Josh needs to access his project management software to present some information. He clicks on the application, and it opens instantly without making the other meeting guests wait while he signs in.

- After the meeting, he has to access sensitive information in a secure database. When he clicks on the link to enter, he is immediately authenticated through Kerberos and does not have to enter separate credentials.

-> With Kerberos, Josh doesn't need to know what’s going on behind the scenes.
He just needs to know that his identity is securely shared across different systems on his network. 
He appreciates the time he saves by not having to sign in to each application.

## How does kerberos work?

### Compare kerberos ticket with movie ticket

- When a person buys a movie ticket, they buy it for a particular movie, at a particular time, on a particular day

- The same is true for a kerberos's authentication token: It can only be used for a certain resource, for a certain period of time, on a certain day. 
When the original token expires, a refresh token is sent in its place to maintain SSO (Single sign-on)

### Main parts of kerberos:
Kerberos has 3 Main Parts:
- User/client             
- Key distribution center (KDC): include Authentication server (AS) and Ticket-granting server (TGS)       
- Service/application

### How Entities Communicate Within Kerberos
![How Entities Communicate](./images/comunication.png)



## Related documentation:

https://blog.netwrix.com/what-is-kerberos/

https://www.pingidentity.com/en/resources/identity-fundamentals/authentication-authorization-protocols/kerberos.html

