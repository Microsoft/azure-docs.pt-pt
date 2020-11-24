---
title: Transferir a propriedade de faturação de uma subscrição do Azure
description: Descreve como transferir a propriedade de faturação de uma subscrição do Azure para outra conta.
keywords: transferir subscrição do azure, subscrição de transferência do azure, mover subscrição do azure para outra conta, alterar proprietário da subscrição do azure, transferir subscrição do azure para outra conta, faturação de transferência do azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: ab71a7fa3d3f92ecf3e1f73835e41fa7170130e6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636610"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de faturação de uma subscrição do Azure para outra conta

Este artigo mostra os passos necessários para transferir a propriedade de faturação de uma subscrição do Azure para outra conta. Antes de transferir a propriedade de faturação de uma subscrição, leia [Sobre a transferência da propriedade de faturação de uma subscrição do Azure](../understand/subscription-transfer.md).

Se quiser manter a propriedade de faturação, mas alterar o tipo da subscrição, veja [Mudar a sua subscrição do Azure para outra oferta](switch-azure-offer.md). Para controlar quem tem acesso aos recursos na subscrição, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

Se for cliente do Contrato Enterprise (EA), o seu administrador empresarial poderá transferir a propriedade de faturação das suas subscrições entre contas. Para obter mais informações, veja [Alterar a propriedade de uma subscrição ou conta do Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Só o administrador de faturação de uma conta pode transferir a propriedade de uma subscrição.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferir a propriedade de faturação de uma subscrição do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como o administrador da conta de faturação que possui a subscrição que quer transferir. Se não souber ao certo se é administrador ou se precisar de saber quem este é, veja [Determinar o administrador de faturação da conta](../understand/subscription-transfer.md#whoisaa).
1. Procure **Cost Management + Faturação**.  
   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Selecione **Subscrições** no painel à esquerda. Consoante o seu acesso, pode ter de selecionar um âmbito da faturação e, em seguida, selecionar **Subscrições** ou **Subscrições do Azure**.
1. Selecione **Transferir propriedade da faturação**  da subscrição que quer transferir.  
   ![Selecionar a subscrição a transferir](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Insira o endereço de e-mail de um utilizador que seja um administrador de faturação da conta e que será o novo proprietário da subscrição.
1. Se estiver a transferir a sua subscrição para uma conta noutro inquilino do Azure AD, selecione se pretende mover a subscrição para o inquilino da nova conta. Para obter mais informações, veja [Transferir a subscrição para uma conta noutro inquilino do Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Se optar por mover a subscrição para o inquilino do Microsoft Azure AD novo da conta, todas as [atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md) para aceder aos recursos na subscrição serão permanentemente removidas. Apenas o utilizador na nova conta que aceite o seu pedido de transferência terá acesso para gerir recursos na subscrição. Como alternativa, pode desmarcar a opção **Inquilino da Subscrição do Azure AD** para transferir a propriedade de faturação sem mover a subscrição para o inquilino da nova conta. Se o fizer, as atribuições de funções do Azure atuais para aceder aos recursos do Azure serão preservadas.  
    ![Enviar página de transferência](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Selecione **Enviar pedido de transferência**.
1. O utilizador recebe um e-mail com instruções para rever o pedido de transferência.  
   ![E-mail de transferência de subscrição enviado para o destinatário](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Para aprovar o pedido de transferência, o utilizador seleciona a ligação no e-mail e segue as instruções. Em seguida, o utilizador seleciona um método de pagamento que será utilizado para pagar a subscrição. Se o utilizador não tiver uma conta do Azure, precisará de se inscrever numa nova conta.  
   ![Página Web de transferência de primeira subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Página Web de transferência de segunda subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Página Web de transferência de terceira subscrição](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Êxito! A subscrição foi transferida.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Transferir uma subscrição para outra conta de inquilino do Azure AD

Quando se inscreve no Azure, é criado um inquilino do Azure Active Directory (AD). O inquilino representa a sua conta. O inquilino é utilizado para gerir o acesso às respetivas subscrições e recursos.

Quando cria uma nova subscrição, esta é alojada no inquilino do Azure AD da sua conta. Se quiser conceder a outras pessoas acesso à sua subscrição ou aos seus recursos, precisa de convidá-las para aderirem ao seu inquilino. Fazê-lo ajuda a controlar o acesso às suas subscrições e recursos.

Quando transfere a propriedade de faturação da sua subscrição para uma conta noutro inquilino do Azure AD, pode mover a subscrição para o inquilino da nova conta. Se o fizer, deixarão de ter acesso todos os utilizadores, grupos ou principais de serviço que tiverem [atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md) para gerir subscrições e respetivos recursos. Apenas o utilizador na nova conta que aceite o seu pedido de transferência terá acesso para gerir os recursos. O novo proprietário tem de adicionar manualmente estes utilizadores à subscrição para conceder acesso ao utilizador que o perdeu. Para obter mais informações, veja [Transferir uma subscrição do Azure para outro diretório do Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Transferir subscrições do Visual Studio e do Partner Network

As subscrições do Visual Studio e do Microsoft Partner Network têm associadoum crédito do Azure mensal recorrente. Quando transfere estas subscrições, o seu crédito não está disponível na conta de faturação de destino. A subscrição utiliza o crédito na conta de faturação de destino. Por exemplo, Bernardo transfere uma subscrição do Visual Studio Enterprise para a conta de Júlia no dia 9 de setembro e a Júlia aceita a transferência. Após a transferência estar concluída, a subscrição começará a utilizar o crédito na conta de Júlia. O crédito será reposto ao nono dia do mês.

## <a name="next-steps-after-accepting-billing-ownership"></a>Próximos passos após aceitar a propriedade da faturação

Se aceitou a propriedade de faturação de uma subscrição do Azure, recomendamos que analise os próximos passos:

1. Reveja e atualize Azure Administrador de Serviço, Coadministradores e as atribuições de funções do Azure. Para saber mais, veja [Adicionar ou alterar os administradores da subscrição](add-change-subscription-administrator.md) e [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas aos serviços desta subscrição, incluindo:
   1. Os certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão para o Azure](../../cloud-services/cloud-services-certs-create.md)
   1. As chaves de acesso para serviços, como o Armazenamento. Para obter mais informações, veja [Acerca das contas de armazenamento do Azure](../../storage/common/storage-account-create.md)
   1. As credenciais de Acesso Remoto para serviços, como as Máquinas Virtuais do Azure.
1. Se estiver a trabalhar com um parceiro, considere atualizar o ID do parceiro nesta subscrição. Pode atualizar a ID do parceiro no [portal do Azure](https://portal.azure.com). Para obter mais informações, veja [Ligar uma ID de parceiro às suas contas do Azure](link-partner-id.md)

## <a name="troubleshooting"></a>Resolução de problemas

Se estiver com dificuldades em transferir subscrições, utilize as seguintes informações de resolução de problemas.

### <a name="the-transfer-subscription-option-is-unavailable"></a>A opção “Transferir subscrição” não está disponível

<a name="no-button"></a> 

A transferência de subscrição personalizada não está disponível para a sua conta de faturação. Atualmente, não há suporte para transferir a propriedade de faturação de subscrições em contas Contrato Enterprise (EA) no portal do Azure. Além disso, as contas do Contrato de Cliente Microsoft criadas enquanto trabalham com um representante da Microsoft não suportam a transferência de propriedade de faturação.

###  <a name="not-all-subscription-types-can-transfer"></a>Nem todos os tipos de subscrições podem ser transferidos

Nem todos os tipos de subscrição suportam a transferência de propriedade de faturação. Para ver a lista de tipos de subscrição que suportam a transferência, veja [Tipos de subscrição suportados](../understand/subscription-transfer.md#supported-subscription-types)

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Erro de acesso recusado ao tentar transferir a propriedade de faturação da subscrição

Pode ver este erro se estiver a tentar transferir uma subscrição do Plano de Microsoft Azure e não tiver a permissão necessária. Para transferir uma subscrição do plano do Microsoft Azure, precisa de ser um proprietário ou colaborador na secção Fatura para a qual a subscrição é faturada. Para obter mais informações, veja [Gerir subscrições para a secção de fatura](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Reveja e atualize Azure Administrador de Serviço, Coadministradores e as atribuições de funções do Azure. Para saber mais, veja [Adicionar ou alterar os administradores da subscrição](add-change-subscription-administrator.md) e [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](../../role-based-access-control/role-assignments-portal.md).