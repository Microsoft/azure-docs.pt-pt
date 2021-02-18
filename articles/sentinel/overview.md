---
title: O que é o Azure Sentinel? | Microsoft Docs
description: Conheça o Azure Sentinel, uma solução de gestão de eventos de informação de segurança nativa em nuvem (SIEM) e orquestração de segurança (SOAR).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570485"
---
# <a name="what-is-azure-sentinel"></a>O que é o Azure Sentinel?

O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de **segurança (SIEM)** e **de orquestração de segurança (SOAR).** O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças. 

Azure Sentinel é a sua visão de olho de pássaros em toda a empresa aliviando o stress de ataques cada vez mais sofisticados, aumentando volumes de alertas e longos períodos de tempo de resolução.

- **Recolha dados à escala de nuvem** em todos os utilizadores, dispositivos, aplicações e infraestruturas, tanto no local como em várias nuvens. 

- **Detete ameaças anteriormente não detetadas** e minimize falsos positivos usando a análise da Microsoft e a inteligência de ameaças incomparáveis. 

- **Investigue ameaças com inteligência artificial** e procure atividades suspeitas em escala, aproveitando anos de trabalho de cibersegurança na Microsoft. 

- **Responda rapidamente a incidentes** com orquestração incorporada e automatização de tarefas comuns.

![Capacidades do núcleo de Azure Sentinel](./media/overview/core-capabilities.png)

Com base na gama completa de serviços Azure existentes, o Azure Sentinel incorpora nativamente fundações comprovadas, como Log Analytics e Logic Apps. O Azure Sentinel enriquece a sua investigação e deteção com IA, e fornece o fluxo de inteligência de ameaça da Microsoft e permite-lhe trazer a sua própria inteligência de ameaça. 

## <a name="connect-to-all-your-data"></a>Conecte-se a todos os seus dados

Para embarcar a Azure Sentinel, primeiro tem de [se ligar às suas fontes de segurança.](connect-data-sources.md) O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e fornecendo integração em tempo real, incluindo soluções Microsoft 365 Defender (anteriormente Microsoft Threat Protection) e 365 fontes da Microsoft, incluindo Office 365, Azure AD, Microsoft Defender for Identity (anteriormente Azure ATP), e Microsoft Cloud App Security, entre outras. Além disso, existem conectores incorporados no ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o formato de evento comum, Syslog ou REST-API para ligar as suas fontes de dados ao Azure Sentinel também. 

![Recoletores de dados](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Livros

Depois de [ligar as suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, pode monitorizar os dados utilizando a integração do Azure Sentinel com os Livros de Trabalho do Azure Monitor, que proporciona versatilidade na criação de livros de trabalho personalizados. Embora os livros de trabalho sejam apresentados de forma diferente no Azure Sentinel, pode ser útil para você ver como [criar relatórios interativos com os livros de trabalho do Monitor Azure](../azure-monitor/visualize/workbooks-overview.md). O Azure Sentinel permite-lhe criar livros de trabalho personalizados através dos seus dados, e também vem com modelos de livros incorporados para lhe permitir obter rapidamente informações sobre os seus dados assim que ligar uma fonte de dados.

![Dashboards](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Análise

Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que tem de rever e investigar, o Azure Sentinel utiliza [analítica para correlacionar alertas em incidentes.](tutorial-detect-threats-built-in.md) **Incidentes** são grupos de alertas relacionados que juntos criam uma possível ameaça que você pode investigar e resolver. Use as regras de correlação incorporadas como está, ou use-as como ponto de partida para construir as suas próprias. O Azure Sentinel também fornece regras de machine learning para mapear o seu comportamento de rede e, em seguida, procurar anomalias em todos os seus recursos. Estas análises ligam os pontos, combinando alertas de baixa fidelidade sobre diferentes entidades em potenciais incidentes de segurança de alta fidelidade.

![Incidentes](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automação de segurança & orquestração

Automatize as suas tarefas comuns e [simplifique a orquestração de segurança com playbooks](tutorial-respond-threats-playbook.md) que se integram com os serviços Azure, bem como com as suas ferramentas existentes. Baseada na base da Azure Logic Apps, a solução de automação e orquestração do Azure Sentinel proporciona uma arquitetura altamente extensível que permite automatização escalável à medida que novas tecnologias e ameaças emergem. Para construir playbooks com Azure Logic Apps, pode escolher entre uma galeria em crescimento de playbooks embutidos. Estes incluem [mais de 200 conectores](../connectors/apis-list.md) para serviços como funções Azure. Os conectores permitem aplicar qualquer lógica personalizada em código, ServiceNow, Jira, Zendesk, pedidos HTTP, Microsoft Teams, Slack, Windows Defender ATP e Cloud App Security.

Por exemplo, se utilizar o sistema de bilhética ServiceNow, pode utilizar as ferramentas fornecidas para utilizar as Aplicações Lógicas Azure para automatizar os seus fluxos de trabalho e abrir um bilhete no ServiceNow sempre que for detetado um determinado evento.

![Manuais de procedimentos](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Investigação

Atualmente em pré-visualização, as ferramentas [de investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam-no a compreender o âmbito e a encontrar a causa principal de uma potencial ameaça à segurança. Você pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica, e aprofundar essa entidade e suas ligações para chegar à causa principal da ameaça. 

![Investigação](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Investigação

Utilize [as poderosas ferramentas de busca e consulta de caça](hunting.md)do Azure Sentinel, baseadas na estrutura do MITRE, que lhe permitem caçar proativamente ameaças de segurança através das fontes de dados da sua organização, antes de um alerta ser desencadeado. Depois de descobrir que consulta de caça fornece informações de alto valor sobre possíveis ataques, também pode criar regras de deteção personalizadas com base na sua consulta, e emergir essas informações como alertas para os seus socorristas de incidentes de segurança. Enquanto caça, você pode criar marcadores para eventos interessantes, permitindo-lhe voltar para eles mais tarde, partilhá-los com outros, e agrupar com outros eventos correlacionados para criar um incidente convincente para a investigação.

![Visão geral da característica de caça](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A comunidade Azure Sentinel é um poderoso recurso para deteção e automação de ameaças. Os nossos analistas de segurança da Microsoft criam constantemente e adicionam novos livros de trabalho, playbooks, consultas de caça e muito mais, publicando-os na comunidade para que possas usar no teu ambiente. Você pode baixar conteúdo de amostras da comunidade privada GitHub [repositório](https://aka.ms/asicommunity) para criar livros de trabalho personalizados, consultas de caça, cadernos e livros de reprodução para Azure Sentinel. 

![Explore a comunidade de utilizadores](./media/overview/community.png)

## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [embarcar os seus dados ao Azure Sentinel](quickstart-onboard.md)e obtenha [visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)