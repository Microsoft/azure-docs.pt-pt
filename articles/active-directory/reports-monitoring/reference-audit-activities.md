---
title: Referência da atividade de auditoria do Azure Active Directory (Azure AD) | Microsoft Docs
description: Obtenha uma descrição geral das atividades de auditoria que podem ser registadas nos seus registos de auditoria no Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66dd017e8f78f1e93c96262b42dc084c165cdef7
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437685"
---
# <a name="azure-ad-audit-activity-reference"></a>Referência da atividade de auditoria do Azure AD

Com os relatórios do Azure Active Directory (Azure AD), pode obter as informações que necessárias para determinar o estado de funcionamento do ambiente.

A arquitetura de relatórios no Azure AD consiste nos seguintes componentes:

- **Relatórios de atividade** 
    - [Inícios de sessão](concept-sign-ins.md) – fornece informações sobre a utilização de aplicações geridas e utilizador atividades de início de sessão
    - [Registos de auditoria](concept-audit-logs.md) - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. 
    
- **Relatórios de segurança** 
    - [Inícios de sessão de risco](concept-risky-sign-ins.md) – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 
    - [Utilizadores sinalizados para risco](concept-user-at-risk.md) – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

Este artigo lista as atividades de auditoria que podem ser registadas nos seus registos de auditoria.

## <a name="access-reviews"></a>Revisões de acesso

|Categoria de Auditoria|Atividade|
|---|---|
|Revisões de Acesso|Revisão de acesso terminada|
|Revisões de Acesso|Adicionar aprovador para aprovação de pedidos|
|Revisões de Acesso|Adicionar revisor à revisão de acesso|
|Revisões de Acesso|Aplicar revisão de acesso|
|Revisões de Acesso|Criar revisão de acesso|
|Revisões de Acesso|Criar programa|
|Revisões de Acesso|Criar aprovação de pedidos|
|Revisões de Acesso|Eliminar revisão de acesso|
|Revisões de Acesso|Eliminar programa|
|Revisões de Acesso|Associar controlo de programa|
|Revisões de Acesso|Integrar nas Revisões de Acesso do Azure AD|
|Revisões de Acesso|Remover revisor da revisão de acesso|
|Revisões de Acesso|Requerer Paragem da Revisão|
|Revisões de Acesso|Requerer aplicação do resultado da revisão|
|Revisões de Acesso|Rever associação da função RBAC|
|Revisões de Acesso|Rever atribuição de aplicações|
|Revisões de Acesso|Rever associação ao grupo|
|Revisões de Acesso|Rever pedido de aprovação de pedidos|
|Revisões de Acesso|Desassociar controlo de programa|
|Revisões de Acesso|Atualizar Revisão de Acesso|
|Revisões de Acesso|Atualizar estado da inclusão de revisões de acesso do Azure AD|
|Revisões de Acesso|Atualizar definições de notificação de correio da revisão de acesso|
|Revisões de Acesso|Atualizar a definição de contagem de periodicidade de revisões de acesso|
|Revisões de Acesso|Atualizar a definição de duração em dias de periodicidade de revisões de acesso|
|Revisões de Acesso|Atualizar a definição de tipo de fim de periodicidade de revisões de acesso|
|Revisões de Acesso|Atualizar definição de tipo de periodicidade de revisões de acesso|
|Revisões de Acesso|Atualizar definições de lembrete da revisão de acesso|
|Revisões de Acesso|Atualizar programa|
|Revisões de Acesso|Atualizar aprovação de pedidos|
|Revisões de Acesso|Utilizador desativado|

## <a name="account-provisioning"></a>Aprovisionamento de contas

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Aplicações|Obter concessões de permissões de aplicação V2|
|Gestão de Aplicações|Obter principais de serviço de aplicação V2 no inquilino atual|
|Gestão de Aplicações|Atualizar aplicação V1|
|Gestão de Aplicações|Atualizar aplicação V2|
|Gestão de Aplicações|Atualizar concessão de permissões de aplicação V2|
|Gestão de Aplicações|Adicionar OAuth2PermissionGrant|
|Gestão de Aplicações|Adicionar atribuição de função de aplicação ao principal de serviço|

