---
title: Controlar o acesso para usuários externos no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba mais sobre as configurações que você pode especificar para controlar o acesso para usuários externos no gerenciamento de direitos Azure Active Directory.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3794f409b2cdc11373dc330099e5ff93d65a2a1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934404"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Controlar o acesso para usuários externos no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O gerenciamento de direitos do Azure AD utiliza o [B2B (Business-to-Business) do Azure ad](../b2b/what-is-b2b.md) para colaborar com pessoas de fora da sua organização em outro diretório. Com o Azure AD B2B, os usuários externos se autenticam em seu diretório base, mas têm uma representação em seu diretório. A representação em seu diretório permite que o usuário receba acesso aos seus recursos.

Este artigo descreve as configurações que você pode especificar para controlar o acesso para usuários externos.

## <a name="how-entitlement-management-can-help"></a>Como o gerenciamento de direitos pode ajudar

Ao usar a experiência de convite [B2B do Azure ad](../b2b/what-is-b2b.md) , você já deve conhecer os endereços de email dos usuários convidados externos que deseja colocar em seu diretório de recursos e trabalhar com o. Isso funciona muito bem quando você está trabalhando em um projeto de menor ou curto prazo e já conhece todos os participantes, mas isso é mais difícil de gerenciar se você tiver muitos usuários com os quais deseja trabalhar ou se os participantes mudarem ao longo do tempo.  Por exemplo, você pode estar trabalhando com outra organização e ter um ponto de contato com essa organização, mas com o passar do tempo, usuários adicionais dessa organização também precisarão de acesso.

Com o gerenciamento de direitos, você pode definir uma política que permite aos usuários de organizações que você especificar para poder solicitar automaticamente um pacote de acesso. Você pode especificar se a aprovação é necessária e uma data de validade para o acesso. Se a aprovação for necessária, você também poderá convidar um ou mais usuários da organização externa para seu diretório e designá-los como aprovadores – já que é provável que eles saibam quais usuários externos de sua organização precisam de acesso. Depois de configurar o pacote de acesso, você pode enviar o link do pacote de acesso para sua pessoa de contato (patrocinador) na organização externa. Esse contato pode compartilhar com outros usuários na organização externa e pode usar esse link para solicitar o pacote de acesso. Os usuários dessa organização que já foram convidados para o seu diretório também podem usar esse link.

Quando uma solicitação é aprovada, o gerenciamento de direitos provisionará o usuário com o acesso necessário, que pode incluir o convite do usuário se ele ainda não estiver em seu diretório. O Azure AD criará automaticamente uma conta de convidado B2B para eles. Observe que um administrador pode ter limitado anteriormente quais organizações têm permissão para colaboração, definindo uma [lista de permissão ou negação B2B](../b2b/allow-deny-list.md) para permitir ou bloquear convites para outras organizações.  Se o usuário não for permitido pela lista de permissões ou bloqueios, eles não serão convidados.

Como você não deseja que o acesso do usuário externo seja feito por último, especifique uma data de validade na política, como 180 dias. Após 180 dias, se o acesso não for estendido, o gerenciamento de direitos removerá todo o acesso associado a esse pacote de acesso. Por padrão, se o usuário que foi convidado por meio do gerenciamento de direitos não tiver outras atribuições de pacote de acesso, quando perderem sua última atribuição, sua conta de convidado será impedida de entrar por 30 dias e, subsequentemente, removida. Isso impede a proliferação de contas desnecessárias. Conforme descrito nas seções a seguir, essas configurações são configuráveis.

## <a name="how-access-works-for-external-users"></a>Como o Access funciona para usuários externos

O diagrama e as etapas a seguir fornecem uma visão geral de como os usuários externos recebem acesso a um pacote do Access.

