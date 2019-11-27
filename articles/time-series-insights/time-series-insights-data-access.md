---
title: Configurar a segurança para conceder acesso a dados-visualização de Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar a segurança, as permissões e o gerenciamento de políticas de acesso a dados em seu ambiente de Azure Time Series Insights visualização.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: b79ca1d93baf1941d5de8db0c314f9cd21e51056
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328114"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso de Azure Time Series Insights visualização.

> [!TIP]
> Leia [autenticação e autorização](time-series-insights-authentication-and-authorization.md) para Azure Active Directory etapas de registro do aplicativo.

## <a name="sign-in-to-time-series-insights"></a>Entrar no Time Series Insights

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Localize seu ambiente de Time Series Insights. Insira `Time Series` na caixa de **pesquisa** . Selecione o **ambiente de série temporal** nos resultados da pesquisa.
1. Selecione o seu ambiente do Time Series Insights na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **políticas de acesso a dados**e, em seguida, selecione **+ Adicionar**.

    [![selecionar e adicionar uma política de acesso a dados](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Escolha **Selecionar usuário**. Procure o nome de usuário ou endereço de email para localizar o usuário que você deseja adicionar. Selecione **selecionar** para confirmar a seleção.

    [![selecionar um usuário para adicionar](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Escolha **selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **colaborador** se desejar permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o usuário consulte dados no ambiente e Salve consultas pessoais e não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![confirmar a função selecionada](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selecione **OK** na página **selecionar função de usuário** .

    [![selecione OK na página Selecionar função de usuário](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Confirme se a página **políticas de acesso a dados** lista os usuários e as funções para cada usuário.

    [![verificar os usuários e funções corretos](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornecer acesso de convidado de outro locatário do Azure AD

A função `Guest` não é uma função de gerenciamento. É um termo usado para uma conta que é convidada de um locatário para outro. Depois que a conta de convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado a ela, como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente de Time Series Insights usando a folha de controle de acesso (IAM). Ou você pode conceder acesso aos dados no ambiente por meio da folha políticas de acesso a dados. Para obter mais informações sobre o acesso convidado do locatário do Azure Active Directory (Azure AD), leia [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente de Time Series Insights para um usuário do Azure AD de outro locatário.

1. Selecione **políticas de acesso a dados**e, em seguida, selecione **+ convidar**.

    [![selecionar políticas de acesso a dados e + convidar](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Insira o endereço de email do usuário que você deseja convidar. Este endereço de email deve ser associado ao Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    [![inserir o endereço de email para localizar o usuário selecionado](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela.

    [![procure a bolha de confirmação para aparecer](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Escolha **Selecionar usuário**. Procure o endereço de email do usuário convidado que você convidou para localizar o usuário que deseja adicionar. Em seguida, **selecione** para confirmar a seleção.

    [![selecionar o usuário e confirmar a seleção](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Escolha **selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **colaborador** se desejar permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o usuário consulte dados no ambiente e Salve consultas pessoais e não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![confirmar a opção de função](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selecione **OK** na página **selecionar função de usuário** .

1. Confirme se a página **políticas de acesso a dados** lista o usuário convidado e as funções para cada usuário convidado.

    [![verificar se os usuários e as funções estão atribuídos corretamente](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Agora, o usuário convidado receberá um email de convite no endereço de email especificado acima. O usuário **convidado selecionará começar a** confirmar sua aceitação e conectar-se à nuvem do Azure.

    [![convidado seleciona introdução para aceitar](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Depois de **selecionar introdução, o usuário** convidado receberá uma caixa de permissões associada à organização do administrador. Ao conceder permissão selecionando **aceitar**, elas serão conectadas.

    [![convidado revisa as permissões e aceita](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. O administrador [compartilha a URL do ambiente](time-series-insights-parameterized-urls.md) com seu convidado.

1. Depois que o usuário convidado estiver conectado ao endereço de email que você usou para convidá-los e aceitar o convite, eles serão direcionados para portal do Azure. 

1. O convidado agora pode acessar o ambiente compartilhado usando a URL do ambiente fornecida pelo administrador. Eles podem inserir essa URL em seu navegador da Web para acesso imediato.

1. O usuário convidado verá o locatário do administrador selecionando seu ícone de perfil no canto superior direito do time Series Explorer.

    [![seleção de avatar em insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Depois que o usuário convidado selecionar o locatário do administrador, ele terá a capacidade de selecionar o ambiente de Time Series Insights compartilhado. 
    
    Agora, eles têm todos os recursos associados à função com a qual você os forneceu na **etapa 5**.

    [![usuário convidado seleciona seu locatário do Azure na lista suspensa](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba [como adicionar uma fonte de eventos de hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente de time Series insights.

* Enviar [eventos para a origem do evento](./time-series-insights-send-events.md).

* Exiba [seu ambiente no time Series insights Preview Explorer](./time-series-insights-update-explorer.md).