## <a name="application-proxy"></a>Proxy da aplicação

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Aplicações|Adicionar aplicação|
|Gestão de Aplicações|Adicionar proprietário à aplicação|
|Gestão de Aplicações|Adicionar proprietário ao principal de serviço|
|Gestão de Aplicações|Adicionar política ao principal de serviço|
|Gestão de Diretórios|Adicionar principal de serviço|
|Gestão de Diretórios|Adicionar credenciais de principal de serviço|
|Gestão de Diretórios|Autorizar aplicação|
|Gestão de Diretórios|Eliminar aplicação|
|Gestão de Diretórios|Eliminação completa da aplicação|
|Gestão de Diretórios|Remover OAuth2PermissionGrant|
|Gestão de Diretórios|Remover atribuição de função de aplicação do principal de serviço|
|Gestão de Diretórios|Remover proprietário da aplicação|
|Recurso|Remover proprietário do principal de serviço|
|Recurso|Remover política do principal de serviço|
|Recurso|Remover principal de serviço|


## <a name="automated-password-rollover"></a>Reversão de palavras-passe automatizada

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Aplicações|Remover credenciais de principal de serviço|


## <a name="b2c"></a>B2C

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Aplicações|Restaurar aplicação|
|Gestão de Aplicações|Revogar autorização|
|Gestão de Aplicações|Atualizar a aplicação|
|Gestão de Aplicações|Atualizar segredos externos|
|Gestão de Aplicações|Atualizar principal de serviço|
|Gestão de Aplicações|Emitir um token de acesso para a aplicação|
|Gestão de Aplicações|Emitir um código de autorização para a aplicação|
|Gestão de Aplicações|Emitir um token de acesso id_token para a aplicação|
|Gestão de Aplicações|Validar as credenciais da conta local|
|Gestão de Aplicações|Validar autenticação de utilizador|
|Gestão de Aplicações|Adicionar permissões de aplicação V2|
|Gestão de Aplicações|Adicionar uma chave com base no segredo ASCII a um contentor de chaves CPIM|
|Gestão de Aplicações|Adicionar uma chave a um contentor de chaves CPIM|
|Gestão de Aplicações|AdminPolicyDatas-SetResources|
|Gestão de Aplicações|AdminUserJourneys-GetResources|
|Gestão de Aplicações|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Criar IdentityProvider|
|Authentication|Criar aplicação V1|
|Authentication|Criar aplicação V2|
|Authentication|Criar domínios personalizados no inquilino|
|Autorização|Criar um novo AdminUserJourney|
|Autorização|Criar json de recurso localizado|
|Autorização|Criar IDP Personalizado novo|
|Autorização|Criar novo IDP|
|Autorização|Criar ou atualizar um recurso de diretório B2C|
|Autorização|Criar política|
|Autorização|Criar política trustFramework|
|Autorização|Criar política trustFramework com prefixo configurável|
|Autorização|Criar atributo de utilizador|
|Autorização|CreateTrustFrameworkPolicy|
|Autorização|Cria ou Atualiza um novo AdminUserJourney|
|Autorização|Eliminar IDP|
|Autorização|Eliminar IdentityProvider|
|Autorização|Eliminar aplicação V1|
|Autorização|Eliminar aplicação V2|
|Autorização|Eliminar concessão de permissões de aplicação V2|
|Autorização|Eliminar um recurso de diretório B2C|
|Autorização|Eliminar um contentor de chaves CPIM|
|Autorização|Eliminar política trustFramework|
|Autorização|Eliminar atributo de utilizador|
|Autorização|Ativar funcionalidade B2C|
|Autorização|Obter recursos de diretório B2C numa subscrição|
|Autorização|Obter IDP Personalizado|
|Autorização|Obter IDP|
|Autorização|Obter aplicações V1 e V2|
|Autorização|Obter aplicação V1|
|Autorização|Obter aplicações V1|
|Autorização|Obter aplicação V2|
|Autorização|Obter aplicações V2|
|Autorização|Obter um recurso de diretório B2C|
|Autorização|Obter uma lista de domínios personalizados no inquilino|
|Autorização|Obter um percurso do utilizador|
|Autorização|Obter afirmações de aplicação permitidas para percurso do utilizador|
|Autorização|Obter afirmações de declaração própria permitidas para percurso do utilizador|
|Autorização|Obter afirmações de declaração própria permitidas da política|
|Autorização|Obter lista de afirmações de saída disponíveis|
|Autorização|Obter definições de conteúdo para percurso do utilizador|
|Autorização|Obter idps para um fluxo de administração específico|
|Autorização|Obter metadados de chaves ativas do contentor de chaves em JWK|
|Autorização|Obter a lista de todos os fluxos de administração|
|Autorização|Obter a lista de etiquetas de todos os fluxos de administração de todos os utilizadores|
|Autorização|Obter a lista de inquilinos de um utilizador|
|Autorização|Obter afirmações de declaração própria de contas locais|
|Autorização|Obter json de recurso localizado|
|Autorização|Obter operações do fornecedor de recursos Microsoft.AzureActiveDirectory|
|Autorização|Obter políticas|
|Autorização|Obter política|
|Autorização|Obter propriedades de recurso de um inquilino|
|Autorização|Obter a lista de IDPs suportados|
|Autorização|Obter a lista de IDPs suportados do percurso do utilizador|
|Autorização|Obter Informações do inquilino|
|Autorização|Obter funcionalidades do inquilino permitidas|
|Autorização|Obter lista de IDPs Personalizados definidos para o inquilino|
|Autorização|Obter lista de IDPs definidos para o inquilino|
|Autorização|Obter lista de IDPs locais definidos para o inquilino|
|Autorização|Obter os detalhes do inquilino de um utilizador para criação de recurso|
|Autorização|Obter a lista de inquilinos|
|Autorização|Obter tenantDomains|
|Autorização|Obter a cultura predefinida suportada para CPIM|
|Autorização|Obter os detalhes de um fluxo de administração|
|Autorização|Obter a lista de UserJourneys deste inquilino|
|Autorização|Obter o conjunto de culturas disponíveis suportadas para CPIM|
|Autorização|Obter política trustFramework|
|Autorização|Obter política trustFramework com xml|
|Autorização|Obter atributo do utilizador|
|Autorização|Obter atributos do utilizador|
|Autorização|Obter a lista de percursos do utilizador|
|Autorização|GetIEFPolicies|
|Autorização|GetIdentityProviders|
|Autorização|GetTrustFrameworkPolicy|
|Autorização|Obtém um contentor de chaves CPIM no formato jwk|
|Autorização|Obtém a lista de contentores de chaves no inquilino|
|Autorização|Obtém o tipo de inquilino|
|Autorização|MigrateTenantMetadata|
|Autorização|Corrigir IdentityProvider|
|Autorização|PutTrustFrameworkPolicy|
|Autorização|PutTrustFrameworkpolicy|
|Autorização|Remover um percurso do utilizador|
|Autorização|Restaurar uma cópia de segurança do contentor de chaves CPIM|
|Autorização|Obter concessões de permissões de aplicação V2|
|Autorização|Obter principais de serviço de aplicação V2 no inquilino atual|
|Autorização|Atualizar o IDP Personalizado|
|Autorização|Atualizar IDP|
|Autorização|Atualizar IDP Local|
|Autorização|Atualizar aplicação V1|
|Autorização|Atualizar aplicação V2|
|Autorização|Atualizar concessão de permissões de aplicação V2|
|Autorização|Atualizar política|
|Autorização|Atualizar atributo de utilizador|
|Autorização|Carregar uma chave encriptada CPIM|
|Autorização|Autorização de utilizador: A API está desativada para featureset de inquilino|
|Autorização|Autorização de utilizador: Acesso concedido ao utilizador como administrador de inquilinos|
|Autorização|Autorização de utilizador: Foi concedido ao utilizador direitos de acesso de utilizadores autenticados|
|Autorização|Verifique se a funcionalidade B2C está ativada|
|Autorização|Verificar se a funcionalidade está ativada|
|Autorização|Criar programa|
|Autorização|Eliminar programa|
|Autorização|Associar controlo de programa|
|Autorização|Integrar nas Revisões de Acesso do Azure AD|
|Autorização|Desassociar controlo de programa|
|Autorização|Atualizar programa|
|Autorização|Desativar SSO de Ambiente de Trabalho|
|Autorização|Desativar o SSO de Ambiente de Trabalho num domínio específico|
|Autorização|Desativar proxy de aplicações|
|Autorização|Desativar autenticação pass-through|
|Autorização|Ativar SSO de Ambiente de Trabalho|
|Gestão de Diretórios|Ativar o SSO de Ambiente de Trabalho num domínio específico|
|Gestão de Diretórios|Ativar o proxy de aplicações|
|Gestão de Diretórios|Ativar a autenticação pass-through|
|Gestão de Diretórios|Criar domínios personalizados no inquilino|
|Gestão de Diretórios|Ativar funcionalidade B2C|
|Gestão de Diretórios|Obter uma lista de domínios personalizados no inquilino|
|Gestão de Diretórios|Obter propriedades de recurso de um inquilino|
|Gestão de Diretórios|Obter Informações do inquilino|
|Gestão de Diretórios|Obter funcionalidades do inquilino permitidas|
|Gestão de Diretórios|Obter tenantDomains|
|Chave|Obtém o tipo de inquilino|
|Chave|Verifique se a funcionalidade B2C está ativada|
|Chave|Verificar se a funcionalidade está ativada|
|Chave|Adicionar parceiro à empresa|
|Chave|Adicionar domínio não verificado|
|Chave|Adicionar domínio verificado|
|Chave|Criar empresa|
|Chave|Criar definições da empresa|
|Chave|Eliminar definições da empresa|
|Chave|Despromover parceiro|
|Chave|Diretório eliminado|
|Outros|Diretório eliminado definitivamente|
|Outros|Diretório agendado para eliminação|
|Recurso|Promover empresa a parceiro|
|Recurso|Purgar propriedades de gestão de direitos|
|Recurso|Remover parceiro da empresa|
|Recurso|Remover domínio não verificado|
|Recurso|Remover domínio verificado|
|Recurso|Definir Informações da Empresa|
|Recurso|Definir funcionalidade Dirsync|
|Recurso|Definir o sinalizador Dirsyncenabled|
|Recurso|Definir Parceria|
|Recurso|Definir limiar de eliminação acidental|
|Recurso|Definir localização dos dados permitida da empresa|
|Recurso|Definir funcionalidade multinacional da empresa ativada|
|Recurso|Definir funcionalidade de diretório no inquilino|
|Recurso|Definir autenticação de domínio|
|Recurso|Configurar definições de federação em domínio|
|Recurso|Definir política de palavras-passe|
|Recurso|Definir propriedades de gestão de direitos|
|Recurso|Atualizar empresa|
|Recurso|Atualizar definições da empresa|
|Recurso|Atualizar domínio|
|Recurso|Verificar domínio|
|Recurso|Verificar domínio verificado por e-mail|
|Recurso|Inclusão|
|Recurso|Atualizar definições de alerta|
|Recurso|Atualizar definições de resumo semanal|
|Recurso|Desativar a repetição de escrita de palavras-passe no diretório|
|Recurso|Ativar a repetição de escrita de palavras-passe no diretório|
|Recurso|Adicionar atribuição de função de aplicação ao grupo|
|Recurso|Adicionar grupo|
|Recurso|Adicionar membro ao grupo|
|Recurso|Adicionar proprietário ao grupo|
|Recurso|Criar definições de grupo|
|Recurso|Eliminar grupo|
|Recurso|Eliminar definições do grupo|
|Recurso|Concluir a aplicação de licença baseada em grupo aos utilizadores|
|Recurso|Eliminação completa do grupo|
|Recurso|Remover atribuição de função de aplicação do grupo|
|Recurso|Remover membro do grupo|
|Recurso|Remover proprietário de grupo|
|Recurso|Restaurar Grupo|
|Recurso|Definir licença de grupo|
|Recurso|Definir grupo para ser gerido pelo utilizador|
|Recurso|Começar a aplicar licença baseada em grupo aos utilizadores|
|Recurso|Acionar recálculo de licença de grupo|
|Recurso|Atualizar grupo|
|Recurso|Atualizar definições de grupo|
|Recurso|Adicionar Membro|
|Recurso|Criar Grupo|
|Recurso|Eliminar Grupo|
|Recurso|Remover Membro|
|Recurso|Atualizar Grupo|
|Recurso|Aprovar um pedido pendente para aderir a um grupo|
|Recurso|Cancelar um pedido pendente para aderir a um grupo|
|Recurso|Criar política de gestão de ciclo de vida|
|Recurso|Eliminar um pedido pendente para aderir a um grupo|
|Recurso|Rejeitar um pedido pendente para aderir a um grupo|
|Recurso|Renovar grupo|
|Recurso|Pedido para aderir a um grupo|
|Recurso|Definir propriedades de grupo dinâmicas|
|Recurso|Atualizar política de gestão de ciclo de vida|
|Recurso|Adicionar uma chave com base no segredo ASCII a um contentor de chaves CPIM|
|Recurso|Adicionar uma chave a um contentor de chaves CPIM|
|Recurso|Eliminar um contentor de chaves CPIM|
|Recurso|Eliminar contentor de chaves|
|Recurso|Obter metadados de chaves ativas do contentor de chaves em JWK|
|Recurso|Obter metadados do contentor de chaves|
|Recurso|Obtém um contentor de chaves CPIM no formato jwk|
|Recurso|Obtém a lista de contentores de chaves no inquilino|
|Recurso|Restaurar uma cópia de segurança do contentor de chaves CPIM|
|Recurso|Guardar o contentor de chaves|
|Recurso|Carregar uma chave encriptada CPIM|
|Recurso|Emitir um código de autorização para a aplicação|
|Recurso|Emitir um token de acesso id_token para a aplicação|


