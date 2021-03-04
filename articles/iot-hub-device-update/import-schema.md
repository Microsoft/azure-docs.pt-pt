---
title: Importar atualizações para a atualização do dispositivo para ioT hub - esquema e outras informações | Microsoft Docs
description: Schema e outras informações relacionadas (incluindo objetos) que são usadas ao importar atualizações para a Atualização do Dispositivo para ioT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055009"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importar atualizações para a atualização do dispositivo para ioT hub - esquema e outras informações
Se quiser importar uma atualização para a Atualização do Dispositivo para ioT Hub, [certifique-se](import-update.md) de que reviu primeiro os [conceitos](import-concepts.md) e como orientar. Se estiver interessado nos detalhes do esquema utilizado na construção de um manifesto de importação, bem como em informações sobre objetos relacionados, consulte abaixo.

## <a name="import-manifest-schema"></a>Esquema manifesto de importação

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` objeto | Atualizar identidade. |
| Atualização DeType | string | Tipo de atualização: <br/><br/> * Especifique `microsoft/apt:1` ao executar uma atualização baseada em pacotes utilizando o agente de referência.<br/> * Especifique `microsoft/swupdate:1` ao executar uma atualização baseada em imagem utilizando o agente de referência.<br/> * Especifique `microsoft/simulator:1` ao utilizar o simulador do agente de amostra.<br/> * Especifique um tipo personalizado se desenvolver um agente personalizado. | Formato: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Máximo de 32 caracteres no total |
| Criteria instalada | string | String interpretado pelo agente para determinar se a atualização foi aplicada com sucesso:  <br/> * Especifique o **valor** da SWVersion para o tipo de atualização `microsoft/swupdate:1` .<br/> * Especifique `{name}-{version}` para o tipo de atualização , de qual nome e versão são `microsoft/apt:1` obtidos a partir do ficheiro APT.<br/> * Especifique o hash do ficheiro de atualização para o tipo de atualização `microsoft/simulator:1` .<br/> * Especifique uma cadeia personalizada se desenvolver um agente personalizado.<br/> | Máximo de 64 caracteres |
| Compatibilidade | Matriz de `CompatibilityInfo` objetos | Informações de compatibilidade do dispositivo compatíveis com esta atualização. | Máximo de 10 itens |
| CreatedDateTime | data/hora | Data e hora em que a atualização foi criada. | Iso 8601 delimitado, data e formato de hora, em UTC |
| Manifestação | string | Importar versão de esquema manifesto. `2.0`Especificar, que será compatível com interface `urn:azureiot:AzureDeviceUpdateCore:1` e `urn:azureiot:AzureDeviceUpdateCore:4` interface. | Deve ser `2.0` |
| Ficheiros | Matriz de `File` objetos | Atualizar ficheiros de carga útil | Máximo de 5 ficheiros |

## <a name="updateid-object"></a>Objeto UpdateId

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| Fornecedor | string | Fornecedor parte da identidade de atualização. | 1-64 caracteres, alfanumérico, ponto e traço. |
| Name | string | Nomeie parte da identidade da atualização. | 1-64 caracteres, alfanumérico, ponto e traço. |
| Versão | versão | Versão parte da identidade de atualização. | 2 a 4 partes, ponto separado número de versão entre 0 e 2147483647. Os principais zeros serão eliminados. |

## <a name="file-object"></a>Objeto de arquivo

| Nome | Tipo | Descrição | Restrições |
| --------- | --------- | --------- | --------- |
| Nome de arquivo | string | Nome do arquivo | Deve ser único dentro de uma atualização |
| TamanhoInBytes | Int64 | Tamanho do ficheiro em bytes. | Máximo de 800 MB por ficheiro individual, ou 800 MB coletivamente por atualização |
| Hashes | `Hashes` objeto | Objeto JSON que contém haxixe(es) do ficheiro |

## <a name="compatibilityinfo-object"></a>Objeto CompatibilidadeInfo

| Nome | Tipo | Descrição | Restrições |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Fabricante do dispositivo com o qual a atualização é compatível. | 1-64 caracteres, alfanumérico, ponto e traço. |
| DeviceModel | string | Modelo do dispositivo com o qual a atualização é compatível. | 1-64 caracteres, alfanumérico, ponto e traço. |

## <a name="hashes-object"></a>Objeto Hashes

| Name | Necessário | Tipo | Descrição |
| --------- | --------- | --------- | --------- |
| Sha256 | Verdadeiro | string | Hash codificado base64 do ficheiro usando o algoritmo SHA-256. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [conceitos de importação.](./import-concepts.md)

Se estiver pronto, experimente o [guia importe How-To,](./import-update.md)que o acompanhará passo a passo através do processo de importação.
