---
title: Ambiente de desenvolvimento Azure IoT Edge | Microsoft Docs
description: Conheça os sistemas suportados e as ferramentas de desenvolvimento de primeira parte que o ajudarão a criar módulos IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a253ff5fde0667c80be5a5b3af00e74abebdc609
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030258"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Prepare o seu ambiente de desenvolvimento e teste para ioT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge move a sua lógica de negócio existente para dispositivos que operam no limite. Para preparar as suas aplicações e cargas de trabalho para funcionar como [módulos IoT Edge,](iot-edge-modules.md)é necessário construí-las como recipientes. Este artigo fornece orientações sobre como configurar o seu ambiente de desenvolvimento para que possa criar com sucesso uma solução IoT Edge. Assim que tiver o seu ambiente de desenvolvimento configurado, poderá aprender a [desenvolver os seus próprios módulos IoT Edge.](module-development.md)

Em qualquer solução IoT Edge, há pelo menos duas máquinas a considerar. Um deles é o dispositivo IoT Edge (ou dispositivos) em si, que executa o módulo IoT Edge. A outra é a máquina de desenvolvimento que usa para construir, testar e implantar módulos. Este artigo centra-se principalmente na máquina de desenvolvimento. Para efeitos de teste, as duas máquinas podem ser as mesmas. Pode executar ioT Edge na sua máquina de desenvolvimento e colocar módulos nela.

## <a name="operating-system"></a>Sistema operativo

O Azure IoT Edge funciona num conjunto específico de [sistemas operativos suportados.](support.md) Para desenvolver para ioT Edge, pode utilizar a maioria dos sistemas operativos que podem operar um motor de contentores. O motor do recipiente é um requisito na máquina de desenvolvimento para construir os seus módulos como recipientes e empurrá-los para um registo de contentores.