## <a name="core-directory"></a>Diretório principal

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Unidade Administrativa|Transferir um tipo de eventos de risco único|
|Gestão de Unidade Administrativa|Transferir administradores e estado de aceitação de resumo semanal|
|Gestão de Unidade Administrativa|Transferir todos os tipos de eventos de risco|
|Gestão de Unidade Administrativa|Transferir eventos sem risco do utilizador|
|Gestão de Unidade Administrativa|Transferir utilizadores sinalizados para risco|
|Gestão de Aplicações|Convites em lote processados|
|Gestão de Aplicações|Convites em lote carregados|
|Gestão de Aplicações|Adicionar proprietário à política|
|Gestão de Aplicações|Adicionar política|
|Gestão de Aplicações|Eliminar política|
|Gestão de Aplicações|Remover credenciais de política|
|Gestão de Aplicações|Atualizar política|
|Gestão de Aplicações|Definir a política de registo de MFA|
|Gestão de Aplicações|Definir política de risco de início de sessão|
|Gestão de Aplicações|Definir política de risco de utilizador|
|Gestão de Aplicações|Editar os Termos de Utilização|
|Gestão de Aplicações|Criar os Termos de Utilização|
|Gestão de Aplicações|Rejeitar Termos de Utilização|
|Gestão de Aplicações|Eliminar Termos de Utilização|
|Gestão de Aplicações|Editar Termos de Utilização|
|Gestão de Aplicações|Publicar Termos de Utilização|
|Gestão de Aplicações|Anular publicação dos Termos de Utilização|
|Gestão de Aplicações|Adicionar certificado SSL da aplicação|
|Gestão de Aplicações|Eliminar enlace SSL|
|Gestão de Aplicações|Registar conector|
|Gestão de Aplicações|AdminPolicyDatas-RemoveResources|
|Gestão de Aplicações|AdminPolicyDatas-SetResources|
|Gestão de Aplicações|AdminUserJourneys-GetResources|
|Gestão de Diretórios|AdminUserJourneys-RemoveResources|
|Gestão de Diretórios|AdminUserJourneys-SetResources|
|Gestão de Diretórios|Criar IdentityProvider|
|Gestão de Diretórios|Criar um novo AdminUserJourney|
|Gestão de Diretórios|Criar json de recurso localizado|
|Gestão de Diretórios|Criar IDP Personalizado novo|
|Gestão de Diretórios|Criar novo IDP|
|Gestão de Diretórios|Criar ou atualizar um recurso de diretório B2C|
|Gestão de Diretórios|Criar política|
|Gestão de Diretórios|Criar política trustFramework|
|Gestão de Diretórios|Criar política trustFramework com prefixo configurável|
|Gestão de Diretórios|Criar atributo de utilizador|
|Gestão de Diretórios|CreateTrustFrameworkPolicy|
|Gestão de Diretórios|Eliminar IDP|
|Gestão de Diretórios|Eliminar IdentityProvider|
|Gestão de Diretórios|Eliminar um recurso de diretório B2C|
|Gestão de Diretórios|Eliminar política trustFramework|
|Gestão de Diretórios|Eliminar atributo de utilizador|
|Gestão de Diretórios|Obter recursos de diretório B2C num grupo de recursos|
|Gestão de Diretórios|Obter recursos de diretório B2C numa subscrição|
|Gestão de Diretórios|Obter IDP Personalizado|
|Gestão de Diretórios|Obter IDP|
|Gestão de Diretórios|Obter um recurso de diretório B2C|
|Gestão de Diretórios|Obter um percurso do utilizador|
|Gestão de Diretórios|Obter afirmações de aplicação permitidas para percurso do utilizador|
|Gestão de Diretórios|Obter afirmações de declaração própria permitidas para percurso do utilizador|
|Gestão de Diretórios|Obter afirmações de declaração própria permitidas da política|
|Gestão de Diretórios|Obter lista de afirmações de saída disponíveis|
|Gestão de Diretórios|Obter definições de conteúdo para percurso do utilizador|
|Gestão de Diretórios|Obter idps para um fluxo de administração específico|
|Gestão de Diretórios|Obter a lista de todos os fluxos de administração|
|Gestão de Diretórios|Obter a lista de etiquetas de todos os fluxos de administração de todos os utilizadores|
|Gestão de Grupos|Obter a lista de inquilinos de um utilizador|
|Gestão de Grupos|Obter afirmações de declaração própria de contas locais|
|Gestão de Grupos|Obter json de recurso localizado|
|Gestão de Grupos|Obter operações do fornecedor de recursos Microsoft.AzureActiveDirectory|
|Gestão de Grupos|Obter políticas|
|Gestão de Grupos|Obter política|
|Gestão de Grupos|Obter a lista de IDPs suportados|
|Gestão de Grupos|Obter a lista de IDPs suportados do percurso do utilizador|
|Gestão de Grupos|Obter lista de IDPs Personalizados definidos para o inquilino|
|Gestão de Grupos|Obter lista de IDPs definidos para o inquilino|
|Gestão de Grupos|Obter lista de IDPs locais definidos para o inquilino|
|Gestão de Grupos|Obter os detalhes do inquilino de um utilizador para criação de recurso|
|Gestão de Grupos|Obter a cultura predefinida suportada para CPIM|
|Gestão de Grupos|Obter os detalhes de um fluxo de administração|
|Gestão de Grupos|Obter a lista de UserJourneys deste inquilino|
|Gestão de Grupos|Obter o conjunto de culturas disponíveis suportadas para CPIM|
|Gestão de Grupos|Obter política trustFramework|
|Gestão de Grupos|Obter política trustFramework com xml|
|Gestão de Grupos|Obter atributo do utilizador|
|Gestão de Políticas|Obter atributos do utilizador|
|Gestão de Políticas|Obter a lista de percursos do utilizador|
|Gestão de Políticas|GetIEFPolicies|
|Gestão de Políticas|GetIdentityProviders|
|Gestão de Políticas|GetTrustFrameworkPolicy|
|Recurso|MigrateTenantMetadata|
|Recurso|Mover recursos|
|Recurso|Corrigir IdentityProvider|
|Recurso|PutTrustFrameworkPolicy|
|Recurso|PutTrustFrameworkpolicy|
|Recurso|Remover um percurso do utilizador|
|Recurso|Atualizar o IDP Personalizado|
|Recurso|Atualizar IDP|
|Recurso|Atualizar IDP Local|
|Recurso|Atualizar um recurso de diretório B2C|
|Recurso|Atualizar política|
|Recurso|Atualizar estado da subscrição|
|Gestão de Funções|Atualizar atributo de utilizador|
|Gestão de Funções|Validar movimentação de recursos|
|Gestão de Funções|Adicionar dispositivo|
|Gestão de Funções|Adicionar configuração do dispositivo|
|Gestão de Funções|Adicionar proprietário registado ao dispositivo|
|Gestão de Funções|Adicionar utilizadores registados ao dispositivo|
|Gestão de Funções|Eliminar dispositivo|
|Gestão de Funções|Eliminar configuração do dispositivo|
|Gestão de Funções|O dispositivo já não está em conformidade|
|Gestão de Funções|O dispositivo já não está a ser gerido|
|Gestão de Utilizadores|Remover proprietário registado do dispositivo|
|Gestão de Utilizadores|Remover utilizadores registados do dispositivo|
|Gestão de Utilizadores|Atualizar o dispositivo|
|Gestão de Utilizadores|Atualizar configuração do dispositivo|
|Gestão de Utilizadores|Adicionar membro elegível à função|
|Gestão de Utilizadores|Adicionar membro à função|
|Gestão de Utilizadores|Adicionar atribuição de função à definição de função|
|Gestão de Utilizadores|Adicionar função a partir de modelo|
|Gestão de Utilizadores|Adicionar membro com âmbito à função|
|Gestão de Utilizadores|Remover membro elegível da função|
|Gestão de Utilizadores|Remover membro de função|
|Gestão de Utilizadores|Remover atribuição de função da definição de função|
|Gestão de Utilizadores|Remover membro com âmbito da função|
|Gestão de Utilizadores|Atualizar função|
|Gestão de Utilizadores|AccessReview_Review|
|Gestão de Utilizadores|AccessReview_Update|
|Gestão de Utilizadores|ActivationAborted|
|Gestão de Utilizadores|ActivationApproved|
|Gestão de Utilizadores|ActivationCanceled|
|Gestão de Utilizadores|ActivationRequested|
|Gestão de Utilizadores|Adicionou|
|Gestão de Utilizadores|Atribuir|


