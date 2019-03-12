---
title: Nome do emissor e chave do emissor dos serviços BizTalk | Documentos da Microsoft
description: Saiba como obter o nome do emissor e chave do emissor para o Service Bus ou controlo de acesso (ACS) nos serviços do BizTalk. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 5eac98ec88b960956c9a0931673e67f530aef8da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542187"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Serviços BizTalk: Nome do emissor e chave do emissor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Serviços BizTalk do Azure utiliza o nome de emissor do Service Bus e a chave do emissor e o nome de emissor de controlo de acesso e a chave do emissor. Especificamente:

| Tarefa | O nome do emissor e chave do emissor |
| --- | --- |
| Implementar a aplicação a partir do Visual Studio |Nome de emissor de controlo de acesso e a chave do emissor |
| Configurar o Portal de serviços BizTalk do Azure |Nome de emissor de controlo de acesso e a chave do emissor |
| Criar reencaminhamentos LOB com os serviços de adaptador do BizTalk no Visual Studio |Nome de emissor do Service Bus e a chave do emissor |

Este tópico lista os passos para obter o nome do emissor e chave do emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome de emissor de controlo de acesso e a chave do emissor
O nome de emissor de controlo de acesso e a chave do emissor são utilizados pelo seguinte:

* A sua aplicação do BizTalk Service do Azure criada no Visual Studio: Para implementar com êxito a aplicação de serviço BizTalk no Visual Studio para o Azure, introduza o nome de emissor de controlo de acesso e a chave do emissor. 
* O Portal de serviços BizTalk do Azure: Quando cria um BizTalk Service e abra o Portal de serviços do BizTalk, seu nome de emissor de controlo de acesso e a chave do emissor são automaticamente registrados para as implementações com os mesmos valores de controlo de acesso.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Obter o nome de emissor de controlo de acesso e a chave do emissor

Para usar o ACS para autenticação e obter os valores de nome do emissor e chave do emissor, incluem os passos gerais:

1. Instalar o [cmdlets do Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adicione a sua conta do Azure: `Add-AzureAccount`
3. Devolva o nome da sua subscrição: `get-azuresubscription`
4. Selecione a sua subscrição: `select-azuresubscription <name of your subscription>` 
5. Crie um novo espaço de nomes: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exemplo:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Quando o novo espaço de nomes do ACS é criado (o que pode demorar vários minutos), os valores de nome do emissor e chave do emissor são enumerados na cadeia de ligação: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Para resumir:  
Nome do emissor = SharedSecretIssuer  
Chave do emissor = SharedSecretKey

Em mais de [New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome de emissor do Service Bus e a chave do emissor
Nome de emissor do Service Bus e a chave do emissor são utilizados pelos serviços de adaptador do BizTalk. No seu projeto de serviços do BizTalk no Visual Studio, utilize os serviços de adaptador do BizTalk para ligar a um sistema de linha de negócio (LOB) no local. Para ligar, criar reencaminhamento LOB e introduza os detalhes de sistema LOB. Ao fazer isso, também é introduzir o nome de emissor do Service Bus e a chave do emissor.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para obter o nome de emissor do Service Bus e a chave do emissor
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure **do Service Bus**e selecione o seu espaço de nomes. 
3. Abra o **políticas de acesso partilhado** propriedades, selecione a política e ver o **cadeia de ligação** para o nome e os valores de chave.  

## <a name="next"></a>Seguinte
Tópicos de serviços BizTalk do Azure adicionais:

* [Instalar o SDK dos serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Consultar Também
* [How to: Utilizar o serviço de gestão de ACS para configurar identidades de serviço](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Serviços BizTalk: Programador, básico, Standard e Premium gráfico de edições](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Serviços BizTalk: Aprovisionamento](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Serviços BizTalk: Gráfico de estado de aprovisionamento](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: Cópia de segurança e restauro](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: Throttling](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

