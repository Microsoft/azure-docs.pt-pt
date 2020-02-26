---
title: O que é Azure Sentinel?. Microsoft Docs
description: Conheça o Azure Sentinel, as suas principais capacidades e como funciona.
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
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581723"
---
# <a name="what-is-azure-sentinel"></a>O que é o Azure Sentinel?

O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de **segurança (SIEM)** escalável, nativa da nuvem, e uma solução automatizada de resposta automatizada de orquestração de **segurança (SOAR).** O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças. 

Azure Sentinel é a sua visão de pássaros em toda a empresa aliviando o stress de ataques cada vez mais sofisticados, aumentando volumes de alertas e longos prazos de resolução.

- **Recolher dados à escala** de nuvem em todos os utilizadores, dispositivos, aplicações e infraestruturas, tanto no local como em várias nuvens. 

- **Detete ameaças anteriormente não detetadas**e minimize falsos positivos usando a análise da Microsoft e inteligência de ameaça incomparável. 

- **Investigue ameaças com inteligência artificial**e procure atividades suspeitas em escala, aproveitando anos de trabalho de cibersegurança na Microsoft. 

- **Responda rapidamente a incidentes** com orquestração incorporada e automatização de tarefas comuns.

![Capacidades centrais do Azure Sentinel](./media/overview/core-capabilities.png)

Com base em toda a gama de serviços Azure existentes, o Azure Sentinel incorpora fundações comprovadas, como log analytics, e Aplicações Lógicas. O Azure Sentinel enriquece a sua investigação e deteção com IA, e fornece o fluxo de inteligência de ameaça da Microsoft e permite-lhe trazer a sua própria inteligência de ameaça. 

## <a name="connect-to-all-your-data"></a>Ligue-se a todos os seus dados

Para embarcar no Azure Sentinel, primeiro precisa de [se ligar às suas fontes](connect-data-sources.md)de segurança. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e proporcionando integração em tempo real, incluindo soluções de Proteção contra Ameaças da Microsoft, e fontes Microsoft 365, incluindo Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security, e muito mais. Além disso, existem conectores incorporados para o ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o formato comum do evento, o Syslog ou o REST-API para ligar as suas fontes de dados ao Azure Sentinel.  

![Colecionadores de dados](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Livros

Depois de [ligar as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pode monitorizar os dados utilizando a integração do Azure Sentinel com os Livros de Trabalho do Monitor Azure, que proporciona versatilidade na criação de livros personalizados. Embora os livros de trabalho sejam exibidos de forma diferente no Azure Sentinel, pode ser útil para si ver como [criar relatórios interativos com livros](../azure-monitor/app/usage-workbooks.md)de trabalho do Monitor Azure . O Azure Sentinel permite-lhe criar livros personalizados através dos seus dados, e também vem com modelos de livro incorporados para permitir-lhe obter rapidamente informações sobre os seus dados assim que ligar uma fonte de dados.

![Dashboards](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Análise

Para o ajudar a reduzir o ruído e minimizar o número de alertas que tem de rever e investigar, o Azure Sentinel utiliza [analíticos para correlacionar os alertas em incidentes](tutorial-detect-threats-built-in.md). **Incidentes** são grupos de alertas relacionados que juntos criam uma ameaça possível que você pode investigar e resolver. Use as regras de correlação incorporadas como está, ou use-as como ponto de partida para construir as suas próprias. O Azure Sentinel também fornece regras de aprendizagem automática para mapear o comportamento da sua rede e, em seguida, procurar anomalias em todos os seus recursos. Estas análises ligam os pontos, combinando alertas de baixa fidelidade sobre diferentes entidades em potenciais incidentes de segurança de alta fidelidade.

![Incidentes](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automação e orquestração de segurança

Automatizar as suas tarefas comuns e [simplificar a orquestração de segurança com playbooks](tutorial-respond-threats-playbook.md) que se integram com os serviços Azure, bem como as suas ferramentas existentes. Construída com base na Azure Logic Apps, a solução de automação e orquestração do Azure Sentinel fornece uma arquitetura altamente extensível que permite a automação escalável à medida que novas tecnologias e ameaças emergem. Para construir playbooks com Apps Lógicas Azure, você pode escolher entre uma galeria crescente de playbooks embutidos. Estes incluem [mais de 200 conectores](https://docs.microsoft.com/azure/connectors/apis-list) para serviços como funções Azure. Os conectores permitem aplicar qualquer lógica personalizada em código, ServiceNow, Jira, Zendesk, PEDIDOS HTTP, Microsoft Teams, Slack, Windows Defender ATP e Cloud App Security.

Por exemplo, se utilizar o sistema de bilhética ServiceNow, pode utilizar as ferramentas fornecidas para utilizar as Aplicações Lógicas Azure para automatizar os seus fluxos de trabalho e abrir um bilhete no ServiceNow sempre que um determinado evento for detetado.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Investigação

Atualmente em pré-visualização, as ferramentas de [investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam-no a compreender o âmbito e a encontrar a causa principal de uma potencial ameaça à segurança. Pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica, e aprofundar essa entidade e as suas ligações para chegar à causa principal da ameaça. 

![Investigação](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Investigação

Utilize [as poderosas ferramentas](hunting.md)de busca e consulta de caça do Azure Sentinel, baseadas na estrutura do MITRE, que lhe permitem caçar proativamente ameaças de segurança através das fontes de dados da sua organização, antes de ser desencadeado um alerta. Depois de descobrir que consulta de caça fornece insights de alto valor sobre possíveis ataques, também pode criar regras de deteção personalizadas com base na sua consulta, e emergir esses insights como alertas para os seus incidentes de segurança. Durante a caça, você pode criar marcadores para eventos interessantes, permitindo-lhe voltar para eles mais tarde, partilhá-los com outros, e agrupar-los com outros eventos correlacionais para criar um incidente convincente para investigação.

![Investigação](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A comunidade Azure Sentinel é um recurso poderoso para deteção e automação de ameaças. Os nossos analistas de segurança da Microsoft criam e adicionam constantemente novos livros de trabalho, playbooks, consultas de caça e muito mais, publicando-os na comunidade para que possa usar no seu ambiente. Você pode baixar conteúdo de amostra do [repositório](https://aka.ms/asicommunity) gitHub da comunidade privada para criar livros personalizados, consultas de caça, cadernos e playbooks para Azure Sentinel. 

![Comunidade](./media/overview/community.png)

## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [bordo dos seus dados para o Azure Sentinel](quickstart-onboard.md)e obtenha visibilidade nos seus dados e ameaças [potenciais.](quickstart-get-visibility.md)
