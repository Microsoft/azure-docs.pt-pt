---
title: Inventário de ativos do Azure Security Center
description: Conheça a experiência de gestão de ativos do Azure Security Center proporcionando visibilidade total sobre todos os recursos monitorizados do Seu Centro de Segurança.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264009"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Explore e gere os seus recursos com ferramentas de inventário e gestão de ativos

A página de inventário de ativos do Azure Security Center fornece uma única página para visualizar a postura de segurança dos recursos que ligou ao Centro de Segurança. 

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades.

Quando algum recurso tiver recomendações pendentes, aparecerão no inventário.

Utilize esta vista e os seus filtros para abordar questões como:

- Qual das minhas assinaturas de nível padrão tem recomendações pendentes?
- Qual das minhas máquinas com a etiqueta "Produção" está a perder o agente do Log Analytics?
- Quantas das minhas máquinas, marcadas com uma etiqueta específica, têm recomendações pendentes?
- Quantos recursos num grupo de recursos específicos têm resultados de segurança de um serviço de avaliação de vulnerabilidades?

As possibilidades de gestão de ativos para esta ferramenta são substanciais e continuam a crescer. 


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar|
|Preços:|Gratuito|
|Funções e permissões necessárias:|Todos os utilizadores|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quais são as principais características do inventário de ativos?

A página de inventário fornece as seguintes ferramentas:

- **Resumos** - Antes de definir quaisquer filtros, uma tira proeminente de valores no topo da vista de inventário mostra:

    - **Recursos totais**: O número total de recursos ligados ao Centro de Segurança.
    - **Recursos insalubres**: Recursos com recomendações de segurança ativas. [Saiba mais sobre recomendações de segurança.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)
    - **Recursos não monitorizados**: Recursos com problemas de monitorização de agentes - eles têm o agente Log Analytics implantado, mas o agente não está a enviar dados ou tem outros problemas de saúde.

