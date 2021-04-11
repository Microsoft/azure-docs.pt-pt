---
title: Tutorial - Ligue uma aplicação genérica ao Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar um dispositivo que executa uma aplicação c, C#, Java, JavaScript ou Python à sua aplicação Azure IoT Central. Modifica o modelo do dispositivo gerado automaticamente adicionando vistas que permitem que um operador interaja com um dispositivo conectado.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: bbf94b6e000d5c082debd6a0d41a8d62b8b3f26e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491105"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, ligar uma aplicação do cliente à sua aplicação Azure IoT Central. A aplicação simula o comportamento de um dispositivo controlador de temperatura. Quando a aplicação se liga à IoT Central, envia o ID do modelo do dispositivo do controlador de temperatura. A IoT Central utiliza o ID do modelo para recuperar o modelo do dispositivo e criar um modelo de dispositivo para si. Adiciona personalizações e vistas ao modelo do dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie e execute o código do dispositivo e veja-o ligar-se à sua aplicação IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Adicione vistas personalizadas a um modelo de dispositivo.
> * Publique o modelo do dispositivo.
> * Utilize uma vista para gerir as propriedades do dispositivo.
> * Chame um comando para controlar o dispositivo.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Ver dados brutos

Como desenvolvedor de dispositivos, pode utilizar a visão **de dados Raw** para examinar os dados brutos que o seu dispositivo está a enviar para a IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="A visão de dados brutos":::

Nesta vista, pode selecionar as colunas para visualizar e definir um intervalo de tempo para visualizar. A coluna **de dados não modelo** mostra dados do dispositivo que não correspondem a nenhuma definição de propriedade ou telemetria no modelo do dispositivo.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Se preferir continuar através do conjunto de tutoriais IoT Central e aprender mais sobre a construção de uma solução IoT Central, consulte:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que aprendeu o básico de como criar um dispositivo usando Java, alguns dos próximos passos sugeridos são:

* Leia [Quais são os modelos do dispositivo para](./concepts-device-templates.md) saber mais sobre o papel dos modelos do dispositivo quando estiver a implementar o código do dispositivo.
* Leia [Se ligar à Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registar os dispositivos com a IoT Central e como a IoT Central assegura as ligações do dispositivo.
* Leia [Telemetria, propriedade e cargas de comando](concepts-telemetry-properties-commands.md) para saber mais sobre os dados que o dispositivo troca com a IoT Central.
* Leia [o guia de desenvolvimento do dispositivo IoT Plug and Play](../../iot-pnp/concepts-developer-guide-device.md).
