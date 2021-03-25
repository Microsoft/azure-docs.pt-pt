---
title: Alocar custos do Azure
description: Este artigo explica como criar regras de alocação de custos para distribuir os custos das subscrições, dos grupos de recursos ou das etiquetas para outras subscrições, outros grupos de recursos ou outras etiquetas.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 414ce626d76c9b7a7d073d6cbfa5a5f4446c3073
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025531"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Criar e gerir regras de alocação de custos do Azure (Pré-visualização)

As empresas grandes, geralmente, têm recursos ou serviços do Azure que são geridos centralmente, mas são utilizados por departamentos internos ou unidades de negócios diferentes. Normalmente, a equipa de gestão central quer realocar o custo dos serviços partilhados de volta aos departamentos internos ou às unidades organizacionais de negócios que estão a utilizar os serviços ativamente. Este artigo ajuda-o a compreender e a utilizar a alocação de custos no Azure Cost Management.

Com a alocação de custos, pode reatribuir ou distribuir os custos de serviços partilhados das subscrições, dos grupos de recursos ou das etiquetas para outras subscrições, outros grupos de recursos ou outras etiquetas na sua organização. A alocação de custos muda os custos dos serviços partilhados para outra subscrição, outros grupos de recursos ou outras etiquetas detidos pelos departamentos internos de consumo ou pelas unidades comerciais. Por outras palavras, a alocação de custos ajuda a gerir e a apresentar _responsabilidade de custos_ de um local para outro.

A alocação de custos não afeta a faturação. As responsabilidades relacionadas com a faturação não são alteradas. O principal objetivo da alocação de custos é ajudar a fazer o estorno dos custos para outros. Todos os processos de estorno da sua organização ocorrem fora do Azure. A alocação de custos ajuda-o a fazer o estorno dos custos, uma vez que os apresenta à medida que são reatribuídos ou distribuídos.

Os custos alocados são apresentados na análise de custos. São apresentados como itens adicionais associados às subscrições, grupos de recursos ou etiquetas visados, que especifica ao criar uma regra de alocação de custos.

> [!NOTE]
> A funcionalidade de alocação de custos do Azure Cost Management está atualmente em pré-visualização pública. Algumas funcionalidades no Cost Management podem não ser suportadas ou podem ter capacidades limitadas.

## <a name="prerequisites"></a>Pré-requisitos