## <a name="identity-protection"></a>Proteção de identidade

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Diretórios|Elevar|
|Gestão de Diretórios|Removido|
|Gestão de Diretórios|Alterações das definições da função|
|Outros|ScanAlertsNow|
|Outros|Inscrever-se|
|Outros|Anular elevação|
|Outros|UpdateAlertSettings|
|Outros|UpdateCurrentState|
|Gestão de Políticas|Revisão de acesso terminada|
|Gestão de Políticas|Adicionar aprovador para aprovação de pedidos|
|Gestão de Políticas|Adicionar revisor à revisão de acesso|
|Gestão de Utilizadores|Aplicar revisão de acesso|
|Gestão de Utilizadores|Criar revisão de acesso|


## <a name="invited-users"></a>Utilizadores convidados

|Categoria de Auditoria|Atividade|
|---|---|
|Outros|Criar aprovação de pedidos|
|Outros|Eliminar revisão de acesso|
|Gestão de Utilizadores|Remover revisor da revisão de acesso|
|Gestão de Utilizadores|Requerer aplicação do resultado da revisão|
|Gestão de Utilizadores|Requerer Paragem da Revisão|
|Gestão de Utilizadores|Rever atribuição de aplicações|
|Gestão de Utilizadores|Rever associação ao grupo|
|Gestão de Utilizadores|Rever associação da função RBAC|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Grupos|Rever pedido de aprovação de pedidos|
|Gestão de Grupos|Atualizar Revisão de Acesso|
|Gestão de Grupos|Atualizar definições de notificação de correio da revisão de acesso|
|Gestão de Grupos|Atualizar a definição de contagem de periodicidade de revisões de acesso|
|Gestão de Grupos|Atualizar a definição de duração em dias de periodicidade de revisões de acesso|
|Gestão de Utilizadores|Atualizar a definição de tipo de fim de periodicidade de revisões de acesso|
|Gestão de Utilizadores|Atualizar definição de tipo de periodicidade de revisões de acesso|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Categoria de Auditoria|Atividade|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Adicionou|
|PIM|AddedOutsidePIM|
|PIM|Atribuir|
|PIM|DismissAlert|
|PIM|Elevar|
|PIM|ReactivateAlert|
|PIM|Removido|
|PIM|RemovedOutsidePIM|
|PIM|Requerer Paragem da Revisão|
|PIM|Alterações das definições da função|
|PIM|ScanAlertsNow|
|PIM|Inscrever-se|
|PIM|Anular a atribuição|
|PIM|Anular elevação|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Gestão de grupos self-service

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Grupos|Repor palavra-passe do utilizador|
|Gestão de Grupos|Restaurar utilizador|
|Gestão de Grupos|Forçar alteração de palavra-passe do utilizador|
|Gestão de Grupos|Definir gestor de utilizador|
|Gestão de Grupos|Definir metadados de token de autorização de utilizadores ativado|
|Gestão de Grupos|Atualizar StsRefreshTokenValidFrom Timestamp|
|Gestão de Grupos|Atualizar segredos externos|
|Gestão de Grupos|Atualizar utilizador|
|Gestão de Grupos|O administrador gera uma palavra-passe temporária|


