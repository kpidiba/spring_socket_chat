# spring_socket_chat

Un WebSocket dans le contexte de Spring Boot est une technologie qui permet une communication bidirectionnelle en temps réel entre un client (souvent un navigateur web) et un serveur. Contrairement aux requêtes HTTP classiques, où le client envoie une requête et attend une réponse du serveur, les WebSockets permettent une communication continue entre les deux parties.

Dans une application Spring Boot, l'utilisation de WebSockets est souvent associée à la communication en temps réel, aux mises à jour en direct et aux notifications. Vous pouvez utiliser les WebSockets pour transmettre des données entre le navigateur et le serveur sans avoir besoin de recharger complètement la page ou d'effectuer des requêtes constantes.

Voici comment fonctionne généralement un WebSocket dans une application Spring Boot :

1. **Établissement de la Connexion :** Lorsqu'un client (comme un navigateur web) souhaite établir une connexion WebSocket avec le serveur, il envoie une requête spéciale dite de "poignée de main" (handshake) au serveur.

2. **Établissement du Canal de Communication :** Une fois que le serveur accepte la demande de poignée de main, une connexion WebSocket est établie entre le client et le serveur. Cette connexion reste ouverte, permettant aux deux parties d'envoyer et de recevoir des messages à tout moment.

3. **Échange de Messages :** Les clients et le serveur peuvent maintenant s'envoyer des messages à travers le canal WebSocket. Les messages peuvent être de n'importe quel format (texte, JSON, etc.).

4. **Gestion des Événements :** Le serveur peut diffuser des messages à tous les clients connectés (broadcast) ou envoyer des messages spécifiques à des clients spécifiques (point à point). Cela permet de créer des fonctionnalités en temps réel telles que les chats en direct, les mises à jour automatiques, les notifications instantanées, etc.

L'utilisation des WebSockets dans Spring Boot est souvent réalisée en conjonction avec Spring WebSockets ou Spring WebFlux (si vous utilisez la programmation réactive). Les bibliothèques comme STOMP (Simple Text Oriented Messaging Protocol) peuvent également être utilisées pour gérer la communication sur les canaux WebSocket.



### CONFIGURATION OF WEBSOCKET

In Spring Framework's WebSocket support, the `configureMessageBroker` method is used to configure the message broker that facilitates the communication between clients (such as browsers) and the server through WebSocket connections. The method takes a `MessageBrokerRegistry` parameter, which allows you to define various settings related to message handling and routing.

Here's what the individual configurations in your given example mean:

1. **`config.enableSimpleBroker("/topic")`:** This line of code enables a simple message broker. A message broker is responsible for distributing messages from the server to various clients that have subscribed to specific topics. In this case, you're enabling a broker that supports the "/topic" destination prefix. It means that messages sent to destinations starting with "/topic" will be broadcasted to all clients subscribed to that topic. For example, if a message is sent to "/topic/messages", all clients subscribing to "/topic/messages" will receive that message.

2. **`config.setApplicationDestinationPrefixes("/app")`:** This line of code sets the application destination prefix. Application destinations are the endpoints to which clients can send messages. By setting this prefix, you define a base path for the destinations that clients can send messages to. For example, if the client wants to send a message to the server, it would send it to "/app/some-destination".

Putting it all together, the `configureMessageBroker` method in your example is configuring the WebSocket message broker with the following behaviors:

- Messages sent to destinations starting with "/topic" will be broadcasted to all subscribed clients.
- Clients can send messages to destinations starting with "/app" to communicate with the server.

This configuration is a common starting point for setting up a basic WebSocket communication pattern. However, it's worth noting that there are more advanced configurations and options available for message routing, including more complex broker configurations and handling direct messaging between clients. The specific configurations you choose will depend on the needs of your application.



### EVENT LISTENER CONFIG

On ecoute tous les sessions qui sont connecte a l' event 

```gcode
@MessageMapping("/chat.sendMessage")
    @SendTo("/topic/public")
    public ChatMessage sendMessage(@Payload ChatMessage chatMessage){
        return chatMessage;
    }
```

