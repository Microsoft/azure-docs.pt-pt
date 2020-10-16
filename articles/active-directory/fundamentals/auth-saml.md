---
title: Autenticação SAML com Diretório Ativo Azure
description: Orientação arquitetónica para alcançar este padrão de autenticação
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
ms.openlocfilehash: 05b13e04db8e83a8a10c2d7fe0aea202dfa3b69c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114504"
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

![diagrama arquitetónico](./media/authentication-patterns/saml-auth.png)

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
