---
title: Configure a segurança para conceder acesso a dados - Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar segurança, permissões e gerir políticas de acesso a dados no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780863"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso de dados a um ambiente

Este artigo discute os dois tipos de políticas de acesso Azure Time Series Insights.

## <a name="sign-in-to-azure-time-series-insights"></a>Inscreva-se no Azure Time Series Insights

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize o seu ambiente Azure Time Series Insights introduzindo `Time Series Insights environments` na caixa **de Pesquisa.** Selecione `Time Series Insights environments` os resultados da pesquisa.
1. Selecione o ambiente Azure Time Series Insights da lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estes passos para conceder acesso aos dados para um utilizadores principal.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione + **Adicionar**.

    [![Selecione e adicione uma Política de Acesso a Dados](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Escolha **Selecionar utilizador**. Procure o nome de utilizador ou o endereço de e-mail para localizar o utilizador que pretende adicionar. **Selecione** para confirmar a seleção.

    [![Selecione um utilizador para adicionar](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Escolha **Função Select**. Escolha a função de acesso adequada para o utilizador:

    * Selecione **O Contribuinte** se pretender permitir que o utilizador altere dados de referência e partilhe consultas e perspetivas guardadas com outros utilizadores do ambiente.

    * Caso contrário, selecione **Reader** para permitir ao utilizador consultar dados no ambiente e salvar consultas pessoais, não partilhadas, no ambiente.

   Selecione **OK** para confirmar a escolha de função.

    [![Confirme o papel selecionado](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecione **OK** na página **'Escolha's Role' (Escolha a função de utilizador).**

    [![Selecione OK na página 'Escolha's Role'](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Confirme que a página Políticas de Acesso a **Dados** lista os utilizadores e as funções para cada utilizador.

    [![Verifique os utilizadores e funções corretos](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornecer acesso ao hóspede de outro inquilino da AD Azure

O `Guest` papel não é um papel de gestão. É um termo usado para uma conta que é convidada de um inquilino para outro. Depois de a conta de hóspedes ser convidada para o diretório do arrendatário, pode ter o mesmo controlo de acesso aplicado a ele como qualquer outra conta. Pode conceder acesso à gestão a um Azure Time Series Insights Environment utilizando a lâmina Access Control (IAM). Ou pode conceder acesso aos dados no ambiente através da lâmina Políticas de Acesso a Dados. Para obter mais informações sobre o acesso ao hóspede do Azure Ative Directory (Azure AD), leia [os utilizadores de colaboração Add Azure Ative Directory B2B no portal Azure](../active-directory/external-identities/add-users-administrator.md).

Siga estes passos para conceder ao hóspede acesso a um ambiente Azure Time Series Insights a um utilizador Azure AD de outro inquilino.

1. Vá ao portal Azure, clique no  **Azure Ative Directory,** desloque-se para baixo no **separador Visão Geral** e, em seguida, selecione **o utilizador convidado**.

    [![Selecione Data Access Polices, em seguida , + Convidar](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Insira o endereço de e-mail para o utilizador que pretende convidar. Este endereço de e-mail deve ser associado ao Azure AD. Pode opcionalmente incluir uma mensagem pessoal com o convite.

    [![Insira o endereço de e-mail para encontrar o utilizador selecionado](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Procure a bolha de confirmação que aparece no ecrã. Também pode clicar em **Notificações** para confirmar que o utilizador convidado foi adicionado.

    [![Procure a bolha de confirmação para aparecer](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Volte ao seu ambiente time series Insights para adicionar o recém-criado utilizador convidado. Clique em **Políticas de Acesso a Dados,** conforme descrito no acesso a **dados do Grant**. **Selecione o utilizador.** Procure o endereço de e-mail do utilizador convidado que convidou para localizar o utilizador que pretende adicionar. Em seguida, **selecione** para confirmar a seleção.

    [![Selecione o utilizador e confirme a seleção](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Escolha **Função Select**. Escolha a função de acesso adequada para o utilizador convidado:

    * Selecione **O Contribuinte** se pretender permitir que o utilizador altere dados de referência e partilhe consultas e perspetivas guardadas com outros utilizadores do ambiente.

    * Caso contrário, selecione **Reader** para permitir ao utilizador consultar dados no ambiente e salvar consultas pessoais, não partilhadas, no ambiente.

   Selecione **OK** para confirmar a escolha de função.

    [![Confirme a escolha do papel](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecione **OK** na página **'Escolha's Role' (Escolha a função de utilizador).**

1. Confirme que a página Políticas de Acesso a **Dados** lista o utilizador convidado e as funções de cada utilizador convidado.

    [![Verifique se os utilizadores e as funções estão corretamente atribuídos](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Agora, o utilizador convidado receberá um e-mail de convite no endereço de e-mail acima especificado. O utilizador convidado irá selecionar **Get Started** para confirmar a sua aceitação e ligar-se à Azure Cloud.

    [![Selecionados por hóspedes Começar a aceitar](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Após a seleção **get start,** o utilizador convidado será apresentado com uma caixa de permissões associada à organização do administrador. Após a concessão de permissão selecionando **Aceitar,** eles serão assinados.

    [![Os comentários dos hóspedes autorizações e aceita](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. O administrador [partilha o URL ambiental](time-series-insights-parameterized-urls.md) com o seu convidado.

1. Depois de o utilizador convidado ser inscrito no endereço de e-mail que usou para os convidar, e eles aceitarem o convite, serão direcionados para o portal Azure.

1. O hóspede pode agora aceder ao ambiente partilhado utilizando o URL ambiental fornecido pelo administrador. Podem inserir esse URL no seu navegador web para acesso imediato.

1. O inquilino do administrador será apresentado ao utilizador convidado depois de selecionar o seu ícone de perfil no canto superior direito do explorador da Série Tempo.

    [![Seleção de avatar na insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Depois de o utilizador convidado selecionar o inquilino do administrador, eles terão a capacidade de selecionar o ambiente partilhado Azure Time Series Insights.

    Eles agora têm todas as capacidades associadas ao papel que lhes forneceu no **passo 5**.

    [![O utilizador convidado seleciona o seu inquilino Azure a partir do drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Leia as etapas de registo de aplicações de [autenticação e autorização](time-series-insights-authentication-and-authorization.md) para o Azure Ative Directory.

* Veja [o seu ambiente no Azure Time Series Insights Explorer](./concepts-ux-panels.md).
