---
title: O que é a visualização do Azure Sentinel? | Microsoft Docs
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 15cf770a372c9a1386dd0293abeac01fd3cacf63
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779753"
---
# <a name="what-is-azure-sentinel-preview"></a>O que é a visualização do Azure Sentinel?

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft Azure Sentinel é uma solução de disparar **(gerenciamento de eventos de informações de segurança)** escalonável, nativa de nuvem e de ingestão de **segurança (** Siem). O Azure Sentinel fornece análise de segurança inteligente e inteligência contra ameaças em toda a empresa, fornecendo uma solução única para detecção de alertas, visibilidade de ameaças, busca proativa e resposta a ameaças. 

O Azure Sentinel é sua visão de olho para toda a empresa, aliviando o estresse de ataques cada vez mais sofisticados, aumentando os volumes de alertas e períodos de tempo de resolução longa.

- **Colete dados em escala de nuvem** em todos os usuários, dispositivos, aplicativos e infraestrutura, tanto localmente quanto em várias nuvens. 

- **Detecte ameaças anteriormente**não detectadas e minimize falsos positivos usando a análise da Microsoft e a inteligência de ameaças incomparável. 

- **Investigue as ameaças com inteligência artificial**e procure atividades suspeitas em escala, aproveitando os anos de trabalho de segurança cibernético na Microsoft. 

- **Responda a incidentes rapidamente** com orquestração interna e automação de tarefas comuns.


![Recursos principais do Azure Sentinel](./media/overview/core-capabilities.png)

Aproveitando toda a gama de serviços do Azure existentes, o Azure Sentinel nativamente incorpora bases comprovadas, como Log Analytics e aplicativos lógicos. O Azure Sentinel enriquece sua investigação e detecção com o ia e fornece o fluxo de inteligência contra ameaças da Microsoft e permite que você traga sua própria inteligência contra ameaças. 

 
## <a name="connect-to-all-your-data"></a>Conectar-se a todos os seus dados

Para o Azure Sentinel integrado, primeiro você precisa [se conectar às suas fontes de segurança](connect-data-sources.md). O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecimento de integração em tempo real, incluindo soluções de proteção contra ameaças da Microsoft e fontes de Microsoft 365, incluindo o Office 365, o Azure AD, o Azure ATP e o Microsoft Cloud App Security e muito mais. Além disso, há conectores internos para o ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato de evento comum, syslog ou REST-API para conectar suas fontes de dados com o Azure Sentinel também.  

![Coletores de dados](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Dashboards

Depois de conectar fontes de dados, você pode escolher em uma galeria de [painéis criados de especialistas](quickstart-get-visibility.md#dashboards) que insights de suas fontes de dados. Cada painel é totalmente personalizável-você pode adicionar sua própria lógica ou modificar consultas, ou pode criar um painel do zero.

Os painéis fornecem visualização interativa usando a análise avançada para ajudar os analistas de segurança a entender melhor o que está acontecendo durante um ataque. As ferramentas de investigação permitem aprofundar-se em qualquer campo, de qualquer dado, para desenvolver rapidamente o contexto de ameaças. 

![Dashboards](./media/overview/dashboards.png)

## <a name="analytics"></a>Análise

Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que você precisa examinar e investigar, o Azure Sentinel usa [a análise para correlacionar alertas em incidentes](tutorial-detect-threats.md). **Incidentes** são grupos de alertas relacionados que, juntos, criam uma possível ameaça acionável que você pode investigar e resolver. Use as regras de correlação internas no estado em que se encontram ou use-as como um ponto de partida para criar suas próprias. O Azure Sentinel também fornece regras de aprendizado de máquina para mapear seu comportamento de rede e, em seguida, procurar anomalias em seus recursos. Essas análises conectam os pontos, combinando alertas de baixa fidelidade sobre entidades diferentes em potenciais incidentes de segurança de alta fidelidade.

![Bolsas](./media/overview/cases.png)

## <a name="user-analytics"></a>Análise de utilizador

Com a integração nativa do ML (Machine Learning) e a [análise do usuário](user-analytics.md), o Azure Sentinel pode ajudar a detectar ameaças rapidamente. O Azure Sentinel integra-se perfeitamente com a proteção avançada contra ameaças do Azure para analisar o comportamento do usuário e priorizar quais usuários você deve investigar primeiro, com base em seus alertas e padrões de atividade suspeitos no Azure Sentinel e Microsoft 365.

![Análise de utilizador](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>& Orquestração da automação de segurança

Automatize suas tarefas comuns e [Simplifique a orquestração de segurança com guias estratégicos](tutorial-respond-threats-playbook.md) que se integram aos serviços do Azure, bem como suas ferramentas existentes. Baseado na base dos aplicativos lógicos do Azure, a automação e a solução de orquestração do Azure Sentinel fornecem uma arquitetura altamente extensível que permite a automação escalonável à medida que novas tecnologias e ameaças surgem. Para criar guias estratégicos com os aplicativos lógicos do Azure, você pode escolher entre uma galeria crescente de guias estratégicos internos. Isso inclui mais de [200 conectores](https://docs.microsoft.com/azure/connectors/apis-list) para serviços como o Azure functions. Os conectores permitem que você aplique qualquer lógica personalizada em código, ServiceNow, JIRA, zendesk, solicitações HTTP, Microsoft Teams, margem de atraso, Windows Defender ATP e Cloud App Security.

Por exemplo, se você usar o sistema de tíquetes do ServiceNow, poderá usar as ferramentas fornecidas para usar os aplicativos lógicos do Azure para automatizar seus fluxos de trabalho e abrir um tíquete no ServiceNow cada vez que um evento específico for detectado.

![Manuais de procedimentos](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Investigação

As ferramentas de [investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam você a entender o escopo e encontrar a causa raiz de uma possível ameaça à segurança. Você pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica e fazer uma busca detalhada nessa entidade e suas conexões para chegar à causa raiz da ameaça. 

![Investigação](./media/overview/investigation.png)


## <a name="hunting"></a>Investigação

Use as [poderosas ferramentas de pesquisa e consulta](hunting.md)do Azure Sentinel, com base na estrutura Mitre, que permite que você busque proativamente as ameaças à segurança nas fontes de dados da sua organização, antes que um alerta seja disparado. Depois de descobrir qual consulta de busca fornece informações de alto valor sobre possíveis ataques, você também pode criar regras de detecção personalizadas com base em sua consulta e trazer essas informações como alertas para seus respondentes de incidente de segurança. Durante a busca, você pode criar indicadores para eventos interessantes, permitindo que você os retorne a eles mais tarde, compartilhe-os com outras pessoas e agrupe-os com outros eventos correlacionados para criar um caso atraente para investigação.

![Investigação](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A Comunidade do Azure Sentinel é um recurso poderoso para detecção e automação de ameaças. Nossos analistas de segurança da Microsoft constantemente criam e adicionam novos dashboards, guias estratégicos, consultas de busca e muito mais, postando-os à Comunidade para você usar em seu ambiente. Você pode baixar o conteúdo de exemplo do [repositório](https://aka.ms/asicommunity) GitHub da Comunidade particular para criar painéis personalizados, consultas de busca, blocos de anotações e guias estratégicos para o Azure Sentinel. 

![Comunidade](./media/overview/community.png)

## <a name="next-steps"></a>Passos Seguintes

- Para começar a usar o Azure Sentinel, você precisa de uma assinatura para Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como integrar [seus dados ao Azure Sentinel](quickstart-onboard.md)e [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
