---
title: Inventário de ativos do Azure Security Center
description: Conheça a experiência de gestão de ativos do Azure Security Center proporcionando visibilidade total sobre todos os recursos monitorizados do Seu Centro de Segurança.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/22/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: e62973c83db90df2857186fe6a556278a3c41a7d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977458"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Explore e gere os seus recursos com ferramentas de inventário e gestão de ativos

A página de inventário de ativos do Azure Security Center fornece uma única página para visualizar a postura de segurança dos recursos que ligou ao Centro de Segurança. 

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades.

Quando algum recurso tiver recomendações pendentes, aparecerão no inventário.

Utilize esta vista e os seus filtros para abordar questões como:

- Qual das minhas assinaturas com o Azure Defender permitiu ter recomendações pendentes?
- Qual das minhas máquinas com a etiqueta "Produção" está a perder o agente do Log Analytics?
- Quantas das minhas máquinas marcadas com uma etiqueta específica têm recomendações pendentes?
- Quantos recursos num grupo de recursos específicos têm resultados de segurança de um serviço de avaliação de vulnerabilidades?

As possibilidades de gestão de ativos para esta ferramenta são substanciais e continuam a crescer. 

> [!TIP]
> As recomendações de segurança são as mesmas que estão na página **de Recomendações,** mas aqui são filtradas para o tipo de recurso específico que selecionou. Para obter mais informações sobre como resolver recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|Todos os utilizadores|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quais são as principais características do inventário de ativos?

A página de inventário fornece as seguintes ferramentas:

- **Resumos** - Antes de definir quaisquer filtros, uma tira proeminente de valores no topo da vista de inventário mostra:

    - **Recursos totais**: O número total de recursos ligados ao Centro de Segurança.
    - **Recursos insalubres**: Recursos com recomendações de segurança ativas. [Saiba mais sobre recomendações de segurança.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)
    - **Recursos não monitorizados**: Recursos com problemas de monitorização de agentes - eles têm o agente Log Analytics implantado, mas o agente não está a enviar dados ou tem outros problemas de saúde.

- **Filtros** - Os múltiplos filtros no topo da página fornecem uma forma de refinar rapidamente a lista de recursos de acordo com a pergunta que está a tentar responder. Por exemplo, se quiser responder à pergunta *Qual das minhas máquinas com a etiqueta 'Produção' está a faltar ao agente Log Analytics?* **Agent monitoring** **Tags**

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Filtragem para recursos produtivos que não são monitorizados":::

    Assim que tiver aplicado filtros, os valores do resumo são atualizados para se relacionarem com os resultados da consulta. 

