---
title: Introdução à visualização de Plug and Play de IoT | Microsoft Docs
description: Saiba mais sobre o IoT Plug and Play Preview. O Plug and Play IoT baseia-se em uma linguagem de modelagem aberta que permite aos dispositivos IoT declarar seus recursos. Os dispositivos IoT apresentam essa declaração, chamada de modelo de capacidade de dispositivo, quando se conectam a soluções de nuvem, como o Azure IoT Central ou aplicativos de parceiros. A solução de nuvem pode, então, entender automaticamente o dispositivo e começar a interagir com ele — tudo sem escrever nenhum código.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 56a73449c69010bc97122023f7dcbc9e3a9df154
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048000"
---
# <a name="what-is-iot-plug-and-play-preview"></a>O que é o IoT Plug and Play Preview?

A visualização da IoT Plug and Play permite que os desenvolvedores de soluções integrem dispositivos a suas soluções sem escrever nenhum código embutido. No núcleo do Plug and Play IoT está um esquema de _modelo de capacidade de dispositivo_ que descreve os recursos do dispositivo. Esse esquema é um documento JSON estruturado como um conjunto de interfaces que incluem definições de:

- _Propriedades_ que representam o estado somente leitura e de leitura/gravação de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade de leitura/gravação.
- _Telemetria_ que são os dados emitidos por um dispositivo, se os dados são um fluxo regular de leituras de sensor, um erro ocasional ou mensagem de informações.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

Você pode reutilizar interfaces em modelos de recursos de dispositivo para facilitar a colaboração e acelerar o desenvolvimento.

Para tornar o IoT Plug and Play funcionar diretamente com o [gêmeos digital do Azure](../digital-twins/about-digital-twins.md), o esquema de plug and Play IOT é definido usando o [DTDL (digital mydefinition Language)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). A IoT Plug and Play e as DTDL estão abertas para a Comunidade, e a Microsoft agradece a colaboração com clientes, parceiros e setor. Ambos se baseiam em padrões W3C abertos, como JSON-LD e RDF, que possibilitam uma adoção mais fácil entre serviços e ferramentas. Além disso, não há nenhum custo adicional para usar o IoT Plug and Play e DTDL. As taxas padrão para o [Hub IOT do Azure](../iot-hub/about-iot-hub.md), o [Azure IOT central](../iot-central/overview-iot-central.md)e outros serviços do Azure permanecem as mesmas.

As soluções criadas no Hub IoT ou IoT Central podem se beneficiar da Plug and Play de IoT.

Este artigo descreve:

- As funções típicas associadas a um projeto que usa Plug and Play de IoT.
- Como usar dispositivos de Plug and Play IoT em seu aplicativo.
- Como desenvolver um aplicativo de dispositivo IoT que dá suporte a Plug and Play de IoT.
- Como certificar um dispositivo de Plug and Play IoT e publicar no [Catálogo de dispositivos certificado para IOT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Funções de utilizador

O Plug and Play IoT é útil para dois tipos de desenvolvedores:

- Um _desenvolvedor de soluções_ é responsável por desenvolver uma solução de IOT usando o Azure IOT e outros recursos do Azure e para identificar dispositivos IOT a serem integrados.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo conectado à sua solução.

## <a name="use-iot-plug-and-play-devices"></a>Usar dispositivos de Plug and Play de IoT

Como desenvolvedor de soluções, você pode desenvolver uma solução de IoT hospedada na nuvem que usa dispositivos de Plug and Play de IoT. Você pode usar qualquer um dos seguintes serviços do Azure:

- [IOT central](../iot-central/overview-iot-central.md) -uma solução de software como serviço IOT totalmente gerenciada que facilita a criação de produtos que conectam os mundos físicos e digitais.
- [Hub IOT](../iot-hub/about-iot-hub.md) -um serviço de nuvem gerenciado que atua como um hub de mensagens para comunicação bidirecional segura entre seu aplicativo IOT e seus dispositivos.

Você pode encontrar dispositivos IoT Plug and Play por meio do catálogo de dispositivos certificado pelo Azure para IoT. Cada dispositivo de Plug and Play IoT no catálogo foi validado e tem um modelo de funcionalidade de dispositivo. Exiba o modelo de capacidade do dispositivo para entender a funcionalidade do dispositivo ou usá-lo para simular o dispositivo no Azure IoT Central.

Ao conectar um dispositivo de Plug and Play IoT, você pode exibir seu modelo de capacidade de dispositivo, as interfaces incluídas no modelo e a telemetria, as propriedades e os comandos definidos nessas interfaces.

## <a name="develop-an-iot-device-application"></a>Desenvolver um aplicativo de dispositivo IoT

Como desenvolvedor de dispositivos, você pode desenvolver um produto de hardware de IoT que ofereça suporte a Plug and Play de IoT. O processo envolve duas etapas principais:

1. Defina o modelo e as interfaces de funcionalidade do dispositivo. Você cria um conjunto de arquivos JSON que declaram os recursos do dispositivo usando o [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Um modelo de funcionalidade de dispositivo descreve uma entidade completa, como um produto físico, e define o conjunto de interfaces implementadas por essa entidade. Interfaces são contratos compartilhados que identificam exclusivamente a telemetria, as propriedades e os comandos com suporte de um dispositivo. As interfaces podem ser reutilizadas em diferentes modelos de capacidade de dispositivo.

1. Crie o software do dispositivo ou o firmware que implementa os recursos declarados no modelo e nas interfaces de funcionalidade do dispositivo. O SDK do Azure IoT inclui APIs para implementar modelos de capacidade de dispositivo.

O [IOT Device Workbench para vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) extensão fornece muitos recursos para ajudá-lo. Por exemplo, como um desenvolvedor de dispositivo, você pode usar a extensão para gerar um projeto de esqueleto C a partir de um modelo de funcionalidade. No entanto, você pode usar qualquer IDE para criar e implementar modelos de capacidade de dispositivo.

## <a name="certify-an-iot-plug-and-play-device"></a>Certificar um dispositivo de Plug and Play IoT

Como desenvolvedor de dispositivos, você pode enviar produtos de hardware IoT para certificação. Você pode publicar um dispositivo certificado no catálogo de dispositivos certificado para IoT. As etapas do processo de certificação incluem:

- Ingresse no [Microsoft Partner Network](https://partner.microsoft.com).
- Integre-se ao portal do Azure IoT certificado.
- Envie um modelo de capacidade de dispositivo Plug and Play de IoT e informações de marketing para criar um novo registro de dispositivo.
- Passe o conjunto automatizado de testes de validação para o dispositivo.
- Publique no catálogo de dispositivos certificado para IoT.

## <a name="regional-availability"></a>Disponibilidade regional
Durante a visualização pública, a IoT Plug and Play está disponível nas regiões Europa Setentrional, EUA Central e leste do Japão. Certifique-se de criar o Hub em uma dessas regiões.

## <a name="message-quotas-in-iot-hub"></a>Cotas de mensagens no Hub IoT
Durante a visualização pública, os dispositivos IoT Plug and Play enviam mensagens separadas por interface, o que pode aumentar o número de mensagens contadas em direção à sua [cota de mensagens](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que você tem uma visão geral do Plug and Play IoT, a próxima etapa sugerida é experimentar um dos guias de início rápido:

- [Usar um modelo de capacidade de dispositivo para criar um dispositivo de Plug and Play de IoT](./quickstart-create-pnp-device.md)
- [Conectar um dispositivo ao Hub IoT](./quickstart-connect-pnp-device.md)
- [Conectar-se a um dispositivo em sua solução](./quickstart-connect-pnp-device-solution.md)
