---
title: Atualização do dispositivo para a atualização do IoT Hub manifesta | Microsoft Docs
description: Saiba como as propriedades são enviadas do serviço de Atualização de Dispositivos para o dispositivo durante uma atualização
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030686"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Atualização do dispositivo para o manifesto de atualização do IoT Hub

## <a name="overview"></a>Descrição geral

A Atualização do Dispositivo para IoT Hub utiliza um _manifesto de atualização_ para comunicar ações e também metadados que suportam essas ações através das propriedades de interface [AzureDeviceUpdateCore.OrchestratorMetadata:4.](./device-update-plug-and-play.md)
Este documento descreve os fundamentos de como a `updateManifest` propriedade, na `AzureDeviceUpdateCore.OrchestratorMetadata:4` interface, é utilizada pelo Agente de Atualização de Dispositivos. As `AzureDeviceUpdateCore.OrchestratorMetadata:4` propriedades da interface são enviadas do serviço Device Update para o serviço IoT Hub para o Agente de Atualização de Dispositivos. É `updateManifest` um objeto JSON serializado que é analisado pelo Agente de Atualização do Dispositivo.

### <a name="an-example-update-manifest"></a>Um manifesto de atualização de exemplo

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

O objetivo do manifesto de atualização é descrever o conteúdo de uma atualização, nomeadamente a sua identidade, tipo, critérios instalados e atualizar metadados de ficheiros. Além disso, o manifesto de atualização é assinado criptograficamente para permitir que o Agente de Atualização do Dispositivo verifique a sua autenticidade. Consulte a documentação de segurança da [Atualização](./device-update-security.md) do Dispositivo para obter mais informações sobre como o manifesto de atualização é utilizado para importar conteúdo de forma segura.

## <a name="import-manifest-vs-update-manifest"></a>Manifesto de importação vs atualização manifesto

É importante compreender as diferenças entre o manifesto de importação e os conceitos manifestos de atualização no Device Update para o IoT Hub. 
* O [manifesto de importação](./import-concepts.md) é criado por quem cria a atualização correspondente. Descreve o conteúdo da atualização que será importada para a Atualização do Dispositivo para ioT Hub. 
* O manifesto de atualização é gerado automaticamente pela Atualização do Dispositivo para o serviço IoT Hub, utilizando algumas das propriedades definidas no manifesto de importação. É utilizado para comunicar informações relevantes ao Agente de Atualização do Dispositivo durante o processo de atualização. 

Cada tipo manifesto tem o seu próprio esquema e versão de esquema.

## <a name="update-manifest-properties"></a>Atualizar propriedades manifesto

As definições de alto nível das propriedades manifestos de atualização podem ser encontradas nas definições de interface encontradas [aqui.](./device-update-plug-and-play.md) Para fornecer um contexto mais profundo, vamos olhar mais de perto as propriedades e como são usadas no sistema.

### <a name="updateid"></a>updateId

Contém o `provider` , e , que representa a `name` `version` atualização exata do dispositivo para a identidade de atualização do IoT Hub utilizada para determinar dispositivos compatíveis para a atualização.

### <a name="updatetype"></a>atualizaçãoType

Representa o tipo de atualização que é tratada por um tipo específico de manipulador de atualização. Segue a forma de `microsoft/swupdate:1` uma atualização baseada em imagem e `microsoft/apt:1` para uma atualização baseada em pacotes (ver `Update Handler Types` secção abaixo).

### <a name="installedcriteria"></a>instaladoCriteria

Uma cadeia que contém informações necessárias pelo Manipulador de Atualização do Agente de Atualização do Dispositivo para determinar se a atualização está instalada no dispositivo. A `Update Handler Types` secção documenta o formato do , para cada tipo de `installedCriteria` atualização suportado pela Atualização do Dispositivo para IoT Hub.

### <a name="files"></a>ficheiros

Informa o Agente de Atualização de Dispositivos quais os ficheiros para descarregar e o haxixe que será utilizado para verificar se os ficheiros foram descarregados corretamente.
Aqui está um olhar mais atento sobre o conteúdo da `files` propriedade:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Fora do `updateManifest` é a matriz de Objeto `fileUrls` JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Tanto o `FILE_ID_STRING` , dentro , e são os `fileUrls` `files` mesmos (por exemplo, "0000" tem `files` o url em "0000" `fileUrls` dentro).

### <a name="manifestversion"></a>manifestoVersão

Uma corda que representa a versão do esquema.

## <a name="update-handler-types"></a>Tipos de manipulador de atualização

|Método de Atualização|Tipo de manipulador de atualização|Tipo de atualização|Critérios instalados|Ficheiros Esperados para Publicação|
|-------------|-------------------|----------|-----------------|--------------|
|Baseado em imagem|SWUpdate|"microsoft/swupdate:versão"|A imagem de referência guarda a sugestão da sua versão no ficheiro /etc/adu-versão.  |.swu file que contém imagem SWUpdate|
|Baseado em pacotes|APT|"microsoft/apt:version"|`<name>` + "-" + `<version>` (propriedades definidas no ficheiro APT Manifesto|`<APT Update Manifest>`.json que contém a configuração APT e lista de pacotes|

