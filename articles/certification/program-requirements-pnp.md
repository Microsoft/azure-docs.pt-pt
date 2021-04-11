---
title: Requisitos de certificação IoT Plug e Play
description: Requisitos do programa de certificação IoT Plug and Play
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: bbe9a3f18463285521dde0ee64b369cffcd71d75
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969388"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Requisitos de certificação IoT Plug e Play

Este documento descreve as capacidades específicas do dispositivo que estarão representadas no catálogo do Dispositivo IoT Azure. Uma capacidade é atributo de dispositivo singular que pode ser implementação de software ou combinação de implementações de software e hardware.

## <a name="program-purpose"></a>Finalidade do Programa

O IoT Plug e o Play Preview permitem aos construtores de soluções integrar dispositivos inteligentes com as suas soluções sem qualquer configuração manual. No cerne do IoT Plug and Play, está um modelo de dispositivo que um dispositivo utiliza para anunciar as suas capacidades a uma aplicação IoT Plug e Play. Este modelo é estruturado como um conjunto de elementos: Telemetria, Propriedades e Comandos.

A promessa da certificação IoT Plug and Play é:

1.  Os modelos e interfaces de dispositivos definidos estão em conformidade com a  [Linguagem de Definição Digital Twin](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Fornecimento seguro e transferência fácil da propriedade do âmbito de ID nos serviços de fornecimento de dispositivos
3.  Fácil integração com soluções baseadas em Azure IoT utilizando as [APIs Digital Twin](../iot-pnp/concepts-digital-twin.md)  : Azure IoT Hub e Azure IoT Central
4.  Verdade do produto validado em dispositivos certificados

## <a name="requirements"></a>Requisitos

**[Obrigatório] Dispositivo em nuvem: O objetivo do teste é garantir que os dispositivos que enviam telemetria funcionam com o IoT Hub**

| **Nome**                | IoTPnP.D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve enviar quaisquer esquemas de telemetria para o IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar os testes. Dispositivo para nuvem (obrigatório): **1.** Valida que o dispositivo pode enviar mensagem para o IoT Hub **2** gerido pela AICS. O utilizador deve especificar o número e a frequência das mensagens. **3.** AICS valida a telemetria é recebida pela instância Hub |
| **Recursos**           | [Etapas de certificação](./overview.md) (tem todos os recursos adicionais) |

**[Obrigatório] DPS: O objetivo do teste é verificar os dispositivos que implementa e suporta o Serviço de Provisionamento de Dispositivos IoT Hub com um dos três métodos de atestação**

| **Nome**                | IoTPnP.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Qualquer dispositivo                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve implementar uma transferência fácil da propriedade do DPS ID Scope sem precisar de recompiler o código incorporado. A Microsoft fornece o [fluxo de trabalho](https://certify.azure.com) do portal para executar os testes para validar que o dispositivo suporta DPS **1.** O utilizador deve selecionar um dos métodos de atestação (tecla X.509, TPM e SAS) **2.** Dependendo do método de atestado, o utilizador precisa de tomar as medidas correspondentes, tais como **a)** Upload X.509 cert para OICS gerido dPS âmbito **b)** Implementar a chave SAS ou chave de endosso no dispositivo |
| **Recursos**           | **a)** [Visão geral do serviço de fornecimento de dispositivos](../iot-dps/about-iot-dps.md), **b)** [Ficheiro de config de amostra para transferência de ID Scope DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/digitaltwin_client/samples/digitaltwin_sample_ll_device/sample_config) |

**[Obrigatório] DTDL v2: O objetivo do teste para garantir que os modelos e interfaces definidos do dispositivo estão em conformidade com a Linguagem de Definição de Gémeos Digitais v2.**                                                              

