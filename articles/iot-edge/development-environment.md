---
title: Ambiente de desenvolvimento azure IoT Edge [ ambiente de desenvolvimento da borda azul ] Microsoft Docs
description: Conheça os sistemas suportados e ferramentas de desenvolvimento de primeira parte que o ajudarão a criar módulos IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511098"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Prepare o seu ambiente de desenvolvimento e teste para ioT Edge

O Azure IoT Edge move a sua lógica de negócio existente para dispositivos que operam no limite. Para preparar as suas aplicações e cargas de trabalho para funcionar como [módulos IoT Edge,](iot-edge-modules.md)precisa construí-las como recipientes. Este artigo fornece orientações sobre como configurar o seu ambiente de desenvolvimento para que possa criar com sucesso uma solução IoT Edge. Assim que tiver o seu ambiente de desenvolvimento configurado, então poderá aprender a [desenvolver os seus próprios módulos IoT Edge.](module-development.md)

Em qualquer solução IoT Edge, existem pelo menos duas máquinas a considerar. Um deles é o dispositivo IoT Edge (ou dispositivos) em si, que executa o módulo IoT Edge. A outra é a máquina de desenvolvimento que usa para construir, testar e implementar módulos. Este artigo centra-se principalmente na máquina de desenvolvimento. Para efeitos de teste, as duas máquinas podem ser as mesmas. Pode executar ioT Edge na sua máquina de desenvolvimento e implementar módulos para a sua máquina de desenvolvimento.

## <a name="operating-system"></a>Sistema operativo

O Azure IoT Edge funciona num conjunto específico de [sistemas operativos suportados.](support.md) Para desenvolver o IoT Edge, pode utilizar a maioria dos sistemas operativos que podem funcionar um motor de contentores. O motor do contentor é um requisito na máquina de desenvolvimento para construir os seus módulos como recipientes e empurrá-los para um registo de contentores.

