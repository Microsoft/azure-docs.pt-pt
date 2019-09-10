---
title: 'Agente de provisionamento do Azure AD Connect: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do agente de provisionamento do Azure AD Connect e descreve os novos recursos e problemas corrigidos
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862112"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente de provisionamento do Azure AD Connect: Histórico de versões
Este artigo lista as versões e os recursos do agente de provisionamento do Azure AD Connect que foram lançados. A equipe do Azure AD atualiza regularmente o agente de provisionamento com novos recursos e funcionalidades. Os agentes de provisionamento são atualizados automaticamente quando uma nova versão é lançada. 

Recomendamos habilitar a atualização automática para seus agentes para garantir que você tenha os recursos e correções de bugs mais recentes. A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status da versão

9 de setembro de 2019: Liberado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreamento e log adicionais para depuração de problemas do agente de provisionamento
* Capacidade de buscar somente os atributos do AD configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Correção de um bug no qual o agente entrou em um estado sem resposta se houvesse problemas com falhas de conexão do AD
* Correção de um bug que causou problemas quando dados binários foram lidos de Active Directory
* Correção de um bug em que-no agente falhou ao renovar a confiança com o serviço de identidade híbrida na nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status da versão

23 de janeiro de 2019: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Arquitetura de conector de & do agente de provisionamento remodelado para melhorar o desempenho, a estabilidade e a confiabilidade 
* Configuração do agente de provisionamento simplificado usando o assistente de instalação orientado por interface do usuário 
* Suporte adicionado para atualizações automáticas de agente

