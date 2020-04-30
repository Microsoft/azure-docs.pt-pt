---
title: Introdução ao Tampão IoT e Pré-visualização de jogo / Microsoft Docs
description: Saiba mais sobre ioT Plug e Reproduzir Pré-visualização. O IoT Plug and Play baseia-se numa linguagem de modelação aberta que permite aos dispositivos IoT declararem as suas capacidades. Os dispositivos IoT apresentam essa declaração, chamada modelo de capacidade do dispositivo, quando se ligam a soluções em nuvem como as aplicações Azure IoT Central ou partner. A solução cloud pode então entender automaticamente o dispositivo e começar a interagir com ele - tudo sem escrever qualquer código.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770442"
---
# <a name="what-is-iot-plug-and-play-preview"></a>O que é IoT Plug e Play Preview?

O IoT Plug e o Play Preview permitem que os desenvolvedores de soluções integrem os dispositivos com as suas soluções sem escrever qualquer código incorporado. No centro do IoT Plug and Play está um esquema de modelo de capacidade de _dispositivo_ que descreve as capacidades do dispositivo. Este esquema é um documento JSON que é estruturado como um conjunto de interfaces que incluem definições de:

- _Propriedades_ que representam o estado de leitura e leitura/escrita de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade apenas para leitura e uma temperatura-alvo num termóstato pode ser uma propriedade de leitura/escrita.
- _Telemetria_ que são os dados emitidos por um dispositivo, quer os dados sejam um fluxo regular de leituras de sensores, um erro ocasional ou mensagem de informação.
- _Comandos_ que descrevem uma função ou operação que pode ser feita num dispositivo. Por exemplo, um comando pode reiniciar um portal ou tirar uma fotografia usando uma câmara remota.

Pode reutilizar interfaces através de modelos de capacidade de dispositivo para facilitar a colaboração e acelerar o desenvolvimento.