![Diagrama mostrando o ciclo de vida de usuários externos](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Você [adiciona uma organização conectada](entitlement-management-organization.md) para o diretório ou domínio do Azure AD com o qual você deseja colaborar.

1. Você cria um pacote do Access em seu diretório que inclui uma política [para os usuários que não estão em seu diretório](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Você envia um [link meu portal de acesso](entitlement-management-access-package-settings.md) para seu contato na organização externa que eles podem compartilhar com seus usuários para solicitar o pacote de acesso.

1. Um usuário externo (**solicitante a** neste exemplo) usa o link meu portal de acesso para [solicitar acesso](entitlement-management-request-access.md) ao pacote de acesso. A forma como o usuário entra depende do tipo de autenticação do diretório ou do domínio definido na organização conectada.

1. Um Aprovador [aprova a solicitação](entitlement-management-request-approve.md) (ou a solicitação é aprovada automaticamente).

1. A solicitação entra no [estado entrega](entitlement-management-process.md).

1. Usando o processo de convite B2B, uma conta de usuário convidado é criada em seu diretório (**solicitante a (convidado)** neste exemplo). Se uma [lista de permissões ou uma lista de negações](../b2b/allow-deny-list.md) for definida, a configuração da lista será aplicada.

1. O usuário convidado recebe acesso a todos os recursos no pacote de acesso. Pode levar algum tempo para que as alterações sejam feitas no Azure AD e em outros serviços online da Microsoft ou aplicativos SaaS conectados. Para obter mais informações, consulte [quando as alterações são aplicadas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. O usuário externo recebe um email indicando que o acesso foi [entregue](entitlement-management-process.md).

1. Para acessar os recursos, o usuário externo pode clicar no link no email ou tentar acessar qualquer um dos recursos de diretório diretamente para concluir o processo de convite.

1. Dependendo das configurações de política, à medida que o tempo passa, a atribuição de pacote de acesso para o usuário externo expira e o acesso do usuário externo é removido.

1. Dependendo do ciclo de vida de configurações de usuários externos, quando o usuário externo não tiver mais nenhuma atribuição de pacote de acesso, o usuário externo será impedido de entrar e a conta de usuário convidado será removida do seu diretório.

## <a name="manage-the-lifecycle-of-external-users"></a>Gerenciar o ciclo de vida de usuários externos

Você pode selecionar o que acontece quando um usuário externo, que foi convidado para seu diretório por meio de uma solicitação de pacote de acesso que está sendo aprovada, não tem mais nenhuma atribuição de pacote de acesso. Isso pode acontecer se o usuário ceder todas as suas atribuições de pacote de acesso ou sua última atribuição de pacote de acesso expirar. Por padrão, quando um usuário externo não tem mais nenhuma atribuição de pacote de acesso, ele é impedido de entrar no seu diretório. Após 30 dias, sua conta de usuário convidado é removida do seu diretório.

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

    ![Configurações para gerenciar o ciclo de vida de usuários externos](./media/entitlement-management-external-users/settings-external-users.png)

1. Na seção **gerenciar o ciclo de vida de usuários externos** , selecione as configurações diferentes para usuários externos.

1. Depois que um usuário externo perder sua última atribuição para qualquer pacote de acesso, se você quiser bloqueá-las de entrar nesse diretório, defina o **bloco usuário externo de entrar nesse diretório** como **Sim**.

    > [!NOTE]
    > Se um usuário estiver impedido de entrar nesse diretório, o usuário não poderá solicitar novamente o pacote de acesso ou solicitar acesso adicional nesse diretório. Não configure o bloqueio da entrada se eles posteriormente precisarem solicitar acesso a outros pacotes de acesso.

1. Depois que um usuário externo perder sua última atribuição para qualquer pacote de acesso, se você quiser remover sua conta de usuário convidado nesse diretório, defina **remover usuário externo** como **Sim**.

    > [!NOTE]
    > O gerenciamento de direitos remove apenas as contas que foram convidadas por meio do gerenciamento de direitos. Além disso, observe que um usuário será impedido de entrar e remover esse diretório, mesmo que esse usuário tenha sido adicionado aos recursos nesse diretório que não tenham acesso às atribuições de pacote. Se o convidado estava presente nesse diretório antes de receber atribuições de pacote de acesso, ele permanecerá. No entanto, se o convidado foi convidado por meio de uma atribuição de pacote de acesso e, depois de ser convidado também foi atribuído a um site do OneDrive for Business ou do SharePoint Online, ele ainda será removido.

1. Se você quiser remover a conta de usuário convidado nesse diretório, poderá definir o número de dias antes que ele seja removido. Se você quiser remover a conta de usuário convidado assim que perder sua última atribuição para qualquer pacote de acesso, defina o **número de dias antes de remover o usuário externo desse diretório** para **0**.

1. Clique em **Guardar**.

## <a name="enable-a-catalog-for-external-users"></a>Habilitar um catálogo para usuários externos

Quando você cria um [novo catálogo](entitlement-management-catalog-create.md), há uma configuração para permitir que os usuários de diretórios externos solicitem pacotes de acesso no catálogo. Se você não quiser que usuários externos tenham permissões para solicitar pacotes de acesso no catálogo, defina **habilitado para usuários externos** como **não**.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

![Painel novo catálogo](./media/entitlement-management-shared/new-catalog.png)

Você também pode alterar essa configuração depois de criar o catálogo.

![Editar configurações do catálogo](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicionar uma organização conectada](entitlement-management-organization.md)
- [Para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
