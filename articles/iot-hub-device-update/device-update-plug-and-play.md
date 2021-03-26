---
title: Compreenda como a atualização do dispositivo para ioT Hub utiliza IoT Plug e Play | Microsoft Docs
description: A Atualização do Dispositivo para IoT Hub utiliza para descobrir e gerir dispositivos que sejam capazes de atualizar por cima do ar.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561819"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Atualização do dispositivo para IoT Hub e IoT Plug e Play

A Atualização do Dispositivo para IoT Hub utiliza [IoT Plug e Play](../iot-pnp/index.yml) para descobrir e gerir dispositivos que são capazes de atualizar por cima do ar. O serviço de Atualização de Dispositivos enviará e receberá propriedades e mensagens de e para dispositivos que utilizem interfaces PnP. A Atualização do Dispositivo para o IoT Hub requer dispositivos IoT para implementar as seguintes interfaces e id de modelo, conforme descrito abaixo.

## <a name="adu-core-interface"></a>ADU Core Interface

A interface 'ADUCoreInterface' é utilizada para enviar ações de atualização e metadados para dispositivos e receber o estado de atualização dos dispositivos. A interface 'ADU Core' é dividida em duas propriedades do Objeto.

O nome do componente esperado no seu modelo é **"azureDeviceUpdateAgent"** ao implementar esta interface. [Saiba mais sobre componentes Azure IoT PnP](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metudata do Agente

O Agente Metadados contém campos que o agente de atualização do dispositivo ou do dispositivo utiliza para enviar informações e estado aos serviços de Atualização do Dispositivo.

|Name|Esquema|Direção|Descrição|Exemplo|
|----|------|---------|-----------|-----------|
|resultadosDesco|número inteiro|dispositivo para nuvem|Um código que contém informações sobre o resultado da última ação de atualização. Pode ser povoado para sucesso ou falha e deve seguir [a especificação do código de estado http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extensoResultCode|número inteiro|dispositivo para nuvem|Um código que contém informações adicionais sobre o resultado. Pode ser povoado para o sucesso ou o fracasso.|0x80004005|
|state|número inteiro|dispositivo para nuvem|É um número inteiro que indica o estado atual do agente de atualização do dispositivo. Veja abaixo os detalhes |Períodos|
|instaladoUpdateId|string|dispositivo para nuvem|Um ID da atualização que está atualmente instalada (através da Atualização do Dispositivo). Este valor será nulo para um dispositivo que nunca tenha feito uma atualização através da Atualização do Dispositivo.|Nulo|
|`deviceProperties`|Mapa|dispositivo para nuvem|O conjunto de propriedades que contêm o fabricante e o modelo.|Veja abaixo os detalhes

#### <a name="state"></a>Estado

É o estado reportado pelo Agente de Atualização do Dispositivo depois de ter recebido uma ação do Serviço de Atualização do Dispositivo. `State` é reportado em resposta a um `Action` (ver `Actions` abaixo) enviado para o Agente de Atualização do Dispositivo a partir do Serviço de Atualização do Dispositivo. Consulte o [fluxo de trabalho geral](understand-device-update.md#device-update-agent) para pedidos que fluem entre o Serviço de Atualização do Dispositivo e o Agente de Atualização do Dispositivo.

|Name|Valor|Descrição|
|---------|-----|-----------|
|Períodos|0|O dispositivo está pronto para receber uma ação do Serviço de Atualização de Dispositivos. Depois de uma atualização bem sucedida, o estado é devolvido ao `Idle` estado.|
|DownloadSDuzed|2|Um download bem sucedido.|
|InstalaçãoSucida|4|Uma instalação bem sucedida.|
|Com falhas|255|Uma falha ocorreu durante a atualização.|

#### <a name="device-properties"></a>Propriedades do Dispositivo

É o conjunto de propriedades que contêm o fabricante e o modelo.

|Name|Esquema|Direção|Description|
|----|------|---------|-----------|
|manufacturer|string|dispositivo para nuvem|O fabricante do dispositivo, relatado através `deviceProperties` de . Esta propriedade é lida a partir de um de dois lugares - a interface 'AzureDeviceUpdateCore' tentará primeiro ler o valor 'aduc_manufacturer' a partir do ficheiro [de ficheiros Configuração.](device-update-configuration-file.md)  Se o valor não for preenchido no ficheiro de configuração, por defeito reportará a definição de tempo de compilação para ADUC_DEVICEPROPERTIES_MANUFACTURER. Esta propriedade só será reportada na hora do arranque.|
|model|string|dispositivo para nuvem|O modelo do dispositivo, relatado através `deviceProperties` de . Esta propriedade é lida a partir de um de dois lugares - a interface AzureDeviceUpdateCore tentará primeiro ler o valor 'aduc_model' a partir do ficheiro [de ficheiros Configuração.](device-update-configuration-file.md)  Se o valor não for preenchido no ficheiro de configuração, por defeito reportará a definição de tempo de compilação para ADUC_DEVICEPROPERTIES_MODEL. Esta propriedade só será reportada na hora do arranque.|
|aduVer|string|dispositivo para nuvem|Versão do agente de atualização do dispositivo em execução no dispositivo. Este valor é lido a partir da construção apenas se durante o tempo de compilação ENABLE_ADU_TELEMETRY_REPORTING for definido para 1 (verdadeiro). Os clientes podem optar por excluir o relatório de versão, definindo o valor para 0 (falso). [Como personalizar as propriedades do agente de atualização do dispositivo](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|string|dispositivo para nuvem|Versão do agente de otimização de entrega em execução no dispositivo. O valor é lido a partir da construção apenas se durante o tempo de compilação ENABLE_ADU_TELEMETRY_REPORTING for definido para 1 (verdadeiro). Os clientes podem optar por excluir a versão reportando definindo o valor para 0 (falso). [Como personalizar as propriedades do agente de otimização de entrega.](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Metadados de serviço

Os metadados de serviço contêm campos que os serviços de Atualização do Dispositivo utilizam para comunicar ações e dados ao agente de Atualização do Dispositivo.

|Name|Esquema|Direção|Description|
|----|------|---------|-----------|
|ação|número inteiro|nuvem para dispositivo|É um inteiro que corresponde a uma ação que o agente deve executar. Valores listados abaixo.|
|updateManifest|string|nuvem para dispositivo|Usado para descrever o conteúdo de uma atualização. Gerado a partir do [Manifesto de Importação](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|Objeto JSON|nuvem para dispositivo|Uma assinatura web JSON (JWS) com teclas web JSON utilizadas para verificação de origem.|
|fileUrls|Mapa|nuvem para dispositivo|Mapa `FileHash` `DownloadUri` de. Diz ao agente quais os ficheiros a descarregar e o haxixe a utilizar para verificar se os ficheiros foram descarregados corretamente.|

#### <a name="action"></a>Ação

`Actions` abaixo representa as ações tomadas pelo Agente de Atualização do Dispositivo, conforme instruído pelo Serviço de Atualização do Dispositivo. O Agente de Atualização do Dispositivo reportará um `State` processamento (ver `State` secção acima) do processamento `Action` recebido. Consulte o [fluxo de trabalho geral](understand-device-update.md#device-update-agent) para pedidos que fluem entre o Serviço de Atualização do Dispositivo e o Agente de Atualização do Dispositivo.

|Name|Valor|Descrição|
|---------|-----|-----------|
|Download|0|Descarregue conteúdo ou atualização publicado e qualquer outro conteúdo necessário|
|Instalar|1|Instale o conteúdo ou a atualização. Normalmente, isto significa chamar o instalador para o conteúdo ou atualização.|
|Aplicar|2|Finalize a atualização. Sinaliza o sistema para reiniciar, se necessário.|
|Cancelar|255|Pare de processar a ação atual e volte para `Idle` . Também será usado para dizer ao agente do `Failed` estado para voltar a `Idle` .|

## <a name="device-information-interface"></a>Interface de informação do dispositivo

A Interface de Informação do Dispositivo é um conceito utilizado dentro da [arquitetura IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Contém propriedades em nuvem que fornecem informações sobre o hardware e o sistema operativo do dispositivo. A Atualização do Dispositivo para IoT Hub utiliza as propriedades deviceInformation.fabricante e DeviceInformation.model para telemetria e diagnósticos. Para saber mais sobre a interface de Informação de Dispositivos, consulte este [exemplo.](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)

O nome do componente esperado no seu modelo é **deviceInformation** ao implementar esta interface. [Saiba mais sobre componentes Azure IoT PnP](../iot-pnp/concepts-components.md)

|Nome|Tipo|Esquema|Direção|Descrição|Exemplo|
|----|----|------|---------|-----------|-----------|
|manufacturer|Propriedade|string|dispositivo para nuvem|Nome da empresa do fabricante do dispositivo. Isto pode ser o mesmo que o nome do fabricante de equipamento original (OEM).|Contoso|
|model|Propriedade|string|dispositivo para nuvem|Nome do modelo do dispositivo ou ID.|Dispositivo de borda IoT|
|swVersion|Propriedade|string|dispositivo para nuvem|Versão do software no seu dispositivo. swVersion pode ser a versão do seu firmware.|4.15.0-122|
|nome do asa|Propriedade|string|dispositivo para nuvem|Nome do sistema operativo no dispositivo.|Ubuntu Server 18.04|
|processadorArtecture|Propriedade|string|dispositivo para nuvem|Arquitetura do processador no dispositivo.|ARM64|
|processadorFabrifurer|Propriedade|string|dispositivo para nuvem|Nome do fabricante do processador no aparelho.|Microsoft|
|totalStorage|Propriedade|string|dispositivo para nuvem|Armazenamento total disponível no dispositivo em kilobytes.|2048|
|totalMemor|Propriedade|string|dispositivo para nuvem|Total de memória disponível no dispositivo em kilobytes.|256|

## <a name="model-id"></a>ID do Modelo 

O Model ID é como os dispositivos inteligentes anunciam as suas capacidades para aplicações IoT Azure com IoT Plug e Play.To aprender mais sobre como construir dispositivos inteligentes para anunciar as suas capacidades para aplicações Azure IoT visitar [ioT plug e play guia de desenvolvimento de dispositivos.](../iot-pnp/concepts-developer-guide-device.md)

A atualização do dispositivo para o IoT Hub requer que o dispositivo inteligente IoT Plug and Play anuncie um ID do modelo com o valor de **"dtmi:AzureDeviceUpdate;1"** como parte da ligação do dispositivo. [Saiba como anunciar um ID modelo.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)