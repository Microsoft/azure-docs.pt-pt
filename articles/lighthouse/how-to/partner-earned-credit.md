---
title: Ligue o iD do seu parceiro para permitir que o parceiro ganhou crédito em recursos delegados
description: Saiba como associar o seu parceiro ID a receber o parceiro de crédito adquirido (PEC) sobre os recursos do cliente que gere através do Farol Azure.
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493374"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Ligue o iD do seu parceiro para permitir que o parceiro ganhou crédito em recursos delegados

Se for membro da [Microsoft Partner Network,](https://partner.microsoft.com/)pode ligar o ID do seu parceiro com as credenciais utilizadas para gerir os recursos do cliente delegado. Isto permite-lhe acompanhar o seu impacto através dos compromissos com os clientes e receber [o parceiro obtido crédito pelos serviços geridos (PEC)](/partner-center/partner-earned-credit).

Se você estiver [a bordo de clientes com ofertas de Serviço Gerido no Azure Marketplace,](publish-managed-services-offers.md)este link acontece automaticamente, utilizando o ID MPN associado à conta Partner Center utilizada para publicar as ofertas. Não são necessárias mais medidas para receber o PEC para estes clientes.

Se você [estiver a bordo clientes usando modelos de Gestão de Recursos Azure,](onboard-customer.md)você precisará tomar medidas para criar este link. Isto é feito [ligando o seu ID MPN](../../cost-management-billing/manage/link-partner-id.md) com pelo menos uma conta de utilizador no seu inquilino gerente que tem acesso a cada uma das suas subscrições a bordo.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associe o seu ID do parceiro quando estiver a bordo de novos clientes

Ao embarcar os clientes através dos modelos de gestor de recursos Azure, utilize o seguinte processo para ligar o ID do seu parceiro e permitir que o parceiro tenha ganho crédito. Precisa de conhecer o seu [parceiro MPN para](/partner-center/partner-center-account-setup#locate-your-mpn-id) completar estes passos.

Para simplificar, recomendamos a criação de uma conta principal de serviço no seu inquilino, associando-a ao seu ID MPN, concedendo-lhe acesso a todos os clientes que você está a bordo com uma [função azure incorporada que é elegível para PEC.](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)

1. [Crie uma conta principal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) de serviço no seu inquilino gerente. Para este exemplo, vamos nomear este serviço principal conta de automação PEC.
1. Utilizando essa conta principal de serviço, [ligue-se à identificação do seu parceiro](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente. Só precisas de fazer isto uma vez.
1. Quando embarcar [num cliente que utilize modelos do Azure Resource Manager,](onboard-customer.md)certifique-se de incluir uma autorização que inclua a Conta de Automação PEC como utilizador com uma [função incorporada Azure que é elegível para PEC.](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)

Ao seguir estes passos, todos os inquilinos que gere estarão associados ao seu ID do parceiro, permitindo-lhe receber PEC para esses clientes. A Conta de Automação PEC não necessita de autenticar ou realizar quaisquer ações no cliente inquilino.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adicione o seu ID do seu parceiro a clientes previamente a bordo

Se já entrou a bordo de um cliente, poderá não querer realizar outra implementação para adicionar o seu responsável pelo serviço de Conta de Automação PEC. Em vez disso, pode associar o ID mpn a uma conta de utilizador que já tenha acesso ao trabalho no inquilino desse cliente. Certifique-se de que a conta foi concedida a um [papel integrado Azure que é elegível para o PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Uma vez que a conta tenha sido [ligada ao seu parceiro ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente, poderá receber PEC para esse cliente.

## <a name="confirm-partner-earned-credit"></a>Confirme o crédito do parceiro

Pode [ver detalhes do PEC no portal Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e confirmar quais os custos que receberam o benefício do PEC.

Se seguiu os passos acima, e não vê a associação, abra um pedido de apoio no portal Azure.

## <a name="next-steps"></a>Próximos passos

- Saiba a adesão à [Rede de Parceiros da Microsoft.](/partner-center/mpn-overview)
- Saiba [como o PEC é calculado e pago.](/partner-center/partner-earned-credit-explanation)
- [Clientes a bordo](onboard-customer.md) do Farol de Azure.
