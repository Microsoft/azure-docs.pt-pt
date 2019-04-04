---
title: Lista dos serviços BizTalk de tarefas de administração e desenvolvimento | Documentos da Microsoft
description: Planejamento e trabalho auxiliam para implementar os serviços BizTalk do Azure.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916245"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administração e a lista de tarefas de desenvolvimento dos serviços BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Introdução
Ao trabalhar com os serviços BizTalk do Microsoft Azure, há vários locais e baseados na nuvem componentes a serem considerados. Para começar a utilizar, considere o seguinte fluxo de processo:  

| Passo | Quem é responsável | Tarefa | Ligações Relacionadas |
| --- | --- | --- | --- |
| 1. |Administrador |Criar subscrição do Microsoft Azure através de uma conta Microsoft ou uma conta institucional |[Portal do Azure](https://portal.azure.com) |
| 2. |Administrador |Criar ou aprovisionar um BizTalk Service. |[Criar um BizTalk Service](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Administrador |Registe-se de que ou implementação de serviços do BizTalk da sua empresa |[Registar e atualizar uma implementação de serviço BizTalk no Portal de serviços do BizTalk](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Administrador |Aplica-se se o aplicativo usa o serviço adaptador do BizTalk para ligar a um sistema de linha de negócio (LOB) no local ou utilizar uma fila ou tópico de destino.  Crie o espaço de nomes do barramento de serviço do Azure. Dê este espaço de nomes, nome de emissor do Service Bus e valores de chave do emissor de barramento de serviço para o desenvolvedor. |[How to: Criar ou modificar um espaço de nomes de serviço do Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [valores obter nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md) |
| 5. |Programador |Instale o SDK e criar o projeto de serviço BizTalk no Visual Studio. |[Instalar o SDK dos serviços BizTalk do Azure](/previous-versions/azure/hh689760(v=azure.100)) e [criar pontos finais de mensagens avançado no Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Programador |Implemente o seu BizTalk Service projeto ao seu serviço BizTalk alojado no Azure. |[Implementar e atualizar o projeto de serviços do BizTalk](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Administrador |Aplica-se de que se estiver a utilizar o EDI.  Pode adicionar parceiros e criar contratos no Portal do Microsoft Azure BizTalk Services. Quando cria um contrato, pode adicionar a ponte de e/ou transformações criadas pelo programador para as definições de contrato. |[Configurar o EDI, AS2 e EDIFACT no Portal dos serviços BizTalk](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Administrador |Usando [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), monitorizar o estado de funcionamento do seu BizTalk Service, incluindo métricas de desempenho. |[Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrador |Com o Portal de serviços BizTalk do Microsoft Azure, gerir os artefactos usados por serviços BizTalk e mensagens de controle que eles são processados pelos ficheiros de ponte. |[Utilizar o Portal dos Serviços BizTalk](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Administrador |Crie um plano de cópia de segurança para criar cópias de segurança do BizTalk Service. |[Continuidade do negócio e recuperação após desastre dos serviços BizTalk](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Próximos Passos
[Tutoriais e Exemplos](/previous-versions/azure/hh689895(v=azure.100))

[Criar o projeto no Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Instalar o SDK dos serviços BizTalk do Azure](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Conceitos
[Criar o projeto no Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 e EDIFACT de mensagens (empresa-empresa)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Outros Recursos
[Adicionar a origem, destino e ponte de pontos finais de mensagens](/previous-versions/azure/hh689877(v=azure.100))  
[Saiba mais e criar mapas de mensagem e transformações](/previous-versions/azure/hh689905(v=azure.100))  
[Usando o BizTalk Adapter Service (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)