- **Filtros** - Os múltiplos filtros no topo da página fornecem uma forma de refinar rapidamente a lista de recursos de acordo com a pergunta que está a tentar responder. Por exemplo, se quiser responder à pergunta *Qual das minhas máquinas com a etiqueta 'Produção' está a faltar ao agente Log Analytics?* **Agent monitoring** **Tags**

    ![Filtragem para recursos produtivos que não são monitorizados](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Assim que tiver aplicado filtros, os valores do resumo são atualizados para se relacionarem com os resultados da consulta. 

- **Opções de exportação** - O inventário oferece a opção de exportar os resultados das suas opções de filtro selecionadas para um ficheiro CSV. Além disso, pode exportar a própria consulta para o Azure Resource Graph Explorer para refinar, guardar ou modificar a consulta KQL.

    ![Opções de exportação do inventário](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A documentação do KQL fornece uma base de dados com alguns dados da amostra, juntamente com algumas perguntas simples para obter a "sensação" para o idioma. [Saiba mais neste tutorial da KQL.](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)

- **Opções de gestão de ativos** - O inventário permite-lhe realizar consultas complexas de descoberta. Quando encontra os recursos que combinam com as suas consultas, o inventário fornece atalhos para operações como:

    - Atribuir tags aos recursos filtrados - selecione as caixas de verificação ao lado dos recursos que pretende marcar
    - A bordo de novos servidores para o Security Center - utilize o botão de barra **de ferramentas add non-Azure servers**


## <a name="how-does-asset-inventory-work"></a>Como funciona o inventário de ativos?

O inventário de ativos utiliza [o Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), um serviço Azure que fornece a capacidade de consultar os dados de postura de segurança do Security Center através de várias subscrições.

A ARG foi concebida para proporcionar uma exploração eficiente de recursos com a capacidade de consulta em escala.

Utilizando a [Língua de Consulta de Kusto (KQL),](https://docs.microsoft.com/azure/data-explorer/kusto/query/)o inventário de ativos pode produzir rapidamente insights profundos cruzando dados ASC com outras propriedades de recursos.


## <a name="how-to-use-asset-inventory"></a>Como usar o inventário de ativos

1. A partir da barra lateral do Centro de Segurança, **selecione Inventário**.

1. Opcionalmente, para apresentar um recurso específico introduza o nome na caixa **de nomes do Filtro.**

1. Selecione as opções relevantes nos filtros para criar a consulta específica que pretende realizar.

    ![Filtros de inventário](./media/asset-inventory/inventory-filters.png)

    Por padrão, os recursos são classificados pelo número de recomendações de segurança ativas.

    > [!IMPORTANT]
    > As opções em cada filtro são específicas dos recursos nas subscrições atualmente selecionadas **e** das suas seleções nos outros filtros.
    >
    > Por exemplo, se selecionou apenas uma subscrição, e a subscrição não tiver recursos com recomendações de segurança pendentes para remediar (0 Recursos Não Saudáveis), o filtro **recomendações** não terá opções. 

1. Para utilizar as **conclusões** de Segurança contêm filtro, introduza texto gratuito a partir do ID, verificação de segurança ou nome CVE de uma vulnerabilidade que encontra para filtrar para os recursos afetados:

    ![Filtro "Conclusões de segurança contêm" filtro](./media/asset-inventory/security-findings-contain-elements.png)

1. Para utilizar o filtro **de nível de preços,** selecione uma ou mais opções (Grátis, Parciais ou Standard):

    - **Grátis** - Recursos que estão no nível de preços gratuitos
    - **Standard** - Recursos que estão no nível de preços padrão
    - **Parcial** - Isto aplica-se a subscrições que estão no nível de preços padrão, mas que têm alguns dos planos de segurança opcionais desativados. Por exemplo, a subscrição a seguir está no nível padrão, mas tem cinco elementos do nível padrão desativado. 

        ![Assinatura no nível de preços padrão (parcial)](./media/asset-inventory/pricing-tier-partial.png)

1. Para examinar mais aprofundadamente os resultados da sua consulta, selecione os recursos que lhe interessam.

1. Opcionalmente, **selecione Ver no explorador de gráficos de recurso** para abrir a consulta no Explorador de Gráficos de Recursos.

    ![Consulta de inventário em ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Se definiu alguns filtros e deixou a página aberta, o Centro de Segurança não atualizará automaticamente os resultados. Quaisquer alterações aos recursos não impactarão os resultados apresentados a menos que recarregue manualmente a página ou selecione **Refresh**.


## <a name="faq---inventory"></a>FAQ - Inventário

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Por que não são mostradas todas as minhas assinaturas, máquinas, contas de armazenamento, etc.

A vista de inventário lista os seus recursos a partir de uma perspetiva de Cloud Security Posture Management (CSPM). Os filtros não devolvem todos os recursos do seu ambiente; apenas os que têm recomendações pendentes (ou 'ativas'). 

Por exemplo, se tiver nove subscrições mas apenas oito têm atualmente recomendações, quando filtrar por **tipo de recurso = Subscrições** só verá as oito subscrições com recomendações ativas:

![Nem todos os submarinos devolvidos quando não há recomendações ativas](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Porque é que alguns dos meus recursos mostram valores em branco nas colunas de preços ou de monitorização de agentes?

Nem todos os recursos monitorizados do Centro de Segurança têm agentes. Por exemplo, contas de Armazenamento Azure ou recursos PaaS tais como discos, Aplicações Lógicas, Análise de Lagos de Dados e Centro de Eventos.

Quando a monitorização de preços ou agentes não é relevante para um recurso, nada será mostrado nessas colunas de inventário.

![Alguns recursos mostram informações em branco nas colunas de monitorização ou preços do agente](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a página de inventário de ativos do Azure Security Center.

Para obter mais informações sobre ferramentas relacionadas, consulte as seguintes páginas:

- [Gráfico de recursos Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Linguagem de Consulta Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)