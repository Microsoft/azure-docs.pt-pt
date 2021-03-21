---
title: Permissões de aplicativos para funções personalizadas em Azure Ative Directory | Microsoft Docs
description: Pré-visualizar permissões de aplicativos da empresa para funções Azure AD personalizadas no portal Azure, PowerShell ou API de gráfico.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466798"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de aplicações da empresa para funções personalizadas no Azure Ative Directory

Este artigo contém as permissões de aplicação da empresa atualmente disponíveis para definições de funções personalizadas no Azure Ative Directory (Azure AD). Neste artigo, encontrará listas de permissão para alguns cenários comuns e a lista completa de permissões de aplicações empresariais. As permissões de procuração de aplicação não são atualmente lançadas nesta versão.

## <a name="required-license-plan"></a>Plano de licença exigido

A utilização desta funcionalidade requer uma licença Azure AD Premium P1 para a sua organização Azure AD. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Permissões de aplicação da empresa

Para obter mais informações sobre como usar estas permissões, consulte [atribuir funções personalizadas para gerir apps empresariais](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Atribuir utilizadores ou grupos a uma aplicação

Para delegar a atribuição de utilizadores e grupos que podem aceder a aplicações de sessão única baseadas em SAML. Permissões necessárias

- microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>Criação de aplicações de galerias

Para delegar a criação de aplicações da Azure AD Gallery como o ServiceNow, F5, Salesforce, entre outros. Permissões necessárias:

- microsoft.diretório/aplicaçãoTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>Configuração de URLs SAML básicos

Para delegar a atualização e a leitura de configurações BÁSICAs DE SAML para aplicações de saturação única baseadas em SAML. Permissões necessárias:

- microsoft.diretório/serviçoPrincipals/autenticação/atualização
- microsoft.diretório/aplicações.myOrganização/autenticação/atualização

### <a name="rolling-over-or-creating-signing-certs"></a>Rolando ou criando certificados de assinatura

Para delegar a gestão dos certificados de assinatura para pedidos de inscrição única baseados na SAML. Permissões necessárias.

microsoft.diretório/aplicações/credenciais/atualização

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Atualização expirando endereço de e-mail de notificação de cert

Para delegar a atualização dos endereços de correio eletrónico de notificação de certificados de inscrição expirado para pedidos de inscrição única baseados em SAML. Permissões necessárias:

- microsoft.diretório/aplicações.myOrganização/autenticação/atualização
- microsoft.diretório/aplicações.myOrganization/permissions/update
- microsoft.diretório/serviçoPrincipals/autenticação/atualização
- microsoft.diretório/serviçoPrincipals/básico/atualização

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Gerir assinatura simbólica SAML e algoritmo de inscrição

Para delegar a atualização da assinatura de token SAML e algoritmo de inscrição para aplicações de saturação única baseadas em SAML. Permissões necessárias:

- microsoft.diretório/aplicaçãoPolicies/basic/update
- microsoft.diretório/aplicações/autenticação/atualização
- microsoft.diretório/serviçoPrincipals/políticas/atualização

### <a name="manage-user-attributes-and-claims"></a>Gerir atributos e reclamações do utilizador

Para delegar a criação, eliminação e atualização de atributos e reclamações do utilizador para aplicações de sessão única baseadas em SAML. Permissões necessárias:

- microsoft.diretório/aplicaçãoPolicies/basic/update
- microsoft.diretório/aplicações/autenticação/atualização
- microsoft.diretório/serviçoPrincipals/políticas/atualização

## <a name="app-provisioning-permissions"></a>Permissões de provisionamento de aplicativos

A realização de qualquer operação de escrita, como a gestão do trabalho, esquema ou credenciais através da UI, também exigirá as permissões de leitura para visualizar a página de provisionamento.

Definir o âmbito de aplicação a todos os utilizadores e grupos ou utilizadores e grupos atribuídos atualmente requer permissões de sincronizaçãoJob e synchronizationCredentiss.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Ligue ou reinicie os postos de trabalho

Para delegar a capacidade de ligar, desligar e reiniciar o provisionamento de empregos. Permissões necessárias:

- microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir  

### <a name="configure-the-provisioning-schema"></a>Configure o esquema de provisionamento  

Para delegar atualizações para atribuir mapeamento. Permissões necessárias:

- microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Leia as definições de provisionamento associadas ao objeto de aplicação

Para delegar a capacidade de ler as definições de provisionamento associadas ao objeto. Permissões necessárias:

- microsoft.diretório/aplicações/sincronização/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Leia as definições de provisionamento associadas ao seu principal serviço

Para delegar a capacidade de ler as definições de provisionamento associadas ao seu principal serviço. Permissões necessárias:

- microsoft.diretório/serviçoPrincipals/sincronização/standard/read

### <a name="authorize-application-access-for-provisioning"></a>Autorizar o acesso à aplicação para provisionamento  

Para delegar a capacidade de autorizar o acesso à aplicação para provisionamento. Exemplo de entrada Oauth bearer token. Permissões necessárias:

- microsoft.diretório/serviçoPrincipals/synchronizationCredentiss/manage

## <a name="full-list-of-permissions"></a>Lista completa de permissões

Permissão | Description
---------- | -----------
microsoft.diretório/aplicaçãoPolicies/allProperties/read | Leia todas as propriedades sobre as políticas de aplicação.
microsoft.diretório/aplicaçãoPolicies/allProperties/update | Atualize todas as propriedades sobre as políticas de aplicação.
microsoft.diretório/aplicaçãoPolicies/basic/update | Atualizar propriedades padrão das políticas de aplicação.
microsoft.diretório/aplicaçãoPolicies/create | Criar políticas de aplicação.
microsoft.diretório/aplicaçãoPolicies/createAsOwner | Criar políticas de aplicação. O criador é adicionado como o primeiro proprietário.
microsoft.diretório/aplicaçãoPolicies/delete | Eliminar as políticas de aplicação.
microsoft.diretório/aplicaçãoPolícias/proprietários/ler | Leia os proprietários sobre as políticas de candidatura.
microsoft.diretório/aplicaçãoPolícias/proprietários/atualização | Atualizar a propriedade do proprietário das políticas de aplicação.
microsoft.diretório/aplicaçãoPolicies/policyAppliedTo/read | Leia as políticas de aplicação aplicadas à lista de objetos.
microsoft.diretório/aplicaçãoPolicies/standard/read | Leia as propriedades padrão das políticas de aplicação.
microsoft.diretório/serviçoPrincipals/allProperties/allTasks | Criar e eliminar serviçosPrincipals e ler e atualizar todas as propriedades no Azure Ative Directory.
microsoft.diretório/serviçoPrincipals/allProperties/read | Leia todas as propriedades no serviçoPrincipals.
microsoft.diretório/serviçoPrincipals/allProperties/update | Atualize todas as propriedades no serviçoPrincipals.
microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/read | Leia as atribuições principais do serviço.
microsoft.diretório/serviçoPrincipals/appRoleAssignedTo/update | Atualizar atribuições principais de funções de serviço.
microsoft.diretório/serviçoPrincipals/appRoleAssignments/read | Leia as atribuições de funções atribuídas aos diretores de serviço.
microsoft.diretório/serviçoPrincipals/audience/update | Atualizar as propriedades do público nos principais serviços.
microsoft.diretório/serviçoPrincipals/autenticação/atualização | Atualizar propriedades de autenticação em principais de serviço.
microsoft.diretório/serviçoPrincipals/básico/atualização | Atualizar propriedades básicas em principais serviços.
microsoft.diretório/serviçoPrincipals/criar | Criar diretores de serviço.
microsoft.diretório/serviçoPrincipals/createAsOwner | Criar diretores de serviço. O criador é adicionado como o primeiro proprietário.
microsoft.diretório/serviçoPrincipals/credenciais/atualização | Atualizar propriedades de credenciais em principais serviços.
microsoft.diretório/serviçoPrincipals/delete | Apague os principais de serviço.
microsoft.diretório/serviçoPrincipals/desativar | Desativar os diretores de serviço.
microsoft.diretório/serviçoPrincipals/enable | Ativar os principais serviços.
microsoft.diretório/serviçoPrincipals/getPasswordSingleSignOnCredentials | Leia as credenciais de inscrição única de senha nos principais serviços.
microsoft.diretório/serviçoPrincipals/managePasswordSingleSignOnCredentials | Gerir credenciais de inscrição únicas na palavra-passe nos principais de serviço.
microsoft.diretório/serviçoPrincipals/oAuth2PermissionGrants/read | Leia os subsídios de autorização delegados aos principais serviços.
microsoft.diretório/serviçoPrincipals/proprietários/ler | Leia os proprietários em diretores de serviço.
microsoft.diretório/serviçoPrincipals/proprietários/atualização | Atualizar os proprietários em diretores de serviço.
microsoft.diretório/serviçoPrincipals/permissões/atualização |  
microsoft.diretório/serviçoPrincipals/políticas/ler | Leia as políticas dos principais serviços.
microsoft.diretório/serviçoPrincipals/políticas/atualização | Atualizar políticas sobre os principais serviços.
microsoft.diretório/serviçoPrincipals/standard/read | Leia as propriedades padrão dos principais de serviço.
microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Leia as definições de provisionamento associadas ao seu principal serviço.
microsoft.diretório/serviçoPrincipals/tag/update | Atualização tags propriedade em principais serviços.
microsoft.diretório/aplicaçãoTemplates/instantiate | Aplicações instantâneas de galeria a partir de modelos de aplicação.
microsoft.diretório/auditoriaLogs/allProperties/read | Leia os registos de auditoria.
microsoft.diretório/signInReports/allProperties/read | Leia os relatórios de inscrição.
microsoft.diretório/aplicações/sincronização/standard/read | Leia as definições de provisionamento associadas ao objeto de aplicação.
microsoft.diretório/serviçoPrincipals/sincronizaçãoJobs/gerir | Gerir todos os aspetos da sincronização de emprego para os recursos principais do serviço
microsoft.diretório/serviçoPrincipals/sincronização/standard/read | Ler as definições de provisionamento associadas aos principais serviços
microsoft.diretório/serviçoPrincipals/sincronizaçãoSchema/gerir | Gerir todos os aspetos da sincronização de schema para os recursos principais do serviço
microsoft.diretório/provisioningLogs/allProperties/read | Leia todas as propriedades dos registos de provisionamento

## <a name="next-steps"></a>Passos seguintes

- [Crie funções personalizadas utilizando o portal Azure, Azure AD PowerShell e Graph API](custom-create.md)
- [Listar atribuições de função](view-assignments.md)