Para que o IoT Plug and Play funcione perfeitamente com as [Gémeas Digitais Azure,](../digital-twins/about-digital-twins.md)o esquema IoT Plug and Play é definido utilizando a Linguagem de [Definição Dupla Digital (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Plug and Play e o DTDL estão abertos à comunidade, e a Microsoft congratula-se com a colaboração com clientes, parceiros e indústria. Ambos baseiam-se em normas W3C abertas, tais como JSON-LD e RDF, que permitem uma adoção mais fácil através de serviços e ferramentas. Além disso, não há custo extra para usar IoT Plug e Play e DTDL. As tarifas padrão para [o Hub Azure IoT,](../iot-hub/about-iot-hub.md) [Azure IoT Central,](../iot-central/core/overview-iot-central.md)e outros serviços Azure permanecem as mesmas.

As soluções construídas no IoT Hub ou IoT Central podem beneficiar do IoT Plug and Play.

Este artigo descreve:

- Os papéis típicos associados a um projeto que usa IoT Plug e Play.
- Como utilizar dispositivos IoT Plug e Play na sua aplicação.
- Como desenvolver uma aplicação de dispositivo IoT que suporta IoT Plug e Play.
- Como certificar um dispositivo IoT Plug and Play e publicar no [catálogo certificado para o catálogo de dispositivos IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Funções de utilizador

IoT Plug and Play é útil para dois tipos de desenvolvedores:

- Um desenvolvedor de _soluções_ é responsável pelo desenvolvimento de uma solução IoT utilizando o Azure IoT e outros recursos Azure e para identificar dispositivos IoT para integrar.
- Um desenvolvedor de _dispositivos_ cria o código que funciona num dispositivo ligado à sua solução.

## <a name="use-iot-plug-and-play-devices"></a>Utilize dispositivos IoT Plug e Play

Como um desenvolvedor de soluções, você pode desenvolver uma solução IoT hospedada em nuvem que utiliza dispositivos IoT Plug e Play. Pode utilizar qualquer um dos seguintes serviços Azure:

- [IoT Central](../iot-central/core/overview-iot-central.md) - uma solução de software IoT totalmente gerida como um serviço que facilita a criação de produtos que ligam os mundos físico e digital.
- [IoT Hub](../iot-hub/about-iot-hub.md) - um serviço de nuvem gerido, que funciona como um centro de mensagens para uma comunicação segura e bidirecional entre a sua aplicação IoT e os seus dispositivos.

Pode encontrar dispositivos IoT Plug e Play através do catálogo de dispositivos Azure Certified for IoT. Cada dispositivo IoT Plug and Play no catálogo foi validado e tem um modelo de capacidade de dispositivo. Veja o modelo de capacidade do dispositivo para compreender a funcionalidade do dispositivo ou usá-lo para simular o dispositivo no Azure IoT Central.

Quando conecta um dispositivo IoT Plug and Play, pode visualizar o seu modelo de capacidade de dispositivo, as interfaces incluídas no modelo, e a telemetria, propriedades e comandos definidos nessas interfaces.

## <a name="develop-an-iot-device-application"></a>Desenvolver uma aplicação de dispositivo IoT

Como desenvolvedor de dispositivos, pode desenvolver um produto de hardware IoT que suporta IoT Plug e Play. O processo envolve dois passos-chave:

1. Defina o modelo e interfaces de capacidade do dispositivo. É autor de um conjunto de ficheiros JSON que declaram as capacidades do seu dispositivo utilizando o [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Um modelo de capacidade de dispositivo descreve uma entidade completa, como um produto físico, e define o conjunto de interfaces implementadas por essa entidade. As interfaces são contratos partilhados que identificam exclusivamente a telemetria, propriedades e comandos suportados por um dispositivo. As interfaces podem ser reutilizadas em diferentes modelos de capacidade do dispositivo.

1. Autor do software ou firmware do dispositivo que implementa as capacidades declaradas no modelo e interfaces de capacidade do dispositivo. O Azure IoT SDK inclui APIs para implementar modelos de capacidade de dispositivo.

O pacote de extensões [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) fornece muitas funcionalidades para o ajudar. Por exemplo, como desenvolvedor de dispositivos, você pode usar uma extensão para gerar um projeto esqueleto C a partir de um modelo de capacidade. No entanto, pode utilizar qualquer IDE para autor e implementar modelos de capacidade de dispositivo.

## <a name="certify-an-iot-plug-and-play-device"></a>Certificar um dispositivo IoT Plug and Play

Como desenvolvedor de dispositivos, pode submeter produtos de hardware IoT para certificação. Pode publicar um dispositivo certificado no catálogo de dispositivos Certificado si. As etapas do processo de certificação incluem:

- Junte-se à Rede de [Parceiros](https://partner.microsoft.com)da Microsoft .
- A bordo do portal Certified for Azure IoT.
- Envie um modelo de capacidade de dispositivo IoT Plug and Play e informações de marketing para criar um novo registo de dispositivos.
- Passe um conjunto automatizado de testes de validação para o dispositivo.
- Publique no catálogo de dispositivos Certificados para IoT.

## <a name="regional-availability"></a>Disponibilidade regional

Durante a pré-visualização pública, o IoT Plug and Play está disponível em todas as regiões.

## <a name="message-quotas-in-iot-hub"></a>Quotas de mensagem no IoT Hub
Durante a pré-visualização pública, os dispositivos IoT Plug e Play enviam mensagens separadas por interface, o que pode aumentar o número de mensagens contadas para a sua [quota de mensagem](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral do IoT Plug and Play, o próximo passo sugerido é experimentar um dos quickstarts:

- [Utilize um modelo de capacidade do dispositivo para criar um dispositivo IoT Plug and Play](./quickstart-create-pnp-device-windows.md)
- [Ligar um dispositivo ao Hub IoT](./quickstart-connect-pnp-device-c-windows.md)
- [Ligar-se a um dispositivo na sua solução](./quickstart-connect-pnp-device-solution-node.md)
