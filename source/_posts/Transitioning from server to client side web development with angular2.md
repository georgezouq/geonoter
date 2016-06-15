# Transitioning from Server to Client Side Web Development with Angular 2

Original:   http://angularshowcase.github.io/ng2-bootstrap-sbadmin/slides/20151110/assets/player/KeynoteDHTMLPlayer.html#0

## Part 1:History

### Brief history of Dynamic Web

  - 1993 : Someone somewhere uses C to customize the HTML devlivered to a brower.Yahoo is born .Perl,ColdFusion,PHP(all via CGL) follow.

  - 1996 : Iframes allow partial page to be loaded via parameterized URL.

  - 1998 : HTML4/DHTML - JS manipulating DOM and dynamic style

  - 2002 : XHR/Ajax opens door from "single page apps".

  - 2006 : JS & CSS frameworks emerge,large apps become hard to maintain.

  - 2010 : MVC(ExtJS,Angular) addresses separation of concerns.Unit testing and TDD emerges and grows.

  - 2013 : Asm/WebAsm-based languages address JS shortcomings.

  - 2015 : First frameworks written in type safe languages

### Net Effect

  - Web finally "Just another native environment".

  - Server does not differentiate by client - Same REST everywhere,server not responsible for view.

  - Web developers avoid politics of fragile server builds,work with tooling and workflows tuned to their productivity instead of some other team's

  - Faster,more reliable web development due to build process and type safety.

### Evolution to GUI




## Part2: Generic Architecture

---------------- Thin vs. Fat Client --------------------

### Thin

  - A thin client wholly relies on the server for client view management.

  - A server session forms the context by which views progress

  - Consider a six-page wizard:each view sequenced and sent by server.

  - Network transaction boundary on every view change.

  - Scaling is difficult with server-based sessions since a cluster node reboot drops the session and in-flight user work.Requires session replication,rerouting requests on the fly,etc.

  - Fat(Standalone) client is one which only communicates changes to model.

  - In out wizard example,user has expectation of losing data if their device reboots.Data that is submitted at end of wizard is data from every page.

  - Network transaction boundary is on reads or writes to model.

  - REST models CRUD via POST,PUT,GET,DELETE.Simple

  - Scaling is simple,any server endpoint can accept transactional requests without reference to previous transactions.

## Authentication (身份认证)

 - Transactional systems are always based on the credentials of a requesting principal.One can't unlock or start a vehicle without a key.The key is the credentials

 - Computers systems are not different.A credential identifies a transactional counterparty.

 - For a human,a credential might be a username and password.For inter-computer communication,we might use a public/private key pair.

 - In any case,we have to decide who is presenting a command before deciding what they can do.

## Authorization (授权)

 - In the example of a vehicle,the key generally grants complete operational access to the vehicle.Some vehicles have "valet keys" with downgraded access

 - Such access levels delineate authorizations or permissions.

 - Now scale permissions to the level of 100k people in a large organization.Example:All members may be granted office access,but each to certain campuses.Only a few members may have access ti payroll,etc.There can easily be hundreds of thousands of permissions once CRUD aspects of each one are captured.

































s
