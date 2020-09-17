---
title: Upgrade para Azure AD Application Proxy / Microsoft Docs
description: Escolha qual a melhor solução de procuração se estiver a atualizar a partir do Microsoft Forefront ou do Portal de Acesso Unificado.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cccabaf069a3027e615892e36e218f865a6c983a
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706665"
---
# <a name="compare-remote-access-solutions"></a>Comparar soluções de acesso remoto

A azure Ative Directory Application Proxy é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Web Application Proxy, a versão no local. Estas duas soluções substituem os produtos anteriores que a Microsoft ofereceu: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Use este artigo para entender como estas quatro soluções se comparam entre si. Para aqueles que ainda usam as soluções de TMG ou UAG precídas, utilize este artigo para ajudar a planear a sua migração para um dos Proxy de aplicações. 


## <a name="feature-comparison"></a>Comparação de funcionalidades

Utilize esta tabela para entender como o Gateway de Gestão de Ameaças (TMG), O Gateway de Acesso Unificado (UAG), o Proxy da Aplicação Web (WAP) e o Azure AD Application Proxy (AP) se comparam entre si.

| Funcionalidade | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificados | Yes | Yes | - | - |
| Publicar seletivamente aplicativos de navegador | Yes | Yes | Yes | Yes |
| Pré-autorização e inscrição única | Yes | Yes | Yes | Yes | 
| Firewall de camada 2/3 | Yes | Yes | - | - |
| Capacidades de procuração avançadas | Yes | - | - | - |
| Capacidades VPN | Yes | Yes | - | - |
| Rico apoio ao protocolo | - | Yes | Sim, se atropelando HTTP | Sim, se passar por HTTP ou através do Gateway de Desktop Remoto |
| Serve como servidor de procuração ADFS | - | Yes | Yes | - |
| Um portal para acesso a aplicações | - | Yes | - | Yes |
| Tradução da ligação do corpo de resposta | Yes | Yes | - | Yes | 
| Autenticação com cabeçalhos | - | Yes | - | Sim, com o PingAccess | 
| Segurança em escala de nuvem | - | - | - | Yes | 
| Acesso Condicional | - | Yes | - | Yes |
| Sem componentes na zona desmilitarizada (DMZ) | - | - | - | Yes |
| Sem ligações de entrada | - | - | - | Yes |

Para a maioria dos cenários, recomendamos o Azure AD Application Proxy como a solução moderna. O Proxy da Aplicação Web é apenas preferido em cenários que requerem um servidor proxy para AD FS, e não é possível utilizar domínios personalizados no Azure Ative Directory. 

A Azure AD Application Proxy oferece benefícios únicos quando comparados com produtos similares, incluindo:

- Alargar a Azure a recursos no local
   - Segurança e proteção em escala de nuvem
   - Funcionalidades como Acesso Condicional e Autenticação Multi-Factor são fáceis de ativar
- Sem componentes na zona desmilitarizada
- Não são necessárias ligações de entrada
- Uma página my apps a que os seus utilizadores podem ir para todas as suas aplicações, incluindo as aplicações Integradas do Microsoft 365, AZure AD e as suas aplicações web no local. 


## <a name="next-steps"></a>Passos seguintes

- [Utilize a Aplicação AZure AD para fornecer acesso remoto seguro a aplicações no local](application-proxy.md)
