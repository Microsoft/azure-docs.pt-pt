---
title: Migração de conta do Portal do Cloud Partner para o Partner Center-Marketplace comercial para o Azure
description: Como migrar sua conta do CPP para o Partner Center. -Marketplace comercial para o Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147126"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migração de conta do Portal do Cloud Partner para o Partner Center

Se você tiver uma conta CPP existente, suas configurações de conta precisarão ser migradas para o Partner Center.

## <a name="account-migration-process"></a>Processo de migração de conta

Se você for um usuário com a função de proprietário em CPP para uma determinada conta, uma faixa amarela será mostrada na página de perfil do Publicador. Você pode pertencer a um dos dois casos a seguir:

- Sua conta já foi migrada e você está pronto para gerenciar as configurações de conta no Partner Center.
- Você precisa executar algumas ações adicionais para concluir a migração da sua conta para o Partner Center.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Sua conta foi migrada para o Partner Center

Para todas as contas que concluíram a migração do CPP para o Partner Center, o gerenciamento de conta ocorrerá no Partner Center. Alterações como adição/exclusão de usuário serão sincronizadas de volta para CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Você ainda não migrou sua conta para o Partner Center

Clique na faixa para iniciar o processo de migração de sua conta. Você deve inserir os seguintes itens:

1. Endereço de email de trabalho: <br> <br> Na maioria dos casos, entre com o endereço de email que você usa para entrar em CPP. Em determinados casos, um endereço de email diferente deve ser usado:

    * Conta Microsoft: Se a conta de CPP for uma conta Microsoft, você precisará inserir um endereço de email de trabalho válido associado ao locatário, para quem a ID de MPN está registrada.

    * Incompatibilidade de locatário: Se seu endereço de email de trabalho não pertencer ao locatário associado à ID de Microsoft Partner Network presente na sua conta de CPP, você verá um erro. Para mover após esse erro, insira um endereço de email associado ao locatário. Uma mensagem de erro fornecerá o nome do locatário.

2. Inscrever-se no programa Microsoft Partner Network

    Caso sua conta de CPP não tenha uma ID de Microsoft Partner Network ou tenha uma que seja inválida, você precisará se inscrever no programa de Microsoft Partner Network como parte do processo de ativação.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Inscrever-se no programa Microsoft Partner Network

As empresas que desejam fazer parceria com a Microsoft devem ingressar no Microsoft Partner Network (MPN) e obter uma ID de MPN. Se você já é membro do Microsoft Partner Network e tem uma ID de MPN, certifique-se de tê-lo disponível, pois você precisará inseri-lo durante o processo de ativação da conta.  

Se você ainda não é membro do Microsoft Partner Network, você pode [ingressar agora](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) para obter uma ID de MPN. Anote sua ID do MPN, pois você precisará inseri-la durante o processo de ativação da conta.

Para saber mais sobre o Microsoft Partner Network, consulte [ingressar o Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) no site do parceiro. Para saber mais sobre como unir os Microsoft Partner Network beneficia os ISVs, consulte o [Hub de recursos do ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Mover soluções baseadas no Dynamics 365 para o Partner Center

Se você tiver criado o Dynamics 365 para o envolvimento do cliente ou o Dynamics 365 para soluções de finanças e operações no portal de GTM de um parceiro comercial, **essas soluções agora devem ser gerenciadas no Partner Center**.

**Se você não moveu suas soluções até 31 de agosto de 2019**, conclua as etapas a seguir assim que possível. Até fazer isso:

- Os ISVs não terão acesso aos benefícios de marketing
- A prioridade de venda conjunta perderá seu status
- Aqueles que exigem a inserção na nuvem ficarão fora de conformidade após 15 de outubro de 2019

> [!NOTE]
> Se sua conta de associação do MPN foi originalmente criada no centro de associação de parceiros (PMC), entre no [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar que sua conta foi migrada antes de concluir as etapas abaixo. Se você vir uma tela de perfil com sua ID do MPN, você estará pronto para continuar. Caso contrário, você deve iniciar a migração da sua conta seguindo os prompts no [centro de associação do parceiro](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Se precisar de ajuda com isso, visite [suporte](https://partner.microsoft.com/support?issueid=100-0077).

1. Vá para a [página Visão geral do Marketplace comercial no Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se você vir "marketplace comercial" no painel de navegação esquerdo, será registrado e deverá prosseguir para a próxima etapa. Caso contrário, [Registre-se no Marketplace comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) agora.

2. Confirme se suas ofertas estão em AppSource [pesquisando suas ofertas](https://appsource.microsoft.com/). Se suas ofertas já estiverem em AppSource, vá para a próxima etapa. Para qualquer oferta que não esteja em AppSource, crie uma nova oferta de [compromisso do cliente do dynamics 365](create-new-customer-engagement-offer.md) ou uma [nova oferta do Dynamics 365 for Operations](create-new-operations-offer.md).

3. Verifique seu registro no programa Business Applications ISV Connect:
   * Na página [contratos](https://partner.microsoft.com/dashboard/account/agreements) no Partner Center, certifique-se de que você aceitou o **adendo Business Applications ISV** para se registrar no programa.
   * Na página [configurações de conta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) , forneça suas informações de cobrança.

4. Envie cada oferta nova e existente para certificação, mesmo que suas ofertas tenham sido certificadas anteriormente. Se qualificado, você pode solicitar a participação na camada Premium durante esse processo. Se sua oferta foi certificada anteriormente, **você deverá concluir a rescertificação de aplicativo até 15 de outubro de 2019.** A certificação ou a nova certificação exigirá que seu aplicativo dê suporte à versão mais recente de nossa plataforma de Business Applications.

5. Acesse o [portal de GTM de um parceiro comercial](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) e adicione a URL de listagem do AppSource na seção links do Marketplace. Se precisar de ajuda com esta etapa, envie um email cosell@microsoft.compara.

## <a name="next-steps"></a>Passos seguintes

- [Gerenciar sua conta do Marketplace comercial no Partner Center](./manage-account.md) 
