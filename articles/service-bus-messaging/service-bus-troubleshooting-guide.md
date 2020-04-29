---
title: Guia de resolução de problemas para o autocarro do serviço azure [ Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens Azure Service Bus e sugeriu ações a tomar quando a exceção ocorre.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887778"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de resolução de problemas para ônibus de serviço Azure
Este artigo fornece dicas e recomendações de resolução de problemas para alguns problemas que você pode ver ao usar o Ônibus de Serviço Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou timeout
Os seguintes passos podem ajudá-lo com problemas de conectividade/certificado/timeout para todos os serviços em *.servicebus.windows.net. 

- Navegue para ou [paraque.](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` Ajuda a verificar se tem problemas de filtragem IP ou de rede virtual ou cadeia de certificados (mais comuns quando se utiliza java SDK).

    Um exemplo de mensagem bem sucedida:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Um exemplo de mensagem de erro de falha:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Execute o seguinte comando para verificar se alguma porta está bloqueada na firewall. As portas utilizadas são 443 (HTTPS), 5671 (AMQP) e 9354 (Net Messaging/SBMP). Dependendo da biblioteca que utiliza, outras portas também são utilizadas. Aqui está o comando da amostra que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Em Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas de conectividade intermitentes, faça o seguinte comando para verificar se existem pacotes caídos. Este comando tentará estabelecer 25 ligações TCP diferentes a cada 1 segundo com o serviço. Em seguida, pode verificar quantos deles conseguiram/falharam e também ver a latência da ligação tCP. Pode descarregar `psping` a ferramenta a partir [daqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Pode utilizar comandos equivalentes se estiver a `tnc` `ping`utilizar outras ferramentas, tais como , e assim por diante. 
- Obtenha um rastreio de rede se os passos anteriores não ajudarem e analisá-lo usando ferramentas como [wireshark](https://www.wireshark.org/). Contacte o Suporte da [Microsoft](https://support.microsoft.com/) se necessário. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemas que podem ocorrer com atualizações/reinícios de serviço
As atualizações e reinícios do serviço backend podem causar o seguinte impacto nas suas aplicações:

- Os pedidos podem ser momentaneamente estrangulados.
- Pode haver uma queda nas mensagens/pedidos de entrada.
- O ficheiro de registo pode conter mensagens de erro.
- As aplicações podem ser desligadas do serviço por alguns segundos.

Se o código de aplicação utilizar o SDK, a política de retry já está incorporada e ativa. A aplicação reconectar-se-á sem impacto significativo na aplicação/fluxo de trabalho.

## <a name="unauthorized-access-send-claims-are-required"></a>Acesso não autorizado: Pedidos de envio são necessários
Pode ver este erro ao tentar aceder a um tópico de ônibus de serviço do Estúdio Visual num computador no local usando uma identidade gerida atribuída pelo utilizador com permissões de envio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Para resolver este erro, instale a biblioteca [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Para mais informações, consulte a autenticação do [desenvolvimento local.](..\key-vault\service-to-service-authentication.md#local-development-authentication) 

Para aprender a atribuir permissões a papéis, consulte [Authenticate uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Service Bus.](service-bus-managed-service-identity.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Exceções ao Gestor de Recursos Azure.](service-bus-resource-manager-exceptions.md) Ele lista exceções geradas ao interagir com o Azure Service Bus usando o Gestor de Recursos Azure (através de modelos ou chamadas diretas).
- [Exceções de mensagens.](service-bus-messaging-exceptions.md) Fornece uma lista de exceções geradas pela .NET Framework for Azure Service Bus. 

