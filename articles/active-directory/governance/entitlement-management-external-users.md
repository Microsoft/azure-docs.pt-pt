---
title: Reger o acesso a utilizadores externos na gestão de direitos da AD Azure - Diretório Ativo Azure
description: Saiba mais sobre as definições que pode especificar para reger o acesso a utilizadores externos na gestão de direitos do Diretório Ativo Do Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7e4837aaf65b0df28ea4a07fb485948309bc7c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261830"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Reger o acesso a utilizadores externos na gestão de direitos da AD Azure

A Azure AD entitlement management utiliza [o Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) para colaborar com pessoas fora da sua organização em outro diretório. Com o Azure AD B2B, os utilizadores externos autenticam o seu diretório de casa, mas têm representação no seu diretório. A representação no seu diretório permite ao utilizador ter acesso aos seus recursos.

Este artigo descreve as definições que pode especificar para reger o acesso a utilizadores externos.

## <a name="how-entitlement-management-can-help"></a>Como a gestão de direitos pode ajudar

Ao utilizar a experiência de convite [Azure AD B2B,](../b2b/what-is-b2b.md) já deve conhecer os endereços de e-mail dos utilizadores externos de hóspedes que pretende trazer para o seu diretório de recursos e trabalhar com. Isto funciona muito bem quando se está a trabalhar num projeto de menor ou curto prazo e já conhece todos os participantes, mas isso é mais difícil de gerir se tiver muitos utilizadores com quem quer trabalhar ou se os participantes mudam ao longo do tempo.  Por exemplo, você pode estar trabalhando com outra organização e ter um ponto de contacto com essa organização, mas com o tempo outros utilizadores dessa organização também precisarão de acesso.

Com a gestão de direitos, pode definir uma política que permite aos utilizadores de organizações que especifica ser capaz de solicitar um pacote de acesso. Pode especificar se a aprovação é necessária e uma data de validade para o acesso. Se for necessária a aprovação, também pode convidar um ou mais utilizadores da organização externa para o seu diretório e designá-los como aprovadores - uma vez que é provável que saibam quais os utilizadores externos da sua organização que precisam de acesso. Depois de configurar o pacote de acesso, pode enviar o link do pacote de acesso para a sua pessoa de contacto (patrocinador) na organização externa. Esse contacto pode partilhar com outros utilizadores da organização externa, e podem usar este link para solicitar o pacote de acesso. Os utilizadores dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando um pedido for aprovado, a gestão do direito fornecerá ao utilizador o acesso necessário, o que pode incluir convidar o utilizador se ainda não estiver no seu diretório. A Azure AD criará automaticamente uma conta de hóspedes B2B para eles. Note que um administrador pode ter anteriormente limitado quais as organizações que são permitidas para colaboração, definindo uma [lista B2B permitir ou negar](../b2b/allow-deny-list.md) lista para permitir ou bloquear convites para outras organizações.  Se o utilizador não for autorizado pela lista de permitir ou bloquear, não será convidado.

Uma vez que não quer que o acesso do utilizador externo dure para sempre, especifice uma data de validade na apólice, como 180 dias. Após 180 dias, se o seu acesso não for alargado, a gestão do direito removerá todos os acessos associados a esse pacote de acesso. Por predefinição, se o utilizador que foi convidado através da gestão de direitos não tiver outras atribuições de pacotes de acesso, então quando perder a sua última atribuição, a sua conta de hóspedes será bloqueada de iniciar sessão durante 30 dias e posteriormente removida. Isto impede a proliferação de contas desnecessárias. Tal como descrito nas seguintes secções, estas definições são configuráveis.

## <a name="how-access-works-for-external-users"></a>Como funciona o acesso a utilizadores externos

O diagrama e os passos seguintes fornecem uma visão geral de como os utilizadores externos têm acesso a um pacote de acesso.

