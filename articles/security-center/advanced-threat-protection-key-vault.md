---
title: Proteção contra ameaças para cofre de chaves Azure
description: Este artigo explica como criar uma proteção avançada contra ameaças para o Cofre chave Azure no Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914811"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para cofre de chaves Azure (pré-visualização)

A proteção avançada de ameaças para o Cofre chave azure fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de aceder ou explorar contas key vault. Utilizando a proteção contra ameaças avançadas nativas no Azure Security Center, pode abordar ameaças sem ser um perito em segurança, e sem aprender sistemas adicionais de monitorização de segurança.

Quando o Centro de Segurança deteta atividade anómala, apresenta alertas. Também envia um e-mail ao administrador de subscrição com detalhes sobre a atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

## <a name="configuring-threat-protection-from-security-center"></a>Configurar a proteção contra ameaças do Centro de Segurança

Por predefinição, a proteção de ameaças avançada está ativada para todas as suas contas Key Vault quando subscrever o nível padrão de preços do Security Center. Para mais informações, consulte [Preços](security-center-pricing.md).

Para ativar ou desativar a proteção para uma subscrição específica:

1. A partir do painel esquerdo no Centro de Segurança, selecione **Preços e configurações**.

1. Selecione a subscrição com as contas de armazenamento para as quais pretende ativar ou desativar a proteção contra ameaças.

1. Selecione **Escalão de preço**.

1. Do **nível** de preços Select por grupo de tipo de recurso, encontre a linha **Key Vaults** e selecione **Ativado** ou **Desativado**.

    [![habilitação ou desativação de proteção avançada de ameaças para o cofre chave no Centro de Segurança Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecione **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a permitir e desativar a proteção avançada de ameaças para o Cofre chave Azure. 

Para material relacionado, consulte os seguintes artigos:

- [Proteção contra ameaças no Centro](threat-protection.md)de Segurança Azure -- Este artigo descreve as fontes de alertas de segurança no Centro de Segurança Azure.
- [Alertas](alerts-reference.md#alerts-azurekv)de segurança do Cofre chave -- A secção chave do cofre da tabela de referência para todos os alertas do Centro de Segurança Azure