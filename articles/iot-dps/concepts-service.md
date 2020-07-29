---
title: Conceitos de serviço no Serviço de Provisionamento de Dispositivos Azure IoT Hub / Microsoft Docs
description: Descreve conceitos de prestação de serviços específicos dos dispositivos com o Serviço de Provisionamento de Dispositivos (DPS) e ioT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79285217"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos de serviço de fornecimento de dispositivos ioT hub

O IoT Hub Device Provisioning Service é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode [providenciar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de forma segura e escalável.

O fornecimento de dispositivos é um processo de duas partes. A primeira parte é estabelecer a ligação inicial entre o dispositivo e a solução IoT *registando* o dispositivo. A segunda parte é aplicar a *configuração* adequada ao dispositivo com base nos requisitos específicos da solução. Uma vez concluídos os dois passos, o aparelho foi completamente *provisionado*. O Serviço de Aprovisionamento de Dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento do dispositivo.

Este artigo apresenta uma visão geral dos conceitos de provisionamento mais aplicáveis à gestão do *serviço.* Este artigo é mais relevante para as pessoas envolvidas no passo de [configuração](about-iot-dps.md#cloud-setup-step) da nuvem de preparar um dispositivo para a implementação.

## <a name="service-operations-endpoint"></a>Ponto final das operações de serviço

O ponto final das operações de serviço é o ponto final para gerir as definições de serviço e manter a lista de inscrições. Este ponto final é utilizado apenas pelo administrador de serviço; não é utilizado por dispositivos.

## <a name="device-provisioning-endpoint"></a>Dispositivo que a provisiona ponto final

O dispositivo que fornece o ponto final é o único ponto final que todos os dispositivos utilizam para o fornecimento automático. O URL é o mesmo para todas as instâncias de prestação de serviços, para eliminar a necessidade de reflash dispositivos com novas informações de conexão em cenários de cadeia de fornecimento. O âmbito de identificação garante o isolamento do inquilino.

## <a name="linked-iot-hubs"></a>Hubs IoT ligados

O Serviço de Provisionamento de Dispositivos só pode prestar dispositivos a centros IoT que tenham sido ligados ao mesmo. A ligação de um hub IoT a uma instância do serviço de provisionamento de dispositivos dá ao serviço permissões de leitura/escrita para o registo de dispositivos do hub IoT; com a ligação, um serviço de Provisionamento de Dispositivos pode registar um ID do dispositivo e definir a configuração inicial no twin do dispositivo. Os centros IoT ligados podem estar em qualquer região de Azure. Pode ligar os hubs de outras subscrições ao seu serviço de fornecimento.

## <a name="allocation-policy"></a>Política de atribuição

A definição de nível de serviço que determina como o Serviço de Provisionamento de Dispositivos atribui dispositivos a um hub IoT. Existem três políticas de alocação suportadas:

* **Distribuição equilibrada:** os centros IoT ligados são igualmente propensos a ter dispositivos a eles a forrados. A definição predefinida. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição.

* **Latência mais baixa**: os dispositivos são a provisionados a um hub IoT com a latência mais baixa do dispositivo. Se vários hubs IoT ligados forneceriam a mesma latência mais baixa, o serviço de fornecimento hashes dispositivos através desses centros

* **Configuração estática através da lista**de inscrições : a especificação do hub IoT desejado na lista de inscrições tem prioridade sobre a política de atribuição ao nível do serviço.

## <a name="enrollment"></a>Inscrição

Uma inscrição é o registo de dispositivos ou grupos de dispositivos que podem registar-se através do provisionamento automático. O registo de inscrições contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o [mecanismo de atestado](concepts-security.md#attestation-mechanism) utilizado pelo dispositivo
- a configuração opcional inicial desejada
- hub IoT desejado
- o ID do dispositivo desejado

Existem dois tipos de matrículas suportadas pelo Serviço de Provisionamento de Dispositivos:

### <a name="enrollment-group"></a>Grupo de inscrições

Um grupo de inscrições é um grupo de dispositivos que partilham um mecanismo específico de atestado. Os grupos de inscrições suportam tanto o X.509 como o simétrico. Todos os dispositivos do grupo de matrículas X.509 apresentam certificados X.509 que tenham sido assinados pela mesma Autoridade de Certificados raiz ou intermédio (CA). Cada dispositivo do grupo de inscrição de chaves simétrica apresenta fichas SAS derivadas da chave simétrica do grupo. O nome do grupo de inscrição e o nome do certificado devem ser alfanuméricos, minúsculos e podem conter hífens.

> [!TIP]
> Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilham uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino.

### <a name="individual-enrollment"></a>Inscrição individual

Uma inscrição individual é uma entrada para um único dispositivo que pode registar-se. As inscrições individuais podem utilizar certificados de folha X.509 ou fichas SAS (de um TPM físico ou virtual) como mecanismos de atestação. O ID de inscrição individual é alfanumérico, minúsculo, e pode conter hífenes. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> Recomendamos a utilização de inscrições individuais para dispositivos que exijam configurações iniciais únicas, ou para dispositivos que só podem autenticar usando fichas SAS através de atestado de TPM.

## <a name="registration"></a>Registo

Um registo é o registo de um dispositivo que regista/a provisiona com sucesso um Hub IoT através do Serviço de Provisionamento de Dispositivos. Os registos de registo são criados automaticamente; podem ser eliminados, mas não podem ser atualizados.

## <a name="operations"></a>Operações

As operações são a unidade de faturação do Serviço de Provisionamento de Dispositivos. Uma operação é a conclusão bem sucedida de uma instrução para o serviço. As operações incluem registos de dispositivos e re-registos; as operações também incluem alterações do lado do serviço, tais como a adição de inscrições na lista de inscrições e a atualização de inscrições na lista de inscrições.
