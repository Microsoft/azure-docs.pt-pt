---
title: O que é a pré-visualização de sentinela do Azure? | Documentos da Microsoft
description: Saiba mais sobre o Azure sentinela, suas principais capacidades e como ele funciona.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2859df94b1418cfa9fbfaa341e6bf08b257875bf
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245298"
---
# <a name="what-is-azure-sentinel-preview"></a>O que é a pré-visualização de sentinela do Azure?

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinela do Microsoft Azure é uma dimensionável, nativas da cloud **gestão de eventos de informações de segurança (SIEM)** e **orquestração de segurança automatizada de resposta (SOAR)** solução. Sentinel do Azure fornece inteligência de ameaças e análise de segurança inteligente toda a empresa, fornecendo uma solução única para deteção de alerta, visibilidade de ameaças, buscar proativa e resposta a ameaças. 

Sentinel do Azure é a exibição de olho-de-birds em toda a empresa resolvendo problemas a sobrecarga de ataques cada vez mais sofisticados, aumentando a volumes de alertas e longos períodos de tempo de resolução.

- **Recolher dados à escala da cloud** em todos os utilizadores, dispositivos, aplicações e infraestrutura, no local e em várias clouds. 

- **Detete ameaças não detetadas previamente**e minimizar falsos positivos, utilização de análises e informações sobre ameaças sem paralelo da Microsoft. 

- **Investigar ameaças com a inteligência artificial**e procurar atividades suspeitas em escala, de anos de trabalho de segurança de cibersegurança da Microsoft. 

- **Responder a incidentes rapidamente** com orquestração interna e a automatização de tarefas comuns.


![Sentinel principais recursos do Azure](./media/overview/core-capabilities.png)

A criar em toda a variedade de serviços do Azure existentes, nativamente sentinela Azure incorpora foundations comprovadas, como o Log Analytics e o Logic Apps. Azure Sentinel otimiza a sua investigação e de deteção com IA e fornece o fluxo de inteligência de ameaças da Microsoft e permite-lhe colocar suas próprias informações sobre ameaças. 

 
## <a name="connect-to-all-your-data"></a>Ligar a todos os seus dados

Para integrar sentinela do Azure, primeiro tem de [ligar às suas origens de segurança](connect-data-sources.md). Sentinel do Azure é fornecido com uma série de conectores para soluções da Microsoft, disponíveis para ser usado na caixa e o fornecimento de integração em tempo real, incluindo soluções de proteção de ameaças da Microsoft e do Microsoft 365 origens, incluindo o Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security e muito mais. Além disso, existem conectores incorporados para o mais amplo ecossistema de segurança para soluções de terceiros. Também pode utilizar o formato de evento comum, Syslog ou REST API para ligar as origens de dados com o Azure sentinela também.  

