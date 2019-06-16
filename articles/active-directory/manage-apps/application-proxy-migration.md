---
title: Atualizar para o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Escolha que solução de proxy é melhor se estiver a atualizar do Microsoft Forefront ou Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108417"
---
# <a name="compare-remote-access-solutions"></a>Compare as soluções de acesso remoto

Proxy de aplicação do Active Directory do Azure é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Proxy de aplicações Web, a versão no local. Essas duas soluções substituem produtos anteriores que Microsoft oferecida: Microsoft Forefront Threat Management Gateway (TMG) e o Unified Access Gateway (UAG). Utilize este artigo para compreender como essas quatro soluções comparam entre si. Para aqueles que ainda utilizam as soluções TMG ou UAG preteridas, utilize este artigo para ajudar a planear a migração para um Proxy de aplicações. 


## <a name="feature-comparison"></a>Comparação de funcionalidades

Utilize esta tabela para compreender como Threat Management Gateway (TMG), o Unified Access Gateway (UAG), o Proxy de aplicações Web (WAP) e o Proxy de aplicações do Azure AD (AP) comparam entre si.

| Funcionalidade | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificados | Sim | Sim | - | - |
| Publicar seletivamente as aplicações de browser | Sim | Sim | Sim | Sim |
| Pré-autenticação e o único início de sessão | Sim | Sim | Sim | Sim | 
| 2/3 firewall de camada | Sim | Sim | - | - |
| Capacidades de proxy de encaminhamento | Sim | - | - | - |
| Capacidades VPN | Sim | Sim | - | - |
| Suporte de protocolo avançado | - | Sim | Sim, se executar através de HTTP | Sim, se executar através de HTTP ou através do Gateway de ambiente de trabalho remoto |
| Funciona como servidor de proxy do AD FS | - | Sim | Sim | - |
| Um portal de acesso à aplicação | - | Sim | - | Sim |
| Conversão de link de corpo de resposta | Sim | Sim | - | Sim | 
| Autenticação com cabeçalhos | - | Sim | - | Sim, com o PingAccess | 
| Segurança de escala da cloud | - | - | - | Sim | 
| Acesso Condicional | - | Sim | - | Sim |
| Não existem componentes na zona desmilitarizada (DMZ) | - | - | - | Sim |
| Não existem ligações de entrada | - | - | - | Sim |

Na maioria dos cenários, é recomendável o Proxy de aplicações do Azure AD como a solução modernos. Proxy de aplicações Web é apenas preferenciais em cenários que exigem um servidor proxy para o AD FS e não é possível utilizar domínios personalizados no Azure Active Directory. 

Proxy de aplicações do Azure AD oferece as vantagens exclusivas quando em comparação aos produtos semelhantes, incluindo:

- Estendendo o Azure AD para recursos no local
   - Proteção e segurança de escala da cloud
   - Funcionalidades como o acesso condicional e multi-factor Authentication são fáceis de ativar
- Não existem componentes na zona desmilitarizada
- Não existem ligações de entrada necessárias
- Painel de acesso de um que os utilizadores podem aceder a para todas as aplicações, incluindo o Office 365, do Azure AD integrado a aplicações SaaS e aplicações de web no local. 


## <a name="next-steps"></a>Passos Seguintes

- [Utilizar a aplicação do Azure AD para fornecer acesso remoto seguro a aplicações no local](application-proxy.md)
