---
title: Construa a ponte de dispositivos Azure IoT Central ! Microsoft Docs
description: Construa a ponte do dispositivo IoT Central para ligar outras nuvens IoT (Sigfox, Partícula, Rede de Coisas, etc.) à sua aplicação IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fc8ea41e804344735cfa2400d5d763622d8811c8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026255"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Construa a ponte do dispositivo IoT Central para ligar outras nuvens IoT à IoT Central

*Este tópico aplica-se aos administradores.*

A ponte de dispositivos IoT Central é uma solução de código aberto que liga o seu Sigfox, Partícula, Rede de Coisas e outras nuvens à sua aplicação IoT Central. Quer esteja a utilizar dispositivos de rastreio de ativos ligados à Rede de Área de Baixa Potência da Sigfox, ou utilizando dispositivos de monitorização da qualidade do ar na Nuvem de Dispositivos de Partículas, ou utilizando dispositivos de monitorização da humidade do solo no TTN, pode aproveitar diretamente a potência da IoT Central utilizando a ponte do dispositivo IoT Central. A ponte do dispositivo liga outras nuvens IoT com a IoT Central, reencaminhando os dados que os seus dispositivos enviam para as outras nuvens através da sua aplicação IoT Central. Na sua aplicação IoT Central, pode construir regras e executar análises sobre esses dados, criar fluxos de trabalho em aplicações Power Automamate e Azure Logic, exportar esses dados, e muito mais. Obtenha a ponte de [dispositivos IoT Central](https://aka.ms/iotcentralgithubdevicebridge) do GitHub

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como funciona?
A ponte de dispositivos IoT Central é uma solução de código aberto no GitHub. Está pronto para combinar com um botão "Implementar para a Azure" que implementa um modelo personalizado de Gestor de Recursos Azure com vários recursos Azure na sua subscrição Azure. Os recursos incluem:
-    App Azure Function
-    Conta de Armazenamento do Azure
-    Plano de Consumo
-    Azure Key Vault

A aplicação de função é a peça crítica da ponte do dispositivo. Recebe pedidos HTTP POST de outras plataformas IoT ou quaisquer plataformas personalizadas através de uma simples integração webhook. Fornecemos exemplos que mostram como ligar-se às nuvens Sigfox, Particle e TTN. Pode facilmente estender esta solução para ligar à sua nuvem IoT personalizada se a sua plataforma puder enviar pedidos HTTP POST para a sua aplicação de função.
A aplicação Function transforma os dados num formato aceite pela IoT Central e reencaminha-os através de APIs DPS.

![Screenshot das funções Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Se a sua aplicação IoT Central reconhecer o dispositivo por ID do dispositivo na mensagem reencaminhada, aparecerá uma nova medição para esse dispositivo. Se o ID do dispositivo nunca tiver sido visto pela sua aplicação IoT Central, a sua aplicação de função tentará registar um novo dispositivo com esse ID do dispositivo, e aparecerá como um "dispositivo não associado" na sua aplicação IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como é que o esmigo?
As instruções estão listadas em detalhe no ficheiro README no repo GitHub. 

## <a name="pricing"></a>Preços
Os recursos Azure serão hospedados na sua subscrição do Azure. Pode saber mais sobre os preços no [ficheiro README.](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="next-steps"></a>Próximos passos
Agora que aprendeu a construir a ponte do dispositivo IoT Central, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Faça a gestão dos seus dispositivos](howto-manage-devices.md)
