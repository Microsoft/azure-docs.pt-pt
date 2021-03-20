---
title: 'Autenticação pass-through Azure AD: versão lança histórico | Microsoft Docs'
description: Este artigo lista todos os lançamentos do agente de autenticação Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91333431"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD Agente de autenticação pass-through: histórico de lançamento de versão 
 
Os agentes instalados no local que permitem a autenticação pass-through são atualizados regularmente para fornecer novas capacidades. Este artigo lista as versões e funcionalidades que são adicionadas quando é introduzida nova funcionalidade. Os agentes de autenticação pass-through são atualizados automaticamente quando uma nova versão é lançada. 

Aqui estão os tópicos relacionados: 

- [Sedús de insuperação do utilizador com autenticação pass-through Azure AD](how-to-connect-pta.md) 
- [Instalação de agente de autenticação pass-through Azure AD](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Estado do lançamento: 
04/09/2020: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- Suporte adicional para direcionar ambientes de nuvem após a instalação. O pacote pode ser fixado a um dado ambiente de nuvem.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Estado de lançamento 
1/22/2019: Lançamento para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte adicional para canais fiáveis do Service Bus para adicionar outra camada de resiliência de conexão para ligações de saída 
- Impor TLS 1.2 durante o registo do agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Estado de lançamento 
4/10/2018: Lançamento para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte à ligação da tomada web 
- Definir TLS 1.2 como o protocolo padrão para o agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Estado de lançamento 
1/31/2018: Lançamento para download  
### <a name="fixed-issues"></a>Problemas corrigidos 
- Consertei um erro que causou fugas de memória no agente. 
- Atualizou a versão Azure Service Bus, que inclui uma correção de erro para problemas de tempo de conector. 
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Suporte adicional para ligações baseadas em websocket entre o agente e os serviços AD da Azure para melhorar a resiliência da ligação

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Estado de lançamento 
11/25/2017: Lançamento para download  
### <a name="fixed-issues"></a>Problemas corrigidos 
- Bugs fixos relacionados com a cache DNS para cenários de procuração predefinidos 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Estado de lançamento 
10/17/2017: Lançamento para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Adicional à funcionalidade de cache DNS para ligações de saída para adicionar resiliência de falhas de DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Estado de lançamento 
08/31/2017: Lançamento para download  
### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 
- Versão GA do agente de autenticação Azure AD Pass-through 

## <a name="next-steps"></a>Passos seguintes

- [Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)