| **Nome**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Qualquer dispositivo                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O [fluxo de trabalho do portal](https://certify.azure.com) valida: **1.** O anúncio do Model ID e a garantia de que o dispositivo está ligado utilizando o MQTT ou o MQTT sobre o protocolo WebSockets **2.** Os modelos estão em conformidade com o DTDL v2 **3.** A telemetria, propriedades e comandos são devidamente implementados e interagem entre o IoT Hub Digital Twin e o Device Twin no dispositivo |
| **Recursos**           | [Atualizações de pré-visualização pública](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**[Obrigatório] Os modelos do dispositivo são publicados no repositório de modelos públicos**

| **Nome**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Qualquer dispositivo                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | Todos os modelos de dispositivos devem ser publicados em repositório público. Os modelos do dispositivo são resolvidos através de modelos disponíveis no repositório público **1.** O utilizador deve publicar manualmente os modelos no repositório público antes de se submeter à certificação. **2.** Note que uma vez publicados os modelos, é imutável. Recomendamos vivamente a publicação apenas quando os modelos e o código do dispositivo incorporado estiverem finalizados.*1 *1 O utilizador deve contactar o suporte da Microsoft para revogar os modelos uma vez publicados no repositório do modelo **3.** [O fluxo de trabalho](https://certify.azure.com) do portal verifica a existência dos modelos no repositório público quando o dispositivo está ligado ao serviço de certificação |
| **Recursos**           | [Repositório de modelos](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**[Obrigatório] Validação do dispositivo físico utilizando o GSG**

| **Nome**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino**                   | Já disponível                                                |
| **Aplica-se a**                            | Qualquer dispositivo                                                   |
| **SO**                                    | Desconhecida                                                     |
| **Tipo de Validação**                       | Manual                                                       |
| **Validação**                            | Os parceiros devem envolver-se com o contacto da Microsoft [iotcert@microsoft.com](mailto:iotcert@microsoft.com) para tomar medidas para realizar validações adicionais no dispositivo físico. Devido à situação do COVID-19, estamos a explorar várias formas de realizar a validação de dispositivos físicos sem enviar o dispositivo para a Microsoft. |
| **Recursos**                             | Os detalhes estão disponíveis mais tarde                                 |
| **Azure Recomendado**       | N/D    |

**[Se implementado] Interface de informação do dispositivo: O objetivo do teste é validar a interface de informação do dispositivo é implementada corretamente no código do dispositivo**

| **Nome**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Qualquer dispositivo                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | [O fluxo de trabalho do portal](https://certify.azure.com) valida o código do dispositivo implementa a interface de [informação do dispositivo](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-previewureiot:DeviceManagement:DeviceInformation:1) **1.** Verifica se os valores são emitidos pelo código do dispositivo para IoT Hub **2.** Verifica se a interface é implementada no DCM (esta implementação mudará em DTDL v2) **3.** As propriedades de verificação não são escritas (ler apenas) **4.** Verifica se o tipo de esquema é string e/ou longo e não nulo |
| **Recursos**           | [Interface definida pela Microsoft](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Azure Recomendado**  | N/D                                                          |

**[Se implementado] Nuvem para dispositivo: O objetivo do teste é garantir que as mensagens podem ser enviadas da nuvem para os dispositivos**

| **Nome**                | IoTPnP.C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve ser capaz de cloud to Device mensagens do IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar estes testes. Nuvem para dispositivo (se implementado): **1.** Valida que o dispositivo pode receber mensagem do IoT Hub **2.** AICS envia mensagem aleatória e valida através da mensagem ACK do dispositivo |
| **Recursos**           | **1.** [Etapas de certificação](./overview.md) (tem todos os recursos adicionais), **2.** [Envie nuvem para mensagens de dispositivo de um hub IoT](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Se implementado] Métodos diretos: O objetivo do teste é garantir que os dispositivos funcionam com o IoT Hub e suporta métodos diretos**

| **Nome**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve ser capaz de receber e responder aos pedidos de comandos do IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar os testes. Métodos diretos (se implementados): **1.** O utilizador tem de especificar a carga útil do método direto. **2.** A AICS valida o pedido de carga útil especificado é enviado a partir do Hub e a mensagem ACK recebida pelo dispositivo |
| **Recursos**           | **1.** [Etapas de certificação](./overview.md) (tem todos os recursos adicionais), **2.** [Compreender métodos diretos do IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Se implementado] Propriedade dupla do dispositivo: O objetivo do teste é garantir que os dispositivos que enviam telemetria funcionam com o IoT Hub e suportam algumas das capacidades do IoT Hub, como métodos diretos, e propriedade twin do dispositivo**

| **Nome**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve enviar quaisquer esquemas de telemetria para o IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar os testes. Propriedade twin do dispositivo (se implementado): **1.** A AICS valida a propriedade de leitura/gravação no dispositivo twin JSON **2.** O utilizador tem de especificar a carga útil JSON a ser alterada **3.** A AICS valida as propriedades desejadas especificadas enviadas a partir do IoT Hub e mensagem ACK recebidas pelo dispositivo |
| **Recursos**           | **1.** [Etapas de certificação](./overview.md) (tem todos os recursos adicionais), **2.** [Use gémeos de dispositivo com IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |