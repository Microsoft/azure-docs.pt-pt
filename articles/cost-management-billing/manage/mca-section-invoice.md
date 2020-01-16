---
title: Organize sua fatura com base em suas necessidades – Azure
description: Saiba como organizar o custo em sua fatura.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991064"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organize os custos personalizando sua conta de cobrança

Sua conta de cobrança do contrato de cliente da Microsoft fornece flexibilidade para organizar seus custos com base em suas necessidades, seja por departamento, projeto ou ambiente de desenvolvimento. 

Este artigo descreve como você pode usar o portal do Azure para organizar seus custos. Ele se aplica a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso ao Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Estruturar sua conta com perfis de cobrança e seções de fatura

Na conta de cobrança de um contrato de cliente da Microsoft, você usa perfis de cobrança e seções de fatura para organizar seus custos.

![Captura de tela que mostra a hierarquia de cobrança do contrato do cliente da Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Perfil de faturação

Um perfil de cobrança representa uma fatura e as informações de cobrança relacionadas, como métodos de pagamento e endereço de cobrança. Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém encargos para o uso do Azure e outras compras do mês anterior.

Um perfil de cobrança é criado automaticamente junto com sua conta de cobrança quando você se inscreve no Azure. Você pode criar perfis de cobrança adicionais para organizar seus custos em várias notas fiscais mensais. 

> [!IMPORTANT]
>
> A criação de perfis de cobrança adicionais pode afetar o custo geral. Para obter mais informações, consulte [coisas a serem consideradas ao adicionar novos perfis de cobrança](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Secção de fatura

Uma seção de fatura representa um agrupamento de custos em sua fatura. Uma seção de fatura é criada automaticamente para cada perfil de cobrança em sua conta. Você pode criar seções adicionais para organizar seus custos com base em suas necessidades. Cada seção de fatura é exibida na fatura com os encargos incorridos nesse mês. 

A imagem abaixo mostra uma fatura com duas seções de fatura – engenharia e marketing. Os encargos de resumo e de detalhes de cada seção são exibidos na fatura. Os preços mostrados na imagem são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure. 

![Imagem mostrando uma fatura com seções](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Estrutura da conta de cobrança para cenários comuns

Esta seção descreve os cenários comuns para organizar os custos e as estruturas de conta de cobrança correspondentes:

|Cenário  |Estrutura  |
|---------|---------|
|A tomada se inscreve no Azure e precisa de uma fatura mensal única. | Um perfil de cobrança e uma seção de fatura. Essa estrutura é configurada automaticamente para o Jack quando ele se inscreve no Azure e não requer etapas adicionais. |

![Gráfico de informações para um cenário de cobrança simples](./media/mca-section-invoice/organize-billing-scenario1.png)

|Cenário  |Estrutura  |
|---------|---------|
|A contoso é uma pequena organização que precisa de uma fatura mensal única, mas que agrupa custos por seus departamentos, marketing e finanças.  | Um perfil de cobrança para a Contoso e uma seção de fatura para departamentos de marketing e finanças. |

![Gráfico de informações para um cenário de cobrança simples](./media/mca-section-invoice/organize-billing-scenario2.png)

|Cenário  |Estrutura  |
|---------|---------|
|A Fabrikam é uma organização de médio porte que precisa de faturas separadas para seus departamentos de engenharia e marketing. Para o departamento de engenharia, eles desejam agrupar os custos por ambientes – produções e desenvolvimento.  | Um perfil de cobrança para departamentos de marketing e finanças. Para o departamento de marketing, uma seção de fatura para o ambiente de produção e desenvolvimento. |

![Gráfico de informações para um cenário de cobrança simples](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Criar uma nova seção de fatura

Para criar uma secção de fatura, precisa de ser **proprietário do perfil de faturação** ou **contribuidor do perfil de faturação**. Para obter mais informações, consulte [gerenciar seções de fatura para o perfil de cobrança](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-section-invoice/search-cmb.png)

3. Selecione **perfis de cobrança** no painel esquerdo. Na lista, selecione um perfil de cobrança. A nova seção será exibida na fatura do perfil de cobrança selecionado. 

   [![captura de tela que mostra a lista de perfis de cobrança](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selecione **seções de fatura** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![captura de tela que mostra a adição de seções de fatura](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Insira um nome para a seção fatura. 

   [![captura de tela que mostra a página de criação da seção fatura](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selecione **Criar**.

## <a name="create-a-new-billing-profile"></a>Criar um novo perfil de cobrança

Para criar um perfil de cobrança, você precisa ser um **proprietário da conta de cobrança** ou um **colaborador da conta de cobrança**. Para obter mais informações, consulte [gerenciar perfis de cobrança para a conta de cobrança](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> A criação de perfis de cobrança adicionais pode afetar o custo geral. Para obter mais informações, consulte [coisas a serem consideradas ao adicionar novos perfis de cobrança](#things-to-consider-when-adding-new-billing-profiles).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-section-invoice/search-cmb.png)

3. Selecione **perfis de cobrança** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Se você não vir o botão Adicionar na página perfil de cobrança, o recurso não estará disponível para sua conta. Atualmente, ele só está disponível para contas que foram configuradas enquanto trabalham com um representante da Microsoft.

4. Preencha o formulário e clique em **Criar**.

   [![captura de tela que mostra a página de criação do perfil de cobrança](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Campo  |Definição  |
    |---------|---------|
    |Nome     | Um nome de exibição que ajuda a identificar facilmente o perfil de cobrança no portal do Azure.  |
    |Número da OC    | Um número de ordem de compra opcional. O número da OC será exibido nas notas fiscais geradas para o perfil de cobrança. |
    |Endereço para faturação   | O endereço de cobrança será exibido nas notas fiscais geradas para o perfil de cobrança. |
    |Fatura de email   | Marque a caixa fatura de email para receber as notas fiscais deste perfil de cobrança por email. Se você não aceitar, poderá exibir e baixar as notas fiscais no portal do Azure.|

5. Selecione **Criar**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Encargos de link para seções de fatura e perfis de cobrança

Depois de personalizar sua conta de cobrança com base em suas necessidades, você poderá vincular assinaturas e outros produtos à seção da fatura desejada e ao perfil de cobrança.

### <a name="link-a-new-subscription"></a>Vincular uma nova assinatura

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Escreva **Subscrições** na caixa de pesquisa.

   [![captura de tela que mostra a pesquisa no portal para assinatura](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selecione **Adicionar** na parte superior da página.

   ![Captura de ecrã que mostra o botão Adicionar na vista Subscrições](./media/mca-section-invoice/subscription-add.png)

4. Se você tiver acesso a várias contas de cobrança, selecione sua conta de cobrança do contrato de cliente da Microsoft.

   ![Captura de ecrã que mostra o botão Adicionar na vista Subscrições](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selecione o perfil de cobrança que será cobrado pelo uso da assinatura. Os encargos para o uso do Azure e outras compras desta assinatura serão cobrados na fatura do perfil de cobrança selecionado.

6. Selecione a seção fatura para vincular os encargos da assinatura. Os encargos serão exibidos nesta seção na fatura do perfil de cobrança.

7. Selecione um plano do Azure e insira um nome amigável para sua assinatura. 

9. Clique em **Criar**.  

### <a name="link-existing-subscriptions-and-products"></a>Vincular assinaturas e produtos existentes

Se você tiver assinaturas do Azure existentes ou outros produtos, como o Azure Marketplace e os recursos de origem do aplicativo, você poderá movê-los de sua seção de fatura existente para outra seção da fatura para reorganizar os custos. 

> [!IMPORTANT]
>
> Assinaturas e outros produtos só podem ser movidos entre seções de fatura que pertencem ao mesmo perfil de cobrança. Não há suporte para a movimentação de assinaturas e produtos entre seções de faturas em perfis de cobrança diferentes.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa de uma subscrição no portal](./media/mca-section-invoice/search-cmb.png)

3. Para vincular uma assinatura a uma nova seção fatura, selecione **assinaturas do Azure** no lado esquerdo da tela. Para outros produtos, como o Azure Marketplace e recursos de origem do aplicativo, selecione **encargos recorrentes**.

   [![captura de tela que mostra a opção de alterar a seção de faturas](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na página, clique nas reticências (três pontos) para a assinatura ou produto que você deseja vincular a uma nova seção de fatura. Selecione a **seção alterar nota fiscal**.

5. Selecione a seção nova fatura no menu suspenso. A lista suspensa mostrará apenas seções de fatura associadas ao mesmo perfil de cobrança da seção fatura existente.

    [![captura de tela que mostra a seleção de uma nova seção de fatura](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selecione **Guardar**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Itens a serem considerados ao adicionar novos perfis de cobrança

### <a name="azure-usage-charges-may-be-impacted"></a>Os encargos de uso do Azure podem ser afetados

Em sua conta de cobrança para um contrato de cliente da Microsoft, o uso do Azure é agregado mensalmente para cada perfil de cobrança. Os preços dos recursos do Azure com preços em camadas são determinados com base no uso de cada perfil de cobrança separadamente. O uso não é agregado nos perfis de cobrança ao calcular o preço. Isso pode afetar o custo geral do uso do Azure para contas com vários perfis de cobrança.

Vejamos um exemplo de como os custos variam para dois cenários. Os preços usados nos cenários são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure.

#### <a name="you-only-have-one-billing-profile"></a>Você tem apenas um perfil de cobrança.

Vamos supor que você esteja usando o armazenamento de blobs de blocos do Azure, que custa US $00184 por GB para os primeiros 50 terabytes (TB) e, em seguida, 00177 por GB para os próximos 450 TB (terabytes). Você usou 100 TB nas assinaturas que são cobradas no seu perfil de cobrança, aqui está o quanto você seria cobrado.

|  Preço da camada (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0   |
|1,77 por TB para os próximos 450 TB/mês    |  50 TB         | 88,5   |
|Total     |     100 TB  | 180,5

O total de encargos para usar 100 TB de dados neste cenário é **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Você tem vários perfis de cobrança.

Agora, vamos supor que você criou outro perfil de cobrança e usou 50 GB por meio de assinaturas que são faturadas para o primeiro perfil de cobrança e 50 GB por meio de assinaturas que são cobradas para o segundo perfil de cobrança, aqui está o quanto você seria cobrado.

`Charges for the first billing profile`

|  Preço da camada (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0.0  |
|Total     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Preço da camada (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0.0  |
|Total     |     50 TB  | 92,0 

O total de encargos para usar 100 TB de dados nesse cenário é **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Os benefícios de reserva do Azure podem não se aplicar a todas as assinaturas

As reservas do Azure com escopo compartilhado são aplicadas a assinaturas em um único perfil de cobrança e não são compartilhadas entre os perfis de cobrança. 

![Gráfico de informações para aplicativo de reserva para uma estrutura de conta de cobrança diferente](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na imagem acima, a contoso tem duas assinaturas. O benefício de reserva do Azure é aplicado de maneira diferente, dependendo de como a conta de cobrança é estruturada. No cenário à esquerda, o benefício de reserva é aplicado às duas assinaturas sendo cobradas no perfil de cobrança de engenharia. No cenário à direita, o benefício de reserva será aplicado somente à assinatura 1, já que é a única assinatura sendo cobrada no perfil de cobrança de engenharia. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft](create-subscription.md)
- [Gerir funções de faturação no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Obter a propriedade da faturação de subscrições do Azure de outros utilizadores noutras contas de faturação](mca-request-billing-ownership.md)