- **Opções de exportação** - O inventário oferece a opção de exportar os resultados das suas opções de filtro selecionadas para um ficheiro CSV. Além disso, pode exportar a própria consulta para o Azure Resource Graph Explorer para refinar, guardar ou modificar a consulta KQL.

    ![Opções de exportação do inventário](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A documentação do KQL fornece uma base de dados com alguns dados da amostra, juntamente com algumas perguntas simples para obter a "sensação" para o idioma. [Saiba mais neste tutorial da KQL.](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)

- **Opções de gestão de ativos** - O inventário permite-lhe realizar consultas complexas de descoberta. Quando encontra os recursos que combinam com as suas consultas, o inventário fornece atalhos para operações como:

    - Atribua etiquetas aos recursos filtrados - selecione as caixas de verificação ao lado dos recursos que pretende marcar.
    - A bordo de novos servidores para o Security Center - utilize o botão de barra **de ferramentas add non-Azure servers.**
    - Automatizar cargas de trabalho com apps Azure Logic - use o botão **Trigger Logic App** para executar uma aplicação lógica em um ou mais recursos. As suas aplicações lógicas têm de ser preparadas com antecedência e aceitar o tipo de gatilho relevante (pedido HTTP). [Saiba mais sobre aplicações lógicas.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)


## <a name="how-does-asset-inventory-work"></a>Como funciona o inventário de ativos?

O inventário de ativos utiliza [o Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), um serviço Azure que fornece a capacidade de consultar os dados de postura de segurança do Security Center através de várias subscrições.

A ARG foi concebida para proporcionar uma exploração eficiente de recursos com a capacidade de consulta em escala.

Utilizando a [Língua de Consulta de Kusto (KQL),](https://docs.microsoft.com/azure/data-explorer/kusto/query/)o inventário de ativos pode produzir rapidamente insights profundos cruzando dados ASC com outras propriedades de recursos.


## <a name="how-to-use-asset-inventory"></a>Como usar o inventário de ativos

1. A partir da barra lateral do Centro de Segurança, **selecione Inventário**.

1. Utilize a caixa **de identificação filter por nome** para exibir um recurso específico ou utilize os filtros conforme descrito abaixo.

1. Selecione as opções relevantes nos filtros para criar a consulta específica que pretende realizar.

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="Opções de filtragem do inventário" lightbox="./media/asset-inventory/inventory-filters.png":::

    Por padrão, os recursos são classificados pelo número de recomendações de segurança ativas.

    > [!IMPORTANT]
    > As opções em cada filtro são específicas dos recursos nas subscrições atualmente selecionadas **e** das suas seleções nos outros filtros.
    >
    > Por exemplo, se selecionou apenas uma subscrição, e a subscrição não tiver recursos com recomendações de segurança pendentes para remediar (0 recursos não saudáveis), o filtro **Recomendações** não terá opções. 

1. Para utilizar as **conclusões** de Segurança contêm filtro, introduza texto gratuito a partir do ID, verificação de segurança ou nome CVE de uma vulnerabilidade que encontra para filtrar para os recursos afetados:

    ![Filtro "Conclusões de segurança contêm" filtro](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > As **conclusões de Segurança contêm** e os **filtros Tags** apenas aceitam um único valor. Para filtrar por mais de um, utilize **filtros Adicionar**.

1. Para utilizar o filtro **Azure Defender,** selecione uma ou mais opções (Desligada, On ou Parcial):

    - **Off** - Recursos que não estão protegidos por um plano Azure Defender. Pode clicar em qualquer um destes e atualizá-los:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Atualize um recurso para Azure Defender a partir do clique direito" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **On** - Recursos protegidos por um plano Azure Defender
    - **Parcial** - Isto aplica-se a subscrições que têm **alguns,** mas não todos os planos do Azure Defender desativados. Por exemplo, a subscrição a seguir tem cinco planos Azure Defender desativados. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Subscrição parcial no Azure Defender":::

1. Para examinar mais aprofundadamente os resultados da sua consulta, selecione os recursos que lhe interessam.

1. Para ver as opções de filtro selecionadas atuais como uma consulta no Explorador de Gráficos de Recurso, selecione **Ver no explorador de gráficos de recursos**.

    ![Consulta de inventário em ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Para executar uma aplicação lógica previamente definida com 

1. Se definiu alguns filtros e deixou a página aberta, o Centro de Segurança não atualizará automaticamente os resultados. Quaisquer alterações aos recursos não impactarão os resultados apresentados a menos que recarregue manualmente a página ou selecione **Refresh**.


## <a name="faq---inventory"></a>FAQ - Inventário

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Por que não são mostradas todas as minhas assinaturas, máquinas, contas de armazenamento, etc.

A vista de inventário lista os recursos conectados do seu Centro de Segurança a partir de uma perspetiva de Cloud Security Posture Management (CSPM). Os filtros não devolvem todos os recursos do seu ambiente; apenas os que têm recomendações pendentes (ou 'ativas'). 

Por exemplo, a imagem que se segue mostra um utilizador com acesso a 38 subscrições, mas apenas 10 têm atualmente recomendações. Assim, quando filtram por **tipo de recurso = Assinaturas, apenas**as 10 assinaturas com recomendações ativas aparecem no inventário:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Nem todos os submarinos devolvidos quando não há recomendações ativas":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Porque é que alguns dos meus recursos mostram valores em branco nas colunas de monitorização do Azure Defender ou de agente?

Nem todos os recursos monitorizados do Centro de Segurança têm agentes. Por exemplo, contas de Armazenamento Azure ou recursos PaaS tais como discos, Aplicações Lógicas, Análise de Lagos de Dados e Centro de Eventos.

Quando a monitorização de preços ou agentes não é relevante para um recurso, nada será mostrado nessas colunas de inventário.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Alguns recursos mostram informações em branco na monitorização do agente ou colunas do Azure Defender":::

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a página de inventário de ativos do Azure Security Center.

Para obter mais informações sobre ferramentas relacionadas, consulte as seguintes páginas:

- [Gráfico de recursos Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)
- [Linguagem de Consulta Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)