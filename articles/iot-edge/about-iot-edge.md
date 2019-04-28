---
title: O que é o Azure IoT Edge | Microsoft Docs
description: Descrição geral do serviço Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 04/17/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 70a8e17ecdd318a800c51e04b4453c182d1fbdc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248882"
---
# <a name="what-is-azure-iot-edge"></a>O que é o Hub IoT Edge

O Azure IoT Edge move as análises e a lógica empresarial personalizada da cloud para os dispositivos, para que a sua organização se possa dedicar às informações relevantes e não à gestão de dados. Aumentar horizontalmente a sua solução de IoT ao empacotar sua lógica de negócio em contentores padrão, em seguida, pode implementar esses contêineres para qualquer um dos seus dispositivos e monitorizá-lo a partir da cloud. 

Nas soluções IoT, o valor empresarial é impulsionado pelas análises, mas nem todas as análises têm de estar na cloud. Se desejar responder a emergências o mais rápido possível, pode executar cargas de trabalho de deteção de anomalias na periferia. Se pretender reduzir os custos de largura de banda e evitar terabytes de dados não processados transferências, pode limpar e agregar os dados localmente, em seguida, apenas envie as informações para a cloud para análise. 

O Azure IoT Edge é composto por três componentes:
* **Módulos do IoT Edge** são contentores que executam serviços do Azure, serviços de terceiros ou seu próprio código. Módulos são implementados em dispositivos do IoT Edge e executados localmente nesses dispositivos. 
* O **runtime do IoT Edge** é executado em cada dispositivo IoT Edge e gere os módulos implementados em cada dispositivo. 
* R **interface baseada na cloud** permite-lhe monitorizar e gerir dispositivos IoT Edge remotamente.

>[!NOTE]
>O Azure IoT Edge está disponível no escalão gratuito e standard do Hub IoT. O escalão gratuito destina-se apenas a testes e avaliação. Para obter mais informações sobre os escalões básico e standard, veja [How to choose the right IoT Hub tier](../iot-hub/iot-hub-scaling.md) (Como escolher o escalão do Hub IoT certo).

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Módulos do IoT Edge são unidades de execução, implementados como contentores compatíveis do Docker, que executam a lógica de negócio na periferia. Podem ser configurados vários módulos para comunicarem entre si, criando um pipeline para processamento de dados. Pode desenvolver módulos personalizados ou empacotar determinados serviços do Azure em módulos que disponibilizam informações offline e na periferia. 

### <a name="artificial-intelligence-at-the-edge"></a>Inteligência artificial na periferia

O Azure IoT Edge permite-lhe implementar processamento de eventos complexo, machine learning, reconhecimento de imagens e outro elevado valor sem ter de escrevê-lo internamente. Serviços do Azure, como as funções do Azure, Azure Stream Analytics e Azure Machine Learning, podem ser executados no local através do Azure IoT Edge. Não está limitado aos serviços do Azure, no entanto. Qualquer pessoa é capaz de criar módulos de IA e disponibilizá-los para a Comunidade para utilização no Azure Marketplace. 

### <a name="bring-your-own-code"></a>Traga o seu próprio código

E se quiser implementar o seu próprio código nos seus dispositivos, o Azure IoT Edge também o suporta. O Azure IoT Edge tem o mesmo modelo de programação dos serviços do Azure IoT. O mesmo código pode ser executado num dispositivo ou na cloud. O Azure IoT Edge suporta o Linux e o Windows, para que possa programar para uma plataforma à sua escolha. Suporta Java, .NET Core 2.0, node. js, C e Python, para que os seus programadores possam programar numa linguagem que já conheçam e utilizam lógica empresarial já existente.

## <a name="iot-edge-runtime"></a>Runtime do IoT Edge

O runtime do Azure IoT Edge permite lógica personalizada e da cloud nos dispositivos IoT Edge. Encontra-se no dispositivo IoT Edge e faz operações de gestão e comunicação. O runtime realiza várias funções:

* Instalar e atualizar as cargas de trabalho no dispositivo.
* Manter as normas de segurança do Azure IoT Edge no dispositivo.
* Certifique-se de que módulos do IoT Edge estão sempre em execução.
* Comunicar o estado de funcionamento do módulo para a cloud para monitorização remota.
* Gerir a comunicação entre os dispositivos de folha a jusante e um dispositivo IoT Edge, entre módulos num dispositivo IoT Edge e entre um dispositivo IoT Edge e a cloud.

![O runtime do IoT Edge envia informações e relatórios para o Hub IoT.](./media/about-iot-edge/runtime.png)

Como utilizar um dispositivo Azure IoT Edge cabe a. O tempo de execução é frequentemente utilizado para implementar o AI em dispositivos de gateway que agregam e processar dados de outros dispositivos no local, mas este modelo de implementação é apenas uma opção. 

O runtime do Azure IoT Edge é executado num vasto conjunto de dispositivos IoT, para que possa ser utilizado de muitas e diversas formas. Ele oferece suporte a sistemas operativos Linux e Windows e abstrai detalhes do hardware. Utilize um dispositivo mais pequeno do que um Raspberry Pi 3, se não estiver a processar o volume de dados ou utilizar um servidor industrial para executar cargas de trabalho com muitos recursos.

## <a name="iot-edge-cloud-interface"></a>Interface na cloud do IoT Edge

É difícil de gerenciar o ciclo de vida de milhões de dispositivos de IoT que são, muitas vezes, faz com que diferentes e modelos ou geograficamente disperso. Cargas de trabalho são criadas e configuradas para um determinado tipo de dispositivo, implementadas para todos os seus dispositivos e monitorizadas para detetar todos os dispositivos com comportamento inadequado. Estas atividades não podem ser feitas individualmente por dispositivo; têm de o ser em escala.

O Azure IoT Edge integra-se facilmente com os aceleradores de soluções do Azure IoT para proporcionar um plano de controlo para as necessidades da sua solução. Serviços cloud permitem-lhe:

* Criem e configurem cargas de trabalho para serem executadas num tipo de dispositivo específico.
* Enviem cargas de trabalho para um conjunto de dispositivos.
* Monitorizem as cargas de trabalho em execução em dispositivos no terreno.

![Telemetria do dispositivo e as ações são coordenadas com a cloud](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Passos Seguintes

Experimente estes conceitos ao [implementar o IoT Edge num dispositivo simulado](quickstart.md).