## <a name="self-service-password-management"></a>Gestão de palavras-passe personalizada

|Categoria de Auditoria|Atividade|
|---|---|
|Gestão de Diretórios|Os administradores exigem que o utilizador reponha a respetiva palavra-passe|
|Gestão de Diretórios|Atribuir utilizador externo à aplicação|
|Gestão de Utilizadores|E-mail não enviado; subscrição do utilizador anulada|
|Gestão de Utilizadores|Convidar utilizador externo|
|Gestão de Utilizadores|Resgatar convite de utilizador externo|
|Gestão de Utilizadores|Criação de inquilinos viral|
|Gestão de Utilizadores|Criação de utilizador viral|
|Gestão de Utilizadores|Registo de Palavras-passe do Utilizador|
|Gestão de Utilizadores|Reposição de Palavra-passe do Utilizador|
|Gestão de Utilizadores|Bloqueado da reposição de palavras-passe personalizada|


## <a name="terms-of-use"></a>Termos de utilização

|Categoria de Auditoria|Atividade|
|---|---|
|Termos de Utilização|Editar os Termos de Utilização|
|Termos de Utilização|Criar os Termos de Utilização|
|Termos de Utilização|Rejeitar Termos de Utilização|
|Termos de Utilização|Eliminar consentimento|
|Termos de Utilização|Eliminar Termos de Utilização|
|Termos de Utilização|Editar Termos de Utilização|
|Termos de Utilização|Termos de utilização de expirar|
|Termos de Utilização|Eliminar disco rígido de termos de utilização|
|Termos de Utilização|Publicar Termos de Utilização|
|Termos de Utilização|Anular publicação dos Termos de Utilização|


## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral de relatórios do Azure AD](overview-reports.md).
- [Relatório de registos de auditoria](concept-audit-logs.md). 
- [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
