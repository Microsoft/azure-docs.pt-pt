---
title: Configurar proteção avançada contra ameaças para o Azure Key Vault
description: Este artigo explica como criar uma proteção avançada contra ameaças para o Cofre chave Azure no Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430555"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Configurar proteção avançada contra ameaças para o Cofre chave Azure (pré-visualização)

A proteção avançada de ameaças para o Cofre chave azure fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de aceder ou explorar contas key vault. Utilizando a proteção contra ameaças avançadas nativas no Azure Security Center, pode abordar ameaças sem ser um perito em segurança, e sem aprender sistemas adicionais de monitorização de segurança.

Quando o Centro de Segurança deteta atividade anómala, apresenta alertas. Também envia um e-mail ao administrador de subscrição com detalhes sobre a atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Criar proteção avançada contra ameaças do Centro de Segurança Azure

Por predefinição, a proteção de ameaça avançada está ativada para todas as suas contas Key Vault quando subscrever o nível Padrão do Centro de Segurança. Para mais informações, consulte [Preços](security-center-pricing.md).

Para ativar ou desativar a proteção para uma subscrição específica, siga estes passos.

1. A partir do painel esquerdo no Centro de Segurança, selecione **Preços e configurações**.
1. Selecione a subscrição com as contas de armazenamento para as quais pretende ativar ou desativar a proteção contra ameaças.
1. Selecione **Escalão de preço**.
1. Do **nível** de preços Select por grupo de tipo de recurso, encontre a linha **Key Vaults** e selecione **Ativado** ou **Desativado**.

    [![habilitação ou desativação de proteção avançada de ameaças para o cofre chave no Centro de Segurança Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Selecione **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a permitir e desativar a proteção avançada de ameaças para o Cofre chave Azure. 

Para outros materiais relacionados, consulte o seguinte artigo:

- [Deteção de ameaças para as camadas de serviços Azure no Centro](security-center-alerts-service-layer.md)de Segurança : Este artigo descreve os alertas relacionados com a proteção avançada de ameaças para o Cofre chave Azure.
