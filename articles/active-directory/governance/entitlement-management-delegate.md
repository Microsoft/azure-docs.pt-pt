---
title: Delegar tarefas no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba mais sobre as funções que você pode atribuir para delegar tarefas no gerenciamento de direitos Azure Active Directory.
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4430e2115e4282ba7bb618184139a845547a06ed
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967082"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegar tarefas no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Por padrão, administradores globais e administradores de usuários podem criar e gerenciar todos os aspectos do gerenciamento de direitos do Azure AD. No entanto, os usuários nessas funções podem não conhecer todos os cenários em que os pacotes de acesso são necessários. Normalmente, são usuários em departamentos que sabem quem precisa colaborar.

Em vez de conceder permissões irrestritas para não administradores, você pode conceder aos usuários as permissões mínimas necessárias para executar seu trabalho e evitar a criação de direitos de acesso inadequados ou conflitantes. Este artigo descreve as funções que você pode atribuir para delegar várias tarefas no gerenciamento de direitos.

## <a name="delegate-example-for-departmental-adoption"></a>Exemplo de delegado para adoção departamental

Para entender como você pode delegar tarefas no gerenciamento de direitos, ele ajuda a considerar um exemplo. Suponha que sua organização tenha os cinco usuários a seguir:

| Utilizador | Departamento | Notas |
| --- | --- | --- |
| Alice | it | Administrador Global |
| BOB | Investigação | Bob também é proprietário de um grupo de pesquisa |
| Carole | Investigação |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa também é proprietário de um aplicativo de marketing |

Os departamentos de pesquisa e de marketing desejam usar o gerenciamento de direitos para seus usuários. Alice ainda não está pronta para que outros departamentos usem o gerenciamento de direitos. Aqui está uma maneira que Alice poderia delegar tarefas aos departamentos de pesquisa e marketing.

1. Alice cria um novo grupo de segurança do Azure AD para criadores de catálogo e adiciona Bob, Carol, Dave e ELISA como membros desse grupo.

1. Alice usa as configurações de gerenciamento de direitos para adicionar esse grupo à função criadores de catálogo.

1. Carol cria um catálogo de **pesquisa** e adiciona Bob como um coproprietário desse catálogo. Bob adiciona o grupo de pesquisa que ele possui ao catálogo como um recurso, para que ele possa ser usado em um pacote de acesso para colaboração de pesquisa.

1. Dave cria um catálogo de **marketing** e adiciona ELISA como um coproprietário desse catálogo. Elisa adiciona o aplicativo de marketing que ela possui ao catálogo como um recurso, para que possa ser usada em um pacote de acesso para colaboração de marketing.

Agora, os departamentos de pesquisa e marketing podem utilizar o gerenciamento de direitos. Bob, Carol, Dave e Elisa podem criar e gerenciar pacotes de acesso em seus respectivos catálogos.

![Exemplo de delegado de gerenciamento de direitos](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Funções de gerenciamento de direitos

O gerenciamento de direitos tem as seguintes funções que são específicas para o gerenciamento de direitos.

| Role | Descrição |
| --- | --- |
| Criador do catálogo | Criar e gerenciar catálogos. Normalmente, um administrador de ti que não é um administrador global ou um proprietário de recurso para uma coleção de recursos. A pessoa que cria um catálogo torna-se automaticamente o primeiro proprietário do catálogo do catálogo e pode adicionar proprietários de catálogos adicionais. |
| Proprietário do catálogo | Edite e gerencie catálogos existentes. Normalmente, um administrador de ti ou proprietários de recursos ou um usuário que o proprietário do catálogo designou. |
| Gerenciador de pacotes de acesso | Edite e gerencie todos os pacotes do Access existentes em um catálogo. |

Além disso, um Aprovador designado e um solicitante de um pacote de acesso também têm direitos, embora não sejam funções.
 
* Aprovador: Autorizado por uma política para aprovar ou negar solicitações para acessar pacotes, embora eles não possam alterar as definições de pacote de acesso.
* Solicitante Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso.

A tabela a seguir lista as tarefas que essas funções podem executar.

| Tarefa | Criador do catálogo | Proprietário do catálogo | Gerenciador de pacotes de acesso | Aprovador |
| --- | :---: | :---: | :---: | :---: |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Adicionar um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Excluir um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Adicionar um proprietário do catálogo ou um Gerenciador de pacotes do Access a um catálogo](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Criar um novo pacote do Access em um catálogo](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Gerenciar funções de recurso em um pacote de acesso](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Criar e editar políticas](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Atribuir diretamente um usuário a um pacote do Access](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Exibir quem tem uma atribuição para um pacote de acesso](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Exibir solicitações de um pacote de acesso](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Exibir os erros de entrega de uma solicitação](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cancelar uma solicitação pendente](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ocultar um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Excluir um pacote de acesso](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aprovar uma solicitação de acesso](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um administrador global pode adicionar ou remover qualquer grupo (grupos de segurança criados na nuvem ou grupos do Office 365 criados na nuvem), aplicativo ou site do SharePoint Online em um catálogo. Um administrador de usuário pode adicionar ou remover qualquer grupo ou aplicativo em um catálogo.

Para um usuário que não seja um administrador global ou um administrador de usuário, para adicionar grupos, aplicativos ou sites do SharePoint Online a um catálogo, esse usuário deve ter a função de gerenciamento de direitos e a função de diretório do Azure ad necessária. A tabela a seguir lista as combinações de função que são necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, você deve ter as mesmas funções.

| Função de diretório do Azure AD | Função de gerenciamento de direitos | Pode adicionar grupo de segurança | Pode adicionar o grupo do Office 365 | Pode adicionar aplicativo | Pode adicionar site do SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrador do usuário](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador do Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviços de equipes](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador de aplicativos](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| [Administrador de aplicativos de nuvem](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| Utilizador | Proprietário do catálogo | Somente se o proprietário do grupo | Somente se o proprietário do grupo | Somente se o proprietário do aplicativo |  |

## <a name="add-a-catalog-creator"></a>Adicionar um criador de catálogo

Se você quiser delegar a criação do catálogo, adicione usuários à função criador do catálogo.  Você pode adicionar usuários individuais ou, para conveniência, pode adicionar um grupo, cujos membros são então capazes de criar catálogos. Siga estas etapas para atribuir um usuário à função de criador de catálogo.

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

1. Na seção **Gerenciamento de direitos de representante** , clique em **Adicionar criadores de catálogo** para selecionar os usuários ou grupos que serão os membros para essa função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Guardar**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Adicionar um proprietário do catálogo ou um Gerenciador de pacotes do Access

Se você quiser delegar o gerenciamento de um catálogo ou acessar pacotes no catálogo, adicione usuários ao proprietário do catálogo ou às funções do Gerenciador de pacotes do Access. Quem cria um catálogo se torna o primeiro proprietário do catálogo. Siga estas etapas para atribuir um usuário ao proprietário do catálogo ou à função do Gerenciador de pacotes do Access.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em catálogos e, em seguida, abra o catálogo ao qual você deseja adicionar administradores.

1. No menu à esquerda, clique em **funções e administradores**.

1. Clique em **Adicionar proprietários** ou **Adicionar gerenciadores de pacotes de acesso** para selecionar os membros dessas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar aprovadores](entitlement-management-access-package-edit.md#policy-request)
- [Adicionar recursos a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