Ce code est un exemple de gestion d'une communication en temps réel via WebSockets dans une application Spring Boot utilisant Spring WebSockets et la messagerie STOMP (Simple Text Oriented Messaging Protocol). Voici ce que fait ce code en détail :

1. **`@MessageMapping("/chat.sendMessage")` :**
   
   - Cette annotation indique que la méthode qui suit gère les messages entrants avec la destination "/chat.sendMessage".
   - Lorsqu'un client envoie un message à cette destination via WebSocket, cette méthode sera invoquée pour traiter ce message.

2. **`@SendTo("/topic/public")` :**
   
   - Cette annotation indique que la méthode envoie la réponse à la destination "/topic/public".
   - Une fois que la méthode `sendMessage` termine son traitement, le message de réponse sera envoyé à tous les clients qui sont abonnés à la destination "/topic/public".

3. **`public ChatMessage sendMessage(@Payload ChatMessage chatMessage)` :**
   
   - Cette méthode prend en paramètre un objet `ChatMessage` en tant que message reçu du client.
   - Le paramètre `@Payload` indique que le contenu du message doit être extrait et associé à l'objet `chatMessage`.
   - La méthode renvoie ensuite l'objet `ChatMessage`.



Ces deux blocs de code font partie d'une application de chat en temps réel utilisant WebSockets dans le contexte de Spring Boot et Spring WebSockets. Ils sont responsables de la gestion des messages envoyés par les clients et de l'ajout de nouveaux utilisateurs au chat. Voici une explication détaillée de chaque bloc de code :

1. **Gestion de l'envoi de messages :**
   
   java
- `@MessageMapping("/chat.sendMessage") @SendTo("/topic/public") public ChatMessage sendMessage(@Payload ChatMessage chatMessage) {     return chatMessage; }`
  
  - L'annotation `@MessageMapping` indique que cette méthode gère les messages entrants avec la destination "/chat.sendMessage".
  - Lorsqu'un client envoie un message à cette destination via WebSocket, cette méthode est appelée pour traiter le message.
  - La méthode prend un objet `ChatMessage` en tant que paramètre, encapsulant le message envoyé par le client.
  - La méthode renvoie l'objet `ChatMessage` reçu, qui est ensuite diffusé à tous les clients abonnés à la destination "/topic/public". Cela permet aux messages d'être affichés dans la zone de discussion commune du chat.

- **Gestion de l'ajout d'utilisateurs :**
  
  java

`@MessageMapping("/chat.addUser") @SendTo("/topic/public") public ChatMessage addUser(@Payload ChatMessage chatMessage, SimpMessageHeaderAccessor headerAccessor) {     // Ajouter le nom d'utilisateur dans la session WebSocket     headerAccessor.getSessionAttributes().put("username", chatMessage.getSender());     return chatMessage; }`

- De manière similaire, l'annotation `@MessageMapping` indique que cette méthode gère les messages entrants avec la destination "/chat.addUser".
- La méthode prend un objet `ChatMessage` en tant que paramètre, contenant le nom de l'utilisateur nouvellement ajouté.
- La classe `SimpMessageHeaderAccessor` permet d'accéder aux attributs de la session WebSocket.
- Dans cette méthode, le nom d'utilisateur est extrait de l'objet `chatMessage` et ajouté comme attribut de session avec la clé "username".
- Tout comme pour la première méthode, le message est ensuite renvoyé et diffusé à tous les clients abonnés à "/topic/public".



### IMPORT LIBRARIE

```js
https://cdnjs.cloudflare.com/ajax/libs/sockjs-client/1.6.1/sockjs.min.js
```

It's the minimized version of the SockJS client library. SockJS is a JavaScript library that provides a cross-browser WebSocket-like  interface for creating and using WebSockets with fallback options for browsers that do not support WebSockets or have restrictions on their  use.

```js
https://cdnjs.cloudflare.com/ajax/libs/stomp.js/2.3.3/stomp.min.js
```

It's minimized version of the STOMP (Simple Text Oriented  Messaging Protocol) JavaScript library. STOMP is a messaging protocol  that is often used in combination with WebSockets to facilitate  communication between clients and servers in real-time applications. It  provides a way for clients and servers to exchange messages in a 
structured and efficient manner.