- Atualmente, a alocação de custos só suporta clientes com um [Contrato de Cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Para criar ou gerir uma regra de alocação de custos, deve utilizar uma conta de Administrador do Enterprise associada aos [Contratos Enterprise](../manage/understand-ea-roles.md). Ou deve ser o proprietário de uma [Conta de faturação](../manage/understand-mca-roles.md) associada aos Contratos de Cliente Microsoft.

## <a name="create-a-cost-allocation-rule"></a>Criar uma regra de alocação de custos

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
2. Navegue até **Cost Management + Faturação** > **Cost Management**.
3. Em **Definições** > **Configuração**, selecione **Alocação de custos (Pré-visualização)** .
4. Confirme que está selecionada a inscrição EA ou a conta de faturação correta.
5. Selecione **+Adicionar**.
6. Introduza um texto descritivo para o nome da regra de alocação de custos.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Exemplo a mostrar a criação de um nome de regra" lightbox="./media/allocate-costs/rule-name.png" :::

A data de início de avaliação da regra é utilizada para gerar as percentagens de alocação de custos pré-preenchidas.

1. Selecione **Adicionar origens** e, em seguida, selecione as subscrições, os grupos de recursos ou as etiquetas para escolher os custos a distribuir.
2. Selecione **Adicionar destinos** e, em seguida, selecione as subscrições, os grupos de recursos ou as etiquetas que vão receber os custos alocados.
3. Se precisar de criar regras de alocação de custos adicionais, repita este processo.

## <a name="configure-the-allocation-percentage"></a>Configurar a percentagem de alocação

Configure a percentagem de alocação para definir como os custos serão divididos de forma proporcional entre os destinos especificados. Pode definir manualmente as percentagens em números inteiros para criar uma regra de alocação. Em alternativa, pode dividir os custos proporcionalmente com base na utilização atual da computação, do armazenamento ou da rede entre os destinos especificados.

Ao distribuir os custos por custo de computação, custo de armazenamento ou custo de rede, a percentagem proporcional é obtida através da avaliação dos custos do destino selecionado. Os custos são associados ao tipo de recurso para o mês de faturação atual.

Ao distribuir custos proporcionais ao custo total, a percentagem proporcional é alocada pela soma ou pelo custo total dos destinos selecionados para o mês de faturação atual.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Exemplo a mostrar a percentagem de alocação" lightbox="./media/allocate-costs/cost-distribution.png" :::

Quando definidas, as percentagens pré-preenchidas são fixas. São utilizadas para todas as alocações em curso. As percentagens apenas são alteradas quando a regra é atualizada manualmente.

1. Selecione uma das seguintes opções na lista **Pré-preencher percentagem para**.
    - **Distribuir uniformemente** – cada um dos destinos recebe uma proporção uniforme em percentagem do custo total.
    - **Custo total** – cria uma taxa proporcional aos destinos com base no custo total. A taxa é utilizada para distribuir os custos das origens selecionadas.
    - **Custo de computação** – cria uma taxa proporcional aos destinos com base no custo de computação do Azure (tipos de recursos no espaço de nomes [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)). A taxa é utilizada para distribuir os custos a partir das origens selecionadas.
    - **Custo de armazenamento** – cria uma taxa proporcional aos destinos com base no custo do armazenamento do Azure (tipos de recursos no espaço de nomes [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)). A taxa é utilizada para distribuir os custos a partir das origens selecionadas.
    - **Custo de rede** – cria uma taxa proporcional aos destinos com base no custo de rede do Azure (tipos de recursos no espaço de nomes [Microsoft.Network](/azure/templates/microsoft.network/allversions)). A taxa é utilizada para distribuir os custos a partir das origens selecionadas.
    - **Personalizada** – permite que uma percentagem em número inteiro seja especificada manualmente. O total especificado deve ser igual a 100%.
1. Quando a regra for configurada, selecione **Criar**.

A regra de alocação inicia o processamento. Quando a regra está ativa, todos os custos da origem selecionada são alocados aos destinos especificados.

> [!NOTE] 
> O processamento da nova regra pode demorar até duas horas, bem como a sua ativação.

## <a name="verify-the-cost-allocation-rule"></a>Verificar a regra de alocação de custos

Quando a regra de alocação de custos está ativa, os custos das origens selecionadas são distribuídos para os destinos de alocação especificados. Utilize as seguintes informações para verificar se o custo está corretamente alocado aos destinos.

### <a name="view-cost-allocation-for-a-subscription"></a>Ver a alocação de custos de uma subscrição

Poderá ver o impacto da regra de alocação na análise de custos. No portal do Azure, aceda a [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selecione uma subscrição na lista abrangida pela regra de alocação de custos ativa. Em seguida, selecione **Análise de custos** no menu. Em Análise de custos, selecione **Agrupar por** e, em seguida, **Alocação de custos**. A vista apresentada mostra uma discriminação dos custos rápida gerada pela subscrição. Os custos alocados à subscrição também são apresentados, como na imagem abaixo.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Exemplo a mostrar a discriminação dos custos" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Ver a alocação de custos de um grupo de recursos

Utilize um processo semelhante para ver o impacto de uma regra de alocação de custos num grupo de recursos. No portal do Azure, aceda a [Grupos de recursos](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Selecione um grupo de recursos na lista abrangida pela regra de alocação de custos ativa. Em seguida, selecione **Análise de custos** no menu. Em Análise de custos, selecione **Agrupar por** e, em seguida, **Alocação de custos**. A vista mostra uma discriminação dos custos rápida gerada pelo grupo de recursos. O custo alocado ao grupo de recursos também é apresentado.

### <a name="view-cost-allocation-for-tags"></a>Ver a alocação de custos das etiquetas

No portal do Azure, navegue até **Cost Management + Faturação** > **Cost Management** > **Análise de custos**. Em Análise de custos, selecione **Adicionar filtro**. Selecione **Etiqueta**, escolha a chave e os valores de etiqueta com custos alocados.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Exemplo a mostrar os custos associados aos itens marcados" lightbox="./media/allocate-costs/tagged-costs.png" :::

Aqui está um vídeo que demonstra como criar uma regra de alocação de custos.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Editar uma regra de alocação de custos existente

Pode editar uma regra de alocação de custos para alterar a origem ou o destino ou se quiser atualizar a percentagem pré-preenchida das opções de computação, de armazenamento ou de rede. Edite as regras da mesma forma que as cria. A modificação das regras existentes pode demorar até duas horas a serem reprocessadas.

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

As secções seguintes contêm perguntas comuns que as pessoas colocam sobre a alocação de custos.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Quais são as limitações atuais da alocação de custos na pré-visualização pública?
<a name="limitations"></a>

Atualmente, a alocação de custos é suportada no Cost Management pelas vistas Análise de custos, Orçamentos e Previsão. Os custos alocados também são apresentados na lista de subscrições e na página de descrição geral das Subscrições.

Os itens seguintes não são atualmente suportados pela pré-visualização pública da alocação de custos:

- [Exportações](tutorial-export-acm-data.md) Agendadas
- Dados expostos pela API [Detalhes de Utilização](/rest/api/consumption/usagedetails/list)
- Área de subscrições de faturação
- [Aplicação Cost Management do Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Conector do Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>Os custos são decompostos nas vistas de orçamentos e previsão?
<a name="budgets-forecast"></a>

Yes. Os custos alocados são decompostos e suportados pelos orçamentos e pelas previsões. As vistas de orçamentos e de previsão mostram os custos que lhes são alocados, conforme configurado pelas regras de alocação de custos.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>O que acontece se uma regra de alocação de custos for eliminada?
<a name="delete-rule"></a>

Quando uma regra de alocação de custos é eliminada, todos os custos mensais de faturação atuais e abertos alocados aos destinos são removidos. Se a regra de alocação de custos já existir há vários meses, o histórico dos meses anteriores dos dados de alocação permanecerá conforme definido originalmente pela regra de alocação.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Porque é que um administrador de inscrição ou um administrador de conta de faturação precisa de criar regras de alocação de custos?
<a name="why-admin"></a>

As regras de alocação de custos são criadas no âmbito da inscrição (Contrato Enterprise) ou no âmbito da Conta de faturação (Contrato de Cliente Microsoft). As permissões para fazer alterações nesses âmbitos exigem privilégios de administrador de faturação.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>Porque é que as origens e os destinos estão limitados a 25 por regra?
<a name="source-target-rule-limit"></a>

Este limite é uma limitação da pré-visualização para garantir uma boa escalabilidade e desempenho da alocação de custos. Os limites serão provavelmente aumentados ou removidos quando as alocações de custos passarem para disponibilidade geral (GA).

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>O que pode acontecer se as regras de alocação de custos (origens/destinos) forem sobrepostas?
<a name="rule-overlap"></a>

As regras que têm origens sobrepostas ou destinos sobrepostos não são recomendadas. As regras de Alocação de Custos são aplicadas pela ordem da data de criação, portanto, se existirem regras de alocação de custos sobrepostas, a regra de alocação com a data de criação mais antiga terá precedência.

## <a name="next-steps"></a>Passos seguintes

- Criar ou atualizar regras de alocação com a [API REST de Alocação de custos](/rest/api/cost-management/costallocationrules)
- Saiba [Como otimizar o investimento na cloud com o Azure Cost Management](cost-mgt-best-practices.md)