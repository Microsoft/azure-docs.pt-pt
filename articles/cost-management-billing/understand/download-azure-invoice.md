---
title: Ver e transferir a sua fatura do Azure
description: Descreve como ver e transferir a sua fatura do Azure.
keywords: faturação, transferência da fatura, fatura do azure, utilização do azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: banders
ms.openlocfilehash: 4e77b167f00e2cfa3838439143c6074bd4122976
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191255"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Pode transferir a sua fatura no [portal do Azure](https://portal.azure.com/) ou recebê-la por e-mail. Se for cliente do Azure com um Contrato Enterprise (cliente EA), não poderá transferir a fatura da sua organização. Em vez disso, as faturas são enviadas para a pessoa que está configurada para receber as faturas da inscrição.

## <a name="when-invoices-are-generated"></a>Quando as faturas são geradas

As faturas são geradas de acordo com o tipo de conta de faturação. São criadas faturas para as contas de faturação do Microsoft Online Service Program (MOSP), do Contrato de Cliente Microsoft (MCA) e do Contrato de Parceiro da Microsoft (MPA). Também são geradas para contas de faturação do Contrato Enterprise (EA). No entanto, estas últimas não são apresentadas no portal do Azure.

Para saber mais sobre as contas de faturação e identificar o tipo da sua conta de faturação, veja [Ver as contas de faturação no portal do Azure](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Faturas das contas de faturação do MOSP

É criada uma conta de faturação do MOSP quando se inscreve no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Os clientes em determinadas regiões que se inscrevem através do site do Azure numa [conta com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) podem ter uma conta de faturação do MCA.

Se não souber ao certo o seu tipo de conta de faturação, veja [Ver o tipo de conta de faturação](../manage/view-all-accounts.md#check-the-type-of-your-account) antes de seguir as instruções deste artigo. 

As contas de faturação do MOSP podem ter as seguintes faturas:

**Custos de serviço do Azure** - é gerada uma fatura para cada subscrição do Azure que contenha recursos do Azure que essas subscrições utilizem. A fatura inclui custos relativos a um período de faturação. O período de faturação é determinado pelo dia do mês no qual a subscrição foi criada.

Por exemplo, o João cria *Sub. Azure 01* a 5 de março e *Sub. Azure 02* a 10 de março. A fatura de *Sub. Azure 01* terá custos desde o quinto dia de um mês até ao quarto dia do mês seguinte. A fatura de *Sub. Azure 02* terá custos desde o décimo dia de um mês até ao nono dia do mês seguinte. Geralmente, as faturas de todas as subscrições do Azure são geradas no dia do mês em que a conta foi criada, mas podem ter um atraso de dois dias. Neste exemplo, se o João tiver criado a conta dele a 2 de fevereiro, as faturas relativas a *Sub. Azure 01* e a *Sub. Azure 02* serão, regra geral, geradas no segundo dia de cada mês, mas podem ter um atraso de dois dias.

**Azure Marketplace, reservas e VMs Spot** - é gerada uma fatura para reservas, produtos do Marketplace e VMs Spot compradas com uma subscrição. A fatura mostra os custos respetivos do mês anterior. Por exemplo, o João comprou uma reserva a 1 de março e outra a 30 de março. Será gerada uma única fatura para ambas as reservas em abril. A fatura do Azure Marketplace, das reservas e das VMs Spot são sempre geradas por volta do nono dia do mês.

Se utilizar um cartão de crédito para pagar o Azure e comprar uma reserva, o Azure gera uma fatura imediatamente. No entanto, se for faturado por uma fatura, a reserva é cobrada na fatura do mês seguinte.

**Plano de suporte do Azure** - é gerada uma fatura para a subscrição do plano de suporte cada mês. A primeira fatura é gerada no dia da compra ou até dois dias depois. As faturas do plano de suporte são normalmente geradas no mesmo dia do mês em que a conta foi criada, mas podem ter um atraso de até dois dias.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Transferir a fatura da subscrição do Azure do MOSP

Só é gerada uma fatura para subscrições que fazem parte de uma conta de faturação do MOSP. [Verifique se tem acesso a uma conta do MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Para transferir a fatura de uma subscrição, tem de a função de administrador de conta. Os utilizadores que tenham a função de proprietário, contribuidor ou leitor podem transferir a fatura da conta, caso o administrador da mesma lhes tenha dado permissão. Para obter mais informações, veja [Permitir que os utilizadores transfiram faturas](../manage/manage-billing-access.md#opt-in).

1. Selecione a sua subscrição na [Página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione **Faturas** na secção de faturação.  
    ![Captura de ecrã que mostra um utilizador a selecionar a opção "faturas" relativa a uma subscrição](./media/download-azure-invoice/select-subscription-invoice.png)
1. Selecione **Transferir** para transferir uma versão PDF da fatura e, em seguida, selecione **Transferir** na secção de faturas.  
    ![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. Também pode transferir uma discriminação diária das quantidades consumidas e dos custos ao selecionar **Transferir** na secção de detalhes da utilização. A preparação do ficheiro CSV pode demorar alguns minutos.  
    ![Captura de ecrã que mostra a página Transferir fatura e utilização](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Para obter mais informações sobre a fatura, veja [Compreender a fatura do Microsoft Azure](../understand/review-individual-bill.md). Para obter ajuda com a gestão dos custos, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Transferir a fatura do plano de suporte do MOSP

Só é gerada uma fatura para subscrições de plano de suporte que fazem parte de uma conta de faturação do MOSP. [Verifique se tem acesso a uma conta do MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account).

Para transferir a fatura de uma subscrição de plano de suporte, tem de a função de administrador de conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.  
    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/download-azure-invoice/search-cmb.png)
1. Selecione **Faturas** no lado esquerdo.
1. Selecione a subscrição do plano de suporte e, em seguida, **Transferir**.  
    [![Captura de ecrã que mostra a lista dos perfis de faturação](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Selecione **Transferir** para transferir uma versão PDF da fatura.  
    ![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-the-your-subscription-invoice"></a>Permitir que outras pessoas transfiram as faturas da subscrição

Para transferir uma fatura:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador da conta da subscrição.

2.  Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/download-azure-invoice/search-cmb.png)

3.  Selecione **Faturas** no lado esquerdo.

4.  Selecione a sua subscrição do Azure e, em seguida, clique em **Permitir que outros transfiram a fatura**.

    [![Captura de ecrã que mostra a seleção do acesso à fatura](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Selecione **Ativado** e **Guardar** na página superior da página.  
    ![Captura de ecrã que mostra a seleção de Ativado para acesso à fatura](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Obter a fatura da subscrição do MOSP por e-mail

Tem de ter a função de administrador de conta numa subscrição ou num plano de suporte para poder receber as respetivas faturas por e-mail. As faturas por e-mail só estão disponíveis para subscrições e planos de suporte, não para reservas nem compras do Azure Marketplace. Depois de ativar, pode adicionar outros destinatários, que também receberão as faturas por e-mail.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Procure **Cost Management + Faturação**.  
3.  Selecione **Faturas** no lado esquerdo.
4.  Selecione a subscrição do Azure ou a subscrição do plano de suporte e, em seguida, **Receber fatura por e-mail**.  
    [![Captura de ecrã que mostra a lista dos perfis de faturação](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Clique em **Fatura por e-mail** e aceite os termos.  
    ![Captura de ecrã que mostra o passo 2 do fluxo de optar por receber](./media/download-azure-invoice/invoicearticlestep02.png)
6. A fatura é enviada para o seu e-mail preferencial para comunicações. Selecione **Atualizar perfil** para atualizar o e-mail.  
    ![Captura de ecrã que mostra o passo 3 do fluxo de optar por receber](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoices"></a>Partilhar faturas da subscrição e do plano de suporte

Se quiser, pode partilhar as faturas da sua subscrição e do seu suporte de plano todos os meses com a equipa de contabilidade ou enviá-las para outro endereço de e-mail que tenha.

1. Siga os passos em [Receber as faturas da subscrição e do plano de suporte por e-mail](#get-mosp-subscription-invoice-in-email) e selecione **Configurar destinatários**.  
    ![Captura de ecrã que mostra um utilizador a selecionar a configuração de destinatários](./media/download-azure-invoice/invoice-article-step03.png)
1. Introduza um endereço de e-mail e selecione **Adicionar destinatário**. Pode adicionar vários endereços de e-mail.  
    ![Captura de ecrã que mostra um utilizador a adicionar mais destinatários](./media/download-azure-invoice/invoice-article-step04.png)
1. Depois de adicionar todos os endereços de e-mail, selecione **Concluído** na parte inferior do ecrã.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faturas das contas de faturação do MCA e do MPA

É criada uma conta de faturação do MCA se a sua organização trabalhar com um representante da Microsoft para assinar o MCA. Alguns clientes em determinadas regiões que se inscrevem através do site do Azure numa [conta com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) podem ter uma conta de faturação do MCA também. Para obter mais informações, veja [Introdução à conta de faturação do MCA](../understand/mca-overview.md).

É criada uma conta de faturação do MPA para os parceiros Fornecedores de Soluções Cloud (CSP) para gestão dos clientes na nova experiência comercial. Os parceiros precisam de ter, pelo menos, um cliente com um [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para gerir a conta de faturação no portal do Azure. Para obter mais informações, veja [Introdução à conta de faturação do MPA](../understand/mpa-overview.md).

Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém os custos para todas as subscrições do Azure e outras compras do mês anterior. Por exemplo, o João criou *Sub. Azure 01* a 5 de março e *Sub. Azure 02* a 10 de março. Comprou a subscrição *Suporte do Azure 01* a 28 de março com o *perfil de faturação 01*. O João vai receber uma fatura no início de abril, que incluirá custos relativos às subscrições do Azure e ao plano de suporte.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Transferir faturas de perfil de faturação do MCA ou do MPA

Para transferir a fatura de um perfil de faturação no portal do Azure, tem de ter a função de proprietário, contribuidor, leitor ou gestor de faturas no perfil. Os utilizadores que tenham a função de proprietário, contribuidor ou leitor numa conta de faturação podem transferir faturas de todos os perfis de faturação na conta.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).

2.  Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/download-azure-invoice/search-cmb.png)

3. Selecione **Faturas** no lado esquerdo.

    [![Captura de ecrã que mostra a página de faturas de uma conta de faturação do MCA](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. Na tabela Faturas, selecione a fatura que pretende transferir.

5. Clique no botão **Transferir PDF da fatura** na parte superior da página.

    [![Captura de ecrã que mostra a transferência do PDF da fatura](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. Também pode transferir a discriminação diária das quantidades consumidas e despesas estimadas ao clicar em **Transferir utilização do Azure**. A preparação do ficheiro CSV pode demorar alguns minutos.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Receber a fatura do perfil de faturação por e-mail

Para atualizar a preferência de receção da fatura por e-mail, tem de ter a função de proprietário ou contribuidor no perfil de faturação ou na respetiva conta de faturação. Depois de ativar a receção por e-mail, todos os utilizadores com a função de proprietário, contribuidor, leitor e gestor de faturas num perfil de faturação recebem a fatura por e-mail. 

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
1.  Procure **Cost Management + Faturação**.  
1.  Selecione **Fatura** no lado esquerdo e, em seguida, **Enviar fatura por e-mail** na parte superior da página.  
    [![Captura de ecrã que mostra a página de faturas de uma conta de faturação do MCA](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Se tiver vários perfis de faturação, selecione um e, em seguida, **Optar por receber**.  
    ![Captura de ecrã que mostra a página de faturas de uma conta de faturação do MCA](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Selecione **Atualizar**.

2.  Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/download-azure-invoice/search-cmb.png)

3.  Selecione **Fatura** no lado esquerdo e, em seguida, **Enviar fatura por e-mail** na parte superior da página.

    [![Captura de ecrã que mostra a página de faturas de uma conta de faturação do MCA](./media/download-azure-invoice/mca-billingprofile-select-emailinvoice.png)](./media/download-azure-invoice/mca-billingprofile-select-emailinvoice-zoomed-in.png)

4.  Se tiver vários perfis de faturação, selecione um e, em seguida, **Optar por receber**.

Pode conceder acesso a outras pessoas para ver, transferir e pagar faturas ao atribuir-lhes a função de gestor de faturas para um perfil de faturação do MCA ou do MPA. Se tiver optado por receber a fatura por e-mail, os utilizadores também receberão as faturas por e-mail.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.  
1. Selecione **Perfis de faturação** no lado esquerdo. Na lista de perfis de faturação, selecione um perfil de faturação para o qual pretende atribuir uma função de gestor de faturas.  
   ![Captura de ecrã que mostra a lista dos perfis de faturação](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Selecione **Controlo de Acesso (IAM)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.  
    ![Captura de ecrã que mostra a página do controlo de acesso](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Na lista pendente Função, selecione **Gestor de Faturas**. Introduza o endereço de e-mail do utilizador para lhe dar acesso. Selecione **Guardar** para atribuir a função.  
   ![Captura de ecrã que mostra a adição de um utilizador como um gestor de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)

1. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa de uma subscrição no portal](./media/download-azure-invoice/search-cmb.png)

1. Selecione **Perfis de faturação** no lado esquerdo. Na lista de perfis de faturação, selecione um perfil de faturação para o qual pretende atribuir uma função de gestor de faturas.

   ![Captura de ecrã que mostra a lista dos perfis de faturação](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)

1. Selecione **Controlo de Acesso (IAM)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de ecrã que mostra a página do controlo de acesso](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)

1. Na lista pendente Função, selecione **Gestor de Faturas**. Introduza o endereço de e-mail do utilizador para lhe dar acesso. Selecione **Guardar** para atribuir a função.

   [![Captura de ecrã que mostra a adição de um utilizador como um gestor de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   
   
##  <a name="why-you-might-not-see-an-invoice"></a>Qual o motivo pelo qual pode não ver uma fatura

<a name="noinvoice"></a>

Podem existir vários motivos para não ver uma fatura:

- A fatura ainda não está pronta
    
    - Passaram menos de 30 dias desde o dia em que subscreveu o Azure. 

    - O Azure fatura-o alguns dias depois do fim do período de faturação. Portanto, uma fatura pode ainda não ter sido gerada.

- Não tem permissão para ver faturas. 
    
    - Se tiver uma conta de faturação do MCA ou do MPA, tem de ter a função de proprietário, contribuidor, leitor ou gestor de faturas no perfil de faturação ou uma função de proprietário, contribuidor ou leitor na conta de faturação para ver as faturas. 
    
    - Relativamente a outras contas de faturação, se não for o Administrador de Conta, pode não ver as faturas.

- A sua conta não suporta faturas.

    - Se tiver uma conta de faturação do Programa Microsoft Online Services (MOSP) e se tiver inscrito numa Conta Gratuita do Azure ou numa subscrição com montante de crédito mensal, só recebe uma fatura quando exceder esse montante.

    - Se tiver uma conta de faturação para um Contrato de Cliente Microsoft (MCA) ou Contrato de Parceiro da Microsoft (MPA), receberá sempre uma fatura.

- Tem acesso à fatura através de uma das suas outras contas.

    - Esta situação acontece normalmente quando clica numa ligação no e-mail a pedir-lhe para ver a sua fatura no portal. Ao clicar na liga, verá uma mensagem de erro: `We can't display your invoices. Please try again`. Verifique se tem sessão iniciada com o endereço de e-mail que tem permissões para ver as faturas.

- Tem acesso à fatura através de uma identidade diferente. 

    - Alguns clientes têm duas identidades com o mesmo endereço de e-mail, uma conta profissional e uma conta Microsoft. Normalmente, apenas uma das identidades tem permissões para ver faturas. Se iniciarem sessão com a identidade que não tem permissão, não verão as faturas. Verifique se está a utilizar a identidade correta para iniciar sessão.

- Iniciou sessão com o inquilino do Azure Ative Directory (AAD) incorreto. 

    - A sua conta de faturação está associada a um inquilino do AAD. Se tiver sessão iniciada num inquilino incorreto, não verá a fatura para as subscrições na sua conta de faturação. Verifique se iniciou sessão no inquilino do Azure Ative Directory (AAD) correto. Se não tiver sessão iniciada no inquilino correto, utilize o seguinte procedimento para trocar o inquilino no portal do Azure:

        1. Selecione o seu e-mail na parte superior direita da página.

        2. Selecione **Trocar diretório**.

           ![Captura de ecrã que mostra a seleção de Trocar diretório no portal](./media/download-azure-invoice/select-switch-directory.png)

        3. Selecione um diretório na secção **Todos os diretórios**.

           ![Captura de ecrã que mostra a seleção de um diretório no portal](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos, veja:

- [Ver e transferir a utilização e os custos do Microsoft Azure](download-azure-daily-usage.md)
- [Understand your bill for Microsoft Azure](review-individual-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Compreender os termos na fatura do Azure](understand-invoice.md)

Se tiver um MCA, veja:

- [Compreender os custos na fatura para o seu perfil de faturação](review-customer-agreement-bill.md)
- [Compreender os termos na fatura para o seu perfil de faturação](mca-understand-your-invoice.md)
- [Compreender o ficheiro de utilização e de custos do Azure para o seu perfil de faturação](mca-understand-your-usage.md)