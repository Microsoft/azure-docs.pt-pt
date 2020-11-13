---
title: Autenticação da OAUTH 2.0 com Diretório Ativo Azure
description: Orientação arquitetónica para a autenticação da AAUTH 2.0 com o Azure Ative Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: daf40a2ced3f753619e9c4723dbe78cd7e51ff21
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577911"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Autenticação OAuth 2.0 com Diretório Ativo Azure

O OAuth 2.0 é o protocolo da indústria para autorização. Permite que um utilizador conceda acesso limitado aos seus recursos protegidos. Projetado para trabalhar especificamente com o Hypertext Transfer Protocol (HTTP), a OAuth separa o papel do cliente do proprietário do recurso. O cliente solicita o acesso aos recursos controlados pelo titular do recurso e hospedados pelo servidor de recursos. O servidor de recursos emite fichas de acesso com a aprovação do proprietário do recurso. O cliente utiliza os tokens de acesso para aceder aos recursos protegidos alojados pelo servidor de recursos. 

O OAuth 2.0 está diretamente relacionado com o OpenID Connect (OIDC). Uma vez que o OIDC é uma camada de autenticação e autorização construída em cima do OAuth 2.0, não é compatível com OAuth 1.0. O Azure Ative Directory (Azure AD) suporta todos os fluxos OAuth 2.0. 

## <a name="use-when"></a>Utilize se:

Para clientes ricos & cenários modernos de aplicações e acesso RESTful Web API.

![Diagrama de arquitetura](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador** : Solicita um serviço a partir da aplicação web (app). O utilizador é tipicamente o proprietário de recursos que detém os dados e tem o poder de permitir que os clientes acedam aos dados ou recursos. 

* **Web browser** : O navegador web com o qual o utilizador interage é o cliente OAuth. 

* **Aplicação Web** : A aplicação web, ou servidor de recursos, é onde reside o recurso ou dados. Confia no servidor de autorização para autenticar e autorizar o cliente OAuth. 

* **Azure AD** : Azure AD é o servidor de autorização, também conhecido como Fornecedor de Identidade (IdP). Lida com segurança com a informação do utilizador, o seu acesso e a relação de confiança. É responsável pela emissão dos tokens que concedem e revogam o acesso aos recursos.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementar OAuth 2.0 com Azure AD

* [Integrara aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protocolos OAuth 2.0 e OpenID Connect na Plataforma de Identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Tipos de aplicações e OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
