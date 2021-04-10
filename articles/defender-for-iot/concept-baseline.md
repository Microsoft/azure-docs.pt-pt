---
title: Verificações de base e personalizadas
description: Conheça o conceito de Azure Defender para linha de base IoT.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779363"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender para linha de base IoT e verificações personalizadas

Este artigo explica o Defender para a linha de base IoT e resume todas as propriedades associadas de verificações personalizadas de base.

## <a name="baseline"></a>Linha de base

Uma linha de base estabelece o comportamento padrão para cada dispositivo e facilita o estabelecimento de comportamentos incomuns ou desvios em comparação com as normas esperadas.

## <a name="baseline-custom-checks"></a>Verificações personalizadas de base

As verificações personalizadas de base estabelecem uma lista personalizada de verificações para cada linha de base do dispositivo utilizando o **módulo gémeo** de identidade do dispositivo.

## <a name="setting-baseline-properties"></a>Definição de propriedades de base

1. No seu Hub IoT, localize e selecione o dispositivo que pretende alterar.

1. Clique no dispositivo e, em seguida, clique no módulo **de insegurança azureiot.**

1. Clique **na identidade do módulo Twin.**

1. Faça o upload do ficheiro **de verificação personalizada** da linha de base para o dispositivo.

1. Adicione propriedades de base ao micro-agente Defender-IoT e clique em **Guardar**.

### <a name="baseline-custom-check-file-example"></a>Exemplo de ficheiro de verificação personalizado de linha de base

Para configurar verificações personalizadas de base:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Propriedades de verificação personalizadas de base

| Name| Estado | Valores válidos| Valores predefinidos| Description |
|------|-----|------|-----|-----|
|linha de baseCustomChecksEnabled|Requerido: verdadeiro |Valores válidos: **Boolean** |Valor predefinido: **falso** |Intervalo de tempo máximo antes de serem enviadas mensagens de alta prioridade.|
|linha de baseCustomChecksFilePath |Requerido: verdadeiro|Valores válidos: **String,** **nulo** |Valor predefinido: **nulo** |Caminho completo da configuração xml de base|
|baseCustomChecksFileHash |Requerido: verdadeiro|Valores válidos: **String,** **nulo** |Valor predefinido: **nulo** |`sha256sum` do ficheiro de configuração xml. Utilize a [referência sha256sum](https://linux.die.net/man/1/sha256sum) para obter informações adicionais. |

Para rever exemplos de base adicionais, consulte o [exemplo de base personalizado -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e o exemplo de base personalizado [-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passos seguintes

- Aceda aos seus [dados de segurança brutos](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações de segurança](concept-recommendations.md)
- Compreender e explorar [alertas de segurança](concept-security-alerts.md)
