---
title: 'Autenticação de Passe-Through Azure AD: Versão lança história de lançamento Microsoft Docs'
description: Este artigo lista todas as versões do agente de autenticação Pass-through Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786457"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD Pass-through Authentication agent: Version release history 
 
Os agentes instalados no local que permitem a Autenticação Pass-through são atualizados regularmente para fornecer novas capacidades. Este artigo lista as versões e funcionalidades que são adicionadas quando é introduzida uma nova funcionalidade. Os agentes de autenticação pass-through são atualizados automaticamente quando uma nova versão é lançada. 

Aqui estão os tópicos relacionados: 

- [Entrada de utilizador com autenticação de passe azure AD](how-to-connect-pta.md) 
- [Instalação de agente de autenticação Pass-through Azure AD](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Estado de lançamento 
1/22/2019: Lançado para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte adicional para canais fiáveis de ônibus de serviço para adicionar outra camada de resiliência de conexão para ligações de saída 
- Impor TLS 1.2 durante o registo do agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Estado de lançamento 
4/10/2018: Lançado para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte de ligação à tomada web 
- Definir TLS 1.2 como o protocolo padrão para o agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Estado de lançamento 
1/31/2018: Lançado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 

- Fixou um inseto que causou algumas fugas de memória no agente. 
- Atualizou a versão Azure Service Bus, que inclui uma correção de bugs para problemas de tempo de conector. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Estado de lançamento 
11/26/2017: Lançado para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte adicional para ligações baseadas em websocket entre o agente e os serviços da AD Azure para melhorar a resiliência da ligação 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Estado de lançamento 
11/25/2017: Lançado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 
- Bugs fixos relacionados com a cache DNS para cenários de procuração padrão 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Estado de lançamento 
10/17/2017: Lançado para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Funcionalidade de cache DNS adicionada para ligações de saída para adicionar resiliência a partir de falhas dNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Estado de lançamento 
08/31/2017: Lançado para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Versão GA do agente de autenticação Pass-through Azure AD 

## <a name="next-steps"></a>Passos seguintes

- [Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)