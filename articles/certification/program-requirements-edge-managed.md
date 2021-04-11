---
title: Requisitos de certificação geridos pela borda
description: Requisitos do programa de certificação edge managed
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969403"
---
# <a name="azure-certification-edge-managed"></a>Azure Certificação Edge Gerido 

Este documento descreve as capacidades específicas do dispositivo que estarão representadas no catálogo de Dispositivos Certificados Azure. Uma capacidade é atributo de dispositivo singular que pode descrever o dispositivo. 

## <a name="program-purpose"></a>Finalidade do Programa

Certificação Edge Managed, uma certificação incremental para além da certificação de dispositivo certificado Azure. Edge Managed foca-se nos padrões de gestão de dispositivos para dispositivos conectados Azure e valida a compatibilidade de tempo de execução IoT Edge para implementação e gestão de módulos. (Anteriormente, este programa foi identificado como o programa de certificação IoT Edge.) 

A certificação Edge Managed valida a compatibilidade do tempo de execução IoT Edge para a implementação e gestão do módulo. Este programa proporciona confiança na gestão de dispositivos IoT conectados Azure.

## <a name="requirements"></a>Requisitos

A certificação Edge Managed requer que todos os requisitos do programa de [base do dispositivo certificado Azure](.\program-requirements-azure-certified-device.md).

**DPS: O objetivo do teste é verificar os dispositivos que implementa e suporta o Serviço de Provisionamento de Dispositivos IoT Hub com um dos três métodos de atestação**

| **Nome**                | AzureReady.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Ignite (na pré-visualização)                                                |
| **Aplica-se a**          | Qualquer dispositivo                                      |
| **SO**                  | Desconhecida                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | A AICS valida o DPS de suporte ao código do dispositivo. **1.** O utilizador tem de selecionar um dos métodos de atestação (tecla X.509, TPM e SAS). **2.** Dependendo do método de atestado, o utilizador precisa de tomar as medidas correspondentes, tais como **a)** Upload X.509 cert para o âmbito DPS gerido pela AICS **b)** Implementar a chave SAS ou a chave de endosso no dispositivo. **3.** Em seguida, o utilizador carregará no botão 'Connect' para ligar ao Hub IoT gerido pela AICS via DPS                                                    |
| **Recursos**           |                                                      |
| **Azure Recomendado:**     | N/D                                                    |

## <a name="iot-edge"></a>IoT Edge

**O tempo de execução da borda existe: O objetivo do teste é certificar-se de que o dispositivo contém tempo de execução IoT Edge ($edgehub e $edgeagent) que estão a funcionar corretamente.**

| **Nome**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Já disponível                                                          |
| **Aplica-se a**          | Dispositivo do IoT Edge                                                   |
| **SO**                  | [Tier1 e Tier2 OS](../iot-edge/support.md)                                                     |
| **Tipo de Validação**     | Automatizada                                                    |
| **Validação**          | A AICS valida a capacidade de implantação do IoT Edge RT instalado. **1.** O utilizador precisa de especificar o SO específico (o SO não está na lista de Tier1/2 não é aceite) **2.** A AICS gera o seu config.yaml e implementa o módulo de borda do [sensor temporário simulado](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **canónico 3.** A AICS valida que o subsistema de contentores compatíveis com estivadores (Moby) está instalado no dispositivo **4.** O resultado do teste é determinado com base na implantação bem sucedida do módulo de borda do sensor temporário simulado e na funcionalidade do subsistema de contentor compatível com docker                                                    |
| **Recursos**           | **a)** [Blog AICS](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b) Etapas** [de certificação](./overview.md) (tem todos os recursos adicionais), **c)** [Requisitos](./program-requirements-azure-certified-device.md) |
| **Azure Recomendado:**     | N/D                                                    |

### <a name="capability-template"></a>Modelo de capacidade:

**Configuração fácil IoT Edge: O objetivo do teste é garantir que o dispositivo IoT Edge é fácil de configurar e valida o tempo de execução IoT Edge é pré-instalado durante a validação do dispositivo físico**

| **Nome**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Disponibilidade de destino** | Disponível agora (atualmente em espera devido ao COVID-19)                                            |
| **Aplica-se a**          | Dispositivo do IoT Edge                                                   |
| **SO**                  | [Tier1 e Tier2 OS](../iot-edge/support.md)                                                     |
| **Tipo de Validação**     | Manual / Laboratório Verificado                                                    |
| **Validação**          | A OEM deve enviar o dispositivo físico para a administração IoT (HCL). A HCL efetua a validação manual no dispositivo físico para verificar: **1.** O EdgeRT está a utilizar o subsistema Moby (versão de redistribuição permitida). Não o estivador **2.** Escolha o módulo de borda mais recente para validar a capacidade de implantação da borda.                                                     |
| **Recursos**           | **a)** [Blog AICS](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b) Etapas** [de certificação,](./overview.md) **c)** [Requisitos](./program-requirements-azure-certified-device.md) |
| **Azure Recomendado:**     | N/D                                                    |