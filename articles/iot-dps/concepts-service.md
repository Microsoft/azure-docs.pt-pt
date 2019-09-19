---
title: Conceitos de serviço no serviço de provisionamento de dispositivos no Hub IoT do Azure | Microsoft Docs
description: Descreve os conceitos de provisionamento de serviço específicos para dispositivos com o serviço de provisionamento de dispositivos e o Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 51486da6b34c0ff1e9b6d05558c2132a416913e9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104370"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos do serviço de provisionamento de dispositivos no Hub IoT

O serviço de provisionamento de dispositivos no Hub IoT é um serviço auxiliar para o Hub IoT que você usa para configurar o provisionamento de dispositivos sem toque para um hub IoT especificado. Com o serviço de provisionamento de dispositivos, você pode [provisionar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de maneira segura e escalonável.

O provisionamento de dispositivos é um processo de duas partes. A primeira parte é estabelecer a conexão inicial entre o dispositivo e a solução de IoT *registrando* o dispositivo. A segunda parte é aplicar a *configuração* adequada ao dispositivo com base nos requisitos específicos da solução. Depois que ambas as etapas forem concluídas, o dispositivo foi totalmente *provisionado*. O Serviço de Aprovisionamento de Dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento do dispositivo.

Este artigo fornece uma visão geral dos conceitos de provisionamento mais aplicáveis ao gerenciamento do *serviço*. Este artigo é mais relevante para as pessoas envolvidas na [etapa de configuração de nuvem](about-iot-dps.md#cloud-setup-step) de preparar um dispositivo para a implantação.

## <a name="service-operations-endpoint"></a>Ponto de extremidade de operações de serviço

O ponto de extremidade de operações de serviço é o ponto de extremidade para gerenciar as configurações de serviço e manter a lista de registro. Esse ponto de extremidade é usado somente pelo administrador de serviços; Ele não é usado por dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto de extremidade de provisionamento do dispositivo

O ponto de extremidade de provisionamento do dispositivo é o único ponto de extremidade que todos os dispositivos usam para provisionamento automático. A URL é a mesma para todas as instâncias de serviço de provisionamento, para eliminar a necessidade de reflashar dispositivos com novas informações de conexão em cenários de cadeia de suprimentos. O escopo da ID garante o isolamento do locatário.

## <a name="linked-iot-hubs"></a>Ligado a hubs IoT

O serviço de provisionamento de dispositivos só pode provisionar dispositivos para hubs IoT que foram vinculados a ele. Vincular um hub IoT a uma instância do serviço de provisionamento de dispositivos dá ao serviço permissões de leitura/gravação para o registro do dispositivo do Hub IoT; com o link, um serviço de provisionamento de dispositivos pode registrar uma ID de dispositivo e definir a configuração inicial no dispositivo. Os hubs IoT vinculados podem estar em qualquer região do Azure. Você pode vincular hubs em outras assinaturas ao serviço de provisionamento.

## <a name="allocation-policy"></a>Política de alocação

A configuração de nível de serviço que determina como o serviço de provisionamento de dispositivos atribui dispositivos a um hub IoT. Existem três políticas de alocação suportadas:

* **Distribuição uniformemente ponderada**: os hubs IOT vinculados são igualmente prováveis de ter dispositivos provisionados para eles. A configuração padrão. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição.

* **Menor latência**: os dispositivos são provisionados para um hub IOT com a menor latência para o dispositivo. Se vários hubs IoT vinculados fornecerem a mesma menor latência, o serviço de provisionamento conterá os dispositivos em hash nesses hubs

* **Configuração estática por meio da lista de registro**: a especificação do Hub IOT desejado na lista de registro tem prioridade sobre a política de alocação de nível de serviço.

## <a name="enrollment"></a>Inscrição

Um registro é o registro de dispositivos ou grupos de dispositivos que podem ser registrados por meio do provisionamento automático. O registro de registro contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o [mecanismo de atestado](concepts-security.md#attestation-mechanism) usado pelo dispositivo
- a configuração opcional inicial desejada
- Hub IoT desejado
- a ID de dispositivo desejada

Há dois tipos de registros com suporte pelo serviço de provisionamento de dispositivos:

### <a name="enrollment-group"></a>Grupo de registros

Um grupo de registro é um grupo de dispositivos que compartilham um mecanismo de atestado específico. Os grupos de registro dão suporte a X. 509, bem como ao Symmetric. Todos os dispositivos no grupo de registro X. 509 apresentam certificados X. 509 que foram assinados pela mesma raiz ou AC (autoridade de certificação) intermediária. Cada dispositivo no grupo de registro de chave simétrica apresenta tokens SAS derivados da chave simétrica do grupo. O nome do grupo de registro e o nome do certificado devem ser alfanuméricos, minúsculos e podem conter hifens.

> [!TIP]
> É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que todos vão para o mesmo locatário.

### <a name="individual-enrollment"></a>Registro individual

Um registro individual é uma entrada para um único dispositivo que pode ser registrado. Registros individuais podem usar certificados de folha X. 509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. A ID de registro em um registro individual é alfanumérica, em letras minúsculas e pode conter hifens. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem ser autenticados usando tokens SAS via atestado de TPM.

## <a name="registration"></a>Registo

Um registro é o registro de um dispositivo que está se registrando/Provisionando com êxito em um hub IoT por meio do serviço de provisionamento de dispositivos. Os registros de registro são criados automaticamente; Eles podem ser excluídos, mas não podem ser atualizados.

## <a name="operations"></a>Operações

As operações são a unidade de cobrança do serviço de provisionamento de dispositivos. Uma operação é a conclusão bem-sucedida de uma instrução para o serviço. As operações incluem o registo de dispositivos - e novos registos -, bem como alterações do lado do serviço, como adicionar e atualizar entradas de listas de inscrição.
