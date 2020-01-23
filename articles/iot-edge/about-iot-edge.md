---
title: O que é o Azure IoT Edge | Microsoft Docs
description: Descrição geral do serviço Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 1ba133acda414d9779e2fb10150bbdd57285e9a5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511217"
---
# <a name="what-is-azure-iot-edge"></a>O que é o Hub IoT Edge

O Azure IoT Edge move as análises e a lógica empresarial personalizada da cloud para os dispositivos, para que a sua organização se possa dedicar às informações relevantes e não à gestão de dados. Escale horizontalmente sua solução de IoT empacotando sua lógica de negócios em contêineres padrão, em seguida, você pode implantar esses contêineres em qualquer um de seus dispositivos e monitorá-los por meio da nuvem.

Nas soluções IoT, o valor empresarial é impulsionado pelas análises, mas nem todas as análises têm de estar na cloud. Se você quiser responder às emergências o mais rápido possível, poderá executar cargas de trabalho de detecção de anomalias na borda. Se você quiser reduzir os custos de largura de banda e evitar a transferência de terabytes de dados brutos, será possível limpar e agregar os dados localmente e, em seguida, enviar apenas as informações para a nuvem para análise.

O Azure IoT Edge é composto por três componentes:

* Os **módulos de IOT Edge** são contêineres que executam serviços do Azure, serviços de terceiros ou seu próprio código. Os módulos são implementados nos dispositivos do IoT Edge e executados localmente nos mesmos.
* O **tempo de execução de IOT Edge** é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite que você monitore e gerencie dispositivos IOT Edge remotamente.

>[!NOTE]
>O Azure IoT Edge está disponível no escalão gratuito e standard do Hub IoT. O escalão gratuito destina-se apenas a testes e avaliação. Para obter mais informações sobre os escalões básico e standard, veja [How to choose the right IoT Hub tier](../iot-hub/iot-hub-scaling.md) (Como escolher o escalão do Hub IoT certo).

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Os módulos de IoT Edge são unidades de execução, implementados como contêineres compatíveis com o Docker, que executam a lógica de negócios na borda. Podem ser configurados vários módulos para comunicarem entre si, criando um pipeline para processamento de dados. Pode desenvolver módulos personalizados ou empacotar determinados serviços do Azure em módulos que disponibilizam informações offline e na periferia.

### <a name="artificial-intelligence-at-the-edge"></a>Inteligência artificial na borda

Azure IoT Edge permite implantar processamento de eventos complexos, aprendizado de máquina, reconhecimento de imagem e outros valores de ia de alto valor sem escrevê-los internamente. Os serviços do Azure, como Azure Functions, Azure Stream Analytics e Azure Machine Learning, podem ser executados localmente por meio de Azure IoT Edge. No entanto, você não está limitado aos serviços do Azure. Qualquer pessoa pode criar módulos de ia e torná-los disponíveis para a Comunidade para uso por meio do Azure Marketplace.

### <a name="bring-your-own-code"></a>Traga o seu próprio código

E se quiser implementar o seu próprio código nos seus dispositivos, o Azure IoT Edge também o suporta. O Azure IoT Edge tem o mesmo modelo de programação dos serviços do Azure IoT. Você pode executar o mesmo código em um dispositivo ou na nuvem. O Azure IoT Edge suporta o Linux e o Windows, para que possa programar para uma plataforma à sua escolha. Ele dá suporte a Java, .NET Core 2,0, Node. js, C e Python para que seus desenvolvedores possam codificar em uma linguagem que já conhecem e usar a lógica de negócios existente.

## <a name="iot-edge-runtime"></a>Runtime do IoT Edge

O runtime do Azure IoT Edge permite lógica personalizada e da cloud nos dispositivos IoT Edge. O tempo de execução reside no dispositivo IoT Edge e executa operações de gerenciamento e de comunicação. O runtime realiza várias funções:

* Instala e atualiza cargas de trabalho no dispositivo.
* Mantém as normas de segurança do Azure IoT Edge no dispositivo.
* Garante que os módulos do IoT Edge estão sempre em execução.
* Reporta o estado de funcionamento dos módulos à cloud, para monitorização remota.
* Gerencia a comunicação entre dispositivos de folha downstream e um dispositivo IoT Edge, entre módulos em um dispositivo IoT Edge e entre um dispositivo IoT Edge e a nuvem.

![O runtime do IoT Edge envia informações e relatórios para o Hub IoT.](./media/about-iot-edge/runtime.png)

O modo como você usa um dispositivo de Azure IoT Edge cabe a você. O tempo de execução é geralmente usado para implantar o ia em dispositivos de gateway que agregam e processam dados de outros dispositivos locais, mas esse modelo de implantação é apenas uma opção.

O tempo de execução de Azure IoT Edge é executado em um grande conjunto de dispositivos IoT que permite usá-lo em uma ampla variedade de formas. Ele dá suporte a sistemas operacionais Linux e Windows e abstrai detalhes de hardware. Use um dispositivo menor do que um Raspberry Pi 3 se não estiver processando muitos dados ou use um servidor industrial para executar cargas de trabalho com uso intensivo de recursos.

## <a name="iot-edge-cloud-interface"></a>Interface na cloud do IoT Edge

É difícil gerenciar o ciclo de vida do software para milhões de dispositivos IoT que geralmente são marcas e modelos diferentes ou geograficamente dispersos. As cargas de trabalho são criadas e configuradas para um determinado tipo de dispositivos, implementadas em todos os seus dispositivos e monitorizadas para detetar dispositivos que possam estar a funcionar mal. Estas atividades não podem ser feitas individualmente por dispositivo; têm de o ser em escala.

O Azure IoT Edge integra-se facilmente com os aceleradores de soluções do Azure IoT para proporcionar um plano de controlo para as necessidades da sua solução. Os serviços de nuvem permitem que você:

* Criem e configurem cargas de trabalho para serem executadas num tipo de dispositivo específico.
* Enviem cargas de trabalho para um conjunto de dispositivos.
* Monitorizem as cargas de trabalho em execução em dispositivos no terreno.

![Telemetria do dispositivo e as ações são coordenadas com a cloud](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Passos seguintes

Experimente estes conceitos ao [implementar o IoT Edge num dispositivo simulado](quickstart.md).
