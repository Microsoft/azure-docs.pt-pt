---
title: Cenários comuns na gestão de direitos - Azure AD
description: Aprenda os passos de alto nível que deve seguir para cenários comuns na gestão de direitos do Diretório Ativo Azure.
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190556"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Cenários comuns na gestão de direitos da AD Azure

Existem várias formas de configurar a gestão de direitos para a sua organização. No entanto, se está apenas a começar, é útil compreender os cenários comuns para administradores, proprietários de catálogos, gestores de pacotes de acesso, aprovadores e requestdores.

## <a name="delegate"></a>Delegar

### <a name="administrator-delegate-management-of-resources"></a>Administrador: Delegar gestão de recursos

1. [Veja o vídeo: Delegação de TI para gerente de departamento](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delege os utilizadores para catalogar a função de criador](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Criador de catálogo: Delegar gestão de recursos

- [Criar um novo catálogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietário do catálogo: Delegar gestão de recursos

1. [Adicione coproprietários ao catálogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Adicione recursos ao catálogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietário do catálogo: Gestão de delegados de pacotes de acesso

1. [Veja o vídeo: Delegação do proprietário do catálogo para aceder ao gestor de pacotes](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delege os utilizadores para aceder à função de gestor de pacotes](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Regule o acesso dos utilizadores na sua organização

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gestor de pacotes de acesso: Permitir que os colaboradores da sua organização solicitem acesso aos recursos

1. [Criar um novo pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipas, aplicações ou sites SharePoint para aceder ao pacote](entitlement-management-access-package-create.md#resource-roles)
1. [Adicione uma política de pedido para permitir que os utilizadores do seu diretório solicitem acesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Especificar definições de expiração](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Requestor: Solicitar acesso aos recursos

1. [Inscreva-se no portal My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Encontre pacote de acesso
1. [Pedir acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: Aprovar pedidos de recursos

1. [Pedido aberto no portal My Access](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar pedido de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Requestor: Veja os recursos a que já tem acesso

1. [Inscreva-se no portal My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Ver pacotes de acesso ativo

## <a name="govern-access-for-users-outside-your-organization"></a>Regule o acesso a utilizadores fora da sua organização

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrador: Colaborar com uma organização de parceiros externos

1. [Leia como funciona o acesso a utilizadores externos](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Rever as definições para utilizadores externos](entitlement-management-external-users.md#settings-for-external-users)
1. [Adicione uma ligação à organização externa](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gestor de pacotes de acesso: Colabore com uma organização de parceiros externos

1. [Criar um novo pacote de acesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Adicionar grupos, equipas, aplicações ou sites SharePoint para aceder ao pacote](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Adicione uma política de pedido para permitir que os utilizadores que não estão no seu diretório solicitem acesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Especificar definições de expiração](entitlement-management-access-package-create.md#lifecycle)
1. [Copiar o link para solicitar o pacote de acesso](entitlement-management-access-package-settings.md)
1. Envie o link para o seu parceiro de contacto externo para partilhar com os seus utilizadores

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Requestor: Solicite o acesso aos recursos como utilizador externo

1. Encontre o link do pacote de acesso que recebeu do seu contacto
1. [Inscreva-se no portal My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Pedir acesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Aprovador: Aprovar pedidos de recursos

1. [Pedido aberto no portal My Access](entitlement-management-request-approve.md#open-request)
1. [Aprovar ou negar pedido de acesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Requestor: Veja os recursos a que já tem acesso

1. [Inscreva-se no portal My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Ver pacotes de acesso ativo

## <a name="day-to-day-management"></a>Gestão do dia-a-dia

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gestor de pacotes de acesso: Atualizar os recursos para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Adicionar ou remover grupos, equipas, aplicações ou sites Do SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gestor de pacotes de acesso: Atualizar a duração de um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Abra o pacote de acesso
1. [Abra as definições do ciclo de vida](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Atualizar as definições de expiração](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gestor de pacotes de acesso: Atualizar como o acesso é aprovado para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Abrir uma política de definições de pedido e aprovação existentes](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Atualizar as definições de aprovação](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gestor de pacotes de acesso: Atualizar as pessoas para um projeto

1. [Vídeo: Gestão do dia-a-dia: As coisas mudaram](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Remova utilizadores que já não precisam de acesso](entitlement-management-access-package-assignments.md)
1. [Abrir uma política de definições de pedido e aprovação existentes](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Adicione utilizadores que precisam de acesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gestor de pacotes de acesso: Atribuir diretamente utilizadores específicos a um pacote de acesso

1. [Se os utilizadores precisarem de diferentes configurações de ciclo de vida, adicione uma nova política ao pacote de acesso](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Atribuir diretamente utilizadores específicos ao pacote de acesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Atribuições e relatórios

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrador: Veja quem tem atribuições para um pacote de acesso

1. Abra um pacote de acesso
1. [Ver atribuições](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Relatórios de arquivo e registos](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrador: Ver os recursos atribuídos aos utilizadores

1. [Ver pacotes de acesso para um utilizador](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Ver atribuições de recursos para um utilizador](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administração programática

Também pode gerir pacotes de acesso, catálogos, políticas, pedidos e atribuições usando o Microsoft Graph.  Um utilizador numa função adequada com uma `EntitlementManagement.ReadWrite.All` aplicação que tenha a permissão delegada pode chamar a API de gestão de [direitos.](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)

## <a name="next-steps"></a>Passos seguintes

- [Delegação e funções](entitlement-management-delegate.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)
