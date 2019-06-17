---
title: Conceitos do serviço de aprovisionamento de dispositivos do Azure IoT Hub do serviço | Documentos da Microsoft
description: Descreve os conceitos de aprovisionamento da serviço específicos a dispositivos com o serviço de aprovisionamento de dispositivos e IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60745813"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos do serviço aprovisionamento de dispositivo Hub IoT

Serviço de aprovisionamento de dispositivo IoT Hub é um serviço auxiliar para o IoT Hub que utiliza para configurar dispositivos sem toques de aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos, pode [aprovisionar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de forma segura e dimensionável.

Aprovisionamento de dispositivos é um processo de duas partes. A primeira parte é estabelecer a ligação inicial entre o dispositivo e a solução de IoT por *registar* o dispositivo. A segunda parte é aplicar o elemento adequado *configuração* para o dispositivo com base nos requisitos específicos da solução. Assim que os dois passos estiverem concluídos, o dispositivo tiver sido totalmente *aprovisionado*. O Serviço de Aprovisionamento de Dispositivos automatiza os dois passos para fornecer uma experiência totalmente integrada de aprovisionamento do dispositivo.

Este artigo fornece uma descrição geral dos conceitos de aprovisionamento mais aplicáveis para gerir o *serviço*. Este artigo é mais relevante para as pessoas envolvidas na [o passo de configuração de cloud](about-iot-dps.md#cloud-setup-step) de preparar para a implementação de um dispositivo.

## <a name="service-operations-endpoint"></a>Ponto final de operações de serviço

O ponto final de operações de serviço é o ponto final para gerir as definições de serviço e mantém a lista de inscrição. Este ponto final é utilizado apenas pelo administrador do serviço; Não é utilizado pelos dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto final de aprovisionamento do dispositivo

O ponto de extremidade de aprovisionamento de dispositivo é o único ponto de extremidade que todos os dispositivos a utilizar para aprovisionamento automático. O URL é o mesmo para todas as instâncias serviço aprovisionamento, para eliminar a necessidade de reflash dispositivos com as novas informações de ligação em cenários de cadeia de fornecimento. O âmbito do ID garante o isolamento de inquilino.

## <a name="linked-iot-hubs"></a>Ligado a hubs IoT

O serviço de aprovisionamento de dispositivos apenas pode aprovisionar dispositivos para os hubs IoT que foram associados ao mesmo. Ligar um hub IoT para uma instância do serviço aprovisionamento de dispositivos fornece as permissões de leitura/escrita de serviço no registo do dispositivo do hub IoT; com a ligação, um serviço de aprovisionamento de dispositivos pode registar um ID de dispositivo e definir a configuração inicial no dispositivo duplo. Ligado a hubs IoT poderão estar em qualquer região do Azure. Pode ligar os hubs de outras subscrições para o serviço de aprovisionamento.

## <a name="allocation-policy"></a>Política de alocação

O nível de serviço, a definição que determina como o serviço aprovisionamento de dispositivos atribui dispositivos para um hub IoT. Existem três políticas de alocação suportadas:

* **Distribuição ponderada uniformemente**: ligado a hubs IoT são igualmente suscetível de ter dispositivos aprovisionados para eles. A predefinição. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição.

* **Latência mais baixa**: dispositivos são aprovisionados para um hub IoT com a latência mais baixa no dispositivo. Se vários hubs IoT ligados forneceria a mesma latência mais baixa, o serviço de aprovisionamento codifica dispositivos entre esses hubs

* **Configuração estática através da lista de inscrição**: especificação do hub IoT pretendido na lista de inscrição tem prioridade sobre a política de alocação de nível de serviço.

## <a name="enrollment"></a>Inscrição

Uma inscrição é o registo de dispositivos ou grupos de dispositivos que pode ser registado através do aprovisionamento automático. O registo de inscrição contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o [mecanismo de atestado](concepts-security.md#attestation-mechanism) usado pelo dispositivo
- a configuração pretendida inicial opcional
- hub IoT desejado
- o ID de dispositivo desejado

Existem dois tipos de inscrição suportada pelo serviço de aprovisionamento de dispositivos:

### <a name="enrollment-group"></a>Grupo de inscrição

Um grupo de inscrição é um grupo de dispositivos que partilham um mecanismo de atestado específico. Todos os dispositivos no grupo de inscrição apresentam certificados X.509 que iniciaram pela mesma raiz ou intermediário certificado de autoridade (CA). Grupos de inscrição podem utilizar apenas o mecanismo de atestado de X.509. O nome do grupo de inscrição e o nome do certificado tem de ser em minúsculas, de alfanuméricos e podem conter hífenes.

> [!TIP]
> Recomendamos que utilize um grupo de inscrição para um grande número de dispositivos que partilham uma configuração inicial pretendida ou para dispositivos pertencerão todos ao mesmo inquilino.

### <a name="individual-enrollment"></a>Inscrição individual

Uma inscrição individual é uma entrada para um único dispositivo que pode ser registado. Inscrições individuais podem utilizar certificados de folha X.509 ou tokens SAS (a partir de um TPM físico ou virtual) como mecanismos de atestação. O ID de registo uma inscrição individual é de alfanuméricos, minúsculas e pode conter hífenes. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

> [!TIP]
> Recomendamos a utilização das inscrições individuais para dispositivos que precisam de configurações iniciais exclusivas ou para dispositivos que só podem autenticar utilizando tokens SAS através de atestado de TPM.

## <a name="registration"></a>Registo

Um registo é o registo de um dispositivo com êxito a registar aprovisionamento/para um IoT Hub através do serviço de aprovisionamento de dispositivo. Registos de registo são criados automaticamente; eles podem ser eliminados, mas eles não podem ser atualizados.

## <a name="operations"></a>Operações

As operações são a unidade de faturação do serviço aprovisionamento de dispositivos. Uma operação é a conclusão com êxito de uma instrução para o serviço. As operações incluem o registo de dispositivos - e novos registos -, bem como alterações do lado do serviço, como adicionar e atualizar entradas de listas de inscrição.
