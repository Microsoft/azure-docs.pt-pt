---
title: Como configurar a proteção avançada contra ameaças para o Azure Key Vault | Microsoft Docs
description: Este artigo explica como configurar a proteção avançada contra ameaças para Azure Key Vault na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521881"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Como configurar a proteção avançada contra ameaças para Azure Key Vault (versão prévia)

A proteção avançada contra ameaças para Azure Key Vault fornece uma camada adicional de inteligência de segurança. Essa ferramenta detecta tentativas potencialmente perigosas de acessar ou explorar contas de Key Vault. Usando a proteção de ameaças avançadas nativa da central de segurança, você pode lidar com ameaças sem ser um especialista em segurança e sem aprender sistemas de monitoramento de segurança adicionais.

Quando a central de segurança detecta a atividade anômala, ela exibe alertas. Ele também envia emails ao administrador da assinatura com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças identificadas. 

> [!NOTE]
> A proteção avançada contra ameaças para Azure Key Vault está disponível atualmente apenas em regiões América do Norte.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Para configurar a proteção avançada contra ameaças na central de segurança do Azure

Por padrão, a proteção avançada contra ameaças está habilitada para todas as suas contas de Key Vault quando você assina a camada Standard da central de segurança (consulte [preços](security-center-pricing.md)). 

Para habilitar ou desabilitar a proteção para uma assinatura específica:

1. Na barra lateral da central de segurança, clique em **preços & configurações**.
1. Selecione a assinatura com as contas de armazenamento para as quais você deseja habilitar ou desabilitar a proteção contra ameaças.
1. Clique em **tipo de preço**.
1. No grupo **selecionar camada de preços por tipo de recurso** , localize a linha cofres de chaves e clique em **habilitado** ou **desabilitado**.
    [![habilitar ou desabilitar a proteção avançada contra ameaças para Key Vault na central de segurança do Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Clique em **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a habilitar e desabilitar a proteção avançada contra ameaças para Azure Key Vault. 

Para obter outros materiais relacionados, consulte o seguinte artigo:

- [Detecção de ameaças para as camadas dos serviços do Azure na central de segurança](security-center-alerts-service-layer.md) – este artigo descreve os alertas relacionados à proteção avançada contra ameaças para Azure Key Vault