Se a sua máquina de desenvolvimento não conseguir executar o Azure IoT Edge, continue neste artigo a aprender sobre [ferramentas](#testing-tools) de teste que o ajudam a testar e depurar localmente.

O sistema operativo da sua máquina de desenvolvimento não tem de corresponder ao sistema operativo do seu dispositivo IoT Edge. No entanto, o sistema de funcionamento do contentor deve ser consistente entre a máquina de desenvolvimento e o dispositivo IoT Edge. Por exemplo, pode desenvolver módulos numa máquina Windows e implantá-los num dispositivo Linux. A máquina Windows precisa de executar recipientes Linux para construir os módulos para o dispositivo Linux.

## <a name="container-engine"></a>Motor de contentor

O conceito central de IoT Edge é que você pode remotamente implementar a sua lógica de negócio e nuvem para dispositivos, embalando-o em recipientes. Para construir contentores, precisa de um motor de contentor na sua máquina de desenvolvimento.

O único motor de contentores suportado para dispositivos IoT Edge em produção é o Moby. No entanto, qualquer motor de contentor compatível com a Iniciativa de Recipiente Aberto, como o Docker, é capaz de construir imagens do módulo IoT Edge.

## <a name="development-tools"></a>Ferramentas de programação

Tanto o Visual Studio como o Visual Studio Code têm extensões adicionais para ajudar a desenvolver soluções IoT Edge. Estas extensões fornecem modelos específicos da linguagem para ajudar a criar e implementar novos cenários IoT Edge. As extensões Azure IoT Edge para Visual Studio e Visual Studio Code ajudam-no a codificar, construir, implementar e depurar as suas soluções IoT Edge. Pode criar uma solução IoT Edge inteira que contém vários módulos, e as extensões atualizam automaticamente um modelo manifesto de implementação com cada nova adição de módulo. Com as extensões, também pode gerir os seus dispositivos IoT a partir de Visuais Studio ou Visual Studio Code. Implementar módulos num dispositivo, monitorizar o estado e ver mensagens à medida que chegam ao IoT Hub. Ambas as extensões utilizam a [ferramenta IoT EdgeHub dev](#iot-edgehub-dev-tool) para permitir o funcionamento e depuragem locais de módulos na sua máquina de desenvolvimento também.

Se preferir desenvolver-se com outros editores ou a partir do CLI, a ferramenta Azure IoT Edge dev fornece comandos para que possa desenvolver e testar a partir da linha de comando. Pode criar novos cenários IoT Edge, construir imagens de módulos, executar módulos num simulador e monitorizar mensagens enviadas para o IoT Hub.

### <a name="visual-studio-code-extension"></a>Extensão do Visual Studio Code

A extensão Azure IoT Edge para Visual Studio Code fornece modelos de módulos IoT Edge construídos em linguagens de programação, incluindo C, C#, Java, Node.js e Python, bem como funções Azure em C#.

Para mais informações e para descarregar, consulte [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Além das extensões IoT Edge, poderá achar útil instalar extensões adicionais para o desenvolvimento. Por exemplo, pode utilizar [o Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerir as suas imagens, contentores e registos. Além disso, todas as principais línguas suportadas têm extensões para Visual Studio Code que podem ajudar quando você está desenvolvendo módulos.

#### <a name="prerequisites"></a>Pré-requisitos

Os modelos do módulo para algumas línguas e serviços têm pré-requisitos necessários para construir as pastas do projeto na sua máquina de desenvolvimento com o Código do Estúdio Visual.

| Modelo de módulo | Pré-requisito |
| --------------- | ------------ |
| Funções do Azure | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Kit de Desenvolvimento Java SE 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Definir a variável do ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Rio Yeoman](https://www.npmjs.com/package/yo) <li> [Gerador de módulo de Node.js Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Extensão Visual Studio 2017/2019

As ferramentas Azure IoT Edge para o Visual Studio fornecem um modelo de módulo IoT Edge construído em C# e C.

Para mais informações e para descarregar, consulte [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) ou [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Ferramenta IoT Edge dev

A ferramenta Azure IoT Edge dev simplifica o desenvolvimento do IoT Edge com habilidades de linha de comando. Esta ferramenta fornece comandos CLI para desenvolver, depurar e testar módulos. A ferramenta IoT Edge dev funciona com o seu sistema de desenvolvimento, quer tenha instalado manualmente as dependências da sua máquina ou esteja a utilizar o recipiente IoT Edge dev.

Para obter mais informações e para começar, consulte [a ferramenta IoT Edge dev wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Ferramentas de teste

Existem várias ferramentas de teste para ajudá-lo a simular dispositivos IoT Edge ou módulos de depurar de forma mais eficiente. A tabela a seguir mostra uma comparação de alto nível entre as ferramentas e, em seguida, as secções individuais descrevem cada ferramenta mais especificamente.

Apenas o tempo de execução IoT Edge é suportado para implementações de produção, mas as seguintes ferramentas permitem simular ou criar facilmente dispositivos IoT Edge para fins de desenvolvimento e teste. Estas ferramentas não são mutuamente exclusivas, mas podem trabalhar juntas para uma experiência de desenvolvimento completa.

| Ferramenta | Também conhecido como | Plataformas suportadas | Melhor para |
| ---- | ------------- | ------------------- | --------- |
| Ferramenta ioT EdgeHub dev  | iotedgehubdev | Windows, Linux, macOS | Simular um dispositivo para depurar módulos. |
| Recipiente IoT Edge dev | iotedgedev | Windows, Linux, macOS | Desenvolver sem instalar dependências. |
| Tempo de execução IoT Edge em um recipiente | iotedgec | Windows, Linux, macOS, ARM | Testes num dispositivo que pode não suportar o tempo de funcionaamento. |
| Recipiente de dispositivo IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Testar um cenário com muitos dispositivos IoT Edge em escala. |

### <a name="iot-edgehub-dev-tool"></a>Ferramenta ioT EdgeHub dev

A ferramenta Azure IoT EdgeHub dev proporciona uma experiência de desenvolvimento local e depuração. A ferramenta ajuda a iniciar módulos IoT Edge sem o tempo de funcionaamento IoT Edge para que possa criar, desenvolver, testar, executar e depurar módulos e soluções IoT Edge localmente. Não é preciso empurrar as imagens para um registo de contentores e implantá-las num dispositivo para testes.

A ferramenta IoT EdgeHub dev foi projetada para funcionar em conjunto com as extensões visual Studio e Visual Studio Code, bem como com a ferramenta IoT Edge dev. Suporta o desenvolvimento do loop interno, bem como os testes de loop externo, integrando-se também com as ferramentas DevOps.

Para mais informações e instalar, consulte [a ferramenta dev Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Recipiente IoT Edge dev

O recipiente Azure IoT Edge dev é um recipiente Docker que tem todas as dependências necessárias para o desenvolvimento do IoT Edge. Este recipiente facilita o início com qualquer idioma que queira desenvolver, incluindo C#, Python, Node.js e Java. Tudo o que precisa de instalar é um motor de contentores, como o Docker ou o Moby, para puxar o contentor para a sua máquina de desenvolvimento.

Para mais informações, consulte [o recipiente Azure IoT Edge dev](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>Tempo de execução IoT Edge em um recipiente

O tempo de funcionaamento do IoT Edge num recipiente proporciona um tempo de funcionaamento completo que leva a cadeia de ligação do seu dispositivo como uma variável ambiental. Este recipiente permite testar módulos e cenários IoT Edge num sistema que pode não suportar o tempo de funcionação de forma nativa, como o macOS. Quaisquer módulos que implementar serão iniciados fora do recipiente de tempo de execução. Se pretender que o tempo de funcionaamento e quaisquer módulos implantados existam dentro do mesmo recipiente, considere o recipiente do dispositivo IoT Edge.

Para obter mais informações, consulte [Running Azure IoT Edge num recipiente](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Recipiente de dispositivo IoT Edge

O recipiente do dispositivo IoT Edge é um dispositivo IoT Edge completo, pronto para ser lançado em qualquer máquina com motor de contentor. O recipiente do dispositivo inclui o tempo de funcionaamento do IoT Edge e o próprio motor de contentores. Cada instância do recipiente é um dispositivo IoT Edge totalmente funcional. O contentor do dispositivo suporta a depuragem remota de módulos, desde que exista uma rota de rede para o módulo. O recipiente do dispositivo é bom para criar rapidamente um grande número de dispositivos IoT Edge para testar cenários em escala ou Gasodutos Azure. Também suporta a implantação de kubernetes via leme.

Para obter mais informações, consulte [o recipiente do dispositivo Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Ferramentas DevOps

Quando estiver pronto para desenvolver soluções à escala para cenários de produção extensivos, aproveite os princípios modernos de DevOps, incluindo automação, monitorização e processos de engenharia de software simplificados. O IoT Edge tem extensões para suportar ferramentas DevOps, incluindo Azure DevOps, Azure DevOps Projects e Jenkins. Se pretender personalizar um gasoduto existente ou utilizar uma ferramenta DevOps diferente, como CircleCI ou TravisCI, pode fazê-lo com as funcionalidades CLI incluídas na ferramenta IoT Edge dev.

Para mais informações, orientação e exemplos, consulte as seguintes páginas:

* [Integração contínua e implantação contínua para Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Criar um pipeline CI/CD para IoT Edge com Azure DevOps Starter](how-to-devops-starter.md)
* [IoT Edge DevOps GitHub repo](https://github.com/toolboc/IoTEdge-DevOps)