![Recoletores de dados](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Dashboards

Depois de ligar a origens de dados pode escolher entre uma galeria de [criada com dashboards](quickstart-get-visibility.md#dashboards) que surgir informações a partir de origens de dados. Cada dashboard é totalmente personalizável – pode adicionar sua própria lógica ou modificar consultas ou pode criar um dashboard a partir do zero.

Os dashboards fornecem visualização interativa através de análises avançadas para ajudar os analistas de segurança, obtenha uma compreensão melhor do que está acontecendo durante um ataque. As ferramentas de investigação permitem-em qualquer campo, de forma mais aprofundada a partir de quaisquer dados, para desenvolver rapidamente o contexto de ameaças. 

![Dashboards](./media/overview/dashboards.png)

## <a name="analytics"></a>Análise

Para ajudar a reduzir o ruído e minimizar o número de alertas, terá de rever e investigar, Azure sentinela usa [analytics para correlacionar alertas em casos de](tutorial-detect-threats.md). **Casos** são grupos de alertas relacionados que juntos criam uma possível-ameaça passíveis de ação que pode investigar e resolver. Utilize as regras de correlação incorporadas como-está, ou usá-los como um ponto de partida para criar as suas próprias. Sentinel do Azure também fornece regras de aprendizado de máquina para mapear o comportamento da sua rede e, em seguida, procurar anomalias todos os seus recursos. Estas análises ligar os pontos, ao combinar os alertas de baixa fidelidade sobre entidades diferentes em potencial incidentes de segurança de alta fidelidade.

![Casos](./media/overview/cases.png)

## <a name="user-analytics"></a>Análise de utilizador

Com integração nativa de machine learning (ML), e [análise de utilizadores](user-analytics.md), sentinela do Azure pode ajudar a detetar ameaças rapidamente. Sentinel do Azure integra-se perfeitamente com o Azure proteção avançada contra ameaças para analisar o comportamento dos utilizadores e atribuir prioridades a quais usuários, deve investigar em primeiro lugar, com base nos seus alertas e padrões de atividade suspeita em sentinela do Azure e o Microsoft 365.

![Análise de utilizador](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Segurança de automatização e orquestração

Automatizar as tarefas comuns e [simplificar a orquestração de segurança com playbooks](tutorial-respond-threats-playbook.md) que se integram com serviços do Azure, bem como as suas ferramentas existentes. Criado sobre a base do Azure Logic Apps, solução de automatização e orquestração do Azure sentinela fornece uma arquitetura altamente extensível que permite a automatização dimensionável como novas tecnologias e ameaças surgem. Para criar playbooks no Azure Logic Apps, pode escolher entre uma galeria crescente de playbooks incorporadas. Estes incluem [mais de 200 conectores](https://docs.microsoft.com/azure/connectors/apis-list) para serviços, como as funções do Azure. Os conectores permitem-lhe aplicar qualquer lógica personalizada em código, ServiceNow, Jira, Zendesk, HTTP solicitações, Microsoft Teams, Slack, do Windows Defender ATP e o Cloud App Security.

Por exemplo, se usar o sistema de emissão de permissões do ServiceNow, pode utilizar as ferramentas fornecidas para utilizar o Azure Logic Apps para automatizar fluxos de trabalho e abrir um pedido de suporte no ServiceNow sempre que um determinado evento é detetado.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Investigação

Azure Sentinel [investigação profunda](tutorial-investigate-cases.md) ferramentas ajudá-lo a compreender o escopo e descobrir a causa de raiz, de uma potencial ameaça de segurança. Pode escolher uma entidade no gráfico interativo para colocar questões interessantes de uma entidade específica e desagregar para essa entidade e as suas ligações para obter a causa da ameaça. 

![Investigação](./media/overview/investigation.png)


## <a name="hunting"></a>Investigação

Utilizar o Azure sentinela [caçar poderosas ferramentas de pesquisa e consulta](hunting.md), com base no framework MITRE, que permitem-lhe proativamente hunt ameaças de segurança entre origens de dados da sua organização, antes de um alerta é acionado. Depois de descobrir que consulta de arquivo terá fornece informações de alto valor sobre possíveis ataques, também pode criar regras de detecção personalizadas com base na sua consulta e aborde essas perspectivas como alertas para os respondentes de incidentes de segurança. Durante a procura, pode criar marcadores para eventos interessantes, permitindo que retornar a elas mais tarde, partilhá-los com outras pessoas e agrupá-los com outros eventos correlating para criar um caso convincente para investigação.

![Investigação](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A Comunidade Azure sentinela é um recurso poderoso para a deteção de ameaças e automação. Nosso analistas de segurança da Microsoft constantemente criar e adicionar novos dashboards, playbooks, consultas de buscar e muito mais, postando-os para a Comunidade para que possa utilizar no seu ambiente. Pode transferir conteúdo de exemplo da Comunidade privada GitHub [repositório](https://aka.ms/asicommunity) para criar dashboards personalizados, consultas de procura, blocos de notas e playbooks para sentinela do Azure. 

![Comunidade](./media/overview/community.png)

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar com o Azure sentinela, terá de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [carregar os dados para o Azure sentinela](quickstart-onboard.md), e [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
