---
title: O que é o Azure Sentinel? | Microsoft Docs
description: Saiba mais sobre o Azure Sentinel, seus principais recursos e como ele funciona.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 964ba16fb7421c58f43d8b7d0d0d96f5cd377579
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261872"
---
# <a name="what-is-azure-sentinel"></a>O que é o Azure Sentinel?

Microsoft Azure Sentinel é uma solução de disparar **(gerenciamento de eventos de informações de segurança)** escalonável, nativa de nuvem e de ingestão de **segurança (** Siem). O Azure Sentinel fornece análise de segurança inteligente e inteligência contra ameaças em toda a empresa, fornecendo uma solução única para detecção de alertas, visibilidade de ameaças, busca proativa e resposta a ameaças. 

O Azure Sentinel é sua visão de olho para toda a empresa, aliviando o estresse de ataques cada vez mais sofisticados, aumentando os volumes de alertas e períodos de tempo de resolução longa.

- **Colete dados em escala de nuvem** em todos os usuários, dispositivos, aplicativos e infraestrutura, tanto localmente quanto em várias nuvens. 

- **Detecte ameaças anteriormente não detectadas**e minimize falsos positivos usando a análise da Microsoft e a inteligência de ameaças incomparável. 

- **Investigue as ameaças com inteligência artificial**e procure atividades suspeitas em escala, aproveitando os anos de trabalho de segurança cibernético na Microsoft. 

- **Responda a incidentes rapidamente** com orquestração interna e automação de tarefas comuns.


![Recursos principais do Azure Sentinel](./media/overview/core-capabilities.png)

Aproveitando toda a gama de serviços do Azure existentes, o Azure Sentinel nativamente incorpora bases comprovadas, como Log Analytics e aplicativos lógicos. O Azure Sentinel enriquece sua investigação e detecção com o ia e fornece o fluxo de inteligência contra ameaças da Microsoft e permite que você traga sua própria inteligência contra ameaças. 

 
## <a name="connect-to-all-your-data"></a>Conectar-se a todos os seus dados

Para o Azure Sentinel integrado, primeiro você precisa [se conectar às suas fontes de segurança](connect-data-sources.md). O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecimento de integração em tempo real, incluindo soluções de proteção contra ameaças da Microsoft e fontes de Microsoft 365, incluindo o Office 365, o Azure AD, o Azure ATP e o Microsoft Cloud App Security e muito mais. Além disso, há conectores internos para o ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato de evento comum, syslog ou REST-API para conectar suas fontes de dados com o Azure Sentinel também.  

![Coletores de dados](./media/collect-data/collect-data-page.png)

## <a name="workbooks"></a>Livros

Depois de [conectar suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, você pode monitorar os dados usando a integração do Azure Sentinel com Azure monitor pastas de trabalho, que fornece versatilidade na criação de pastas de trabalho personalizadas. Enquanto as pastas de trabalho são exibidas de forma diferente no Azure Sentinel, pode ser útil ver como [criar relatórios interativos com pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md). O Azure Sentinel permite que você crie pastas de trabalho personalizadas em seus dados e também vem com modelos de pasta de trabalho internos para que você possa obter informações rapidamente em seus dados assim que você conectar uma fonte de dados.

![Dashboards](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Análise

Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que você precisa examinar e investigar, o Azure Sentinel usa [a análise para correlacionar alertas em incidentes](tutorial-detect-threats-built-in.md). **Incidentes** são grupos de alertas relacionados que, juntos, criam uma possível ameaça acionável que você pode investigar e resolver. Use as regras de correlação internas no estado em que se encontram ou use-as como um ponto de partida para criar suas próprias. O Azure Sentinel também fornece regras de aprendizado de máquina para mapear seu comportamento de rede e, em seguida, procurar anomalias em seus recursos. Essas análises conectam os pontos, combinando alertas de baixa fidelidade sobre entidades diferentes em potenciais incidentes de segurança de alta fidelidade.

![Incidentes](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>& Orquestração da automação de segurança

Automatize suas tarefas comuns e [Simplifique a orquestração de segurança com guias estratégicos](tutorial-respond-threats-playbook.md) que se integram aos serviços do Azure, bem como suas ferramentas existentes. Baseado na base dos aplicativos lógicos do Azure, a automação e a solução de orquestração do Azure Sentinel fornecem uma arquitetura altamente extensível que permite a automação escalonável à medida que novas tecnologias e ameaças surgem. Para criar guias estratégicos com os aplicativos lógicos do Azure, você pode escolher entre uma galeria crescente de guias estratégicos internos. Isso inclui mais de [200 conectores](https://docs.microsoft.com/azure/connectors/apis-list) para serviços como o Azure functions. Os conectores permitem que você aplique qualquer lógica personalizada em código, ServiceNow, JIRA, zendesk, solicitações HTTP, Microsoft Teams, margem de atraso, Windows Defender ATP e Cloud App Security.

Por exemplo, se você usar o sistema de tíquetes do ServiceNow, poderá usar as ferramentas fornecidas para usar os aplicativos lógicos do Azure para automatizar seus fluxos de trabalho e abrir um tíquete no ServiceNow cada vez que um evento específico for detectado.

![Manuais de procedimentos](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Investigação

Atualmente em visualização, as ferramentas de [investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam você a entender o escopo e encontrar a causa raiz de uma possível ameaça à segurança. Você pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica e fazer uma busca detalhada nessa entidade e suas conexões para chegar à causa raiz da ameaça. 

![Investigação](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Investigação

Use as [poderosas ferramentas de pesquisa e consulta](hunting.md)do Azure Sentinel, com base na estrutura Mitre, que permite que você busque proativamente as ameaças à segurança nas fontes de dados da sua organização, antes que um alerta seja disparado. Depois de descobrir qual consulta de busca fornece informações de alto valor sobre possíveis ataques, você também pode criar regras de detecção personalizadas com base em sua consulta e trazer essas informações como alertas para seus respondentes de incidente de segurança. Durante a busca, você pode criar indicadores para eventos interessantes, permitindo que você os retorne a eles mais tarde, compartilhe-os com outras pessoas e agrupe-os com outros eventos correlacionados para criar um incidente atraente para investigação.

![Investigação](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A Comunidade do Azure Sentinel é um recurso poderoso para detecção e automação de ameaças. Nossos analistas de segurança da Microsoft criam e adicionam constantemente novas pastas de trabalho, guias estratégicos, consultas de busca e muito mais, postando-as à Comunidade para que você use em seu ambiente. Você pode baixar o conteúdo de exemplo do [repositório](https://aka.ms/asicommunity) GitHub da Comunidade particular para criar pastas de trabalho personalizadas, consultas de busca, blocos de anotações e guias estratégicos para o Azure Sentinel. 

![Comunidade](./media/overview/community.png)

## <a name="next-steps"></a>Passos seguintes

- Para começar a usar o Azure Sentinel, você precisa de uma assinatura para Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como integrar [seus dados ao Azure Sentinel](quickstart-onboard.md)e [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