![Diagrama mostrando o ciclo de vida de utilizadores externos](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Adicione [uma organização conectada](entitlement-management-organization.md) para o diretório ou domínio azure com o que pretende colaborar.

1. Cria um pacote de acesso no seu diretório que inclui uma política [para utilizadores que não estão no seu diretório.](entitlement-management-access-package-create.md#for-users-not-in-your-directory)

1. Envia um link do [portal My Access](entitlement-management-access-package-settings.md) para o seu contacto na organização externa que pode partilhar com os seus utilizadores para solicitar o pacote de acesso.

1. Um utilizador externo **(Requestor A** neste exemplo) utiliza o link do portal My Access para solicitar o acesso ao pacote de [acesso.](entitlement-management-request-access.md) A forma como o utilizador assina depende do tipo de autenticação do diretório ou domínio definido na organização conectada.

1. Um aprovador [aprova o pedido](entitlement-management-request-approve.md) (ou o pedido é aprovado automaticamente).

1. O pedido vai para o estado de [entrega.](entitlement-management-process.md)

1. Utilizando o processo de convite B2B, é criada uma conta de utilizador convidado no seu diretório (**Requestor A (Convidado)** neste exemplo). Se for definida uma lista de autorizações ou uma lista de [negação,](../b2b/allow-deny-list.md) a definição da lista será aplicada.

1. O utilizador convidado tem acesso a todos os recursos do pacote de acesso. Pode levar algum tempo para que as alterações sejam feitas em Azure AD e em outros Serviços Online da Microsoft ou aplicações SaaS conectadas. Para mais informações, consulte [Quando são aplicadas alterações](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. O utilizador externo recebe um e-mail indicando que o seu acesso foi [entregue.](entitlement-management-process.md)

1. Para aceder aos recursos, o utilizador externo pode clicar no link no e-mail ou tentar aceder diretamente a qualquer um dos recursos de diretório para completar o processo de convite.

1. Dependendo das definições de política, à medida que o tempo passa, a atribuição do pacote de acesso para o utilizador externo expira e o acesso do utilizador externo é removido.

1. Dependendo do ciclo de vida das definições de utilizadores externos, quando o utilizador externo já não tem nenhuma atribuição de pacotes de acesso, o utilizador externo está impedido de iniciar sessão e a conta de utilizador convidado é removida do seu diretório.

## <a name="settings-for-external-users"></a>Definições para utilizadores externos

Para garantir que as pessoas fora da sua organização possam solicitar pacotes de acesso e ter acesso aos recursos desses pacotes de acesso, existem algumas configurações que deve verificar que estão devidamente configuradas.

### <a name="enable-catalog-for-external-users"></a>Ativar o catálogo para utilizadores externos

- Por predefinição, quando cria um [novo catálogo,](entitlement-management-catalog-create.md)está habilitado a permitir que utilizadores externos solicitem pacotes de acesso no catálogo. Certifique-se de que **o Ativado para utilizadores externos** está definido para **Sim**.

    ![Editar definições de catálogo](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Configure as definições de colaboração externa Azure AD B2B

- Permitir que os hóspedes convidem outros hóspedes para o seu diretório significa que os convites dos hóspedes podem ocorrer fora da gestão do direito. Recomendamos que os **hóspedes possam convidar** a **Não** para permitir apenas convites devidamente governados.
- Se estiver a utilizar a lista de licenças B2B, deve certificar-se de que qualquer domínio que queira associar à gestão de direitos é adicionado à lista. Em alternativa, se estiver a utilizar a lista de negação B2B, tem de se certificar de que qualquer domínio com o qual pretenda associar-se não é adicionado à lista.
- Se criar uma política de gestão de direitos para **todos os utilizadores** (Todas as organizações conectadas + quaisquer novos utilizadores externos), qualquer B2B permitir á de si ou negar configurações de lista que tenha terá precedência. Portanto, certifique-se de incluir os domínios que pretende incluir nesta política à sua lista de licenças se estiver a usar um, e excluí-los da sua lista de negação se estiver a usar uma lista de negação.
- Se pretender criar uma política de gestão de direitos que inclua **todos os utilizadores** (Todas as organizações conectadas + quaisquer novos utilizadores externos), tem primeiro de ativar a autenticação de código de acesso único por e-mail para o seu diretório. Para mais informações, consulte a [autenticação de senha única por e-mail (pré-visualização)](../b2b/one-time-passcode.md#opting-in-to-the-preview).
- Para obter mais informações sobre as definições de colaboração externa Azure AD B2B, consulte a [colaboração externa Enable B2B e gerequem quem pode convidar os hóspedes.](../b2b/delegate-invitations.md)

    ![Definições de colaboração externa da Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Reveja as suas políticas de Acesso Condicional

- Certifique-se de excluir os hóspedes de quaisquer políticas de Acesso Condicional que os novos utilizadores não possam cumprir, uma vez que isso os impedirá de iniciar sessão no seu diretório. Por exemplo, os hóspedes provavelmente não têm um dispositivo registado, não estão num local conhecido, e não querem voltar a registar-se para autenticação multi-fator (MFA), por isso, adicionar estes requisitos numa política de Acesso Condicional impedirá os hóspedes de usar o direito gestão. Para mais informações, consulte Quais são as condições no Acesso Condicional do [Diretório Ativo do Azure?](../conditional-access/concept-conditional-access-conditions.md)

    ![Política de Acesso Condicional Azure AD exclui definições](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Reveja as definições de partilha externa sharePoint Online

- Se pretender incluir sites SharePoint Online nos seus pacotes de acesso para utilizadores externos, certifique-se de que a definição de partilha externa ao nível da organização está definida para **qualquer pessoa** (os utilizadores não precisam de iniciar sessão) ou novos **e hóspedes existentes** (os hóspedes devem iniciar sessão ou fornecer um código de verificação). Para mais informações, consulte [a partilha externa ligar ou desligar.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Se quiser restringir qualquer partilha externa fora da gestão de direitos, pode definir o cenário de partilha externa aos **hóspedes existentes.** Depois, apenas novos utilizadores que são convidados através da gestão de direitos poderão ter acesso a esses sites. Para mais informações, consulte [a partilha externa ligar ou desligar.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Certifique-se de que as definições de nível do site permitem o acesso ao hóspede (as mesmas seleções de opções que anteriormente listadas). Para mais informações, consulte [a partilha externa de um site](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Reveja as definições de partilha de grupo seleções do Office 365

- Se pretender incluir os grupos office 365 nos seus pacotes de acesso a utilizadores externos, certifique-se de que os utilizadores do **Let adicionam novos hóspedes à organização** para permitir o acesso dos hóspedes. Para mais informações, consulte [Gerir o acesso dos hóspedes ao Office 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Se quiser que os utilizadores externos possam aceder ao site e recursos do SharePoint Online associados a um grupo do Office 365, certifique-se de que liga a partilha externa do SharePoint Online. Para mais informações, consulte [a partilha externa ligar ou desligar.](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)

- Para obter informações sobre como definir a política de hóspedes para os grupos office 365 ao nível do diretório na PowerShell, consulte [Exemplo: Configure A política de hóspedes para grupos ao nível do diretório](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Reveja as definições de partilha das suas Equipas

- Se pretender incluir equipas nos seus pacotes de acesso para utilizadores externos, **certifique-se** de que o acesso dos hóspedes nas Equipas microsoft está definido para O Acesso ao **Hóspede.** Para mais informações, consulte o acesso dos [hóspedes da Configure no centro de administração da Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Gerir o ciclo de vida dos utilizadores externos

Pode selecionar o que acontece quando um utilizador externo, que foi convidado para o seu diretório através de um pedido de pacote de acesso aprovado, já não tem nenhuma atribuição de pacotes de acesso. Isto pode acontecer se o utilizador renunciar a todas as suas atribuições de pacotes de acesso, ou a sua última atribuição de pacotede acesso expirar. Por predefinição, quando um utilizador externo já não tem nenhuma atribuição de pacotes de acesso, está impedido de iniciar sessão no seu diretório. Após 30 dias, a sua conta de utilizador convidado é removida do seu diretório.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, na secção **de gestão** do Direito, clique em **Definições**.

1. Clique em **Editar**.

    ![Configurações para gerir o ciclo de vida dos utilizadores externos](./media/entitlement-management-external-users/settings-external-users.png)

1. Na secção **Gerir o ciclo de vida dos utilizadores externos,** selecione as diferentes definições para utilizadores externos.

1. Uma vez que um utilizador externo perca a sua última atribuição a quaisquer pacotes de acesso, se quiser impedi-los de iniciar a sua sessão neste diretório, descontete o **utilizador externo do Bloco de iniciar sessão neste diretório** para **Sim**.

    > [!NOTE]
    > Se um utilizador estiver impedido de iniciar sessão neste diretório, o utilizador não poderá resolicitar o pacote de acesso ou solicitar acesso adicional neste diretório. Não configure bloqueá-los impedindo-os de iniciar a sua inscrição se posteriormente precisarem de solicitar acesso a outros pacotes de acesso.

1. Uma vez que um utilizador externo perca a sua última atribuição a quaisquer pacotes de acesso, se pretender remover a sua conta de utilizador convidado neste diretório, desempente **o utilizador externo** para **Sim**.

    > [!NOTE]
    > A gestão de direitos apenas remove contas que foram convidadas através da gestão de direitos. Além disso, note que um utilizador será impedido de iniciar sessão e removido deste diretório mesmo que esse utilizador tenha sido adicionado aos recursos neste diretório que não foram atribuições de pacotes de acesso. Se o hóspede estivesse presente neste diretório antes de receber as atribuições do pacote de acesso, permanecerão. No entanto, se o hóspede foi convidado através de uma atribuição de pacote de acesso, e depois de convidado também foi designado para um site OneDrive para Negócios ou SharePoint Online, eles ainda serão removidos.

1. Se pretender remover a conta de utilizador convidado neste diretório, pode definir o número de dias antes de ser removido. Se pretender remover a conta de utilizador convidado assim que perder a sua última atribuição a quaisquer pacotes de acesso, desempente o número de dias antes de retirar o **utilizador externo deste diretório** para **0**.

1. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar uma organização conectada](entitlement-management-organization.md)
- [Para utilizadores que não estão no seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Resolução de problemas](entitlement-management-troubleshoot.md)
