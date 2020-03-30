---
title: Delegação e funções na gestão de direitos - Azure AD
description: Aprenda a delegar a governação de acesso de administradores de TI a gestores de departamentos e gestores de projetos para que eles possam gerir o acesso a si mesmos.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261843"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegação e funções na gestão de direitos da AD Azure

Por padrão, administradores globais e administradores de utilizadores podem criar e gerir todos os aspetos da gestão de direitos da Azure AD. No entanto, os utilizadores destas funções podem não conhecer todas as situações em que são necessários pacotes de acesso. Normalmente são utilizadores dentro dos respetivos departamentos, equipas ou projetos que sabem com quem estão a colaborar, usando que recursos e por quanto tempo. Em vez de conceder permissões sem restrições a não administradores, pode conceder aos utilizadores as menores permissões necessárias para desempenharem o seu trabalho e evitarem criar direitos de acesso contraditórios ou inadequados.

Este vídeo fornece uma visão geral de como delegar a governação de acesso do administrador de TI a utilizadores que não são administradores.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemplo de delegado

Para compreender como pode delegar a governação de acesso na gestão de direitos, ajuda a considerar um exemplo. Suponha que a sua organização tem o seguinte administrador e gestores.

![Delegado de administrador de TI a gestores](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Como administradora de TI, hana tem contactos em cada departamento-- Mamta em Marketing, Mark in Finance, e Joe in Legal que são responsáveis pelos recursos do seu departamento e conteúdo crítico de negócios.

Com a gestão dos direitos, pode delegar a governação de acesso a estes não administradores porque são eles que sabem quais os utilizadores que precisam de acesso, por quanto tempo e a que recursos. Isto garante que as pessoas certas estão a gerir o acesso aos seus departamentos.

Aqui está uma maneira de Hana delegar a governação de acesso aos departamentos de marketing, finanças e jurídicos.

1. Hana cria um novo grupo de segurança Azure AD, e adiciona Mamta, Mark e Joe como membros do grupo.

1. Hana adiciona esse grupo ao papel de criadores de catálogo.

    Mamta, Mark e Joe podem agora criar catálogos para os seus departamentos, adicionar recursos de que os seus departamentos precisam, e fazer mais delegações dentro do catálogo.

    Note que Mamta, Mark e Joe não podem ver os catálogos um do outro.

1. A Mamta cria um catálogo de **Marketing,** que é um recipiente de recursos.

1. A Mamta adiciona os recursos que o seu departamento de marketing detém a este catálogo.

1. A Mamta pode adicionar mais pessoas do seu departamento como proprietárias de catálogos para este catálogo. Isto ajuda a partilhar as responsabilidades de gestão do catálogo.

1. A Mamta pode ainda delegar a criação e gestão de pacotes de acesso no catálogo de Marketing aos gestores de projetos no departamento de Marketing. Ela pode fazê-lo atribuindo-lhes o papel de gestor de pacotes de acesso. Um gestor de pacotes de acesso pode criar e gerir pacotes de acesso. 

O diagrama seguinte mostra catálogos com recursos para os departamentos de marketing, finanças e jurídicos. Utilizando estes catálogos, os gestores de projetos podem criar pacotes de acesso para as suas equipas ou projetos.

![Exemplo de delegado de gestão de direitos](./media/entitlement-management-delegate/elm-delegate.png)

Após a delegação, o departamento de marketing pode ter funções semelhantes à tabela seguinte.

| Utilizador | Papel de trabalho | Papel da AD Azure | Papel de gestão de direitos |
| --- | --- | --- | --- |
| Hana | Administrador de TI | Administrador global ou administrador de utilizador |  |
| Mamta | Gestor de marketing | Utilizador | Criador de catálogo e proprietário do Catálogo |
| Bob | Chumbo de marketing | Utilizador | Proprietário do catálogo |
| Jéssica | Gestor de projetos de marketing | Utilizador | Gestor de pacotes de acesso |

## <a name="entitlement-management-roles"></a>Funções de gestão de direitos

A gestão de direitos tem as seguintes funções específicas para a gestão de direitos.

| Papel de gestão de direitos | Descrição |
| --- | --- |
| Criador de catálogo | Criar e gerir catálogos. Tipicamente um administrador de TI que não é um administrador global, ou um proprietário de recursos para uma coleção de recursos. A pessoa que cria um catálogo torna-se automaticamente a primeira proprietária do catálogo do catálogo, podendo adicionar mais proprietários de catálogos. Um criador de catálogo não consegue gerir ou ver catálogos que não possuem e não consegue adicionar recursos que não possuem a um catálogo. Se o criador do catálogo precisar de gerir outro catálogo ou adicionar recursos que não possuam, pode solicitar para ser coproprietário desse catálogo ou recurso. |
| Proprietário do catálogo | Editar e gerir catálogos existentes. Normalmente um administrador de TI ou proprietários de recursos, ou um utilizador que o proprietário do catálogo designou. |
| Gestor de pacotes de acesso | Editar e gerir todos os pacotes de acesso existentes dentro de um catálogo. |

Além disso, um aprovador designado e um solicitador de um pacote de acesso também têm direitos, embora não sejam funções.

| Direita | Descrição |
| --- | --- |
| Approver | Autorizados por uma política de aprovação ou negação de pedidos de acesso aos pacotes, embora não possam alterar as definições do pacote de acesso. |
| Requerente | Autorizado por uma política de um pacote de acesso a solicitar esse pacote de acesso. |

A tabela seguinte enumera as tarefas que as funções de gestão de direitos podem desempenhar.

| Tarefa | Administrador | Criador de catálogo | Proprietário do catálogo | Gestor de pacotes de acesso |
| --- | :---: | :---: | :---: | :---: |
| [Delegado a um criador de catálogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Adicionar uma organização associada](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Adicione um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Adicione um proprietário de catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Apagar um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegado a um gestor de pacotes de acesso](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Remova um gestor de pacotes de acesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Criar um novo pacote de acesso num catálogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Alterar as funções de recurso num pacote de acesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Criar e editar políticas](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Atribuir diretamente um utilizador a um pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Veja quem tem uma atribuição para um pacote de acesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ver os pedidos de um pacote de acesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ver erros de entrega de um pedido](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Reprocessar um pedido](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Cancelar um pedido pendente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Esconda um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Eliminar um pacote de acesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um administrador global pode adicionar ou remover qualquer grupo (grupos de segurança criados em nuvem ou grupos 365 criados em nuvem), aplicação ou site SharePoint Online num catálogo. Um administrador do Utilizador pode adicionar ou remover qualquer grupo ou aplicação num catálogo.

Para um utilizador que não seja um administrador global ou um administrador de utilizador, adicionar grupos, aplicações ou sites SharePoint Online a um catálogo, esse utilizador deve ter tanto a função de diretório Azure AD necessária *como* a função de gestão de direitos do proprietário do catálogo. A tabela seguinte enumera as combinações de papéis que são necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, deve ter as mesmas funções.

| Papel de diretório azure ad | Papel de gestão de direitos | Pode adicionar grupo de segurança | Pode adicionar Office 365 Group | Pode adicionar app | Pode adicionar site Online SharePoint |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrador de utilizadores](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador do Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviço de equipas](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador de candidatura](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| [Administrador de aplicação em nuvem](../users-groups-roles/directory-assign-admin-roles.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| Utilizador | Proprietário do catálogo | Só se o dono do grupo | Só se o dono do grupo | Só se o dono de aplicativos |  |

Para determinar o papel menos privilegiado para uma tarefa, também pode referenciar as funções de Administrador através de [uma tarefa de administrador no Diretório Ativo azure](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Passos seguintes

- [Delegar governação de acesso a criadores de catálogos](entitlement-management-delegate-catalog.md)
- [Criar e gerir um catálogo de recursos](entitlement-management-catalog-create.md)
