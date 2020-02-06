---
title: 'Azure AD Connect Provisioning Agent: Version release history | Microsoft Docs'
description: Este artigo lista todas as versões do Agente de Provisionamento de Ligação AD Azure e descreve novas funcionalidades e questões fixas
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
ms.date: 02/04/2020
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac9eac2d17fa0a4f1db487d4c7f8beb67de24a9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030687"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente de provisionamento de ligação Azure AD: histórico de lançamento de versão
Este artigo lista as versões e funcionalidades do Agente de Provisionamento de Ligação de Diretório Ativo Azure que foram lançados. A equipa da Azure AD atualiza regularmente o Agente de Provisionamento com novas funcionalidades e funcionalidades. O Agente de Provisionamento é atualizado automaticamente quando uma nova versão é lançada. 

Recomendamos que aatualização automática para os seus agentes garanta que possui as mais recentes funcionalidades e correções de bugs. A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Estado de libertação

4 de dezembro de 2019: Lançado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para o fornecimento de [nuvem Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar os dados do utilizador, do contacto e do grupo desde o Diretório Ativo no local até à AD Azure


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Estado de libertação

9 de setembro de 2019: Lançado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreios adicionais e exploração de registo para depurar problemas de agente de provisionamento
* Capacidade de obter apenas os atributos Azure AD que estão configurados no mapeamento para melhorar o desempenho de sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Fixou um bug em que o agente entrou em estado sem resposta se houvesse problemas com falhas de ligação da AD Azure
* Fixou um bug que causou problemas quando os dados binários foram lidos do Diretório Ativo do Azure
* Fixou um bug em que o agente não conseguiu renovar a confiança com o serviço de identidade híbrida em nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Estado de libertação

23 de janeiro de 2019: Lançado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Renovado o Agente de Provisionamento e arquitetura de conector para um melhor desempenho, estabilidade e fiabilidade 
* Simplificada configuração do Agente de Provisionamento utilizando o assistente de instalação orientado pela UI 
* Suporte adicional para atualizações automáticas de agentes

