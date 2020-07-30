---
title: Novidades IoT Plug e Play Preview Refresh / Microsoft Docs
description: Saiba quais as novidades com o lançamento IoT Plug e Play Preview Refresh.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: f212143d90b6a6bf9c8f1f597c276b9ab4100617
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406265"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT Plug e Play atualização de pré-visualização

Este artigo descreve as principais alterações no SDKS, bibliotecas, ferramentas e serviços no lançamento de pré-visualização IoT Plug and Play em julho de 2020. O anterior lançamento de pré-visualização do IoT Plug and Play foi em agosto de 2019.

## <a name="digital-twins-definition-language-dtdl"></a>Linguagem de definição de gémeos digitais (DTDL)

Esta versão adiciona suporte para [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) e deprecia [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

A lista a seguir mostra as principais diferenças entre DTDL v1 e DTDL v2. Em DTDL v2:

- Os IDs do modelo têm o prefixo `dtmi` em vez de `urn` . Os identificadores digitais de modelos duplos (DTMI) substituem os `urn` IDs digitais de gémeos digitais pré-fixados utilizados no DTDL v1. A versão é agora precedida com um `;` . Por exemplo, anteriormente onde `urn:CompanyName:Model:1` utilizou, agora utiliza- se `dtmi:CompanyName:Model;1` .
- Desemalte o `@context` para em vez de `dtmi:dtdl:context;2` `http://azureiot.com/v1/contexts/IoTModel.json` .
- Utilize o tipo **interface** em vez dos tipos **CapabilityModel** para modelar um dispositivo.
- **Os componentes** substituem as instâncias **da Interface.** Pode definir **Relacionamentos** e **Componentes** como parte do conteúdo de uma **Interface.**
- Uma **Interface** pode herdar de outra **Interface.**
- Pode estender o DTDL utilizando _tipos semânticos extensíveis._ Este sistema de tipo extensível oferece uma maior flexibilidade do que os tipos semânticos codificados, tais como temperatura e humidade em DTDL v1.
- A propriedade **displayUnit** foi removida.
- Não pode usar sublinhados de liderança ou de fuga num nome. Os principais sublinhados num nome são reservados para uso do sistema.

Para trabalhar com dTDL v1, é necessário utilizar a versão anterior do SDK e do Azure IoT Explorer 0.10.x. Para trabalhar com dTDL v2, precisa da versão mais recente do SDK e do Azure IoT Explorer 0.11.x.

## <a name="no-component-and-multiple-component-implementations"></a>Sem implementações de componentes e múltiplos componentes

Dispositivos simples que utilizam algumas telemetrias, comandos ou propriedades podem ser descritos numa única interface sem utilizar componentes. Qualquer dispositivo existente pode tornar-se um IoT Plug and Play anunciando o **Model ID** sem alterações na implementação do dispositivo existente.

Dispositivos mais complexos podem agrupar telemetria, comandos e propriedades em diferentes interfaces para gerir a complexidade e permitir a reutilização através de dispositivos. Estes dispositivos devem ser atualizados para seguir um conjunto de regras simples definidas nas convenções de [mensagem IoT Plug and Play Preview](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registo e descoberta

Nesta versão, os dispositivos anunciam o seu **Model ID** com ioT Hub em todas as ligações. O IoT Hub caches o **Model ID** permitindo soluções de backend para recuperar o **Model ID** usando a propriedade twin do `modelId` dispositivo. O **Model ID** também pode ser recuperado `$metadata.$model` no twin digital.

## <a name="microsoft-defined-interfaces"></a>Interfaces definidas pela Microsoft

As seguintes interfaces definidas pela Microsoft são depreciadas e não são publicadas no novo repositório de modelos:

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

A interface a seguir é publicada no novo repositório do modelo: `dtmi:azure:DeviceManagement:DeviceInformation;1` disponível no URL [ https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ;1?api-versão=2020-05-01-pré-visualização](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

A estrutura de eventos da [fonte](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) de **eventos DigitalTwinChangeEvents** mudou para utilizar o formato **JSON-Patch.** Esta mudança é uma mudança de rutura sem suporte de retrocompatibilidade.

## <a name="message-routing"></a>Encaminhamento de mensagens

As mensagens de telemetria têm as seguintes alterações na recolha [de SystemProperties.](../iot-hub/iot-hub-devguide-messages-construct.md)

Agora inclui uma propriedade **dt-dataschema** que é o **Model ID** registado pelo dispositivo.

A propriedade **dt-subject** representa o componente que envia mensagem de telemetria.

A propriedade **iothub-interface-name** é depreciada.

## <a name="device-and-service-sdks"></a>SDKs de dispositivos e de serviços

Não há retrocompatibilidade com versões anteriores dos SDKs. Terá de alterar o seu código se passar para a versão mais recente de um SDK.

Com a abordagem baseada na convenção, não há necessidade de SDKs de clientes separados. A partir desta versão de pré-visualização, as bibliotecas **DigitalTwinClient** existentes são depreciadas em todas as línguas. Em vez disso, os SDKs do cliente do dispositivo IoT Hub foram atualizados para incluir uma opção para anunciar o **Model ID**.

Os dispositivos que não utilizam componentes requerem alterações mínimas de código - apenas anunciando o **ID**do modelo . Dispositivos mais complexos que utilizam vários componentes podem necessitar de algumas funções reutilizáveis para implementar as [convenções.](concepts-convention.md) As amostras do dispositivo incluem um conjunto de funções que poderá reutilizar na implementação do seu dispositivo.

### <a name="service-sdks"></a>SDKs de Serviço

O serviço SDK está disponível em [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) e [Python.](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md)

## <a name="vs-code-extension"></a>Extensão do Código VS

A extensão [de workbench de dispositivo Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) fornece suporte de autoria para DTDL v1, integração com a versão anterior do repositório de modelos, e geração de código.

Se necessitar de suporte de autoria DTDL v2 no Código VS, instale a nova [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) no Código VS. A extensão não proporciona integração com o repositório de modelo ou geração de código. Gerir modelos no repositório é agora feito usando uma [UI web](https://aka.ms/iotmodelrepo).

## <a name="digital-twin-service-side-rest-apis"></a>APIs de descanso do lado do serviço digital twin

As [APIs](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) e as cargas de trabalho do lado do serviço digital twin ficaram alteradas. As APIs suportadas são:

- Recuperar um gémeo digital.
- Chame um comando.
- Atualize um gémeo digital utilizando a carga útil **JSON-Patch.**

As APIs de REST existentes continuam a ser apoiadas nesta versão.

## <a name="model-repository"></a>Repositório de modelos

Há agora um único repositório de modelos que contém tanto modelos publicados como modelos privados de empresas protegidas pela RBAC. Todos os modelos têm um identificador único e são imutáveis uma vez criados.

Os repositórios de modelos da empresa existentes do lançamento anterior não são suportados nesta versão. Pode continuar a utilizar o website [Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com/products) para gerir os antigos modelos DTDL v1. No entanto, já não é possível utilizar este website para registar, testar e certificar dispositivos.

A extensão Azure IoT para o CLI Azure não suporta o novo repositório de modelos. Os `az iot pnp` comandos funcionam apenas os repositórios de modelos a partir do lançamento anterior.

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central está atualmente a ser atualizado para suportar o lançamento de pré-visualização IoT Plug and Play.
