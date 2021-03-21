---
title: As melhores práticas de desenvolvimento de dispositivos na Azure IoT Central | Microsoft Docs
description: Este artigo descreve as melhores práticas para a conectividade do dispositivo na Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054705"
---
# <a name="best-practices-for-device-development"></a>Melhores práticas para o desenvolvimento de dispositivos

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Estas recomendações mostram como implementar dispositivos para aproveitar a recuperação de desastres incorporada e a escala automática na IoT Central.

A lista a seguir mostra o fluxo de alto nível quando um dispositivo se liga à IoT Central:

1. Utilize O DPS para abastecer o dispositivo e obter uma cadeia de ligação do dispositivo.

1. Utilize o fio de ligação para ligar o ponto final interno do Hub IoT da IoT Central. Envie dados e receba dados da sua aplicação IoT Central.

1. Se o dispositivo obtiver falhas de ligação, então, dependendo do tipo de erro, ou recandidrói a ligação ou reprovisione o dispositivo.

## <a name="use-dps-to-provision-the-device"></a>Utilize dPS para o fornecimento do dispositivo

Para providenciar um dispositivo com DPS, utilize o ID de âmbito, credenciais e identificação do dispositivo a partir da sua aplicação IoT Central. Para saber mais sobre os tipos de credenciais, consulte [a inscrição em grupo X.509](concepts-get-connected.md#x509-group-enrollment) e [a inscrição no grupo SAS.](concepts-get-connected.md#sas-group-enrollment) Para saber mais sobre os IDs do dispositivo, consulte [o registo do Dispositivo.](concepts-get-connected.md#device-registration)

Com o sucesso, o DPS devolve uma cadeia de ligação que o dispositivo pode utilizar para ligar à sua aplicação IoT Central. Para resolver os erros de provisão, consulte [Verifique o estado de provisionamento do seu dispositivo](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

O dispositivo pode cache a cadeia de ligação para utilização para ligações posteriores. No entanto, o aparelho deve estar preparado para [lidar com falhas de ligação](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Ligue-se à IoT Central

Utilize o fio de ligação para ligar o ponto final interno do Hub IoT da IoT Central. A ligação permite-lhe enviar telemetria para a sua aplicação IoT Central, sincronizar os valores de propriedade com a sua aplicação IoT Central e responder aos comandos enviados pela sua aplicação IoT Central.

## <a name="handle-connection-failures"></a>Lidar com falhas de ligação

Para fins de escala ou recuperação de desastres, a IoT Central pode atualizar o seu hub IoT subjacente. Para manter a conectividade, o código do dispositivo deve lidar com erros de ligação específicos, estabelecendo uma ligação ao novo ponto final do Hub IoT.

Se o dispositivo obtiver algum dos seguintes erros quando se liga, deve refazer o passo de provisionamento com DPS para obter uma nova cadeia de ligação. Estes erros significam que a cadeia de ligação que o dispositivo está a utilizar já não é válida:

- Ponto final IoT Hub inacessível.
- Ficha de segurança expirada.
- Dispositivo desativado no Hub IoT.

Se o dispositivo obtiver algum dos seguintes erros quando se liga, deve utilizar uma estratégia de back-off para voltar a tentar a ligação. Estes erros significam que a cadeia de ligação que o dispositivo está a utilizar ainda é válida, mas as condições transitórias estão a impedir o dispositivo de se ligar:

- Dispositivo bloqueado pelo operador.
- Erro interno 500 do serviço.

Para saber mais sobre os códigos de erro do dispositivo, consulte [as ligações do dispositivo de resolução de problemas](troubleshoot-connection.md).

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Reveja algum código de amostra que mostre como usar fichas SAS em [Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md)
- Saiba como [conectar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT](how-to-connect-devices-x509.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Saiba como definir um novo tipo de [dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)
