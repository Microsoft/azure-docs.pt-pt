---
title: Recursos para aplicações migratórias para o Diretório Ativo do Azure Microsoft Docs
description: Recursos para ajudá-lo a migrar o acesso e autenticação da aplicação para o Azure Ative Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968731"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Recursos para candidaturas migratórias para o Diretório Ativo do Azure

Recursos para ajudá-lo a migrar o acesso e autenticação da aplicação para o Azure Ative Directory (Azure AD). Faça este pequenohttps://aka.ms/AppsMigrationFeedback) levantamento ( para fornecer feedback sobre a sua experiência de migrar aplicações para a AD Azure (incluindo bloqueadores para migração, necessidade de ferramentas/orientação ou razões para manter o seu IDP no local). 

| Recurso  | Descrição  |
|:-----------|:-------------|
|[Migrando as suas apps para o Azure AD](https://aka.ms/migrateapps/whitepaper) | Este livro branco apresenta os benefícios da migração e descreve como planear a migração em quatro fases claramente delineadas: descoberta, classificação, migração e gestão contínua. Você será guiado através de como pensar sobre o processo e dividir o seu projeto em pedaços fáceis de consumir. Ao longo do documento estão ligações a recursos importantes que o ajudarão ao longo do caminho. |
|[Guia de soluções: Aplicações migratórias dos Serviços da Federação de Diretórios Ativos (AD FS) para a Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Este guia de soluções percorre-o através das mesmas quatro fases de planeamento e execução de um projeto de migração de aplicações descrito a um nível mais elevado no livro branco da migração. Neste guia, você aprenderá a aplicar essas fases ao objetivo específico de mover uma aplicação de Azure Directy Federated Services (AD FS) para Azure AD.|
| [Ferramenta: Roteiro de prontidão de migração de serviços da Federação de Serviços de Diretório Ativo](https://aka.ms/migrateapps/adfstools) | Este é um script que pode executar no seu servidor ative directory federation services (AD FS) para determinar a prontidão de apps para migração para AD Azure.|
| [Plano de implementação: Migrar de AD FS para sincronização de hash de senha](https://aka.ms/ADFSTOPHSDPDownload) | Com a sincronização de hash de palavra-passe, as hashes das palavras-passe dos utilizadores são sincronizadas de diretório ativo no local para Azure AD. Isto permite que a AD Azure autentique os utilizadores sem interagir com o Diretório Ativo no local.| 
| [Plano de implantação: Migrar da AD FS para a autenticação pass-through](https://aka.ms/ADFSTOPTADPDownload)|A autenticação pass-through azure AD ajuda os utilizadores a iniciar em simultâneo aplicações no local e na nuvem utilizando a mesma palavra-passe. Esta funcionalidade proporciona aos seus utilizadores uma melhor experiência, uma vez que têm menos uma senha para recordar. Também reduz os custos de helpdesk de TI porque os utilizadores são menos propensos a esquecer como iniciar sessão quando só precisam de se lembrar de uma palavra-passe. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local.|
| [Plano de implementação: Permitir um único sign-on para uma aplicação SaaS com AD Azure](https://aka.ms/SSODPDownload) | O único registo (SSO) ajuda-o a aceder a todas as aplicações e recursos necessários para fazer negócios, ao mesmo tempo que assina apenas uma vez, utilizando uma única conta de utilizador. Por exemplo, depois de um utilizador ter assinado, o utilizador pode passar do Microsoft Office, para O SalesForce, para o Box sem autenticar (por exemplo, digitar uma palavra-passe) uma segunda vez. 
| [Plano de implementação: Alargamento de apps a AD Azure com Procuração de Aplicações](https://aka.ms/AppProxyDPDownload)| O acesso dos portáteis dos funcionários e de outros dispositivos a aplicações no local envolveu tradicionalmente redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs). Estas soluções não só são complexas e difíceis de proteger, como também são caras em termos de configuração e gestão. A Procuração de Aplicações Azure AD facilita o acesso às aplicações no local. |
| [Planos de implementação](../fundamentals/active-directory-deployment-plans.md) | Encontre mais planos de implementação para funcionalidades de implementação como autenticação multi-Factor, Acesso Condicional, fornecimento de utilizadores, SSO sem emenda, reset de senha de autosserviço, e muito mais! |


