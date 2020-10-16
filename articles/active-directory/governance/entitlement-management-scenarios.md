---
title: Cenários comuns na gestão de direitos - Azure AD
description: Conheça os passos de alto nível que deve seguir para cenários comuns na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8a8f76ce7d46c0a44dd86ef1490c1c74a7992a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979504"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Cenários comuns na gestão de direitos da AD Azure

Existem várias formas de configurar a gestão de direitos para a sua organização. No entanto, se está apenas a começar, é útil compreender os cenários comuns para administradores, proprietários de catálogos, gestores de pacotes de acesso, aprovadores e solicitadores.

## <a name="delegate"></a>Delegar

### <a name="administrator-delegate-management-of-resources"></a>Administrador: Gestão de recursos de delegados

1. [Ver vídeo: Delegação de TI para gerente de departamento](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegar utilizadores para catalogar o papel de criador](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Criador de catálogo: Gestão delegada de recursos

- [Criar um novo catálogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietário do catálogo: Gestão de recursos delegados

1. [Adicionar coproprietários ao catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Adicione recursos ao catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietário do catálogo: Gestão delegada de pacotes de acesso

1. [Veja o vídeo: Delegação do proprietário do catálogo ao gestor de pacotes](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegar utilizadores para aceder à função de gestor de pacotes](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Regule o acesso aos utilizadores na sua organização

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gestor de pacotes de acesso: Permitir que os colaboradores da sua organização solicitem acesso aos recursos

1. [Criar um novo pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipas, aplicações ou sites SharePoint para aceder ao pacote](entitlement-management-access-package-create.md#resource-roles)
1. [Adicione uma política de pedido para permitir que os utilizadores no seu diretório solicitem acesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Especificar as definições de expiração](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Solicitador: Solicitar acesso a recursos

1. [Inscreva-se no portal 'O Acesso'](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Encontre pacote de acesso
1. [Pedir acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovação: Aprovar pedidos de recursos

1. [Pedido aberto no portal My Access](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar pedido de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Solicitador: Ver os recursos a que já tem acesso

1. [Inscreva-se no portal 'O Acesso'](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Ver pacotes de acesso ativo

## <a name="govern-access-for-users-outside-your-organization"></a>Regule o acesso a utilizadores fora da sua organização

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrador: Colaborar com uma organização parceira externa

1. [Leia como funciona o acesso a utilizadores externos](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Definições de revisão para utilizadores externos](entitlement-management-external-users.md#settings-for-external-users)
1. [Adicionar uma ligação à organização externa](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gestor de pacotes de acesso: Colaborar com uma organização parceira externa

1. [Criar um novo pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipas, aplicações ou sites SharePoint para aceder ao pacote](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Adicione uma política de pedido para permitir que os utilizadores que não estão no seu diretório solicitem acesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Especificar as definições de expiração](entitlement-management-access-package-create.md#lifecycle)
1. [Copie o link para solicitar o pacote de acesso](entitlement-management-access-package-settings.md)
1. Envie o link para o seu parceiro de contato parceiro externo para partilhar com os seus utilizadores

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Solicitador: Solicitar acesso a recursos como utilizador externo

1. Encontre o link de pacote de acesso que recebeu do seu contacto
1. [Inscreva-se no portal 'O Acesso'](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Pedir acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovação: Aprovar pedidos de recursos

1. [Pedido aberto no portal My Access](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar pedido de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Solicitador: Ver os recursos a que já tem acesso

1. [Inscreva-se no portal 'O Acesso'](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Ver pacotes de acesso ativo

## <a name="day-to-day-management"></a>Gestão do dia-a-dia

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gestor de pacotes de acesso: Atualizar os recursos para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Adicionar ou remover grupos, equipas, aplicações ou sites SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gestor de pacotes de acesso: Atualizar a duração de um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Abra as definições do ciclo de vida](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Atualizar as definições de expiração](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gestor de pacotes de acesso: Atualizar como o acesso é aprovado para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Abrir uma política existente de configurações de pedidos](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Atualizar as definições de aprovação](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gestor de pacotes de acesso: Atualizar as pessoas para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Remover utilizadores que já não precisam de acesso](entitlement-management-access-package-assignments.md)
1. [Abrir uma política existente de configurações de pedidos](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Adicionar utilizadores que precisam de acesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gestor de pacotes de acesso: Atribua diretamente utilizadores específicos a um pacote de acesso

1. [Se os utilizadores precisarem de configurações diferentes do ciclo de vida, adicione uma nova política ao pacote de acesso](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Atribuir diretamente utilizadores específicos ao pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Atribuições e relatórios

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrador: Ver quem tem atribuições a um pacote de acesso

1. Abrir um pacote de acesso
1. [Ver atribuições](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Relatórios e registos de arquivo](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrador: Ver recursos atribuídos aos utilizadores

1. [Ver pacotes de acesso para um utilizador](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Ver atribuições de recursos para um utilizador](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administração programática

Também pode gerir pacotes de acesso, catálogos, políticas, pedidos e atribuições usando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode chamar a [API de gestão de direitos.](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta )

## <a name="next-steps"></a>Passos seguintes

- [Delegação e funções](entitlement-management-delegate.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)