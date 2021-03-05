---
title: Reger o acesso a utilizadores externos na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba mais sobre as definições que pode especificar para reger o acesso de utilizadores externos na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c78dcbc34deca769739f82964df41ebfc596ea
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176795"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Reger o acesso dos utilizadores externos na gestão dos direitos da AD Azure

A gestão de direitos da AD Azure utiliza [a Azure AD business-to-business (B2B)](../external-identities/what-is-b2b.md) para colaborar com pessoas fora da sua organização em outro diretório. Com o Azure AD B2B, os utilizadores externos autenticam-se no seu diretório de origem, mas têm uma representação no seu diretório. A representação no seu diretório permite ao utilizador ter acesso aos seus recursos.

Este artigo descreve as definições que pode especificar para reger o acesso a utilizadores externos.

## <a name="how-entitlement-management-can-help"></a>Como a gestão de direitos pode ajudar

Ao utilizar a experiência de convite [Azure AD B2B,](../external-identities/what-is-b2b.md) já deve conhecer os endereços de e-mail dos utilizadores convidados externos que pretende trazer para o seu diretório de recursos e trabalhar com eles. Isto funciona muito bem quando está a trabalhar num projeto de menor ou de curto prazo e já conhece todos os participantes, mas isso é mais difícil de gerir se tiver muitos utilizadores com quem quer trabalhar ou se os participantes mudam ao longo do tempo.  Por exemplo, pode estar a trabalhar com outra organização e ter um ponto de contacto com essa organização, mas com o passar do tempo utilizadores adicionais dessa organização também precisarão de acesso.

Com a gestão de direitos, pode definir uma política que permite aos utilizadores de organizações que especificar para poderem auto-solicitar um pacote de acesso. Pode especificar se é necessária a aprovação e uma data de validade para o acesso. Se for necessária a aprovação, também pode convidar um ou mais utilizadores da organização externa para o seu diretório e designá-los como aprovadores - uma vez que são suscetíveis de saber quais os utilizadores externos da sua organização que precisam de acesso. Uma vez configurado o pacote de acesso, pode enviar o link do pacote de acesso para a sua pessoa de contacto (patrocinador) na organização externa. Esse contacto pode partilhar com outros utilizadores da organização externa, e podem usar este link para solicitar o pacote de acesso. Os utilizadores dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando um pedido for aprovado, a gestão de direitos irá providenciar ao utilizador o acesso necessário, o que pode incluir convidar o utilizador se ainda não estiver no seu diretório. O Azure AD criará automaticamente uma conta de hóspedes B2B para eles. Note que um administrador pode ter limitado previamente quais as organizações que são permitidas para colaboração, estabelecendo uma [lista B2B de permitir ou negar convites](../external-identities/allow-deny-list.md) para outras organizações.  Se o utilizador não for autorizado pela lista de admissões ou bloqueios, então não serão convidados.

Uma vez que não pretende que o acesso do utilizador externo dure para sempre, especifique uma data de validade na apólice, como 180 dias. Após 180 dias, se o seu acesso não for prolongado, a gestão de direitos removerá todos os acessos associados a esse pacote de acesso. Por padrão, se o utilizador que foi convidado através da gestão de direitos não tiver outras atribuições de pacotes de acesso, então quando perderem a sua última atribuição, a sua conta de hóspedes ficará impedida de iniciar sessão durante 30 dias e posteriormente removida. Isto impede a proliferação de contas desnecessárias. Como descrito nas seguintes secções, estas definições são configuráveis.

## <a name="how-access-works-for-external-users"></a>Como o acesso funciona para utilizadores externos

O diagrama e as etapas seguintes fornecem uma visão geral de como os utilizadores externos têm acesso a um pacote de acesso.

