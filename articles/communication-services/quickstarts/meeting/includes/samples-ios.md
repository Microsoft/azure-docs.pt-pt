---
title: Utilização das equipas de serviços de comunicação Azure Incorporadas para iOS
description: Nesta visão geral, você vai aprender a usar a biblioteca Azure Communication Services Teams Embed library para iOS.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0a1dd8f69cb79e42e56ab44981820e31abf204e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803754"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Complete o quickstart para [começar com a adição de Equipas Embed à sua aplicação](../getting-started-with-teams-embed.md)

## <a name="teams-embed-events"></a>Equipas Incorporam Eventos

Adicione a `MeetingUIClientDelegate` sua aula.

```swift
class ViewController: UIViewController, MeetingUIClientDelegate {

    private var meetingClient: MeetingUIClient?
```

Desema estalem `meetingUIClientDelegate` o `self` .

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    meetingClient?.meetingUIClientDelegate = self
}
```

Implementar as `didUpdateCallState` funções e `didUpdateRemoteParticipantCount` as funções.

```swift
    func meetingUIClient(didUpdateCallState callState: CallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticpantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticpantCount)")
    }
```

## <a name="assigning-avatars-for-users"></a>Atribuição de avatares para utilizadores

Adicione a `MeetingUIClientIdentityProviderDelegate` sua aula.

```swift
class ViewController: UIViewController, MeetingUIClientIdentityProviderDelegate {

    private var meetingClient: MeetingUIClient?
```

Desemagre a `MeetingUIClientIdentityProviderDelegate` antes de se juntar à `self` reunião.

```swift
private func joinMeeting() {
    meetingClient?.meetingUIClientIdentityProviderDelegate = self
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")

    meetingClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Mapear cada um `userMri` com o avatar correspondente.

```swift
    func avatarFor(userIdentifier: String, completionHandler: @escaping (UIImage?) -> Void) {
        if (userIdentifier.starts(with: "8:teamsvisitor:")) {
            // Anonymous teams user will start with prefix 8:teamsvistor:
            let image = UIImage (named: "avatarPink")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:orgid:")) {
            // OrgID user will start with prefix 8:orgid:
            let image = UIImage (named: "avatarDoctor")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:acs:")) {
            // ACS user will start with prefix 8:acs:
            let image = UIImage (named: "avatarGreen")
            completionHandler(image!)
        }
        else {
            completionHandler(nil)
        }
}
```