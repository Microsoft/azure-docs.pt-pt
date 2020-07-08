---
title: Proteção contra ameaças para cofre de chaves Azure
description: Este artigo explica como criar uma proteção avançada de ameaças para o Azure Key Vault no Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77914811"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para cofre de chave Azure (pré-visualização)

A proteção avançada de ameaças para o Azure Key Vault fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de aceder ou explorar contas key Vault. Usando a proteção de ameaças avançadas nativas no Azure Security Center, você pode abordar ameaças sem ser um perito em segurança, e sem aprender sistemas adicionais de monitorização de segurança.

Quando o Centro de Segurança deteta atividade anómala, apresenta alertas. Envia também um e-mail ao administrador de subscrição com detalhes da atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

## <a name="configuring-threat-protection-from-security-center"></a>Configurar a proteção contra ameaças do Centro de Segurança

Por predefinição, a proteção avançada de ameaças está ativada para todas as suas contas Key Vault quando subscreve o nível de preços padrão do Security Center. Para mais informações, consulte [a Fixação de Preços.](security-center-pricing.md)

Para ativar ou desativar a proteção de uma subscrição específica:

1. A partir do painel esquerdo no Centro de Segurança, **selecione as definições de preços &**.

1. Selecione a subscrição com as contas de armazenamento para as quais deseja ativar ou desativar a proteção contra ameaças.

1. **Selecione o nível de preços**.

1. A partir do **nível de preços Select por** grupo tipo de recurso, encontre a linha **Cofres chave** e selecione **Ativado** ou **Desativado**.

    [![Permitir ou desativar a proteção avançada de ameaças para o Cofre de Chaves no Centro de Segurança Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecione **Guardar**.


## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a permitir e desativar a proteção avançada de ameaças para o Cofre da Chave Azure. 

Para obter material relacionado, consulte os seguintes artigos:

- [Proteção contra ameaças no Centro de Segurança Azure](threat-protection.md)-- Este artigo descreve as fontes de alerta de segurança no Centro de Segurança Azure.
- [Alertas de segurança key Vault](alerts-reference.md#alerts-azurekv)-- A secção do Cofre chave da tabela de referência para todos os alertas do Centro de Segurança Azure