![Diagrama que mostra o ciclo de vida dos utilizadores externos](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. [Você adiciona uma organização conectada](entitlement-management-organization.md) para o diretório ou domínio Azure AD com o quais deseja colaborar.

1. Cria um pacote de acesso no seu diretório que inclui uma política [Para utilizadores que não estão no seu diretório.](entitlement-management-access-package-create.md#for-users-not-in-your-directory)

1. Envia um link do [portal My Access](entitlement-management-access-package-settings.md) para o seu contacto na organização externa que pode partilhar com os seus utilizadores para solicitar o pacote de acesso.

1. Um utilizador externo **(Requestor A** neste exemplo) utiliza o link do portal My Access para solicitar o [acesso](entitlement-management-request-access.md) ao pacote de acesso. A forma como o utilizador assina depende do tipo de autenticação do diretório ou domínio definido na organização conectada.

1. Um aprovador [aprova o pedido](entitlement-management-request-approve.md) (ou o pedido é aprovado automaticamente).

1. O pedido vai para o [estado de entrega.](entitlement-management-process.md)

1. Utilizando o processo de convite B2B, é criada uma conta de utilizador convidada no seu **diretório (Requestor A (Convidado)** neste exemplo. Se for definida uma [lista de admissões ou uma lista de negação,](../external-identities/allow-deny-list.md) a definição da lista será aplicada.

1. O utilizador convidado tem acesso a todos os recursos do pacote de acesso. Pode levar algum tempo para que as alterações sejam feitas no AD AZure e em outros Serviços Microsoft Online ou aplicações SaaS conectadas. Para obter mais informações, consulte [Quando as alterações são aplicadas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. O utilizador externo recebe um e-mail indicando que o seu acesso foi [entregue.](entitlement-management-process.md)

1. Para aceder aos recursos, o utilizador externo pode clicar no link no e-mail ou tentar aceder a qualquer um dos recursos do diretório diretamente para completar o processo de convite.

1. Dependendo das definições de política, à medida que o tempo passa, a atribuição do pacote de acesso para o utilizador externo expira e o acesso do utilizador externo é removido.

1. Dependendo do ciclo de vida das definições de utilizadores externos, quando o utilizador externo já não tem nenhuma atribuição de pacotes de acesso, o utilizador externo fica impedido de iniciar sessão e a conta de utilizador do hóspede é removida do seu diretório.

## <a name="settings-for-external-users"></a>Definições para utilizadores externos

Para garantir que pessoas fora da sua organização podem solicitar pacotes de acesso e ter acesso aos recursos nesses pacotes de acesso, existem algumas configurações que deve verificar que estão devidamente configuradas.

### <a name="enable-catalog-for-external-users"></a>Ativar catálogo para utilizadores externos

- Por padrão, quando se cria um [novo catálogo,](entitlement-management-catalog-create.md)é permitido aos utilizadores externos solicitarem pacotes de acesso no catálogo. Certifique-se de que **o Enabled para utilizadores externos** está definido para **Sim**.

    ![Editar configurações de catálogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configure as definições de colaboração externa Azure AD B2B

- Permitir que os hóspedes convidem outros hóspedes para o seu diretório significa que os convites dos hóspedes podem ocorrer fora da gestão de direitos. Recomendamos que **os hóspedes possam convidar** para o **Nº** apenas para permitir convites devidamente governados.
- Se estiver a utilizar a lista de autorizações B2B, deve certificar-se de que qualquer domínio que pretenda associar-se à gestão de direitos é adicionado à lista. Em alternativa, se estiver a utilizar a lista de negação B2B, deve certificar-se de que qualquer domínio com o qual pretende associar-se não é adicionado à lista.
- Se criar uma política de gestão de direitos para **todos os utilizadores** (todas as organizações conectadas + quaisquer novos utilizadores externos) e um utilizador não pertencer a uma organização conectada no seu diretório, uma organização conectada será automaticamente criada para eles quando solicitar o pacote. Qualquer B2B permite ou nega as definições da lista que tiver terá precedência. Portanto, certifique-se de incluir os domínios que pretende incluir nesta política na sua lista de autorizações se estiver a utilizar um, e exclua-os da sua lista de negação se estiver a utilizar uma lista de negação.
- Se pretender criar uma política de gestão de direitos que inclua **Todos os utilizadores** (Todas as organizações conectadas + quaisquer novos utilizadores externos), tem primeiro de ativar a autenticação de código de acesso de e-mail para o seu diretório. Para mais informações, consulte [a autenticação de código de acesso](../external-identities/one-time-passcode.md)de uma vez por email.
- Para obter mais informações sobre as definições de colaboração externa Azure AD B2B, consulte [a colaboração externa do Enable B2B e gere quem pode convidar os hóspedes.](../external-identities/delegate-invitations.md)

    ![Definições de colaboração externa Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Reveja as suas políticas de Acesso Condicional

- Certifique-se de excluir os hóspedes de quaisquer políticas de Acesso Condicional que os novos utilizadores convidados não possam cumprir, pois isso irá impedi-los de iniciar sedutá-lo no seu diretório. Por exemplo, os hóspedes provavelmente não têm um dispositivo registado, não estão num local conhecido, e não querem voltar a registar-se para a autenticação de vários fatores (MFA), pelo que a adição destes requisitos numa política de Acesso Condicional impedirá os hóspedes de usarem a gestão de direitos. Para mais informações, consulte [quais são as condições no Azure Ative Directory Conditional Access?](../conditional-access/concept-conditional-access-conditions.md)

    ![A política de acesso condicional Azure AD exclui definições](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Reveja as definições de partilha externa do SharePoint Online

- Se pretender incluir sites SharePoint Online nos seus pacotes de acesso para utilizadores externos, certifique-se de que a definição de partilha externa ao nível da organização é definida para **Qualquer pessoa** (os utilizadores não necessitam de iniciar sposição) ou **os hóspedes novos e existentes** (os hóspedes devem iniciar sposição ou fornecer um código de verificação). Para obter mais informações, consulte [Ligue ou desligue a partilha externa](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Se quiser restringir qualquer partilha externa fora da gestão de direitos, pode definir a definição de partilha externa para **os hóspedes existentes.** Em seguida, apenas os novos utilizadores que são convidados através da gestão de direitos poderão ter acesso a esses sites. Para obter mais informações, consulte [Ligue ou desligue a partilha externa](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Certifique-se de que as definições de nível do site permitem o acesso ao hóspede (as mesmas opções que as seleções anteriormente listadas). Para obter mais informações, consulte [Turn external sharing on or off for a site](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Reveja as definições de partilha de grupos microsoft 365

- Se pretender incluir os grupos Microsoft 365 nos seus pacotes de acesso a utilizadores externos, certifique-se de que **os utilizadores do Let adicionam novos hóspedes à organização** está definido para o **On** para permitir o acesso dos hóspedes. Para obter mais informações, consulte [Gerir o acesso dos hóspedes aos Grupos Microsoft 365.](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access)

- Se pretender que os utilizadores externos possam aceder ao site e recursos do SharePoint Online associados a um grupo Microsoft 365, certifique-se de que liga a partilha externa do SharePoint Online. Para obter mais informações, consulte [Ligue ou desligue a partilha externa](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Para obter informações sobre como definir a política de hóspedes para os grupos Microsoft 365 ao nível do diretório no PowerShell, consulte [exemplo: Configurar a política do Convidado para grupos ao nível do diretório](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Reveja as definições de partilha das suas equipas

- Se pretender incluir equipas nos seus pacotes de acesso a utilizadores externos, certifique-se de que o acesso dos hóspedes ao Site Permite que o acesso dos **hóspedes nas Equipas da Microsoft** esteja definido para **permitir** o acesso dos hóspedes. Para obter mais informações, consulte [o acesso dos hóspedes ao Configure no centro de administração da Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Gerir o ciclo de vida dos utilizadores externos

Pode selecionar o que acontece quando um utilizador externo, que foi convidado para o seu diretório através de um pedido de pacote de acesso que está a ser aprovado, já não tem nenhuma atribuição de pacotes de acesso. Isto pode acontecer se o utilizador renunciar a todas as suas atribuições de pacotes de acesso, ou se a sua última atribuição de pacote de acesso expirar. Por predefinição, quando um utilizador externo já não tem nenhuma atribuição de pacotes de acesso, estão impedidos de iniciar sessão no seu diretório. Após 30 dias, a conta de utilizador do hóspede é removida do seu diretório.

**Papel pré-requisito:** Administrador global ou administrador de utilizadores

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, na secção **de gestão de** **direitos,** clique em Definições .

1. Clique em **Editar**.

    ![Configurações para gerir o ciclo de vida dos utilizadores externos](./media/entitlement-management-external-users/settings-external-users.png)

1. Na secção **Gerir o ciclo de vida dos utilizadores externos,** selecione as diferentes definições para utilizadores externos.

1. Uma vez que um utilizador externo perca a sua última atribuição a quaisquer pacotes de acesso, se quiser impedi-los de iniciar sessão neste diretório, descreva o **utilizador externo do Bloco de iniciar sessão neste diretório** para **Sim**.

    > [!NOTE]
    > Se um utilizador estiver impedido de iniciar sessão neste diretório, o utilizador não poderá reencontrar o pacote de acesso ou solicitar acesso adicional neste diretório. Não configurá-los impedindo-os de se inscreverem se posteriormente precisarem de solicitar o acesso a outros pacotes de acesso.

1. Uma vez que um utilizador externo perca a sua última atribuição a quaisquer pacotes de acesso, se pretender remover a conta de utilizador do hóspede neste diretório, dedi o **utilizador externo** a **Sim**.

    > [!NOTE]
    > A gestão de direitos só remove contas que foram convidadas através da gestão de direitos. Além disso, note que um utilizador será impedido de iniciar sessão e removido deste diretório, mesmo que esse utilizador tenha sido adicionado a recursos neste diretório que não foram acessos de atribuição de pacotes. Se o hóspede esteve presente neste diretório antes de receber atribuição de pacotes de acesso, permanecerão. No entanto, se o hóspede foi convidado através de uma atribuição de pacote de acesso, e depois de ser convidado também foi atribuído a um site OneDrive para negócios ou SharePoint Online, eles ainda serão removidos.

1. Se pretender remover a conta de utilizador do utilizador neste diretório, pode definir o número de dias antes de ser removido. Se pretender remover a conta de utilizador do hóspede assim que perder a sua última atribuição a quaisquer pacotes de acesso, desagreva o Número de dias antes de retirar o **utilizador externo deste diretório** para **0**.

1. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar uma organização associada](entitlement-management-organization.md)
- [Para utilizadores que não estão no seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Resolução de problemas](entitlement-management-troubleshoot.md)
