---
title: Conceitos de serviço no Serviço de Provisionamento de Dispositivos Hub Azure IoT [ Microsoft Docs
description: Descreve conceitos de provisionamento de serviços específicos de dispositivos com o Serviço de Provisionamento de Dispositivos (DPS) e IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285217"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos de serviço de provisionamento de dispositivos IoT Hub

O Serviço de Provisionamento de Dispositivos IoT Hub é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode [fornecer](concepts-auto-provisioning.md) automaticamente milhões de dispositivos de forma segura e escalável.

O fornecimento de dispositivos é um processo em duas partes. A primeira parte é estabelecer a ligação inicial entre o dispositivo e a solução IoT, *registando* o dispositivo. A segunda parte é aplicar a *configuração* adequada ao dispositivo com base nos requisitos específicos da solução. Uma vez concluídos os dois passos, o dispositivo foi totalmente *provisionado*. O Serviço de Aprovisionamento de Dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento do dispositivo.

Este artigo dá uma visão geral dos conceitos de provisionamento mais aplicáveis à gestão do *serviço.* Este artigo é mais relevante para as pessoas envolvidas na etapa de [configuração](about-iot-dps.md#cloud-setup-step) da nuvem de obter um dispositivo pronto para a implementação.

## <a name="service-operations-endpoint"></a>Ponto final de operações de serviço

O ponto final das operações de serviço é o ponto final para gerir as definições de serviço e manter a lista de inscrições. Este ponto final é utilizado apenas pelo administrador de serviço; não é utilizado por dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto final de fornecimento de dispositivos

O ponto final de fornecimento do dispositivo é o ponto final único que todos os dispositivos utilizam para o fornecimento automático. O URL é o mesmo para todas as instâncias de serviço de provisionamento, para eliminar a necessidade de repiscar os dispositivos com novas informações de ligação em cenários da cadeia de fornecimento. O âmbito de identificação garante o isolamento dos inquilinos.

## <a name="linked-iot-hubs"></a>Centros ioT ligados

O Serviço de Provisionamento de Dispositivos só pode fornecer dispositivos a centros IoT que tenham sido ligados ao mesmo. Ligar um hub IoT a uma instância do serviço de provisionamento de dispositivos dá ao serviço permissões de leitura/escrita para o registo do dispositivo do hub IoT; com o link, um serviço de fornecimento de dispositivos pode registar um ID do dispositivo e definir a configuração inicial no dispositivo twin. Os centros ioT ligados podem estar em qualquer região de Azure. Pode ligar os hubs a outras subscrições ao seu serviço de provisionamento.

## <a name="allocation-policy"></a>Política de atribuição

A definição de nível de serviço que determina como o Serviço de Fornecimento de Dispositivos atribui dispositivos a um hub IoT. Existem três políticas de alocação suportadas:

* **Distribuição uniformemente ponderada**: os centros IoT ligados são igualmente suscetíveis de ter dispositivos aprovisionados. A definição padrão. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição.

* **Latência mais baixa**: os dispositivos são aprovisionados para um hub IoT com a latência mais baixa do dispositivo. Se vários centros ioT ligados forneceriam a mesma latência mais baixa, o serviço de fornecimento hashes dispositivos através desses hubs

* **Configuração estática através da lista**de inscrições : a especificação do centro ioT desejado na lista de inscrições tem prioridade sobre a política de atribuição ao nível do serviço.

## <a name="enrollment"></a>Inscrição

Uma inscrição é o registo de dispositivos ou grupos de dispositivos que podem registar-se através do fornecimento automático. O registo de inscrições contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o mecanismo de [atestado](concepts-security.md#attestation-mechanism) utilizado pelo dispositivo
- a configuração inicial opcional desejada
- centro ioT desejado
- o ID do dispositivo desejado

Existem dois tipos de inscrições suportadas pelo Serviço de Provisionamento de Dispositivos:

### <a name="enrollment-group"></a>Grupo de inscrições

Um grupo de inscrições é um grupo de dispositivos que partilham um mecanismo específico de atestado. Os grupos de inscrição apoiam tanto o X.509 como o simétrico. Todos os dispositivos do grupo de matrículaX.509 apresentam certificados X.509 assinados pela mesma root ou autoridade de certificados intermédios (CA). Cada dispositivo do grupo de matrículas simétrica apresenta tokens SAS derivados da chave simétrica do grupo. O nome do grupo de matrícula e o nome do certificado devem ser alfanuméricos, minúsculos e podem conter hífenes.

> [!TIP]
> Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilhem uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino.

### <a name="individual-enrollment"></a>Inscrição individual

Uma inscrição individual é uma entrada para um único dispositivo que pode registar-se. As matrículas individuais podem utilizar certificados de folhax.509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. O ID de registo numa matrícula individual é alfanumérico, minúsculo e pode conter hífenes. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> Recomendamos a utilização de inscrições individuais para dispositivos que requeiram configurações iniciais únicas, ou para dispositivos que só podem autenticar utilizando tokens SAS através de atestado TPM.

## <a name="registration"></a>Registo

Um registo é o registo de um dispositivo que regista/disponibiliza com sucesso um Hub IoT através do Serviço de Provisionamento de Dispositivos. Os registos de registo são criados automaticamente; podem ser eliminados, mas não podem ser atualizados.

## <a name="operations"></a>Operações

As operações são a unidade de faturação do Serviço de Provisionamento de Dispositivos. Uma operação é a conclusão bem sucedida de uma instrução ao serviço. As operações incluem registos e reinscrições de dispositivos; as operações também incluem alterações do lado do serviço, tais como a adição de entradas na lista de inscrições e a atualização das inscrições na lista de inscrições.
