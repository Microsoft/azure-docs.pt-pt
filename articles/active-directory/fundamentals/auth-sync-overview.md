---
title: Visão geral do protocolo de autenticação e sincronização do Azure Ative Directory
description: Orientação arquitetónica sobre a integração do AZure AD com protocolos de autenticação e padrões de sincronização
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441203"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Integrações do Azure Ative Directory com protocolos de autenticação e sincronização

O Microsoft Azure Ative Directory (Azure AD) permite a integração com muitos protocolos de autenticação e sincronização. As integrações de autenticação permitem-lhe utilizar o Azure AD e as suas funcionalidades de segurança e gestão com poucas ou nenhumas alterações nas suas aplicações que utilizam métodos de autenticação legado. As integrações de sincronização permitem sincronizar os dados do utilizador e do grupo com o AZure AD e, em seguida, as capacidades de gestão AZure AD do utilizador. Alguns padrões de sincronização também permitem o fornecimento automatizado.

## <a name="legacy-authentication-protocols"></a>Protocolos de autenticação de legados

A tabela que se segue apresenta integração Azure AD de autenticação com protocolos de autenticação legado e suas capacidades. Selecione o nome de um protocolo de autenticação para ver

* Uma descrição detalhada

* Quando Utilizar

* Diagrama arquitetónico

* Explicação dos componentes do sistema

* Links para como implementar a integração

 

| Protocolo de autenticação| Autenticação| Autorização| Multi-Factor Authentication| Acesso Condicional |
| - |- | - | - | - |
| [Autenticação baseada em cabeçalho](auth-header-based.md)|![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação LDAP](auth-ldap.md)| ![marca de verificação](./media/authentication-patterns/check.png)| | |  |
| [Autenticação OAuth 2.0](auth-oauth2.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação OIDC](auth-oidc.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação SSO baseada em palavra-passe](auth-password-based-sso.md )| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação RADIUS]( auth-radius.md)| ![marca de verificação](./media/authentication-patterns/check.png)| | ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Serviços remotos de Gateway de Desktop](auth-remote-desktop-gateway.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Concha segura (SSH)](auth-ssh.md) |  ![marca de verificação](./media/authentication-patterns/check.png)| | ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação SAML](auth-saml.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |
| [Autenticação do Windows - Delegação Restrita Kerberos](auth-kcd.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Padrões de sincronização

A tabela a seguir apresenta a integração AD do Azure com padrões de sincronização e as suas capacidades. Selecione o nome de um padrão para ver

* Uma descrição detalhada

* Quando Utilizar

* Diagrama arquitetónico

* Explicação dos componentes do sistema

* Links para como implementar a integração



| Padrão de sincronização| Sincronização de diretórios| Aprovisionamento de utilizadores |
| - | - | - |
| [Sincronização de diretórios](sync-directory.md)| ![marca de verificação](./media/authentication-patterns/check.png)|  |
| [Sincronização LDAP](sync-ldap.md)| ![marca de verificação](./media/authentication-patterns/check.png)|  |
| [Sincronização de SCIM](sync-scim.md)| ![marca de verificação](./media/authentication-patterns/check.png)| ![marca de verificação](./media/authentication-patterns/check.png) |

