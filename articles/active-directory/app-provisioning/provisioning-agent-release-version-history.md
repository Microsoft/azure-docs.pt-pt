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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183249"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente de provisionamento de ligação Azure AD: histórico de lançamento de versão
Este artigo lista as versões e funcionalidades do Agente de Provisionamento de Ligação de Diretório Ativo Azure que foram lançados. A equipa da Azure AD atualiza regularmente o Agente de Provisionamento com novas funcionalidades e funcionalidades. O Agente de Provisionamento é atualizado automaticamente quando uma nova versão é lançada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Estado de lançamento

4 de dezembro de 2019: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Inclui suporte para o fornecimento de [nuvem Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar os dados do utilizador, do contacto e do grupo desde o Diretório Ativo no local até à AD Azure


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Estado de lançamento

9 de setembro de 2019: Lançado para atualização automática

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Capacidade de configurar rastreios adicionais e exploração de registo para depurar problemas de agente de provisionamento
* Capacidade de obter apenas os atributos Azure AD que estão configurados no mapeamento para melhorar o desempenho de sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Fixou um bug em que o agente entrou em estado sem resposta se houvesse problemas com falhas de ligação da AD Azure
* Fixou um bug que causou problemas quando os dados binários foram lidos do Diretório Ativo do Azure
* Fixou um bug em que o agente não conseguiu renovar a confiança com o serviço de identidade híbrida em nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Estado de lançamento

23 de janeiro de 2019: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Renovado o Agente de Provisionamento e arquitetura de conector para um melhor desempenho, estabilidade e fiabilidade 
* Simplificada configuração do Agente de Provisionamento utilizando o assistente de instalação orientado pela UI 
* Suporte adicional para atualizações automáticas de agentes

