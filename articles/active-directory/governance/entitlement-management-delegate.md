---
title: Delegação e funções no gerenciamento de direitos-Azure AD
description: Saiba como delegar a governança de acesso de administradores de ti a gerentes de departamento e gerentes de projeto para que eles possam gerenciar o acesso.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422590"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegação e funções no gerenciamento de direitos do Azure AD

Por padrão, administradores globais e administradores de usuários podem criar e gerenciar todos os aspectos do gerenciamento de direitos do Azure AD. No entanto, os usuários nessas funções podem não saber todas as situações em que os pacotes de acesso são necessários. Normalmente, são os usuários dentro dos respectivos departamentos, equipes ou projetos que sabem com quem eles estão colaborando, usando quais recursos e por quanto tempo. Em vez de conceder permissões irrestritas para não administradores, você pode conceder aos usuários as permissões mínimas necessárias para executar seu trabalho e evitar a criação de direitos de acesso inadequados ou conflitantes.

Este vídeo fornece uma visão geral de como delegar a governança de acesso do administrador de ti aos usuários que não são administradores.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemplo de delegado

Para entender como você pode delegar a governança de acesso no gerenciamento de direitos, ele ajuda a considerar um exemplo. Suponha que sua organização tenha os seguintes administradores e gerentes.

![Delegar do administrador de ti para gerentes](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Como o administrador de ti, o Hana tem contatos em cada departamento – Mamta em marketing, Mark in Finance e Joe, legal, responsáveis pelos recursos do departamento e pelo conteúdo comercialmente crítico.

Com o gerenciamento de direitos, você pode delegar a governança de acesso a esses não-administradores, pois eles são aqueles que sabem quais usuários precisam de acesso, por quanto tempo e quais recursos. Isso garante que as pessoas certas estejam Gerenciando o acesso para seus departamentos.

Aqui está uma maneira que o Hana poderia delegar a governança de acesso para os departamentos jurídico, de marketing e finanças.

1. O Hana cria um novo grupo de segurança do Azure AD e adiciona Mamta, Mark e Joe como membros do grupo.

1. O Hana adiciona esse grupo à função criadores de catálogo.

    Mamta, Mark e Joe agora podem criar catálogos para seus departamentos, adicionar recursos de que seus departamentos precisam e fazer uma delegação adicional no catálogo.

    Observe que Mamta, Mark e Joe não conseguem ver os catálogos uns dos outros.

1. O Mamta cria um catálogo de **marketing** , que é um contêiner de recursos.

1. Mamta adiciona os recursos que seu departamento de marketing possui a esse catálogo.

1. O Mamta pode adicionar outras pessoas de seu departamento como proprietários de catálogo para este catálogo. Isso ajuda a compartilhar as responsabilidades de gerenciamento de catálogo.

1. O Mamta pode delegar ainda mais a criação e o gerenciamento de pacotes de acesso no catálogo de marketing para gerentes de projeto no departamento de marketing. Ela pode fazer isso atribuindo-os à função Gerenciador de pacotes de acesso. Um Gerenciador de pacotes do Access pode criar e gerenciar pacotes de acesso. 

O diagrama a seguir mostra os catálogos com recursos para os departamentos de marketing, finanças e legais. Usando esses catálogos, os gerentes de projeto podem criar pacotes de acesso para suas equipes ou projetos.

![Exemplo de delegado de gerenciamento de direitos](./media/entitlement-management-delegate/elm-delegate.png)

Após a delegação, o departamento de marketing pode ter funções semelhantes à tabela a seguir.

| Utilizador | Função da tarefa | Função do Azure AD | Função de gerenciamento de direitos |
| --- | --- | --- | --- |
| Hana | Administrador de ti | Administrador global ou administrador de usuários |  |
| Mamta | Gerente de marketing | Utilizador | Criador do catálogo e proprietário do catálogo |
| BOB | Líder de marketing | Utilizador | Proprietário do catálogo |
| Jessica | Gerente de projeto de marketing | Utilizador | Gerenciador de pacotes de acesso |

## <a name="entitlement-management-roles"></a>Funções de gerenciamento de direitos

O gerenciamento de direitos tem as seguintes funções que são específicas para o gerenciamento de direitos.

| Função de gerenciamento de direitos | Descrição |
| --- | --- |
| criador do catálogo | Criar e gerenciar catálogos. Normalmente, um administrador de ti que não é um administrador global ou um proprietário de recurso para uma coleção de recursos. A pessoa que cria um catálogo torna-se automaticamente o primeiro proprietário do catálogo do catálogo e pode adicionar proprietários de catálogos adicionais. Um criador de catálogo não pode gerenciar ou Ver os catálogos que não possuem e não pode adicionar recursos que não possuem a um catálogo. Se o criador do catálogo precisar gerenciar outro catálogo ou adicionar recursos que eles não possuem, eles poderão solicitar que sejam um coproprietário desse catálogo ou recurso. |
| Proprietário do catálogo | Edite e gerencie catálogos existentes. Normalmente, um administrador de ti ou proprietários de recursos ou um usuário que o proprietário do catálogo designou. |
| Gerenciador de pacotes de acesso | Edite e gerencie todos os pacotes do Access existentes em um catálogo. |

Além disso, um Aprovador designado e um solicitante de um pacote de acesso também têm direitos, embora não sejam funções.

| Direita | Descrição |
| --- | --- |
| Executivo | Autorizado por uma política para aprovar ou negar solicitações para acessar pacotes, embora eles não possam alterar as definições de pacote de acesso. |
| Requerente | Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso. |

A tabela a seguir lista as tarefas que as funções de gerenciamento de direitos podem executar.

| Tarefa | administrador | criador do catálogo | Proprietário do catálogo | Gerenciador de pacotes de acesso |
| --- | :---: | :---: | :---: | :---: |
| [Delegar a um criador de catálogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Adicionar uma organização conectada](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Adicionar um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Adicionar um proprietário do catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Excluir um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegar a um Gerenciador de pacotes do Access](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Remover um Gerenciador de pacotes de acesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Criar um novo pacote do Access em um catálogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Alterar funções de recurso em um pacote de acesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Criar e editar políticas](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Atribuir diretamente um usuário a um pacote do Access](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Exibir quem tem uma atribuição para um pacote de acesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Exibir solicitações de um pacote de acesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Exibir os erros de entrega de uma solicitação](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Reprocessar uma solicitação](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Cancelar uma solicitação pendente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ocultar um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Excluir um pacote de acesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um administrador global pode adicionar ou remover qualquer grupo (grupos de segurança criados na nuvem ou grupos do Office 365 criados na nuvem), aplicativo ou site do SharePoint Online em um catálogo. Um administrador de usuário pode adicionar ou remover qualquer grupo ou aplicativo em um catálogo.

Para um usuário que não seja um administrador global ou um administrador de usuário, para adicionar grupos, aplicativos ou sites do SharePoint Online a um catálogo, esse usuário deve *ter a* função de gerenciamento de direitos e a função de diretório do Azure ad necessária. A tabela a seguir lista as combinações de função que são necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, você deve ter as mesmas funções.

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

Para determinar a função com privilégios mínimos para uma tarefa, você também pode referenciar [funções de administrador por tarefa de administrador no Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Passos seguintes

- [Delegar governança de acesso para criadores de catálogo](entitlement-management-delegate-catalog.md)
- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
