---
title: Ambiente de desenvolvimento de Azure IoT Edge | Microsoft Docs
description: Saiba mais sobre os sistemas com suporte e as ferramentas de desenvolvimento de terceiros que o ajudarão a criar módulos de IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ddfa93328fb3533a937cc7f0d81482b66275faf3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848953"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Prepare seu ambiente de desenvolvimento e teste para IoT Edge

Azure IoT Edge move a lógica de negócios existente para dispositivos que operam na borda. Para preparar seus aplicativos e cargas de trabalho para serem executados como [IOT Edge módulos](iot-edge-modules.md), você precisa compilá-los como contêineres. Este artigo fornece orientação sobre como configurar seu ambiente de desenvolvimento para que você possa criar com êxito uma solução de IoT Edge. Depois de configurar seu ambiente de desenvolvimento, você pode aprender a [desenvolver seus próprios módulos de IOT Edge](module-development.md).

Em qualquer solução de IoT Edge, há pelo menos duas máquinas a serem consideradas. Uma é a IoT Edge dispositivo (ou dispositivos) em si, que executa o módulo IoT Edge. A outra é a máquina de desenvolvimento que você usa para compilar, testar e implantar módulos. Este artigo se concentra principalmente no computador de desenvolvimento. Para fins de teste, os dois computadores podem ser os mesmos. Você pode executar IoT Edge em seu computador de desenvolvimento e implantar módulos nele.

## <a name="operating-system"></a>Sistema operativo

Azure IoT Edge é executado em um conjunto específico de [sistemas operacionais com suporte](support.md). Para o desenvolvimento para IoT Edge, você pode usar a maioria dos sistemas operacionais que podem executar um mecanismo de contêiner. O mecanismo de contêiner é um requisito no computador de desenvolvimento para criar seus módulos como contêineres e enviá-los por push para um registro de contêiner. 

