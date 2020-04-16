---
title: Configure segurança para conceder acesso a dados - Pré-visualização de Insights da Série De Tempo Azure / Microsoft Docs
description: Aprenda a configurar a segurança, permissões e gerir as políticas de acesso a dados no seu ambiente de pré-visualização da Série de Tempo Azure Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 1735daf66483df496141a642ac6633973aa0abf0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407535"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso de dados a um ambiente

Este artigo discute os dois tipos de políticas de acesso de pré-visualização da Série de Tempo Azure Insights.

> [!TIP]
> Ler Etapas de [autenticação e autorização](time-series-insights-authentication-and-authorization.md) para as etapas de registo da aplicação Azure Ative Directory.

## <a name="sign-in-to-time-series-insights"></a>Inscreva-se na Time Series Insights

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize o seu ambiente time series Insights. Insira `Time Series` na caixa **de pesquisa.** Selecione **Ambientes de Séries de Tempo** nos resultados da pesquisa.
1. Selecione o seu ambiente do Time Series Insights na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estes passos para conceder acesso aos dados para um utilizador principal.

1. Selecione Políticas de Acesso a **Dados**e, em seguida, selecione **+ Adicionar**.

    [![Selecione e adicione uma Política de Acesso a Dados](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Escolha **Selecionar o utilizador**. Procure o nome do utilizador ou endereço de e-mail para localizar o utilizador que pretende adicionar. **Selecione Selecione** para confirmar a seleção.

    [![Selecione um utilizador para adicionar](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Escolha **selecione a função**. Escolha a função de acesso adequada para o utilizador:

    * Selecione **Colaborador** se pretender permitir que o utilizador altere os dados de referência e partilhe consultas e perspetivas guardadas com outros utilizadores do ambiente.

    * Caso contrário, selecione **Reader** para permitir ao utilizador consultar dados no ambiente e guardar consultas pessoais, não partilhadas no ambiente.

   Selecione **OK** para confirmar a escolha do papel.

    [![Confirmar o papel selecionado](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecione **OK** na página **'Função de Utilizador Select'.**

    [![Selecione OK na página Defunte função do utilizador](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Confirme que a página Políticas de Acesso a **Dados** lista os utilizadores e as funções para cada utilizador.

    [![Verifique os utilizadores e funções corretos](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornecer acesso a hóspedes de outro inquilino da AD Azure

O `Guest` papel não é um papel de gestão. É um termo usado para uma conta que é convidada de um inquilino para outro. Depois de a conta de hóspedes ser convidada para o diretório do inquilino, pode ter o mesmo controlo de acesso aplicado a ela como qualquer outra conta. Pode conceder acesso à gestão a um Time Series Insights Environment utilizando a lâmina de Controlo de Acesso (IAM). Ou pode conceder acesso aos dados do ambiente através da lâmina Data Access Policies. Para obter mais informações sobre o acesso dos hóspedes do Azure Ative Directory (Azure AD), leia [add Azure Ative Directory B2B no portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estes passos para dar acesso ao hóspede a um ambiente Time Series Insights a um utilizador da AD Azure de outro inquilino.

1. Selecione Políticas de Acesso a **Dados**e, em seguida, selecione **+ Convidar**.

    [![Selecione Data Access Polices, em seguida+ Convidar](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Insira o endereço de e-mail para o utilizador que pretende convidar. Este endereço de e-mail deve ser associado à Azure AD. Você pode opcionalmente incluir uma mensagem pessoal com o convite.

    [![Insira o endereço de e-mail para encontrar o utilizador selecionado](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Procure a bolha de confirmação que aparece no ecrã.

    [![Procure a bolha de confirmação para aparecer](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Escolha **Selecionar o utilizador**. Procure o endereço de e-mail do utilizador convidado que convidou para localizar o utilizador que pretende adicionar. Em seguida, **selecione** para confirmar a seleção.

    [![Selecione o utilizador e confirme a seleção](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Escolha **selecione a função**. Escolha a função de acesso adequada para o utilizador convidado:

    * Selecione **Colaborador** se pretender permitir que o utilizador altere os dados de referência e partilhe consultas e perspetivas guardadas com outros utilizadores do ambiente.

    * Caso contrário, selecione **Reader** para permitir ao utilizador consultar dados no ambiente e guardar consultas pessoais, não partilhadas no ambiente.

   Selecione **OK** para confirmar a escolha do papel.

    [![Confirme a escolha do papel](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecione **OK** na página **'Função de Utilizador Select'.**

1. Confirme que a página Políticas de Acesso a **Dados** lista o utilizador convidado e as funções para cada utilizador convidado.

    [![Verifique se os utilizadores e as funções estão corretamente atribuídos](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Agora, o utilizador convidado receberá um e-mail de convite no endereço de e-mail acima especificado. O utilizador convidado irá selecionar **Get Started** para confirmar a sua aceitação e ligação ao Azure Cloud.

    [![Selecionados para hóspedes Começaram a aceitar](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Depois de selecionar **Get Started,** o utilizador convidado será apresentado com uma caixa de permissões associada à organização do administrador. Ao conceder permissão selecionando **Aceitar,** serão assinados.

    [![Os hóspedes analisam permissões e aceitam](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. O administrador [partilha o URL ambiental](time-series-insights-parameterized-urls.md) com o seu convidado.

1. Depois de o utilizador convidado ser inscrito no endereço de e-mail que usou para convidá-los, e eles aceitarem o convite, eles serão direcionados para o portal Azure. 

1. O hóspede pode agora aceder ao ambiente partilhado utilizando o URL ambiental fornecido pelo administrador. Podem introduzir esse URL no seu navegador web para acesso imediato.

1. O inquilino do administrador será apresentado ao utilizador convidado depois de selecionar o seu ícone de perfil no canto superior direito do explorador da Série Time.

    [![Seleção de avatar no insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Depois de o utilizador convidado selecionar o inquilino do administrador, eles terão a capacidade de selecionar o ambiente de Insights da Série De Tempo partilhado. 
    
    Eles agora têm todas as capacidades associadas ao papel que lhes forneceu no **passo 5**.

    [![O utilizador convidado seleciona o seu inquilino Azure a partir do drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como adicionar uma fonte de evento saqueada [pelo Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente Time Series Insights.

* Envie [eventos para a fonte do evento.](./time-series-insights-send-events.md)

* Veja [o seu ambiente no explorador de pré-visualização](./time-series-insights-update-explorer.md)da Série De Tempo Insights .
