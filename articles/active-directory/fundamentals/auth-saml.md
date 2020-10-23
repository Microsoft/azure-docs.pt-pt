---
title: Autenticação SAML com Diretório Ativo Azure
description: Orientação arquitetónica para a realização da autenticação SAML com o Azure Ative Directory
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
ms.openlocfilehash: 1ab14413de1f999747e5b3fb58b505e0a9258a55
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441220"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Autenticação SAML com Diretório Ativo Azure

A Linguagem de Marcação de Afirmação de Segurança (SAML) é um padrão aberto para a troca de dados de autenticação e autorização entre um fornecedor de identidade e um prestador de serviços. SAML é uma linguagem de marcação baseada em XML para afirmações de segurança, que são declarações que os prestadores de serviços usam para tomar decisões de controlo de acesso. 

A especificação SAML define três funções:

* O principal, geralmente um utilizador
* O fornecedor de identidade (IdP)
* O prestador de serviços (SP)


## <a name="use-when"></a>Utilizar quando

Há a necessidade de fornecer uma única experiência de sign-on (SSO) para uma aplicação SAML da empresa.

Embora um dos casos de uso mais importantes que a SAML aborda é o SSO, especialmente estendendo sSO através dos domínios de segurança, existem outros casos de uso (chamados perfis) também. 

![diagrama arquitetónico para SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Utilizador**: Solicita um serviço a partir da aplicação.

* **Web browser**: O componente com o que o utilizador interage.

* **Aplicação Web**: Aplicação empresarial que suporta SAML e utiliza Azure AD como IdP.

* **Token**: Uma afirmação SAML (também conhecida como fichas SAML) que transporta conjuntos de reclamações feitas pelo IdP sobre o princípio (utilizador). Contém informações de autenticação, atributos e declarações de decisão de autorização.

* **Azure AD**: IdP de nuvem empresarial que fornece autenticação SSO e multi-factor para aplicações SAML. Sincroniza, mantém e gere informações de identidade para os utilizadores, ao mesmo tempo que fornece serviços de autenticação a aplicações de gestão. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementar a autenticação SAML com Azure AD

* [Tutoriais para integrar aplicações saaS usando diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Configuração do sign-on único baseado em SAML para aplicações não-galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Como o Azure AD utiliza o protocolo SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
