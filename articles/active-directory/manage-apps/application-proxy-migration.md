---
title: Upgrade para Procuração de Aplicação AD Azure [ Microsoft Docs
description: Escolha qual a solução proxy melhor se estiver a atualizar a partir da Microsoft Forefront ou do Gateway de Acesso Unificado.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67108417"
---
# <a name="compare-remote-access-solutions"></a>Comparar soluções de acesso remoto

A Procuração de Aplicações de Diretório Ativo Azure é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é Web Application Proxy, a versão no local. Estas duas soluções substituem os produtos anteriores que a Microsoft ofereceu: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Use este artigo para entender como estas quatro soluções se comparam umas às outras. Para aqueles que ainda usam as soluções Deprecated TMG ou UAG, use este artigo para ajudar a planear a sua migração para uma das proxy de aplicação. 


## <a name="feature-comparison"></a>Comparação de funcionalidades

Utilize esta tabela para entender como o Gateway de Gestão de Ameaças (TMG), gateway de acesso unificado (UAG), Proxy de Aplicação Web (WAP) e Procuração de Aplicações AD Azure (AP) se comparam entre si.

| Funcionalidade | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificados | Sim | Sim | - | - |
| Publicar seletivamente aplicações de navegador | Sim | Sim | Sim | Sim |
| Pré-autenticação e inscrição única | Sim | Sim | Sim | Sim | 
| Firewall camada 2/3 | Sim | Sim | - | - |
| Capacidades de procuração avançadas | Sim | - | - | - |
| Capacidades VPN | Sim | Sim | - | - |
| Apoio ao protocolo rico | - | Sim | Sim, se atropelar http | Sim, se passar por HTTP ou através do Gateway remote desktop |
| Serve como servidor proxy ADFS | - | Sim | Sim | - |
| Um portal para acesso a aplicações | - | Sim | - | Sim |
| Tradução de ligação corporal de resposta | Sim | Sim | - | Sim | 
| Autenticação com cabeçalhos | - | Sim | - | Sim, com PingAccess | 
| Segurança em escala de nuvem | - | - | - | Sim | 
| Acesso Condicional | - | Sim | - | Sim |
| Não há componentes na zona desmilitarizada (DMZ) | - | - | - | Sim |
| Sem ligações de entrada | - | - | - | Sim |

Para a maioria dos cenários, recomendamos o Procuraproxy de Aplicação AD Azure como a solução moderna. O Proxy de Aplicação Web só é preferido em cenários que requerem um servidor proxy para AD FS, e não pode utilizar domínios personalizados no Diretório Ativo Do Azure. 

A Procuração de Aplicações Azure AD oferece benefícios únicos quando comparado com produtos similares, incluindo:

- Alargar a AD Azure aos recursos no local
   - Segurança e proteção em escala de nuvem
   - Funcionalidades como Acesso Condicional e Autenticação multi-Factor são fáceis de ativar
- Sem componentes na zona desmilitarizada
- Não são necessárias ligações de entrada
- Um painel de acesso a que os seus utilizadores podem recorrer para todas as suas aplicações, incluindo o O365, as aplicações Integradas do Azure AD SaaS e as suas aplicações web no local. 


## <a name="next-steps"></a>Passos seguintes

- [Utilize a Aplicação AD Azure para fornecer acesso remoto seguro a aplicações no local](application-proxy.md)
