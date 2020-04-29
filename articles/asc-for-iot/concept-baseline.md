---
title: Verificações de base e personalizadas
description: Conheça o conceito de Azure Security Center para a linha de base IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311644"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Centro de Segurança Azure para a linha de base ioT e verificações personalizadas

Este artigo explica o Azure Security Center para a linha de base IoT, e resume todas as propriedades associadas de verificações personalizadas de base.

## <a name="baseline"></a>Linha de base

Uma linha de base estabelece um comportamento padrão para cada dispositivo e facilita a determinação de comportamentos ou desvios incomuns das normas esperadas.

## <a name="baseline-custom-checks"></a>Verificações personalizadas de base

As verificações personalizadas de base estabelecem uma lista personalizada de verificações para cada linha de base do dispositivo utilizando o **mísero de identidade** do Módulo do dispositivo.

## <a name="setting-baseline-properties"></a>Definição de propriedades de base

1. No seu Hub IoT, localize e selecione o dispositivo que pretende alterar.
1. Clique no dispositivo e, em seguida, clique no módulo de **segurança azul.**
1. Clique em **Módulo Identidade Twin**.
1. Faça upload do ficheiro **de verificação personalizado** de base para o dispositivo.
1. Adicione propriedades de base ao módulo de segurança e clique em **Guardar**.

### <a name="baseline-custom-check-file-example"></a>Exemplo de ficheiro personalizado de linha de base

Para configurar as verificações personalizadas de base:

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

## <a name="baseline-custom-check-properties"></a>Propriedades de verificação personalizada supérvia

| Nome| Estado | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Obrigatório: verdadeiro |Valores válidos: **Boolean** |Valor predefinido: **falso** |Intervalo de tempo máximo antes de enviar mensagens de alta prioridade.|
|linha de baseCustomChecksFilePath |Obrigatório: verdadeiro|Valores válidos: **String,** **nulo** |Valor por defeito: **nulo** |Caminho completo da configuração xml de base|
|linha de baseCustomChecksFileHash |Obrigatório: verdadeiro|Valores válidos: **String,** **nulo** |Valor por defeito: **nulo** |`sha256sum`do ficheiro de configuração xml. Utilize a [referência sha256sum](https://linux.die.net/man/1/sha256sum) para obter informações adicionais. |

Para rever exemplos de base adicionais, consulte o [exemplo de base personalizado -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e o exemplo de base personalizado [-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passos seguintes

- Aceda aos seus [dados de segurança bruta](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações](concept-recommendations.md) de segurança
- Compreender e explorar [alertas](concept-security-alerts.md) de segurança
