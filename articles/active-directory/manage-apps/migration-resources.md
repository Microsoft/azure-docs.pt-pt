---
title: Recursos para a migração de aplicações para o Azure Active Directory | Documentos da Microsoft
description: Recursos para ajudar a migrar o acesso à aplicação e a autenticação para o Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: celested
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ce62d745bfb0299dc8f3eb401f5a66e38580f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440667"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Recursos para a migração de aplicativos para o Azure Active Directory

Recursos para ajudar a migrar o acesso à aplicação e a autenticação para o Azure Active Directory (Azure AD). Este breve inquérito (https://aka.ms/AppsMigrationFeedback) para fornecer comentários nas suas aplicações de migrar de experiência para o Azure AD (incluindo bloqueadores de migração, precisa de ferramentas / orientação ou motivos para reter o IDP no local). 

| Recurso  | Descrição  |
|:-----------|:-------------|
|[Migrar seus aplicativos para o Azure AD](https://aka.ms/migrateapps/whitepaper) | Este white paper apresenta as vantagens da migração e descreve como planear a migração em quatro fases, claramente descritos: deteção, classificação, migração e gerenciamento contínuo. Vai ser orientado ao longo como pensar sobre o processo e dividir seu projeto em partes de fácil compreensão. Ao longo do documento são links para recursos importantes que ajudarão ao longo do caminho. |
|[Guia de soluções: Migrar aplicações dos serviços de Federação do Active Directory (AD FS) para o Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Este guia de soluções descreve as mesmas quatro fases de planejamento e a execução de um projeto de migração de aplicação descrito num nível mais alto o White Paper de migração. Neste guia, aprenderá como aplicar essas fases para o objetivo específico de mover uma aplicação de Azure Directory Federated Services (AD FS) para o Azure AD.|
| [Ferramenta: Script de preparação de migração de serviços de Federação do Active Directory](https://aka.ms/migrateapps/adfstools) | Este é um script pode executar no seu servidor de serviços de Federação do Active Directory (AD FS) no local para determinar a preparação dos aplicativos para a migração para o Azure AD.|
| [Plano de implantação: Migrar do AD FS para sincronização de hash de palavra-passe](https://aka.ms/ADFSTOPHSDPDownload) | Com a sincronização de hash de palavra-passe, os hashes de palavras-passe do utilizador são sincronizados do Active Directory no local para o Azure AD. Isso permite que o Azure AD para autenticar os utilizadores sem a interação com o Active Directory no local.| 
| [Plano de implantação: Migrar do AD FS para autenticação pass-through](https://aka.ms/ADFSTOPTADPDownload)|Autenticação pass-through do Azure AD ajuda os utilizadores que inicie sessão no local e aplicações baseadas na cloud com a mesma palavra-passe. Esta funcionalidade fornece os seus utilizadores com uma melhor experiência, uma vez que eles têm um menos palavra-passe para se lembrar. Também reduz os custos de suporte técnico de TI vez que os usuários têm menos probabilidade de esquecer como iniciar sessão, quando apenas precisarem de Lembre-se de uma palavra-passe. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local.|
| [Plano de implantação: Ativar o início de sessão único para uma aplicação SaaS com o Azure AD](https://aka.ms/SSODPDownload) | Única início de sessão (SSO) ajuda a que aceder a todas as aplicações e recursos de que precisa para fazer negócio, ao iniciar sessão apenas uma vez, com uma conta de utilizador único. Por exemplo, após um utilizador tem sessão iniciada, o utilizador pode mover da Microsoft Office, ao SalesForce, a caixa sem autenticação (por exemplo, escrever uma palavra-passe) uma segunda vez. 
| [Plano de implantação: Alargamento das aplicações para o Azure AD com o Proxy de aplicações](https://aka.ms/AppProxyDPDownload)| Fornecer acesso a partir de computadores portáteis de funcionários e outros dispositivos para locais aplicativos sempre envolveu redes privadas virtuais (VPNs) ou desmilitarizada zonas (DMZs). Estas soluções não só são complexas e difíceis de proteger, como também são caras em termos de configuração e gestão. Proxy de aplicações do Azure AD torna mais fácil para aceder a aplicações no local. |
| [Planos de implantação](../fundamentals/active-directory-deployment-plans.md) | Encontre mais planos de implementação para a implementação de recursos, como a autenticação Multifator, acesso condicional, aprovisionamento de utilizadores, totalmente integrada SSO, reposição de palavra-passe self-service e muito mais! |


