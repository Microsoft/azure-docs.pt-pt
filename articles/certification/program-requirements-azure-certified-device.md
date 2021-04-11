---
title: Requisitos de dispositivo certificado Azure
description: Requisitos do programa Azure Certified Device
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 948fe25da8468e887693fe8c9f75f675dfbea858
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969404"
---
# <a name="azure-certified-device-requirements"></a>Requisitos de dispositivo certificado Azure 
(anteriormente conhecido como IoT Hub)

Este documento descreve as capacidades específicas do dispositivo que estarão representadas no catálogo de Dispositivos Certificados Azure. Uma capacidade é atributo de dispositivo singular que pode ser implementação de software ou combinação de implementações de software e hardware. 

## <a name="program-purpose"></a>Finalidade do Programa

A Microsoft está a simplificar a certificação IoT e Azure Certified Device é um programa de certificação de base para garantir que quaisquer tipos de dispositivos são adquiram-se de forma segura ao Azure IoT Hub.

A certificação de dispositivo certificado Azure é:

1. Telemetria de suporte do dispositivo que funciona com ioT Hub
2.  Serviço de Provisionamento de Dispositivos IoT Hub (DPS) de suporte a dispositivos de segurança a provisionado ao Azure IoT Hub
3.  O dispositivo suporta uma entrada fácil da transferência de iD DPS alvo sem exigir que o utilizador recompile o código incorporado.
4.  Opcionalmente valida outros elementos, tais como nuvem para mensagens de dispositivo, métodos diretos e dispositivo twin 

## <a name="requirements"></a>Requisitos

**[Obrigatório] Dispositivo em nuvem: O objetivo do teste é garantir que os dispositivos que enviam telemetria funcionam com o IoT Hub**

| **Nome**                | AzureCertified.D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve enviar quaisquer esquemas de telemetria para o IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.come) para executar os testes. Dispositivo para nuvem (obrigatório): **1.** Valida que o dispositivo pode enviar mensagem para o IoT Hub **2** gerido pela AICS. O utilizador deve especificar o número e a frequência das mensagens. **3.** AICS valida a telemetria é recebida pela instância Hub |
| **Recursos**           | [Etapas de certificação](./overview.md) (tem todos os recursos adicionais) |

**[Obrigatório] DPS: O objetivo do teste é verificar os dispositivos que implementa e suporta o Serviço de Provisionamento de Dispositivos IoT Hub com um dos três métodos de atestação**

| **Nome**                | AzureCertified.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Novo                                                          |
| **Aplica-se a**          | Qualquer dispositivo                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo suporta uma entrada fácil da propriedade do âmbito de ID DPS alvo sem precisar de recompiler o código incorporado. A Microsoft fornece o [fluxo de trabalho](https://certify.azure.com) do portal para executar os testes para validar que o dispositivo suporta DPS **1.** O utilizador deve selecionar um dos métodos de atestação (tecla X.509, TPM e SAS) **2.** Dependendo do método de atestado, o utilizador precisa de tomar as medidas correspondentes, tais como **a)** Upload X.509 cert para OICS gerido dPS âmbito **b)** Implementar a chave SAS ou chave de endosso no dispositivo |
| **Recursos**           | [Visão geral do serviço de fornecimento de dispositivos](../iot-dps/about-iot-dps.md) |

**[Se implementado] Nuvem para dispositivo: O objetivo do teste é garantir que as mensagens podem ser enviadas da nuvem para os dispositivos**                                                              

| **Nome**                | AzureCertified.C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                            |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve ser capaz de cloud to Device mensagens do IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar estes testes. Nuvem para dispositivo (se implementado): **1.** Valida que o dispositivo pode receber mensagem do IoT Hub **2.** AICS envia mensagem aleatória e valida através da mensagem ACK do dispositivo  |
| **Recursos**           | **a) Etapas** [de certificação](./overview.md) (tem todos os recursos adicionais) **b)** [Enviar nuvem para mensagens de dispositivo a partir de um Hub IoT](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Se implementado] Métodos diretos: O objetivo do teste é garantir que os dispositivos funcionam com o IoT Hub e suporta métodos diretos**

| **Nome**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                            |
| **Aplica-se a**          | Dispositivo de folha/dispositivo edge                                                   |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | O dispositivo deve ser capaz de receber e responder aos pedidos de comandos do IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar os testes. Métodos diretos (se implementados) **1.** O utilizador tem de especificar a carga útil do método direto. **2.** A AICS valida o pedido de carga útil especificado é enviado a partir do Hub e a mensagem ACK recebida pelo dispositivo |
| **Recursos**           | **a) Etapas** [de certificação](./overview.md) (tem todos os recursos adicionais) **b)** [Compreender métodos diretos do IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Se implementado] Propriedade dupla do dispositivo: O objetivo do teste é garantir que os dispositivos que enviam telemetria funcionam com o IoT Hub e suportam algumas das capacidades do IoT Hub, como métodos diretos, e propriedade twin do dispositivo**

| **Nome**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino**                   | Já disponível                                            |
| **Aplica-se a**                            | Dispositivo de folha/dispositivo edge                                                   |
| **SO**                                    | Desconhecida                                                     |
| **Tipo de Validação**                       | Automatizada                                                       |
| **Validação**                            | O dispositivo deve enviar quaisquer esquemas de telemetria para o IoT Hub. A Microsoft fornece o [fluxo de trabalho do portal](https://certify.azure.com) para executar os testes. Propriedade twin do dispositivo (se implementada) **1.** A AICS valida a propriedade de leitura/gravação no dispositivo twin JSON **2.** O utilizador tem de especificar a carga útil JSON a ser alterada **3.** A AICS valida as propriedades desejadas especificadas enviadas a partir do IoT Hub e mensagem ACK recebidas pelo dispositivo |
| **Recursos**                             | **a) Etapas** [de certificação](./overview.md) (tem todos os recursos adicionais) **b)** [Usar gémeos de dispositivo com IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |