---
title: Delegação e funções na gestão de direitos - Azure AD
description: Saiba como delegar a governação de acesso de administradores de TI a gestores de departamentos e gestores de projetos para que eles possam gerir o acesso a si mesmos.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8e6c13600cb5940351d31b54af403584cc68a5f
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515653"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegação e funções na gestão dos direitos da AD Azure

Por padrão, os administradores globais e os administradores de utilizadores podem criar e gerir todos os aspetos da gestão de direitos Azure AD. No entanto, os utilizadores destas funções podem não conhecer todas as situações em que os pacotes de acesso são necessários. Tipicamente são os utilizadores dentro dos respetivos departamentos, equipas ou projetos que sabem com quem estão a colaborar, utilizando quais os recursos e por quanto tempo. Em vez de conceder permissões sem restrições a não administradores, pode conceder aos utilizadores as menos permissões necessárias para desempenhar o seu trabalho e evitar a criação de direitos de acesso conflituosos ou inadequados.

Este vídeo fornece uma visão geral de como delegar a governação de acesso do administrador de TI a utilizadores que não são administradores.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemplo de delegado

Para entender como pode delegar a governação de acesso na gestão de direitos, ajuda a considerar um exemplo. Suponha que a sua organização tem o seguinte administrador e gestores.

![Delegado de administrador de TI a gestores](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Como administradora de TI, Hana tem contactos em cada departamento-- Mamta em Marketing, Mark in Finance, e Joe in Legal, que são responsáveis pelos recursos do seu departamento e conteúdo crítico de negócios.

Com a gestão de direitos, pode delegar a governação de acesso a estes não administradores porque são eles que sabem quais os utilizadores que precisam de acesso, por quanto tempo, e a que recursos. Isto garante que as pessoas certas estão a gerir o acesso aos seus departamentos.

Aqui está uma maneira de Hana delegar a governação de acesso aos departamentos de marketing, finanças e jurídicos.

1. Hana cria um novo grupo de segurança Azure AD, e adiciona Mamta, Mark e Joe como membros do grupo.

1. Hana adiciona este grupo ao papel de criadores de catálogo.

    Mamta, Mark e Joe podem agora criar catálogos para os seus departamentos, adicionar recursos que os seus departamentos precisam, e fazer mais delegação dentro do catálogo.

    Note que Mamta, Mark e Joe não podem ver os catálogos um do outro.

1. A Mamta cria um catálogo **de Marketing,** que é um contentor de recursos.

1. Mamta adiciona os recursos que o seu departamento de marketing detém a este catálogo.

1. Mamta pode adicionar mais pessoas do seu departamento como proprietários de catálogos para este catálogo. Isto ajuda a partilhar as responsabilidades de gestão do catálogo.

1. A Mamta pode ainda delegar a criação e gestão de pacotes de acesso no catálogo de Marketing aos gestores de projetos no departamento de Marketing. Ela pode fazê-lo atribuindo-os ao papel de gestor de pacotes de acesso. Um gestor de pacotes de acesso pode criar e gerir pacotes de acesso. 

O diagrama seguinte mostra catálogos com recursos para os departamentos de marketing, finanças e jurídicos. Utilizando estes catálogos, os gestores de projetos podem criar pacotes de acesso para as suas equipas ou projetos.

![Exemplo de delegado de gestão de direitos](./media/entitlement-management-delegate/elm-delegate.png)

Após a delegação, o departamento de marketing poderá ter funções semelhantes à tabela seguinte.

| User | Papel de trabalho | Papel de AD AZure | Papel de gestão de direitos |
| --- | --- | --- | --- |
| Hana | Administrador de TI | Administrador global ou administrador de utilizadores |  |
| Mamta | Gestor de marketing | User | Criador de catálogo e proprietário de catálogo |
| Bob | Chumbo de marketing | User | Proprietário do catálogo |
| Jéssica | Gestor de projetos de marketing | User | Gestor de pacotes de acesso |

## <a name="entitlement-management-roles"></a>Funções de gestão de direitos

A gestão de direitos tem as seguintes funções específicas para a gestão de direitos.

| Papel de gestão de direitos | Description |
| --- | --- |
| Criador de catálogos | Criar e gerir catálogos. Tipicamente um administrador de TI que não é um administrador global, ou um proprietário de recursos para uma recolha de recursos. A pessoa que cria um catálogo torna-se automaticamente o primeiro proprietário do catálogo, e pode adicionar proprietários adicionais de catálogos. Um criador de catálogos não consegue gerir ou ver catálogos que não possuem e não pode adicionar recursos que não possuem a um catálogo. Se o criador do catálogo precisar de gerir outro catálogo ou adicionar recursos que não possuam, podem solicitar para ser coproprietário desse catálogo ou recurso. |
| Proprietário do catálogo | Editar e gerir os catálogos existentes. Normalmente, um administrador de TI ou proprietários de recursos, ou um utilizador que o proprietário do catálogo designou. |
| Gestor de pacotes de acesso | Editar e gerir todos os pacotes de acesso existentes dentro de um catálogo. |
| Gestor de atribuição de pacotes de acesso | Editar e gerir todas as atribuições de pacotes de acesso existentes. |

Além disso, um aprovador designado e um solicitador de um pacote de acesso também têm direitos, embora não sejam funções.

| Direita | Description |
| --- | --- |
| Aprovador | Autorizados por uma política para aprovar ou negar pedidos de acesso a pacotes, embora não possam alterar as definições do pacote de acesso. |
| Requerente | Autorizado por uma política de um pacote de acesso para solicitar esse pacote de acesso. |

A tabela que se segue enumera as tarefas que as funções de gestão de direitos podem desempenhar.

| Tarefa | Admin | Criador de catálogos | Proprietário do catálogo | Gestor de pacotes de acesso | Gestor de atribuição de pacotes de acesso |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegado a um criador de catálogos](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Adicionar uma organização associada](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Criar um novo catálogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Adicione um recurso a um catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Adicione um proprietário de catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Editar um catálogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Excluir um catálogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delegado a um gestor de pacotes de acesso](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Remover um gestor de pacotes de acesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Criar um novo pacote de acesso num catálogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Alterar funções de recursos num pacote de acesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Criar e editar políticas](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Atribuir diretamente um utilizador a um pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Remova diretamente um utilizador de um pacote de acesso](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Ver quem tem uma atribuição a um pacote de acesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Ver os pedidos de um pacote de acesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Ver erros de entrega de um pedido](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Reprocessar um pedido](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Cancelar um pedido pendente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Esconda um pacote de acesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Excluir um pacote de acesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Funções necessárias para adicionar recursos a um catálogo

Um administrador global pode adicionar ou remover qualquer grupo (grupos de segurança criados na nuvem ou grupos Microsoft 365), aplicação ou site SharePoint Online num catálogo. Um administrador do Utilizador pode adicionar ou remover qualquer grupo ou aplicação num catálogo, exceto para um grupo configurado como atribuível a uma função de diretório. Note que um administrador de utilizador pode gerir pacotes de acesso num catálogo que inclui grupos configurados como atribuíveis a uma função de diretório.

Para um utilizador que não seja um administrador global ou um administrador do Utilizador, para adicionar grupos, aplicações ou sites SharePoint Online a um catálogo, esse utilizador deve ter *tanto* a função de diretório AD AD exigida como a função de gestão de direitos do proprietário do catálogo. A tabela que se segue lista as combinações de funções que são necessárias para adicionar recursos a um catálogo. Para remover recursos de um catálogo, você deve ter as mesmas funções.

| Papel de diretório AD Azure | Papel de gestão de direitos | Pode adicionar grupo de segurança | Pode adicionar Microsoft 365 Group | Pode adicionar app | Pode adicionar site SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrador global](../roles/permissions-reference.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrador de utilizadores](../roles/permissions-reference.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrador do Intune](../roles/permissions-reference.md) | Proprietário do catálogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrador do Exchange](../roles/permissions-reference.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador de serviço de equipas](../roles/permissions-reference.md) | Proprietário do catálogo |  | :heavy_check_mark: |  |  |
| [Administrador do SharePoint](../roles/permissions-reference.md) | Proprietário do catálogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrador de aplicação](../roles/permissions-reference.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| [Administrador de aplicação em nuvem](../roles/permissions-reference.md) | Proprietário do catálogo |  |  | :heavy_check_mark: |  |
| User | Proprietário do catálogo | Só se o dono do grupo | Só se o dono do grupo | Só se o proprietário da aplicação |  |

Para determinar o papel menos privilegiado para uma tarefa, também pode referenciar [as funções de Administrador através da tarefa de administração no Diretório Ativo Azure](../roles/delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Passos seguintes

- [Delegar governação de acesso aos criadores de catálogos](entitlement-management-delegate-catalog.md)
- [Criar e gerir um catálogo de recursos](entitlement-management-catalog-create.md)
