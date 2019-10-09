---
title: Entender a central de segurança do Azure para a linha de base IoT | Microsoft Docs
description: Saiba mais sobre o conceito da central de segurança do Azure para a linha de base da IoT.
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
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176621"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Central de segurança do Azure para linha de base IoT e verificações personalizadas

Este artigo explica a central de segurança do Azure para a linha de base IoT e resume todas as propriedades associadas de verificações personalizadas de linha de base.

## <a name="baseline"></a>Linha de base

Uma linha de base estabelece o comportamento padrão para cada dispositivo e torna mais fácil estabelecer comportamento incomum ou desvio de normas esperadas.  

## <a name="baseline-custom-checks"></a>Verificações personalizadas de linha de base

As verificações personalizadas de linha de base estabelecem uma lista personalizada de verificações para cada linha de base de dispositivo usando a **identidade do módulo** "/" do dispositivo. 

## <a name="setting-baseline-properties"></a>Definindo propriedades de linha de base

1. No Hub IoT, localize e selecione o dispositivo que você deseja alterar.
1. Clique no dispositivo e, em seguida, clique no módulo **azureiotsecurity** .
1. Clique em **identidade do módulo**.
1. Carregue o arquivo de **verificações personalizadas de linha de base** no dispositivo.
1. Adicione Propriedades de linha de base ao módulo de segurança e clique em **salvar**.

### <a name="baseline-custom-check-file-example"></a>Exemplo de arquivo de verificação personalizada de linha de base

Para configurar verificações personalizadas de linha de base:

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

## <a name="baseline-custom-check-properties"></a>Propriedades da verificação personalizada de linha de base

| Name| State | Valores válidos| Valores predefinidos| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Necessário: verdadeiro |Valores válidos: **valor booleano** |Valor padrão: **false** |Intervalo de tempo máximo antes que as mensagens de alta prioridade sejam enviadas.|
|baselineCustomChecksFilePath |Necessário: verdadeiro|Valores válidos: **Cadeia de caracteres**, **nulo** |Valor padrão: **nulo** |Caminho completo da configuração XML de linha de base|
|baselineCustomChecksFileHash |Necessário: verdadeiro|Valores válidos: **Cadeia de caracteres**, **nulo** |Valor padrão: **nulo** |`sha256sum` do arquivo de configuração XML. Use a [referência sha256sum](https://linux.die.net/man/1/sha256sum) para obter informações adicionais. |

Para examinar exemplos de linha de base adicionais, consulte [exemplo de linha de base personalizado-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e [exemplo de linha de base personalizado-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Passos seguintes

- Acessar seus [dados brutos de segurança](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entender e explorar as [recomendações de segurança](concept-recommendations.md)
- Entender e explorar [alertas de segurança](concept-security-alerts.md)
