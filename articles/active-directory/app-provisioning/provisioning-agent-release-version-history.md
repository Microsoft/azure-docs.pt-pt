---
title: 'Azure AD Connect Provisioning Agent: Version release history / Microsoft Docs'
description: Este artigo lista todos os lançamentos do Agente de Provisionamento AD AD AD Ad e descreve novas funcionalidades e problemas fixos
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 9e05d1a85f17800ddf4d77e4e4acba6396a8da47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84781570"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect Provisioning Agent: O histórico de lançamento da versão

Este artigo lista as versões e funcionalidades do Azure Ative Directory Connect Provisioning Agent que foram lançados. A equipa Azure AD atualiza regularmente o Agente de Provisionamento com novas funcionalidades e funcionalidades. O Agente Provisionante é atualizado automaticamente quando uma nova versão é lançada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Estado de lançamento

4 de dezembro de 2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Inclui suporte para [fornecimento de nuvem Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar dados de utilizador, contato e grupo de diretórios ativos no local para Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Estado de lançamento

9 de setembro de 2019: Lançado para atualização automática

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Capacidade de configurar rastreios adicionais e registos para depuração de problemas de agente de provisionamento
* Capacidade de obter apenas os atributos AD Azure que estão configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigiu um bug em que o agente entrou em estado sem resposta se houvesse problemas com falhas de ligação AD Azure
* Corrigi um bug que causou problemas quando os dados binários foram lidos a partir do Azure Ative Directory
* Corrigiu um bug em que o agente não renovou a confiança com o serviço de identidade híbrida em nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Estado de lançamento

23 de janeiro de 2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Renovado o Agente de Provisionamento e a arquitetura do conector para um melhor desempenho, estabilidade e fiabilidade 
* Simplificado a configuração do Agente de Provisioning utilizando o assistente de instalação orientado para a UI 
* Apoio adicional para atualizações automáticas de agentes

