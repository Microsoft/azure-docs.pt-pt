---
title: Alterar as informações de contacto de uma conta de faturação do Azure
description: Descreve como alterar as informações de contacto da sua conta de faturação do Azure
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperfq2
ms.openlocfilehash: 05b93fd02ec229306d4e3e150b1e8baab072b597
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672002"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Alterar as informações de contacto de uma conta de faturação do Azure

Este artigo ajuda-o a atualizar as informações de contacto de uma *conta de faturação* no portal do Azure. As instruções para atualizar as informações de contacto variam consoante o tipo de conta de faturação. Para saber mais sobre as contas de faturação e identificar o tipo da sua conta de faturação, veja [Ver as contas de faturação no portal do Azure](view-all-accounts.md). A conta de faturação do Azure é separada da conta de utilizador do Azure e da [Conta Microsoft](https://account.microsoft.com/).

Se pretender atualizar as suas informações de perfil de utilizador do Azure Active Directory, só os administradores de utilizadores podem fazer alterações. Se não lhe tiver sido atribuída a função de administrador de utilizadores, contacte o seu administrador de utilizadores. Para obter mais informações sobre como alterar um perfil de utilizador, veja [Adicionar ou atualizar as informações do perfil de um utilizador com o Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Endereço do comprador* - O endereço do comprador é o endereço e as informações de contacto da organização ou do indivíduo responsável por uma conta de faturação. É apresentado em todas as faturas geradas para a conta de faturação.

*Endereço para faturação* - O endereço para faturação é o endereço e as informações de contacto da organização ou do indivíduo responsável pelas faturas geradas para uma conta de faturação. Para uma conta de faturação de um Programa do Serviço Online da Microsoft (MOSP), é apresentado um endereço para faturação em todas as faturas geradas para a conta. Para uma conta de faturação para um Contrato de Cliente Microsoft (MCA), é apresentado um endereço para faturação de cada perfil de faturação na fatura gerada para o perfil de faturação.

*Endereço de e-mail de contacto para e-mails do serviço e de marketing* - pode especificar um endereço de e-mail diferente do endereço de e-mail com o qual inicia sessão para receber notificações importantes relacionadas com faturação, serviço e recomendações sobre a sua conta do Azure. Os e-mails de notificações do serviço, como problemas de segurança urgentes, alterações aos preços ou alterações interruptivas aos serviços utilizados na sua conta são sempre enviados para o endereço de início de sessão.

## <a name="update-an-mosp-billing-account-address"></a>Atualizar o endereço de uma conta de faturação MOSP

1. Inicie sessão no portal do Azure com o endereço de e-mail que tem a permissão do administrador de conta na conta.
1. Procure **Cost Management + Faturação**.  
    ![Captura de ecrã que mostra onde procurar cost management + billing no portal do Azure](./media/change-azure-account-profile/search-cmb.png)
1. Selecione **Propriedades** no lado esquerdo.  
    ![Captura de ecrã que mostra as propriedades da conta de faturação do MOSP](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Selecione **Atualizar endereço para faturação** para atualizar os endereços do comprador e de faturação. Introduza o novo endereço e, em seguida, selecione **Guardar**.  
    ![Captura de ecrã que mostra o endereço de atualização da conta de faturação do MOSP](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Atualizar o endereço do comprador de uma conta de faturação MCA

1. Inicie sessão no portal do Azure com o endereço de e-mail que tem um proprietário ou uma função de contribuidor na conta de faturação de um Contrato de Cliente Microsoft.
1. Procure **Cost Management + Faturação**.  
    ![Captura de ecrã que mostra onde procurar no portal do Azure](./media/change-azure-account-profile/search-cmb.png)
1. Selecione **Propriedades** no lado esquerdo e, em seguida, selecione **Atualizar comprador**.  
    ![Captura de ecrã que mostra as propriedades de uma conta de faturação do MCA, onde o endereço de venda pode ser modificado](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Introduza o novo endereço e selecione **Guardar**.  
    ![Captura de ecrã que mostra a atualização do endereço de venda de uma conta do MCA](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Algumas contas requerem verificação adicional antes de a informação do comprador ser atualizada. Se a sua conta exigir aprovação manual, ser-lhe-á pedido que contacte o suporte do Azure.

## <a name="update-an-mca-billing-account-address"></a>Atualizar o endereço de uma conta de faturação MCA

1. Inicie sessão no portal do Azure com o endereço de e-mail que tem um proprietário ou uma função de contribuidor numa conta ou perfil de faturação de um MCA.
1. Procure **Cost Management + Faturação**.  
1. Selecione **Perfis de faturação** no lado esquerdo.
1. Selecione um perfil de faturação para atualizar o endereço para faturação.  
    ![Captura de ecrã que mostra a página Perfis de faturação, na qual pode selecionar um perfil de faturação](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Selecione **Propriedades** no lado esquerdo.
1. Selecione **Atualizar endereço**.  
    ![Captura de ecrã que mostra onde atualizar o endereço](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Introduza o novo endereço e, em seguida, selecione **Guardar**.  
    ![Captura de ecrã que mostra a atualização do endereço](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>E-mails de serviço e marketing

Ser-lhe-á solicitado no portal do Azure que verifique ou atualize o seu endereço de e-mail a cada 90 dias. A Microsoft envia e-mails para este endereço de e-mail com informações relacionadas com a conta do Azure para:

- Notificações do serviço
- Alertas de segurança
- Efeitos de faturação
- Suporte
- Comunicações de marketing
- Recomendações de melhores práticas, com base na sua utilização do Azure

Introduza o endereço de e-mail no qual pretende receber comunicações sobre a sua conta. Ao introduzir um endereço de e-mail, está a optar por receber comunicações da Microsoft.

![Exemplo da mensagem para atualizar as suas informações de contacto](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Alterar o endereço de e-mail de contacto

Pode alterar o seu endereço de e-mail de contacto ao utilizar um dos seguintes métodos. A atualização do seu endereço de e-mail de contacto não atualiza o endereço de e-mail com o qual inicia sessão.

1. Se for administrador de uma conta MOSP, siga as instruções em [Atualizar o endereço de uma conta de faturação MOSP](#update-an-mosp-billing-account-address) e selecione **Atualizar informações de contacto**  no último passo. Em seguida, introduza o novo endereço de e-mail.
1. Aceda à área [Informações de contacto](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) no portal do Azure e introduza o novo endereço de e-mail. 
1. No portal do Azure, selecione o ícone com as suas iniciais ou imagem. Em seguida, selecione o menu de contexto ( **...** ). Em seguida, selecione **As Minhas Informações de Contacto** no menu e introduza o novo endereço de e-mail.

![Exemplo de atualização de um endereço de e-mail no Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Recusar e-mails de marketing

Para recusar a receção de e-mails de marketing:

1. Aceda ao [formulário de pedido](https://account.microsoft.com/profile/permissions-link-request) para submeter um pedido através do endereço de e-mail do perfil. Receberá uma ligação por e-mail para atualizar as suas preferências.
1. Selecione a ligação para abrir a página **Gerir permissões de comunicação**. Esta página mostra os tipos de comunicações de marketing que recebe no endereço de e-mail. Desmarque as seleções que já não quer e, em seguida, selecione **Guardar**.  
    ![Exemplo da página para gerir permissões de comunicação](./media/change-azure-account-profile/manage-communication-permissions.png)

Quando recusa comunicações de marketing, continua a receber notificações de serviço, com base na sua conta.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Atualizar o endereço de e-mail com o qual inicia sessão

Não pode atualizar o endereço de e-mail que utiliza para aceder à sua conta. No entanto, se tiver uma conta de faturação MOSP, pode inscrever-se noutra conta com o novo endereço de e-mail e transferir a propriedade das suas subscrições para a conta seguinte. Para uma conta de faturação MCA, pode conceder permissões ao novo endereço de e-mail relacionadas com a sua conta.

## <a name="update-your-credit-card"></a>Atualizar o cartão de crédito

Para saber como atualizar as informações do cartão de crédito, veja [Alterar o cartão de crédito utilizado para pagar subscrições do Azure](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Atualizar o país ou a região

A alteração do país ou da região para uma conta existente não é suportada. No entanto, pode criar uma nova conta num país ou região diferente e, em seguida, contactar o suporte do Azure para transferir a sua subscrição para a nova conta.

## <a name="change-the-subscription-name"></a>Alterar o nome da subscrição

1. Inicie sessão no portal do Azure, selecione **Subscrição** no painel esquerdo e, em seguida, selecione a subscrição para a qual pretende mudar o nome.
1. Selecione **Descrição Geral** e, em seguida, selecione **Mudar o nome** na barra de comandos.  
    ![Exemplo de mudança de nome da subscrição do Azure](./media/change-azure-account-profile/rename-sub.png)
1. Depois de alterar o nome, selecione **Guardar**.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Ver as suas contas de faturação](view-all-accounts.md)
