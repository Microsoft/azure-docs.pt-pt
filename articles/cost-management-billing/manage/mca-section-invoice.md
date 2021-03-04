---
title: Organizar a fatura de acordo com as suas necessidades – Azure
description: Saiba como organizar os custos da sua fatura. Pode criar perfis de faturação e secções de fatura para personalizar a sua conta de faturação.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c3ee643d525cb5394369aba3b0e803bd1a94c852
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036242"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Personalizar a conta de faturação para organizar os custos

A conta de faturação do Contrato de Cliente Microsoft oferece-lhe flexibilidade para organizar os custos de acordo com as suas necessidades, seja por departamento, projeto ou ambiente de desenvolvimento.

Este artigo descreve como pode utilizar o portal do Azure para organizar os custos. Aplica-se a uma conta de faturação de um Contrato de Cliente Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Assista aos custos do [Organize personalizando o vídeo da sua conta de faturação do Microsoft Customer Agreement](https://www.youtube.com/watch?v=7RxTfShGHwU) para aprender a organizar custos para a sua conta de faturação.

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Estruturar a conta com perfis de faturação e secções de fatura

Na conta de faturação de um Contrato de Cliente Microsoft, utiliza os perfis de faturação e as secções de fatura para organizar os custos.

![Captura de ecrã que mostra a hierarquia de faturação do Contrato de Cliente Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Perfil de faturação

Um perfil de faturação representa uma fatura e as informações de faturação relacionadas, como métodos de pagamento e endereço de faturação. Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém os custos relativos à utilização do Azure e a outras compras do mês anterior.

Um perfil de faturação é criado automaticamente em conjunto com a conta de faturação quando se inscreve no Azure. Pode criar perfis de faturação adicionais para organizar os custos em várias faturas mensais.

> [!IMPORTANT]
>
> A criação de perfis de faturação adicionais pode afetar o custo global. Para obter mais informações, veja as [Questões a considerar ao adicionar novos perfis de faturação](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Secção de fatura

Uma fatura representa um agrupamento de custos na fatura. Uma secção de fatura é criada automaticamente para cada perfil de faturação na conta. Pode criar secções adicionais para organizar os custos de acordo com as suas necessidades. Cada secção de fatura é apresentada na fatura com os custos incorridos nesse mês.

A imagem abaixo mostra uma fatura com duas secções de fatura – Engenharia e Marketing. Os custos resumidos e detalhados para cada secção são apresentados na fatura. Os preços mostrados na imagem são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure.

![Imagem a mostrar uma fatura com secções](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Estrutura da conta de faturação para cenários comuns

Esta secção descreve cenários comuns de organização de custos e as estruturas de contas de faturação correspondentes:

|Cenário  |Estrutura  |
|---------|---------|
|O João inscreve-se no Azure e precisa de uma única fatura mensal. | Um perfil de faturação e uma secção de fatura. Esta estrutura é configurada automaticamente para o João quando ele se inscreve no Azure e não requer quaisquer passos adicionais. |

![Infográfico de um cenário de faturação individual.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Cenário  |Estrutura  |
|---------|---------|
|A Contoso é uma pequena organização que precisa de uma única fatura mensal, mas que os custos sejam agrupados pelos respetivos departamentos – marketing e engenharia.  | Um perfil de faturação da Contoso e uma secção de fatura para cada departamento: o de marketing e o de engenharia. |

![Infográfico de um cenário de faturação múltipla.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Cenário  |Estrutura  |
|---------|---------|
|A Fabrikam é uma organização média que precisa de faturas separadas para os respetivos departamentos de engenharia e marketing. Para o departamento de engenharia, pretendem agrupar os custos por ambientes – produção e desenvolvimento.  | Um perfil de faturação para cada departamento: o de marketing e o de engenharia. Para o departamento de engenharia, uma secção de fatura para cada ambiente: o de produção e o de desenvolvimento. |

![Infográfico de um cenário de faturação múltipla, com faturação separada para ambientes de produção e desenvolvimento.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Criar uma nova secção de fatura

Para criar uma secção de fatura, precisa de ser **proprietário do perfil de faturação** ou **contribuidor do perfil de faturação**. Para obter mais informações, veja [Gerir as secções de fatura do perfil de faturação](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-section-invoice/search-cmb.png)

3. Selecione **Perfis de faturação** no painel esquerdo. Na lista, selecione um perfil de faturação. A nova secção é apresentada na fatura do perfil de faturação selecionado.

   [![Captura de ecrã que mostra a lista dos perfis de faturação.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selecione **Secções de fatura** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de ecrã que mostra a adição de secções de fatura](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Introduza um nome para a secção de fatura.

   [![Captura de ecrã que mostra a página de criação das secções de fatura](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selecione **Criar**.

## <a name="create-a-new-billing-profile"></a>Criar um novo perfil de faturação

Para criar um novo perfil de faturação, tem de ser **proprietário da conta de faturação** ou **contribuidor a conta de faturação**. Para obter mais informações, veja [Gerir perfis de faturação para a conta de faturação](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> A criação de perfis de faturação adicionais pode afetar o custo global. Para obter mais informações, veja as [Questões a considerar ao adicionar novos perfis de faturação](#things-to-consider-when-adding-new-billing-profiles).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-section-invoice/search-cmb.png)

3. Selecione **Perfis de faturação** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de ecrã que mostra a lista de perfis de faturação com Adicionar selecionado.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Se não vir o botão Adicionar na página do Perfil de faturação, significa que a funcionalidade não está disponível para a conta. Atualmente, a funcionalidade só está disponível para contas que tenham sido configuradas aquando do trabalho com um representante da Microsoft.

4. Preencha o formulário e clique em **Criar**.

   [![Captura de ecrã que mostra a página de criação de perfis de faturação](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Campo  |Definição  |
    |---------|---------|
    |Nome     | Um nome a apresentar ajuda a identificar facilmente o perfil de faturação no portal do Azure.  |
    |Número da nota de encomenda    | Um número de nota de encomenda opcional. O número de nota de encomenda é apresentado nas faturas geradas para o perfil de faturação. |
    |Endereço para faturação   | O endereço para faturação é apresentado nas faturas geradas para o perfil de faturação. |
    |Fatura por e-mail   | Marque a caixa da fatura por e-mail para receber as faturas deste perfil de faturação por e-mail. Se optar por não receber, poderá ver e transferir as faturas no portal do Azure.|

5. Selecione **Criar**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Associar os custos às secções de fatura e aos perfis de faturação

Depois de ter personalizado a conta de faturação de acordo com as suas necessidades, poderá associar as subscrições e outros produtos à secção de fatura e ao perfil de faturação pretendidos.

### <a name="link-a-new-subscription"></a>Associar a uma nova subscrição

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Escreva **Subscrições** na caixa de pesquisa.

   [![Captura de ecrã que mostra a pesquisa de uma subscrição no portal do Azure.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selecione **Adicionar** na parte superior da página.

   ![Captura de ecrã que mostra o botão Adicionar na vista Subscrições de uma subscrição nova.](./media/mca-section-invoice/subscription-add.png)

4. Se tiver acesso a várias contas de faturação, selecione a conta de faturação do seu Contrato de Cliente Microsoft.

   ![Captura de ecrã que mostra o painel Criar subscrição.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selecione o perfil de faturação que será cobrado pela utilização da subscrição. Os custos da utilização do Azure e outras compras desta subscrição serão cobrados na fatura do perfil de faturação selecionado.

6. Selecione a secção de fatura a associar aos custos da subscrição. Os custos são apresentados nesta secção na fatura do perfil de faturação.

7. Selecione um plano do Azure e introduza um nome amigável para a subscrição.

9. Clique em **Criar**.  

### <a name="link-existing-subscriptions-and-products"></a>Associar subscrições e produtos existentes

Se tiver subscrições do Azure existentes ou outros produtos, como recursos do Azure Marketplace e recursos de origem da Aplicação, poderá transferi-los da secção de fatura existente para outra secção de fatura para reorganizar os custos.

> [!IMPORTANT]
>
> As subscrições e outros produtos só podem ser movidos entre secções de fatura que pertençam ao mesmo perfil de faturação. Não é possível mover subscrições e produtos entre secções de fatura em perfis de faturação diferentes.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa por Cost Management + Faturação no portal do Azure.](./media/mca-section-invoice/search-cmb.png)

3. Para associar uma subscrição a uma nova secção de fatura, selecione **Subscrições do Azure** no lado esquerdo do ecrã. Para outros produtos, como recursos do Azure Marketplace e recursos de origem da Aplicação, selecione **Custos Recorrentes**.

   [![Captura de ecrã que mostra a opção para alterar a secção de fatura](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na página, clique nas reticências (três pontos) para a subscrição ou produto que quer associar a uma nova secção de fatura. Selecione **Alterar seleção de fatura**.

5. Selecione a nova secção de fatura na lista pendente. A lista pendente mostra apenas as secções de fatura que estão associadas ao mesmo perfil de faturação da secção de fatura existente.

    [![Captura de ecrã que mostra a seleção da nova secção da fatura](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selecione **Guardar**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Questões a considerar ao adicionar novos perfis de faturação

### <a name="azure-usage-charges-may-be-impacted"></a>Os custos de utilização do Azure podem ser afetados

Na conta de faturação de um Contrato de Cliente Microsoft, a utilização do Azure é agregada mensalmente para cada perfil de faturação. Os preços dos recursos do Azure com preços em escalões são determinados separadamente com base na utilização em cada perfil de faturação. A utilização não é agregada nos perfis de faturação ao calcular o preço, o que pode afetar o custo geral da utilização do Azure para contas com vários perfis de faturação.

Vejamos um exemplo de como os custos variam para dois cenários. Os preços utilizados nos cenários são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure.

#### <a name="you-only-have-one-billing-profile"></a>Tem apenas um perfil de faturação.

Vamos supor que está a utilizar um armazenamento de blobs de blocos do Azure, que custa 0,00184 USD por GB para os primeiros 50 terabytes (TB) e, depois, 0,00177 por GB para os próximos 450 terabytes (TB). Utilizou 100 TB nas subscrições faturadas no perfil de faturação. Eis quanto lhe seria cobrado.

|  Preços do escalão (USD) |Quantidade | Quantidade (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0   |
|1,77 por TB para os próximos 450 TB/mês    |  50 TB         | 88,5   |
|Total     |     100 TB  | 180,5

O total de custos pela utilização de 100 TB de dados neste cenário é de **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Tem vários perfis de faturação.

Vamos, agora, supor que criou outro perfil de faturação e utilizou 50 GB nas subscrições faturadas no primeiro perfil de faturação e 50 GB nas subscrições faturadas no segundo perfil de faturação. Eis quanto lhe seria cobrado.

`Charges for the first billing profile`

|  Preços do escalão (USD) |Quantidade | Quantidade (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0,0  |
|Total     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Preços do escalão (USD) |Quantidade | Quantidade (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0,0  |
|Total     |     50 TB  | 92,0

O total de custos pela utilização de 100 TB de dados neste cenário é de **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Os benefícios de reserva do Azure podem não se aplicar a todas as subscrições

As reservas do Azure de âmbito partilhado aplicam-se a subscrições num único perfil de faturação e não são partilhadas entre os perfis de faturação.

![Gráfico informativo referente à aplicação de reserva para diferentes estruturas de contas de faturação](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na imagem acima, a Contoso tem duas subscrições. O benefício de Reserva do Azure aplica-se de forma diferente consoante a forma como a conta de faturação está estruturada. No cenário à esquerda, o benefício de reserva aplica-se às duas subscrições a faturar no perfil de faturação de engenharia. No cenário à direita, o benefício de reserva só é aplicado à subscrição 1, uma vez que é a única subscrição a ser faturada no perfil de faturação de engenharia.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft](create-subscription.md)
- [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Obter a propriedade da faturação de subscrições do Azure de outros utilizadores noutras contas de faturação](mca-request-billing-ownership.md)
