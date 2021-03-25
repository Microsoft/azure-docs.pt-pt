---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 43e3463a3284f57825073888146b38fa14cbf5d3
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109046"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone adquirido em recursos dos Serviços de Comunicação. [como obter um número de telefone.](../../telephony-sms/get-phone-number.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Complete o quickstart para [começar com a adição de chamadas à sua aplicação](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.
- Pode construir e executar a sua aplicação com os Serviços de Comunicação Azure chamando a SDK para iOS:

## <a name="setting-up"></a>Configuração

## <a name="start-a-call-to-phone"></a>Inicie uma ligação para o telefone.

Especifique o número de telefone adquirido no recurso Serviços de Comunicação, que será usado para iniciar a chamada:
> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)

Modificar `startCall` o manipulador de eventos que será executado quando o botão *'Chamada's iniciar* é premido:

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.startCall([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

Pode construir e executar a sua aplicação no simulador iOS selecionando **Product**  >  **Run** ou utilizando o atalho de teclado (&#8984;-R).

![Olhar final e sensação da app de arranque rápido](../media/ios/quick-start-make-call.png)

Pode esboçar uma chamada para telefone fornecendo um número de telefone no campo de texto adicionado e clicando no botão **'Chamada'.**
> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)

> [!NOTE]
> A primeira vez que fizer uma chamada, o sistema irá pedir-lhe acesso ao microfone. Numa aplicação de produção, deve utilizar a `AVAudioSession` API [verificar o estado da permissão](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e atualizar graciosamente o comportamento da sua aplicação quando a permissão não for concedida.
