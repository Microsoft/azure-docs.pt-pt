---
title: Delegar tarefas na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba mais sobre as funções que pode atribuir a delegar tarefas na gestão de direitos do Azure Active Directory.
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
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798640"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegar tarefas na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Por predefinição, os administradores globais e administradores de utilizador podem criar e gerir todos os aspetos da gestão de direitos do Azure AD. No entanto, os utilizadores nestas funções talvez não saiba todos os cenários em que os pacotes de acesso são necessárias. Normalmente, é os utilizadores dentro de departamentos que sabe que precisam de colaborar.

Em vez de conceder permissões sem restrições aos não-administradores, pode conceder aos utilizadores a, pelo menos, permissões necessárias para realizar seu trabalho e evite criar em conflito ou direitos de acesso inadequado. Este artigo descreve as funções que pode atribuir a delegar tarefas de várias na gestão de direitos.

## <a name="delegate-example-for-departmental-adoption"></a>Exemplo de delegado para a adoção departamental

Para compreender como pode delegar tarefas de gestão de direitos, ele ajuda a considerar um exemplo. Suponha que sua organização tem os seguintes cinco utilizadores:

| Utilizador | Departamento | Notas |
| --- | --- | --- |
| Alice | TI | Administrador global |
| BOB | Investigação | BOB também é proprietário de um grupo de pesquisa |
| Carole | Investigação |  |
| Dave | Marketing |  |
| Elisa | Marketing | ELISA também é proprietário de um aplicativo de Marketing |

A pesquisa e departamentos de Marketing pretendem utilizar a gestão de direitos para os seus utilizadores. Alice ainda não está pronta para outros departamentos utilizar a gestão de direitos. Eis uma forma que Alice pode delegar tarefas para a pesquisa e departamentos de marketing.

1. Alice cria um novo segurança do Azure AD para os criadores de catálogo de grupos e adiciona Bob, Carol, Dave e Elisa como membros desse grupo.

1. Alice utiliza as definições de gestão de direitos para adicionar esse grupo à função de criadores de catálogo.

1. Camila cria um **Research** catalogue e adiciona o Bob como coproprietário desse catálogo. BOB adiciona o grupo de pesquisa, que ele é proprietário para o catálogo de como um recurso, para que ele pode ser usado num pacote de acesso para colaboração de pesquisa.

1. Dave cria um **Marketing** catalogue e adiciona Elisa como coproprietário desse catálogo. ELISA adiciona a aplicação marketing que ela detém para o catálogo, como um recurso, para que ele pode ser usado num pacote de acesso para colaboração de marketing.

Agora, a pesquisa e os departamentos de marketing podem utilizar a gestão de direitos. BOB, Carol, Dave e Elisa, podem criar e gerir pacotes de acesso nos seus respectivos catálogos.

![Exemplo de delegado de gestão de direitos](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Funções de gestão de direitos

Gestão de direitos tem as seguintes funções que são específicas da gestão de direitos.

| Role | Descrição |
| --- | --- |
| Criador de catálogo | Criar e gerir catálogos. Normalmente, um administrador de TI que não seja um Administrador Global ou um proprietário do recurso para uma coleção de recursos. A pessoa que cria automaticamente um catálogo torna-se primeiro proprietário de catálogo no catálogo e pode adicionar os proprietários de catálogo adicionais. |
| Proprietário de catálogo | Editar e gerir catálogos existentes. Normalmente, um administrador de TI ou os proprietários de recursos ou um utilizador que tiver designado o proprietário do catálogo. |
| Gestor de pacotes de acesso | Editar e gerir todos os pacotes de acesso existentes dentro de um catálogo. |

Além disso, um aprovador designado e um requerente de um pacote de acesso também têm direitos, apesar de não são funções.
 
* Aprovador: Autorizado por uma política para aprovar ou recusar pedidos de acesso a pacotes, mas não podem alterar as definições de acesso do pacote.
* Requerente: Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso.

A tabela seguinte lista as tarefas que podem realizar estas funções.

| Tarefa | Criador de catálogo | Proprietário de catálogo | Gestor de pacotes de acesso | Aprovador |
| --- | :---: | :---: | :---: | :---: |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Adicionar um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Eliminar um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Adicionar um proprietário de catálogo ou um Gestor de pacotes de acesso a um catálogo](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Criar um novo pacote de acesso num catálogo](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Gerir funções de recursos num pacote de acesso](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Especifique quem pode pedir um pacote de acesso](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Atribuir diretamente um utilizador a um pacote de acesso](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ver quem tem uma atribuição a um pacote de acesso](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ver os pedidos de um pacote de acesso](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ver os erros de entrega de um pedido](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Cancelar um pedido pendente](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ocultar um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Eliminar um pacote de acesso](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Aprovar um pedido de acesso](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um Administrador Global pode adicionar ou remover qualquer grupo (grupos de segurança criados na nuvem ou nuvem criada grupos do Office 365), aplicação ou site do SharePoint Online num catálogo. Um administrador do utilizador pode adicionar ou remover qualquer grupo ou aplicação num catálogo.

Para um utilizador que não é um Administrador Global ou administrador de utilizador, para adicionar grupos, aplicações ou sites do SharePoint Online a um catálogo, esse utilizador tem de ter *ambos* o necessários do Azure AD directory função e catálogo proprietário elegibilidade função de gerenciamento. A tabela seguinte lista as combinações de função que são necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, tem de ter as mesmas funções.

| Função de diretório do Azure AD | Função de gestão de direitos | Pode adicionar o grupo de segurança | Pode adicionar o grupo do Office 365 | Pode adicionar aplicação | Pode adicionar o site do SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrador de utilizadores](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador do Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviços de equipes](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador da aplicação](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo |  |  | :heavy_check_mark: |  |
| [Administrador da aplicação cloud](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário de catálogo |  |  | :heavy_check_mark: |  |
| Utilizador | Proprietário de catálogo | Apenas se for proprietário de grupo | Apenas se for proprietário de grupo | Apenas se for proprietário da aplicação |  |

## <a name="add-a-catalog-creator"></a>Adicionar um criador de catálogo

Se quiser delegar a criação de catálogo, pode adicionar utilizadores à função de criador do catálogo.  Pode adicionar utilizadores individuais, ou para conveniência, pode adicionar um grupo, cujos membros são, em seguida, pode criar catálogos. Siga estes passos para atribuir um utilizador à função de criador de catálogo.

**Função de pré-requisitos:** Administrador global ou administrador de utilizadores

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, na **gestão de direitos** secção, clique em **definições**.

1. Clique em **Editar**.

1. Na **delegar a gestão de direitos** secção, clique em **adicionar criadores de catálogo** para selecionar os utilizadores ou grupos que serão membros para esta função de gestão de direitos.

1. Clique em **Selecionar**.

1. Clique em **Guardar**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Adicionar um proprietário de catálogo ou um Gestor de pacotes de acesso

Se quiser delegar a gestão de um acesso ou do catálogo de pacotes no catálogo, pode adicionar utilizadores para o proprietário de catálogo ou funções de Gestor de pacote de acesso. Quem cria um catálogo, torna-se o primeiro proprietário de catálogo. Siga estes passos para atribuir um utilizador para o proprietário de catálogo ou função de Gestor de pacote de acesso.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende adicionar os administradores.

1. No menu à esquerda, clique em **funções e os administradores**.

1. Clique em **adicionar proprietários** ou **adicionar gestores de pacotes de acesso** para selecionar os membros destas funções.

1. Clique em **selecione** para adicionar esses membros.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar aprovadores](entitlement-management-access-package-edit.md#policy-request)
- [Adicionar recursos a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
