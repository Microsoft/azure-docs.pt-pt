---
title: Planos de implantação - Azure Active Directory | Documentos da Microsoft
description: Documentação de orientação de ponta a ponta sobre como implementar muitos recursos do Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 6ca6d0ab9f18665f6e271bedba63ce98996ba7db
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445988"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implementação do Azure Active Directory
Está à procura de orientações completas para implementar algumas das capacidades do Azure Active Directory (Azure AD)? Os planos de implementação que se seguem orientam-no ao longo do valor comercial, das considerações de planeamento, de design e dos procedimentos operacionais necessários para implementar algumas das capacidades mais comuns do Azure AD. 

Nos documentos, vai encontrar modelos de e-mails, diagramas da arquitetura do sistema, casos de teste comuns, entre outras informações. 

Gostaríamos muito de saber a sua opinião sobre os documentos. Responda a este breve [inquérito](https://aka.ms/deploymentplanfeedback) para nos dizer se os documentos lhe foram úteis. 

|Cenário |Descrição |
|-|-|
|[Início de sessão único](https://aka.ms/SSODPDownload)|O início de sessão único ajuda-o a aceder a todos os recursos e aplicações de que precisa para trabalhar ao iniciar sessão só uma vez e com uma única conta de utilizador. Depois de iniciar sessão, pode utilizar o Microsoft Office, o SalesForce ou o Box sem ter de se autenticar (por exemplo, introduzir uma palavra-passe) uma segunda vez.|
|[Aprovisionamento de utilizadores de entrada baseadas no workday](https://aka.ms/WorkdayDeploymentPlan)|Controlado por workday Inbound aprovisionamento do utilizador do Active Directory cria uma base para a governação de identidade em curso e melhora a qualidade dos processos de negócios que se baseiam nos dados de identidade autoritativas. Utilizar esta funcionalidade, pode facilmente gerir o ciclo de vida de identidade dos funcionários e os operadores de contingência ao configurar as regras que mapeiam os processos de participante-Movimentador-Leaver (por exemplo, transferência de nova contratação, Terminate,) para ações de aprovisionamento de IT (como criar, Enable, Desativar e eliminar contas).|
|[Painel de Acesso](https://aka.ms/AccessPanelDPDownload)|Proporcionar aos usuários um hub simple para detetar e aceder a todos os seus aplicativos. Ativá-los para serem mais produtivos com capacidades self-service, como a capacidade de solicitar acesso a aplicações de novas e grupos, ou gerir o acesso a estes recursos em nome de outros.|
|[Aprovisionamento de utilizadores](https://aka.ms/UserProvisioningDPDownload)|O Azure AD ajuda-o a automatizar a criação, a manutenção e a remoção de identidades de utilizadores em aplicações da cloud (SaaS), como o Dropbox, o Salesforce, o ServiceNow, entre outras.|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Ao utilizar métodos de autenticação aprovados pelo administrador, o MFA do Azure ajuda a salvaguardar o acesso a dados e aplicações, enquanto cumpre a exigência de um processo de início de sessão simples.|
|[Acesso condicional](https://aka.ms/CADPDownload)|Com o acesso condicional, pode implementar decisões de controlo de acesso automatizadas relativamente a quem pode aceder às aplicações na cloud com base em condições.|
|[ADFS para Autenticação Pass-through](https://aka.ms/ADFSTOPTADPDownload)|A Autenticação Pass-through do Azure AD ajuda os utilizadores a iniciar sessão em aplicações no local e na cloud com as mesmas palavras-passe. Esta funcionalidade proporciona uma experiência melhor aos seus utilizadores - menos uma palavra-passe para memorizar - e reduz os custos com o suporte de TI, uma vez que é menos provável que os utilizadores se esqueçam de como iniciar sessão. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local.|
|[ADFS para Sincronização Hash de Palavras-passe](https://aka.ms/ADFSTOPHSDPDownload)|Com a Sincronização Hash de Palavras-passe, são sincronizados hashes das palavras-passe dos utilizadores do Active Directory no local com o Azure AD, o que permite que este último autentique os utilizadores sem interagir com o Active Directory no local.|
|[SSO totalmente integrado](https://aka.ms/SeamlessSSODPDownload)|O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Depois de ativar esta funcionalidade, os utilizadores não precisam de introduzir as palavras-passe para iniciar sessão no Azure AD e, por norma, nem sequer têm de introduzir os nomes de utilizador. Esta funcionalidade dá aos utilizadores acesso fácil às aplicações baseadas na cloud sem serem precisos componentes no local adicionais.|
|[Reposição personalizada de palavras-passe](https://aka.ms/SSPRDPDownload)|A reposição personalizada de palavras-passe ajuda os utilizadores a repor as respetivas palavras-passe, sem qualquer intervenção do administrador, quando e onde precisarem.|
|[Proxy de Aplicações do Azure AD](https://aka.ms/AppProxyDPDownload)|Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Querem trabalhar nos seus próprios dispositivos, quer sejam tablets, telemóveis ou portáteis. E também esperam poder aceder a todas as aplicações, quer aplicações SaaS na cloud, quer aplicações da empresa no local. A disponibilização de acesso a aplicações no local envolvia, tradicionalmente, redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs). Estas soluções não só são complexas e difíceis de proteger, como também são caras em termos de configuração e gestão. Existe uma forma melhor! - Proxy de aplicações do Azure AD|

