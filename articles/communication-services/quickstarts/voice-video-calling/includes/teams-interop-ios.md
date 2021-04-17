---
title: Quickstart - Junte-se a uma reunião de equipas a partir de uma aplicação para iOS
description: Neste tutorial, você aprende a se juntar a uma reunião de equipas usando os Serviços de Comunicação Azure Chamando SDK para iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 363799cee5d66b718bb8ba06f4afd442add15148
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564548"
---
Neste arranque rápido, você vai aprender a se juntar a uma reunião de equipas usando os Serviços de Comunicação Azure chamando SDK para iOS.

## <a name="prerequisites"></a>Pré-requisitos

- Um serviço de comunicação em [funcionamento chamando a aplicação iOS.](../getting-started-with-calling.md)
- Uma [colocação de equipas.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Adicione os controlos de UI das equipas e ative os controlos de UI das equipas

Substitua o código no ContentView.swift por seguinte corte. A caixa de texto será usada para entrar no contexto de reunião das Equipas e o botão será utilizado para se juntar à reunião especificada:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Obtenha o link de reunião das equipas

O link de encontro das equipas pode ser recuperado usando APIs de gráfico. Isto é detalhado na [documentação do Gráfico.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Os Serviços de Comunicação Que Ligam a SDK aceitam uma ligação completa de reuniões de equipas. Este link é devolvido como parte do `onlineMeeting` recurso, acessível sob a [ `joinWebUrl` propriedade.](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Também pode obter as informações necessárias da reunião do URL do Encontro de **Reuniões** na reunião das Equipas.

## <a name="launch-the-app-and-join-teams-meeting"></a>Lance a app e junte-se à reunião de Equipas

Pode construir e executar a sua aplicação no simulador iOS selecionando **Product**  >  **Run** ou utilizando o atalho de teclado (&#8984;-R).

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Screenshot mostrando a aplicação completa.":::

Insira o contexto das Equipas na caixa de texto e pressione o Encontro de Equipas de *Adesão* para se juntar à reunião das Equipas a partir da sua aplicação de Serviços de Comunicação.