Se a sua máquina de desenvolvimento não conseguir executar o Azure IoT Edge, continue neste artigo para aprender sobre [ferramentas](#testing-tools) de teste que o ajudam a testar e depurar localmente.

O sistema operativo da sua máquina de desenvolvimento não tem de corresponder ao sistema operativo do seu dispositivo IoT Edge. No entanto, o sistema de funcionamento do contentor deve ser consistente entre a máquina de desenvolvimento e o dispositivo IoT Edge. Por exemplo, pode desenvolver módulos numa máquina do Windows e implantá-los num dispositivo Linux. A máquina Windows precisa de executar contentores Linux para construir os módulos para o dispositivo Linux.

## <a name="container-engine"></a>Motor de contentor

O conceito central do IoT Edge é que você pode implantar remotamente a sua lógica de negócio e nuvem para dispositivos, embalando-o em recipientes. Para construir contentores, precisa de um motor de contentor na sua máquina de desenvolvimento.

O único motor de contentor suportado para dispositivos IoT Edge em produção é o Moby. No entanto, qualquer motor de contentor compatível com a Iniciativa Open Container, como o Docker, é capaz de construir imagens de módulos IoT Edge.

## <a name="development-tools"></a>Ferramentas de programação

Tanto o Visual Studio como o Visual Studio Code têm extensões adicionais para ajudar a desenvolver soluções IoT Edge. Estas extensões fornecem modelos específicos da linguagem para ajudar a criar e implementar novos cenários de IoT Edge. As extensões Azure IoT Edge para Visual Studio e Visual Studio Code ajudam-no a codificar, construir, implementar e desinserm as suas soluções IoT Edge. Pode criar uma solução IoT Edge inteira que contenha vários módulos, e as extensões atualizam automaticamente um modelo de manifesto de implementação a cada nova adição de módulos. Com as extensões, também pode gerir os seus dispositivos IoT a partir de dentro do Visual Studio ou do Visual Studio Code. Desloque os módulos para um dispositivo, monitorize o estado e veja as mensagens à medida que chegam ao IoT Hub. Ambas as extensões utilizam a [ferramenta IoT EdgeHub dev](#iot-edgehub-dev-tool) para permitir o funcionamento local e a depuração de módulos na sua máquina de desenvolvimento.

Se preferir desenvolver-se com outros editores ou com o CLI, a ferramenta Azure IoT Edge dev fornece comandos para que possa desenvolver e testar a partir da linha de comando. Pode criar novos cenários IoT Edge, construir imagens de módulos, executar módulos num simulador e monitorizar mensagens enviadas para o IoT Hub.

### <a name="visual-studio-code-extension"></a>Extensão do Visual Studio Code

A extensão Azure IoT Edge para Visual Studio Code fornece modelos de módulos IoT Edge construídos em linguagens de programação, incluindo C, C#, Java, Node.js e Python, bem como funções Azure em C#.

Para mais informações e para descarregar, consulte [ferramentas Azure IoT para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Além das extensões IoT Edge, poderá ser útil instalar extensões adicionais para o desenvolvimento. Por exemplo, pode utilizar [o Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerir as suas imagens, contentores e registos. Além disso, todos os principais idiomas suportados têm extensões para Visual Studio Code que podem ajudar quando você está desenvolvendo módulos.

#### <a name="prerequisites"></a>Pré-requisitos

Os modelos de módulos para alguns idiomas e serviços têm pré-requisitos necessários para construir as pastas do projeto na sua máquina de desenvolvimento com Código de Estúdio Visual.

| Modelo de módulo | Pré-requisito |
| --------------- | ------------ |
| Funções do Azure | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Kit de Desenvolvimento Java SE 10](https://aka.ms/azure-jdks) <li> [Definir a variável ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Nó.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Gerador de módulos Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Pitão](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Extensão visual studio 2017/2019

As ferramentas Azure IoT Edge para O Estúdio Visual fornecem um modelo de módulo IoT Edge construído em C# e C.

Para mais informações e para descarregar, consulte [Azure IoT Edge Tools para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) ou [Azure IoT Edge Tools para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Ferramenta de dev IoT Edge

A ferramenta Azure IoT Edge dev simplifica o desenvolvimento do IoT Edge com capacidades de linha de comando. Esta ferramenta fornece comandos CLI para desenvolver módulos de depuração e teste. A ferramenta IoT Edge dev funciona com o seu sistema de desenvolvimento, quer tenha instalado manualmente as dependências da sua máquina ou esteja a utilizar o recipiente ioT Edge dev.

Para mais informações e para começar, consulte [ioT Edge dev tool wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Ferramentas de teste

Existem várias ferramentas de teste para o ajudar a simular dispositivos IoT Edge ou depurar módulos de forma mais eficiente. A tabela seguinte mostra uma comparação de alto nível entre as ferramentas, e então as secções individuais descrevem cada ferramenta mais especificamente.

Apenas o tempo de execução do IoT Edge é suportado para implementações de produção, mas as seguintes ferramentas permitem simular ou criar facilmente dispositivos IoT Edge para fins de desenvolvimento e teste. Estas ferramentas não são mutuamente exclusivas, mas podem trabalhar em conjunto para uma experiência de desenvolvimento completa.

| Ferramenta | Também conhecido como | Plataformas suportadas | Melhor para |
| ---- | ------------- | ------------------- | --------- |
| Ferramenta ioT EdgeHub dev  | iotedgehubdev | Windows, Linux, MacOS | Simular um dispositivo para depurar módulos. |
| Recipiente de v IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Desenvolver sem instalar dependências. |
| Tempo de corrido da Borda IoT em um recipiente | iotedgec | Windows, Linux, MacOS, ARM | Testar num dispositivo que pode não suportar o tempo de execução. |
| Recipiente de dispositivo IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testar um cenário com muitos dispositivos IoT Edge em escala. |

### <a name="iot-edgehub-dev-tool"></a>Ferramenta ioT EdgeHub dev

A ferramenta Azure IoT EdgeHub dev proporciona uma experiência de desenvolvimento local e depuração. A ferramenta ajuda a iniciar módulos IoT Edge sem o tempo de funcionação do IoT Edge para que possa criar, desenvolver, testar, executar e depurar módulos e soluções IoT Edge localmente. Não é preciso empurrar as imagens para um registo de contentores e implantá-las num dispositivo para testes.

A ferramenta IoT EdgeHub foi concebida para funcionar em conjunto com as extensões visual studio e visual studio code, bem como com a ferramenta IoT Edge dev. Suporta o desenvolvimento do loop interno, bem como os testes de loop externo, por isso integra-se também com as ferramentas DevOps.

Para mais informações e para instalar, consulte [a ferramenta Dev Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Recipiente de v IoT Edge

O recipiente de v Azure IoT Edge é um recipiente Docker que tem todas as dependências que você precisa para o desenvolvimento de IoT Edge. Este recipiente facilita o início com qualquer língua em que queira desenvolver, incluindo C#, Python, Node.js e Java. Tudo o que precisa instalar é um motor de contentor, como Docker ou Moby, para puxar o contentor para a sua máquina de desenvolvimento.

Para mais informações, consulte o [recipiente dev Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Tempo de corrido da Borda IoT em um recipiente

O tempo de execução do IoT Edge num recipiente proporciona um tempo de execução completo que toma a corda de ligação do dispositivo como uma variável ambiental. Este recipiente permite testar módulos e cenários IoT Edge num sistema que pode não suportar o tempo de funcionação de forma nativa, como o MacOS. Quaisquer módulos que implementar serão iniciados fora do recipiente de tempo de funcionação. Se pretender que o tempo de funcionação e quaisquer módulos implantados existam dentro do mesmo recipiente, considere o recipiente do dispositivo IoT Edge.

Para mais informações, consulte [Running Azure IoT Edge num recipiente](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Recipiente de dispositivo IoT Edge

O recipiente do dispositivo IoT Edge é um dispositivo IoT Edge completo, pronto a ser lançado em qualquer máquina com motor de contentor. O recipiente do dispositivo inclui o tempo de funcionação do IoT Edge e o próprio motor de contentores. Cada instância do recipiente é um dispositivo IoT Edge totalmente funcional. O contentor do dispositivo suporta depuração remota de módulos, desde que exista uma rota de rede para o módulo. O recipiente do dispositivo é bom para criar rapidamente um grande número de dispositivos IoT Edge para testar cenários em escala ou pipelines Azure. Também apoia a implantação de kubernetes através do leme.

Para mais informações, consulte o recipiente do [dispositivo Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Ferramentas DevOps

Quando estiver pronto para desenvolver soluções à escala para cenários de produção extensivos, aproveite os princípios modernos da DevOps, incluindo automação, monitorização e processos de engenharia de software simplificados. O IoT Edge tem extensões para apoiar ferramentas DevOps, incluindo Azure DevOps, Azure DevOps Projects e Jenkins. Se quiser personalizar um pipeline existente ou utilizar uma ferramenta de DevOps diferente como a CircleCI ou travisCI, pode fazê-lo com as funcionalidades CLI incluídas na ferramenta IoT Edge dev.

Para obter mais informações, orientação e exemplos, consulte as seguintes páginas:

* [Integração contínua e implantação contínua para Azure IoT Edge](how-to-ci-cd.md)
* [Crie um oleoduto CI/CD para IoT Edge com projetos Azure DevOps](how-to-devops-project.md)
* [Plugin Azure IoT Edge Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [Repo IoT Edge DevOps GitHub](https://github.com/toolboc/IoTEdge-DevOps)
