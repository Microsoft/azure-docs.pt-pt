---
title: Delegar funções de administrador de aplicações - Azure Active Directory | Documentos da Microsoft
description: Gestão de acesso de aplicações delegar funções para conceder direitos de permissões no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9409d196e307b75a14a272dee6c8c93789ca8556
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669793"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegar funções de administrador da aplicação no Azure Active Directory

 O Azure AD permite-lhe delegar a gestão de acesso de aplicação a um conjunto de funções administrativas incorporadas. Além de reduzir a sobrecarga de administrador global, o delegar privilégios especializados para gerir as tarefas de acesso de aplicação pode melhorar a sua postura de segurança e reduzir o potencial de acesso não autorizado. Problemas de delegação e Diretrizes gerais são discutidas [delegar a administração no Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegar a administração de aplicação

As seguintes funções de concedem permissões para gerir registos de aplicação, as definições de início de sessão únicas, utilizador e as atribuições de grupo e para dar consentimento a permissões delegadas e permissões de aplicação (excluindo o Microsoft Graph e o Azure AD Graph). A única diferença é que a função de administrador do aplicativo também concede permissões para gerir definições de Proxy de aplicações. Nenhuma função concede a capacidade para gerir as definições de acesso condicional.
> [!IMPORTANT]
> Utilizadores atribuídos esta função podem adicionar as credenciais para um aplicativo e usar as credencias para representar a identidade da aplicação. Essa representação de identidade da aplicação pode ser uma elevação do privilégio ao longo do que o utilizador pode fazer em suas atribuições de funções no Azure AD. Um utilizador atribuído a esta função potencialmente poderia criar ou atualizar utilizadores ou outros objetos quando for representar o aplicativo.

Para conceder a capacidade para gerir o acesso de aplicação no portal do Azure:

1. Inicie sessão no seu [inquilino do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta ou elegível para a função de Administrador Global no inquilino.
2. Quando tiver permissões suficientes, abra a [página de funções e os administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Abra uma das seguintes funções para ver as atribuições de membro:
  * **Administrador da aplicação**
  * **Administrador da aplicação cloud**
4. Sobre o **membros** página para a função, selecione **Add member**.
5. Selecione um ou mais membros a adicionar à função. <!--Members can be users or groups.-->

Pode exibir a descrição para estas funções no [funções disponíveis](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegar o registo de aplicações

Por predefinição, todos os utilizadores podem criar registos de aplicações, mas pode seletivamente conceder permissão para dar consentimento para autorizar uma aplicação ou de permissão para criar registos de aplicações.

1. Inicie sessão no seu [inquilino do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta ou elegível para a função de Administrador Global no inquilino.
2. Quando tiver obtido permissões suficientes, defina uma ou ambas das seguintes ações:
  * Sobre o [página de definições de utilizador para o seu inquilino](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), defina **os utilizadores podem registar aplicações** para não.
  * Sobre o [definições de utilizador para aplicações empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), defina **os utilizadores podem dar consentimento a aplicações aceder aos dados da empresa em nome deles** para não.
3. Em seguida, atribua utilizadores que necessitam desta permissão para serem membros da função de desenvolvedor do aplicativo conforme necessário.

Quando um usuário registra um aplicativo, eles são adicionados automaticamente como o primeiro proprietário para a aplicação.

## <a name="delegate-app-ownership"></a>Delegar a propriedade de aplicação

Proprietários e os proprietários de registo de aplicação podem cada gerir apenas as aplicações de aplicação ou os registos de aplicações que possuem. Por exemplo, quando adiciona um proprietário para a aplicação do Salesforce, o que esse proprietário pode gerir acesso e a configuração para Salesforce, mas não todas as outras aplicações. Uma aplicação pode ter muitos proprietários e o utilizador pode ser o proprietário de muitos dos aplicativos.

Um proprietário da aplicação pode:

* Alterar propriedades da aplicação, como o nome e permissões os pedidos de aplicação
* Gerir credenciais
* Configurar o início de sessão único
* Atribuir acesso de utilizador
* Adicionar ou remover outros proprietários
* Editar o manifesto da aplicação
* Publicar a aplicação na Galeria de aplicações

> [!IMPORTANT]
> Utilizadores atribuídos esta função podem adicionar as credenciais para um aplicativo e usar as credencias para representar a identidade da aplicação. Essa representação de identidade da aplicação pode ser uma elevação do privilégio ao longo do que o utilizador pode fazer em suas atribuições de funções no Azure AD. Um utilizador atribuído a esta função potencialmente poderia criar ou atualizar utilizadores ou outros objetos quando for representar o aplicativo.

O proprietário de um registo de aplicação pode ver e editar o registo de aplicações.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Para atribuir um proprietário a uma aplicação

1. Inicie sessão no seu [inquilino do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta ou elegível para o administrador da aplicação ou o administrador da aplicação de Cloud para o inquilino.
2. Sobre o [página de registos de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) para o inquilino, selecione uma aplicação para abrir o **descrição geral** página da aplicação.
3. Selecione **proprietários** para ver a lista de proprietários da aplicação.
4. Selecione **adicionar** para selecionar um ou mais proprietário para adicionar à aplicação.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Para atribuir um proprietário para um registo de aplicação

1. Inicie sessão no seu [inquilino do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta ou elegível para o administrador da aplicação ou a função de administrador do aplicativo em nuvem no inquilino.
2. Se tiver permissões suficientes, diante a [página de aplicações empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) para o inquilino, selecione um registo de aplicações para abri-lo.
3. Selecione **definições**.
4. Selecione **proprietários** sobre o **definições** página para ver a lista de proprietários da aplicação.
5. Selecione **proprietário adicionar** para selecionar um ou mais proprietário para adicionar à aplicação.

## <a name="next-steps"></a>Passos Seguintes

* [Referência de função de administrador do Azure AD](directory-assign-admin-roles.md)
