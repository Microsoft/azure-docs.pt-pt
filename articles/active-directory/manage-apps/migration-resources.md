---
title: Recursos para a migração de apps para a Azure Ative Directory | Microsoft Docs
description: Recursos para ajudá-lo a migrar o acesso e autenticação da aplicação ao Azure Ative Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376652"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Recursos para migrar aplicações para o AAD

Recursos para ajudá-lo a migrar o acesso e autenticação da aplicação ao Azure Ative Directory (Azure AD).

| Recurso  | Descrição  |
|:-----------|:-------------|
|[Migrando as suas apps para Azure AD](https://aka.ms/migrateapps/whitepaper) | Este livro branco apresenta os benefícios da migração e descreve como planear a migração em quatro fases claramente delineadas: descoberta, classificação, migração e gestão em curso. Você será guiado através de como pensar sobre o processo e dividir o seu projeto em peças fáceis de consumir. Ao longo do documento estão ligações a recursos importantes que o ajudarão ao longo do caminho. |
|[Guia de solução: Aplicativos migratórios dos Serviços da Federação de Diretórios Ativos (AD FS) para Azure AD](./migrate-adfs-apps-to-azure.md) | Este guia de solução acompanha-o pelas mesmas quatro fases de planeamento e execução de um projeto de migração de aplicações descrito a um nível mais elevado no papel branco migratório. Neste guia, você aprenderá a aplicar essas fases ao objetivo específico de mover uma aplicação de Azure Directory Federated Services (AD FS) para Azure AD.|
|[Tutorial de desenvolvedor: AD FS para Azure AD aplicação livro de migração para desenvolvedores](https://aka.ms/adfsplaybook) | Este conjunto de amostras de código ASP.NET e tutoriais de acompanhamento irá ajudá-lo a aprender a migrar de forma segura e segura as suas aplicações integradas com serviços da Federação de Diretório Ativo (AD FS) para o Azure Ative Directory (Azure AD). Este tutorial está focado em programadores que não só precisam de aprender a configurar aplicações tanto em AD FS como Azure AD, como também se tornam conscientes e confiantes das mudanças que a sua base de código exigirá neste processo.|
| [Ferramenta: Roteiro de prontidão de migração de serviços da Federação de Diretórios Ativos](https://aka.ms/migrateapps/adfstools) | Este é um script que você pode executar no seu servidor Ative Directory Federation Services (AD FS) para determinar a prontidão de apps para migração para Azure AD.|
| [Plano de implantação: Migração de FS AD para sincronização de haxixe de palavra-passe](https://aka.ms/ADFSTOPHSDPDownload) | Com a sincronização de hash de palavra-passe, as hashes das palavras-passe do utilizador são sincronizadas desde o Ative Directory até ao Azure AD. Isto permite que a Azure AD autuça os utilizadores sem interagir com o Ative Directory no local.| 
| [Plano de implantação: Migração de FS AD para autenticação transitada](https://aka.ms/ADFSTOPTADPDownload)|A autenticação pass-through AD AD ajuda os utilizadores a iniciar súmis nas aplicações no local e na nuvem usando a mesma senha. Esta funcionalidade proporciona aos seus utilizadores uma melhor experiência, uma vez que têm menos uma senha para se lembrarem. Também reduz os custos de it helpdesk porque os utilizadores são menos propensos a esquecer como entrar quando só precisam de se lembrar de uma palavra-passe. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local.|
| [Plano de implementação: Permitir o único sign-on a uma aplicação SaaS com Azure AD](https://aka.ms/SSODPDownload) | O único sign-on (SSO) ajuda-o a aceder a todas as aplicações e recursos necessários para fazer negócios, enquanto faz a sessão apenas uma vez, utilizando uma única conta de utilizador. Por exemplo, depois de um utilizador ter assinado, o utilizador pode passar do Microsoft Office, para SalesForce, para Box sem autenticar (por exemplo, digitando uma palavra-passe) uma segunda vez. 
| [Plano de implementação: Alargar apps ao Azure AD com Proxy de aplicações](https://aka.ms/AppProxyDPDownload)| O acesso de computadores portáteis e outros dispositivos a aplicações no local tem tradicionalmente envolvido redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs). Estas soluções não só são complexas e difíceis de proteger, como também são caras em termos de configuração e gestão. O Azure AD Application Proxy facilita o acesso a aplicações no local. |
| [Planos de implementação](../fundamentals/active-directory-deployment-plans.md) | Encontre mais planos de implementação para implementar funcionalidades como autenticação multi-factor, acesso condicional, fornecimento de utilizador, SSO sem costura, redefinição de senha de autosserviço e muito mais! |