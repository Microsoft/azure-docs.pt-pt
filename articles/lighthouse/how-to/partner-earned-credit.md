---
title: Ligue o seu parceiro de ID para acompanhar o seu impacto em recursos delegados
description: Saiba como associar o seu parceiro ID a receber o parceiro de crédito adquirido (PEC) sobre os recursos do cliente que gere através do Farol Azure.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372098"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Ligue o seu parceiro de ID para acompanhar o seu impacto em recursos delegados 

Se for membro da [Microsoft Partner Network,](https://partner.microsoft.com/)pode ligar o ID do seu parceiro às credenciais utilizadas para gerir os recursos delegados do cliente, permitindo à Microsoft identificar e reconhecer parceiros que impulsionam o sucesso do cliente da Azure. Esta associação também permite que os parceiros [CSP (Fornecedor de Soluções Cloud)](/partner-center/csp-overview) recebam [crédito ganho pelo parceiro por serviços geridos (PEC)](/partner-center/partner-earned-credit) para clientes que tenham [assinado o Contrato de Cliente Microsoft (MCA)](/partner-center/confirm-customer-agreement) e que estão [ao abrigo do plano do Azure](/partner-center/azure-plan-get-started).

Para obter reconhecimento para as atividades do Azure Lighthouse, terá de [ligar o seu ID MPN](../../cost-management-billing/manage/link-partner-id.md) a pelo menos uma conta de utilizador no seu inquilino gerente, e garantir que a conta ligada tem acesso a cada uma das suas subscrições a bordo.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associe o seu ID do parceiro quando estiver a bordo de novos clientes

Utilize o seguinte processo para ligar o ID do seu parceiro (e ativar o crédito obtido pelo parceiro, se aplicável). Precisa de conhecer o seu [parceiro MPN para](/partner-center/partner-center-account-setup#locate-your-mpn-id) completar estes passos. Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro.

Para simplificar, recomendamos a criação de uma conta principal de serviço no seu inquilino, ligando-a ao seu **ID MPN Associado,** concedendo-lhe acesso a todos os clientes que você está a bordo com uma [função azure incorporada que é elegível para PEC.](/partner-center/azure-roles-perms-pec)

1. [Crie uma conta principal de utilizador](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) de serviço no seu inquilino gerente. Para este exemplo, usaremos o nome *Dem automação de fornecedor para* esta conta principal de serviço.
1. Utilizando essa conta principal de serviço, [ligue-se ao seu ID MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente. Só precisas de fazer isto uma vez.
1. Quando embarcar num cliente [que utilize modelos ARM](onboard-customer.md) ou [ofertas de Serviço Gerido,](publish-managed-services-offers.md)certifique-se de incluir uma autorização que inclua a Conta de Automação do Fornecedor como utilizador com uma [função incorporada Azure que é elegível para PEC.](/partner-center/azure-roles-perms-pec)

Ao seguir estes passos, todos os inquilinos que gere estarão associados ao seu parceiro ID. A Conta de Automação do Fornecedor não necessita de autenticar ou realizar quaisquer ações no cliente inquilino.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagrama mostrando o processo de ligação do parceiro com o Farol Azure.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adicione o seu ID do seu parceiro a clientes previamente a bordo

Se já entrou a bordo de um cliente, poderá não querer realizar outra implementação para adicionar o seu fornecedor de conta de automação principal. Em vez disso, pode ligar o seu **ID MPN Associado** a uma conta de utilizador que já tem acesso ao trabalho no inquilino desse cliente. Certifique-se de que a conta foi concedida a um [papel integrado Azure que é elegível para o PEC](/partner-center/azure-roles-perms-pec).

Uma vez que a conta tenha sido [ligada ao seu ID MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente, poderá acompanhar o reconhecimento pelo seu impacto nesse cliente.

## <a name="confirm-partner-earned-credit"></a>Confirme o crédito do parceiro

Pode [ver detalhes do PEC no portal Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e confirmar quais os custos que receberam o benefício do PEC. Lembre-se que o PEC só se aplica aos clientes da CSP que assinaram o MCA e estão ao abrigo do plano Azure.

Se seguiu os passos acima, e não vê a associação esperada, abra um pedido de apoio no portal Azure.

Também pode utilizar o [Partner Center SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) e filtrar `rateOfPartnerEarnedCredit` para automatizar a verificação do PEC para uma subscrição.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Rede de Parceiros da Microsoft.](/partner-center/mpn-overview)
- Saiba [como o PEC é calculado e pago.](/partner-center/partner-earned-credit-explanation)
- [Clientes a bordo](onboard-customer.md) do Farol de Azure.