Se o computador de desenvolvimento não puder executar Azure IoT Edge, continue neste artigo para saber mais sobre as [ferramentas de teste](#testing-tools) que ajudam você a testar e depurar localmente. 

O sistema operacional do seu computador de desenvolvimento não precisa corresponder ao sistema operacional do seu dispositivo de IoT Edge. No entanto, o sistema operacional do contêiner deve ser consistente entre o computador de desenvolvimento e o dispositivo de IoT Edge. Por exemplo, você pode desenvolver módulos em um computador Windows e implantá-los em um dispositivo Linux. O computador Windows precisa executar contêineres do Linux para criar os módulos para o dispositivo Linux. 

## <a name="container-engine"></a>Mecanismo de contêiner

O conceito central de IoT Edge é que você pode implantar remotamente sua lógica de negócios e de nuvem para dispositivos empacotando-o em contêineres. Para criar contêineres, você precisa de um mecanismo de contêiner em seu computador de desenvolvimento. 

O único mecanismo de contêiner com suporte para dispositivos IoT Edge em produção é Moby. No entanto, qualquer mecanismo de contêiner compatível com a iniciativa Open container, como Docker, é capaz de criar IoT Edge imagens de módulo. 

## <a name="development-tools"></a>Ferramentas de programação

O Visual Studio e o Visual Studio Code têm extensões complementares para ajudar a desenvolver soluções de IoT Edge. Essas extensões fornecem modelos específicos de idioma para ajudar a criar e implantar novos cenários de IoT Edge. As extensões de Azure IoT Edge para Visual Studio e Visual Studio Code ajudam a codificar, criar, implantar e depurar suas soluções de IoT Edge. Você pode criar uma solução de IoT Edge inteira que contém vários módulos, e as extensões atualizam automaticamente um modelo de manifesto de implantação com cada adição de módulo nova. Com as extensões, você também pode gerenciar seus dispositivos IoT no Visual Studio ou Visual Studio Code. Implantar módulos em um dispositivo, monitorar o status e exibir mensagens à medida que eles chegam no Hub IoT. Ambas as extensões usam a [ferramenta de desenvolvimento de IOT EdgeHub](#iot-edgehub-dev-tool) para habilitar a execução local e a depuração de módulos em seu computador de desenvolvimento também. 

Se você preferir desenvolver com outros editores ou a partir da CLI, a ferramenta de desenvolvimento de Azure IoT Edge fornecerá comandos para que você possa desenvolver e testar a partir da linha de comando. Você pode criar novos cenários de IoT Edge, compilar imagens de módulo, executar módulos em um simulador e monitorar mensagens enviadas ao Hub IoT. 

### <a name="visual-studio-code-extension"></a>Extensão do Visual Studio Code

A extensão de Azure IoT Edge para Visual Studio Code fornece modelos de módulo IoT Edge criados em linguagens de C#programação, incluindo C,, Java, Node. js e Python, bem C#como Azure Functions no. 

Para obter mais informações e baixar, consulte [ferramentas de IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Além das extensões de IoT Edge, talvez você ache útil instalar extensões adicionais para o desenvolvimento. Por exemplo, você pode usar o [suporte do Docker para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) gerenciar suas imagens, contêineres e registros. Além disso, todos os principais idiomas com suporte têm extensões para Visual Studio Code que podem ajudar quando você está desenvolvendo módulos. 

#### <a name="prerequisites"></a>Pré-requisitos

Os modelos de módulo para alguns idiomas e serviços têm pré-requisitos que são necessários para criar as pastas de projeto em seu computador de desenvolvimento com Visual Studio Code.

| Modelo de módulo | Pré-requisito |
| --------------- | ------------ |
| Funções do Azure | [SDK do .NET Core 2,1](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [SDK do .NET Core 2,1](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Definir a variável de ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge gerador de módulo node. js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pontos](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Extensão do Visual Studio 2017/2019

As ferramentas de Azure IoT Edge para o Visual Studio fornecem um modelo de módulo C# de IOT Edge baseado em e C. 

Para obter mais informações e baixar, consulte [Azure IOT Edge Tools for visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) ou [Azure IOT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Ferramenta de desenvolvimento de IoT Edge

A ferramenta de desenvolvimento de Azure IoT Edge simplifica IoT Edge desenvolvimento com habilidades de linha de comando. Essa ferramenta fornece comandos da CLI para desenvolver, depurar e testar módulos. A ferramenta de desenvolvimento de IoT Edge funciona com seu sistema de desenvolvimento, independentemente de você ter instalado manualmente as dependências em seu computador ou usando o contêiner IoT Edge dev. 

Para obter mais informações e começar, consulte [IOT Edge dev Tool wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Ferramentas de teste

Existem várias ferramentas de teste para ajudá-lo a simular IoT Edge dispositivos ou módulos de depuração com mais eficiência. A tabela a seguir mostra uma comparação de alto nível entre as ferramentas e, em seguida, seções individuais descrevem cada ferramenta mais especificamente. 

Somente o tempo de execução de IoT Edge tem suporte para implantações de produção, mas as ferramentas a seguir permitem simular ou criar facilmente IoT Edge dispositivos para fins de desenvolvimento e teste. Essas ferramentas não são mutuamente exclusivas, mas podem trabalhar juntas para uma experiência de desenvolvimento completa. 

| Ferramenta | Também conhecido como | Plataformas suportadas | Melhor para |
| ---- | ------------- | ------------------- | --------- |
| Ferramenta de desenvolvimento de IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simulando um dispositivo para depurar módulos. |
| Contêiner de desenvolvimento de IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Desenvolvendo sem a instalação de dependências. |
| IoT Edge tempo de execução em um contêiner | iotedgec | Windows, Linux, MacOS, ARM | Teste em um dispositivo que pode não dar suporte ao tempo de execução. |
| IoT Edge contêiner de dispositivo | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Testando um cenário com muitos dispositivos IoT Edge em escala. |

### <a name="iot-edgehub-dev-tool"></a>Ferramenta de desenvolvimento de IoT EdgeHub

A ferramenta de desenvolvimento do Azure IoT EdgeHub fornece uma experiência local de desenvolvimento e depuração. A ferramenta ajuda a iniciar módulos de IoT Edge sem o tempo de execução do IoT Edge para que você possa criar, desenvolver, testar, executar e depurar IoT Edge módulos e soluções localmente. Você não precisa enviar imagens por push a um registro de contêiner e implantá-las em um dispositivo para teste.

A ferramenta de desenvolvimento do IoT EdgeHub foi projetada para funcionar em conjunto com as extensões do Visual Studio e do Visual Studio Code, bem como com a ferramenta de desenvolvimento de IoT Edge. Ele dá suporte ao desenvolvimento de loop interno, bem como ao teste de loop externo, portanto, integra-se também às ferramentas DevOps. 

Para obter mais informações e instalar, consulte [ferramenta de desenvolvimento do Azure IOT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Contêiner de desenvolvimento de IoT Edge

O contêiner de desenvolvimento de Azure IoT Edge é um contêiner do Docker que tem todas as dependências de que você precisa para o desenvolvimento de IoT Edge. Esse contêiner facilita a introdução a qualquer linguagem que você deseja desenvolver, incluindo C#, Python, Node. js e Java. Tudo o que você precisa instalar é um mecanismo de contêiner, como Docker ou Moby, para efetuar pull do contêiner para seu computador de desenvolvimento. 

Para obter mais informações, consulte [Azure IOT Edge contêiner de desenvolvimento](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge tempo de execução em um contêiner

O tempo de execução de IoT Edge em um contêiner fornece um tempo de execução completo que usa a cadeia de conexão do dispositivo como uma variável de ambiente. Esse contêiner permite que você teste IoT Edge módulos e cenários em um sistema que talvez não ofereçam suporte ao tempo de execução nativamente, como o MacOS. Todos os módulos que você implantar serão iniciados fora do contêiner de tempo de execução. Se você quiser que o tempo de execução e os módulos implantados existam dentro do mesmo contêiner, considere o IoT Edge contêiner de dispositivo.

Para obter mais informações, consulte [executando Azure IOT Edge em um contêiner](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge contêiner de dispositivo

O contêiner de dispositivo IoT Edge é um dispositivo de IoT Edge completo, pronto para ser iniciado em qualquer computador com um mecanismo de contêiner. O contêiner do dispositivo inclui o tempo de execução de IoT Edge e um próprio mecanismo de contêiner. Cada instância do contêiner é um dispositivo de IoT Edge de autoprovisionamento totalmente funcional. O contêiner do dispositivo dá suporte à depuração remota de módulos, desde que haja uma rota de rede para o módulo. O contêiner do dispositivo é bom para criar rapidamente grandes números de dispositivos IoT Edge para testar cenários em escala ou Azure Pipelines. Ele também dá suporte à implantação em kubernetes por meio de Helm. 

Para obter mais informações, consulte [Azure IOT Edge contêiner de dispositivo](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Ferramentas DevOps

Quando você estiver pronto para desenvolver soluções em escala para cenários de produção extensivos, aproveite os princípios modernos de DevOps, incluindo automação, monitoramento e processos de engenharia de software simplificados. IoT Edge tem extensões para dar suporte a ferramentas de DevOps, incluindo o Azure DevOps, o Azure DevOps Projects e o Jenkins. Se você quiser personalizar um pipeline existente ou usar uma ferramenta de DevOps diferente, como CircleCI ou TravisCI, poderá fazer isso com os recursos da CLI incluídos na ferramenta de desenvolvimento de IoT Edge.

Para obter mais informações, diretrizes e exemplos, consulte as seguintes páginas:
* [Integração contínua e implantação contínua para Azure IoT Edge](how-to-ci-cd.md)
* [Criar um pipeline de CI/CD para IoT Edge com Azure DevOps Projects](how-to-devops-project.md)
* [Azure IoT Edge plug-in Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [Repositório GitHub IoT Edge DevOps](https://github.com/toolboc/IoTEdge-DevOps)
