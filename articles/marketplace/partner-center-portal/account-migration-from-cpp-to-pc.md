---
title: Migração de conta do Portal do Cloud Partner para o Partner Center-Marketplace comercial para o Azure
description: Como migrar sua conta do CPP para o Partner Center. -Marketplace comercial para o Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.openlocfilehash: 9ab9a57641e6b34942ae6d4293311714177aa012
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533182"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migração de conta do Portal do Cloud Partner para o Partner Center

Quando suas ofertas são migradas do Portal do Cloud Partner (CPP) para o Partner Center (PC), elas ficam bloqueadas para edição em CPP. Neste ponto, as configurações de sua conta precisam ser migradas para o Partner Center.  As configurações de conta, bem como suas ofertas, podem ser gerenciadas no Partner Center.

## <a name="account-migration-process"></a>Processo de migração de conta

Quando as ofertas são migradas do CPP, sua conta é configurada para migração. 
 
Se você for um usuário com a função de proprietário em CPP para uma determinada conta, uma faixa amarela será mostrada na página de perfil do Publicador.  Você será solicitado a mover as configurações da conta para o Partner Center. 

Clique na faixa para iniciar o processo de migração de sua conta. Você deve inserir os seguintes itens:

### <a name="work-e-mail-address"></a>**Endereço de email de trabalho**

Na maioria dos casos, entre com o endereço de email que você usa para entrar em CPP. Em determinados casos, um endereço de email diferente deve ser usado:

* Conta da Microsoft: Se a conta de CPP for uma conta da Microsoft, você precisará inserir um endereço de email de trabalho válido associado ao locatário, para quem a ID de MPN está registrada. 

* Incompatibilidade de locatário: Se o seu endereço de email de trabalho não pertencer ao locatário associado à ID de Microsoft Partner Network presente na sua conta de CPP, você verá um erro. Para mover após esse erro, insira um endereço de email associado ao locatário. Uma mensagem de erro fornecerá o nome do locatário. 

### <a name="sign-up-for-microsoft-partner-network-program"></a>Inscrever-se no programa Microsoft Partner Network

Caso sua conta de CPP não tenha uma ID de Microsoft Partner Network ou tenha uma que seja inválida, você precisará se inscrever no programa de Microsoft Partner Network como parte do processo de ativação.

## <a name="account-activation-is-complete"></a>A ativação da conta foi concluída

A migração de conta precisa ocorrer apenas uma vez para uma determinada conta. Depois que um determinado parceiro tiver concluído a migração para a conta, todos os proprietários verão esse comportamento na página de perfil do editor:

1. Você verá a página de configurações de parceiro no Microsoft Partner Network, em que você pode gerenciar as configurações de conta do parceiro da Microsoft. 
2. Depois que a migração da conta for concluída, uma faixa amarela na página de perfil do Publicador será exibida para os usuários que pertencem à função de proprietário em CPP para uma determinada conta, solicitando que eles gerenciem suas configurações de conta no Partner Center. 
3. A página de configurações de conta em CPP é então convertida para o modo somente leitura. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Mover soluções baseadas no Dynamics 365 para o Partner Center

Se você tiver o Dynamics 365 para o envolvimento do cliente ou o Dynamics 365 para soluções de finanças e operações no portal de GTM de um parceiro comercial, **siga estas instruções até 31 de agosto de 2019** para mover essas soluções para o Partner Center.

> [!NOTE]
> Se sua conta foi originalmente criada no Partner Membership Center (PMC), entre no [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar que sua conta foi migrada antes de concluir as etapas abaixo. Se você vir uma tela de perfil com sua ID do MPN, você estará pronto para continuar. Caso contrário, você deve iniciar a migração da sua conta seguindo os prompts no [centro de associação do parceiro](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Se precisar de ajuda com isso, visite [suporte](https://partner.microsoft.com/support?issueid=100-0077).

1. Vá para a [página Visão geral do Marketplace comercial no Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se você vir "marketplace comercial" no painel de navegação esquerdo, será registrado e deverá prosseguir para a próxima etapa. Caso contrário, [Registre-se no Marketplace comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) agora.
2. Confirme se suas ofertas estão em AppSource [pesquisando suas ofertas](https://appsource.microsoft.com/). Se suas ofertas já estiverem em AppSource, vá para a próxima etapa. Para qualquer oferta que não esteja em AppSource, crie uma nova oferta de [compromisso do cliente do dynamics 365](create-new-customer-engagement-offer.md) ou uma [nova oferta do Dynamics 365 for Operations](create-new-operations-offer.md).
3. Verifique seu registro no programa Business Applications ISV Connect:
  
   * Na página [contratos](https://partner.microsoft.com/dashboard/account/agreements) no Partner Center, certifique-se de que você aceitou o **adendo Business Applications ISV** para se registrar no programa.
   * Na página [configurações de conta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) , forneça suas informações de cobrança.

4. Envie cada oferta nova e existente para certificação, mesmo que suas ofertas tenham sido certificadas anteriormente. **É recomendável enviar o mais cedo possível para permitir tempo suficiente para aprovação antes de 31 de agosto de 2019.**
5. Acesse o [portal de GTM de um parceiro comercial](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) e adicione a URL de listagem do AppSource na seção links do Marketplace. Se precisar de ajuda com esta etapa, envie um email cosell@microsoft.compara.

## <a name="next-steps"></a>Passos Seguintes

- [Gerenciar sua conta do Marketplace comercial no Partner Center](./manage-account.md) 
