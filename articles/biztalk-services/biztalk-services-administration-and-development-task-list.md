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
ms.openlocfilehash: baee8c8e931a18c1d72b52c6141c29cba98f9c53
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096406"
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
| 2. |Administrador |Criar ou aprovisionar um BizTalk Service. |[Criar um Serviço BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Administrador |Registe-se de que ou implementação de serviços do BizTalk da sua empresa |[Registar e atualizar uma implementação de serviço BizTalk no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administrador |Aplica-se se o aplicativo usa o serviço adaptador do BizTalk para ligar a um sistema de linha de negócio (LOB) no local ou utilizar uma fila ou tópico de destino.  Crie o espaço de nomes do barramento de serviço do Azure. Dê este espaço de nomes, nome de emissor do Service Bus e valores de chave do emissor de barramento de serviço para o desenvolvedor. |[How to: Criar ou modificar um espaço de nomes de serviço do Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [valores obter nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md) |
| 5. |Programador |Instale o SDK e criar o projeto de serviço BizTalk no Visual Studio. |[Instalar o SDK dos serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [criar pontos finais de mensagens avançado no Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Programador |Implemente o seu BizTalk Service projeto ao seu serviço BizTalk alojado no Azure. |[Implementar e atualizar o projeto de serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administrador |Aplica-se de que se estiver a utilizar o EDI.  Pode adicionar parceiros e criar contratos no Portal do Microsoft Azure BizTalk Services. Quando cria um contrato, pode adicionar a ponte de e/ou transformações criadas pelo programador para as definições de contrato. |[Configurar o EDI, AS2 e EDIFACT no Portal dos serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administrador |Usando [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), monitorizar o estado de funcionamento do seu BizTalk Service, incluindo métricas de desempenho. |[Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrador |Com o Portal de serviços BizTalk do Microsoft Azure, gerir os artefactos usados por serviços BizTalk e mensagens de controle que eles são processados pelos ficheiros de ponte. |[Com o Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administrador |Crie um plano de cópia de segurança para criar cópias de segurança do BizTalk Service. |[Continuidade do negócio e recuperação após desastre dos serviços BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Próximos Passos
[Tutoriais e exemplos](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalar o SDK dos serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceitos
[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 e EDIFACT de mensagens (empresa-empresa)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Outros Recursos
[Adicionar a origem, destino e ponte de pontos finais de mensagens](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Saiba mais e criar mapas de mensagem e transformações](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Usando o BizTalk Adapter Service (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)

