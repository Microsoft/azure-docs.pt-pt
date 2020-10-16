---
title: Ligue o seu parceiro de ID para acompanhar o seu impacto em recursos delegados
description: Saiba como associar o seu parceiro ID a receber o parceiro de crédito adquirido (PEC) sobre os recursos do cliente que gere através do Farol Azure.
ms.date: 10/13/2020
ms.topic: how-to
ms.openlocfilehash: 95483cfabb7632182a7c23ae4963f2d38a2bd2c3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019919"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Ligue o seu parceiro de ID para acompanhar o seu impacto em recursos delegados 

Se for membro da [Microsoft Partner Network,](https://partner.microsoft.com/)pode ligar o ID do seu parceiro com as credenciais utilizadas para gerir os recursos do cliente delegado. Partner Admin Link (PAL) permite à Microsoft identificar e reconhecer parceiros que impulsionam o sucesso do cliente da Azure. Este link também permite que os parceiros [da CSP (Cloud Solution Provider)](/partner-center/csp-overview) recebam [crédito adquirido pelos serviços geridos (PEC)](/partner-center/partner-earned-credit) para clientes que [assinaram o Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) e estão [ao abrigo do plano Azure.](/partner-center/azure-plan-get-started)

Se você estiver [a bordo de clientes com ofertas de Serviço Gerido no Azure Marketplace,](publish-managed-services-offers.md)a ligação acontece automaticamente usando o ID MPN associado à conta Partner Center utilizada para publicar as ofertas. Não são necessárias mais medidas para acompanhar o seu impacto para estes clientes.

Se você [estiver a bordo clientes usando modelos de Gestão de Recursos Azure,](onboard-customer.md)você precisará tomar medidas para criar este link. Isto é feito [ligando o seu ID MPN](../../cost-management-billing/manage/link-partner-id.md) com pelo menos uma conta de utilizador no seu inquilino gerente que tem acesso a cada uma das suas subscrições a bordo.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associe o seu ID do parceiro quando estiver a bordo de novos clientes

Ao embarcar os clientes através dos modelos Azure Resource Manager (modelos ARM), utilize o seguinte processo para ligar o ID do seu parceiro (e ativar o crédito adquirido pelo parceiro, se aplicável). Precisa de conhecer o seu [parceiro MPN para](/partner-center/partner-center-account-setup#locate-your-mpn-id) completar estes passos. Utilize o **ID do MPN associado** apresentado no seu perfil de parceiro.

Para simplificar, recomendamos a criação de uma conta principal de serviço no seu inquilino, ligando-a ao seu **ID MPN Associado,** concedendo-lhe acesso a todos os clientes que você está a bordo com uma [função azure incorporada que é elegível para PEC.](/partner-center/azure-roles-perms-pec)

1. [Crie uma conta principal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) de serviço no seu inquilino gerente. Para este exemplo, usaremos o nome *Dem automação de fornecedor para* este responsável de serviço.
1. Utilizando essa conta principal de serviço, [ligue-se ao seu ID MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente. Só precisas de fazer isto uma vez.
1. Quando [embarcar num cliente utilizando modelos ARM,](onboard-customer.md)certifique-se de que inclui uma autorização que inclui a Conta de Automação do Fornecedor como utilizador com uma [função incorporada Azure que é elegível para PEC.](/partner-center/azure-roles-perms-pec)

Ao seguir estes passos, todos os inquilinos que gere estarão associados ao seu parceiro ID. A Conta de Automação do Fornecedor não necessita de autenticar ou realizar quaisquer ações no cliente inquilino.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagrama mostrando o processo PAL com Farol Azure.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adicione o seu ID do seu parceiro a clientes previamente a bordo

Se já entrou a bordo de um cliente, poderá não querer realizar outra implementação para adicionar o seu fornecedor de conta de automação principal. Em vez disso, pode ligar o seu **ID MPN Associado** a uma conta de utilizador que já tem acesso ao trabalho no inquilino desse cliente. Certifique-se de que a conta foi concedida a um [papel integrado Azure que é elegível para o PEC](/partner-center/azure-roles-perms-pec).

Uma vez que a conta tenha sido [ligada ao seu ID MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu inquilino gerente, poderá acompanhar o reconhecimento pelo seu impacto nesse cliente.

## <a name="confirm-partner-earned-credit"></a>Confirme o crédito do parceiro

Pode [ver detalhes do PEC no portal Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e confirmar quais os custos que receberam o benefício do PEC. Lembre-se que o PEC só se aplica aos clientes da CSP que assinaram o MCA e estão ao abrigo do plano Azure.

Se seguiu os passos acima, e não vê a associação, abra um pedido de apoio no portal Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Rede de Parceiros da Microsoft.](/partner-center/mpn-overview)
- Saiba [como o PEC é calculado e pago.](/partner-center/partner-earned-credit-explanation)
- [Clientes a bordo](onboard-customer.md) do Farol de Azure.