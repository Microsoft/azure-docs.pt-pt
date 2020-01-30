---
title: Configurar a proteção avançada contra ameaças para o Azure Key Vault | Microsoft Docs
description: Este artigo explica como configurar a proteção avançada contra ameaças para Azure Key Vault na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f7f716734875d652de575991a4dc1b7bfe948ae3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773521"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Configurar a proteção avançada contra ameaças para Azure Key Vault (versão prévia)

A proteção avançada contra ameaças para Azure Key Vault fornece uma camada adicional de inteligência de segurança. Essa ferramenta detecta tentativas potencialmente perigosas de acessar ou explorar contas de Key Vault. Usando a proteção de ameaças avançadas nativa na central de segurança do Azure, você pode lidar com ameaças sem ser um especialista em segurança e sem aprender sistemas de monitoramento de segurança adicionais.

Quando a central de segurança detecta a atividade anômala, ela exibe alertas. Ele também envia emails ao administrador da assinatura com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças identificadas.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Configurar a proteção avançada contra ameaças na central de segurança do Azure

Por padrão, a proteção avançada contra ameaças está habilitada para todas as suas contas de Key Vault quando você assina a camada Standard da central de segurança. Para obter mais informações, consulte [preços](security-center-pricing.md).

Para habilitar ou desabilitar a proteção para uma assinatura específica, siga estas etapas.

1. No painel esquerdo na central de segurança, selecione **preços & configurações**.
1. Selecione a assinatura com as contas de armazenamento para as quais você deseja habilitar ou desabilitar a proteção contra ameaças.
1. Selecione **Escalão de preço**.
1. No grupo **selecionar camada de preços por tipo de recurso** , localize a linha **cofres de chaves** e selecione **habilitado** ou **desabilitado**.

    [![habilitação ou desativação de proteção avançada de ameaças para o cofre chave no Centro de Segurança Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Selecione **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a habilitar e desabilitar a proteção avançada contra ameaças para Azure Key Vault. 

Para obter outros materiais relacionados, consulte o seguinte artigo:

- [Detecção de ameaças para as camadas dos serviços do Azure na central de segurança](security-center-alerts-service-layer.md): Este artigo descreve os alertas relacionados à proteção avançada contra ameaças para o Azure Key Vault.
