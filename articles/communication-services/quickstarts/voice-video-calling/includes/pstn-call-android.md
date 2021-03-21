---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 96f5fc868d4fa090848096174eb6fcdd34ef8388
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "104729486"
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

## <a name="setting-up"></a>Configuração

### <a name="add-pstn-functionality-to-your-app"></a>Adicione a funcionalidade PSTN à sua aplicação

Adicione o `PhoneNumber` tipo à sua aplicação modificando o **MainActivity.java:**


```java
import com.azure.android.communication.common.PhoneNumberIdentifier;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUserIdentifier" or "com.azure.communication.common.client.CommunicationTokenCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Inicie uma ligação para o telefone.

Especifique o número de telefone que adquiriu no seu recurso de Serviços de Comunicação. Isto será usado para iniciar a chamada:

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)

Modificar `startCall()` o manipulador de eventos na .java **MainActivity,** de modo a que lide com chamadas telefónicas:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumberIdentifier callerPhone = new PhoneNumberIdentifier("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.startCall(
                getApplicationContext(),
                new PhoneNumberIdentifier[] {new PhoneNumberIdentifier(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Lance a app e chame o bot de eco

A aplicação pode agora ser lançada utilizando o botão "Run App" na barra de ferramentas (Shift+F10). Pode esboar uma chamada para telefone fornecendo um número de telefone no campo de texto adicionado e clicando no botão **CALL.**
> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)

![Screenshot mostrando a aplicação completa.](../media/android/quickstart-android-call-pstn.png)
