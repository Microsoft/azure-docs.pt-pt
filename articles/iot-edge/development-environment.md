---
title: Ambiente de desenvolvimento do Azure IoT Edge | Documentos da Microsoft
description: Saiba mais sobre os sistemas suportados e as ferramentas de desenvolvimento originais que o ajudará a que criar módulos do IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3d1fb393138aeb590bc0645d6abe93652a52a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247558"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Preparar o ambiente de teste e desenvolvimento para o IoT Edge

O Azure IoT Edge move a lógica de negócios existente para funcionar no limite de dispositivos. Para preparar as suas aplicações e cargas de trabalho para ser executado como [módulos do IoT Edge](iot-edge-modules.md), terá de criá-los como contentores. Este artigo fornece orientações sobre como configurar o ambiente de desenvolvimento, para que pode criar com êxito uma solução de IoT Edge. Depois de configurar o ambiente de desenvolvimento, em seguida, pode aprender como [desenvolver seus próprios módulos do IoT Edge](module-development.md).

Em qualquer solução de IoT Edge, existem, pelo menos, duas máquinas a serem considerados. Um é o dispositivo IoT Edge (ou dispositivos) em si, que executa o módulo do IoT Edge. O outro é a máquina de desenvolvimento que utiliza para criar, testar e implementar módulos. Este artigo se concentra principalmente na máquina de desenvolvimento. Para fins de teste, as duas máquinas podem ser a mesma. Pode executar o IoT Edge no computador de desenvolvimento e implementar módulos no mesmo.

## <a name="operating-system"></a>Sistema operativo

O Azure IoT Edge é executado num conjunto específico de [sistemas operativos suportados](support.md). Para o desenvolvimento para o IoT Edge, pode utilizar a maioria dos sistemas operativos que pode ser executado um mecanismo de contentor. O mecanismo de contentor é um requisito na máquina de desenvolvimento para compilar seus módulos como contentores e emiti-las para um registo de contentor. 

Se o computador de desenvolvimento não é possível executar o Azure IoT Edge, continue neste artigo para saber mais sobre [ferramentas de teste](#testing-tools) que ajudá-lo a testar e depurar localmente. 

O sistema operativo do computador de desenvolvimento não precisa coincidir com o sistema operativo do seu dispositivo IoT Edge. No entanto, o sistema operativo do contentor tem de ser consistente entre o computador de desenvolvimento e o dispositivo IoT Edge. Por exemplo, pode desenvolver módulos numa máquina Windows e implementá-las num dispositivo de Linux. A máquina do Windows tem de executar contentores do Linux para criar os módulos para o dispositivo de Linux. 

## <a name="container-engine"></a>Motor de contentor

O conceito central do IoT Edge é que pode implementar remotamente a sua lógica de negócios e na cloud para dispositivos ao empacotá-lo em contentores. Para criar contentores, terá um mecanismo de contentor no seu computador de desenvolvimento. 

O mecanismo de contentor suportados apenas para dispositivos do IoT Edge na produção é Moby. No entanto, qualquer mecanismo de contentor compatível com a iniciativa Open do contentor, como o Docker, é capaz de criação de imagens do módulo de IoT Edge. 

## <a name="development-tools"></a>Ferramentas de programação

O Visual Studio e o Visual Studio Code tenham extensões de suplemento para o ajudar a desenvolver soluções de IoT Edge. Essas extensões fornecem modelos de idioma específico para o ajudar a criar e implementar novos cenários de IoT Edge. As extensões do Azure IoT Edge para Visual Studio e Visual Studio Code ajuda de código, criar, implementar e depurar as suas soluções de IoT Edge. Pode criar uma solução de IoT Edge inteira, que contém vários módulos e as extensões atualizam automaticamente um modelo de manifesto de implantação com cada nova adição de módulo. Com as extensões, também pode gerenciar seus dispositivos IoT a partir de dentro do Visual Studio ou o Visual Studio Code. Implementar módulos num dispositivo, monitorizar o estado e ver as mensagens quando chegam ao IoT Hub. Ambas as extensões de utilizar o [ferramenta de desenvolvimento IoT EdgeHub](#iot-edgehub-dev-tool) para ativar o local em execução e depuração de módulos no computador de desenvolvimento. 

Se desenvolver com outros editores ou a partir da CLI, a ferramenta de desenvolvimento do Azure IoT Edge fornece comandos para que possam desenvolver e testar a partir da linha de comando. Pode criar novos do IoT Edge cenários, criar imagens de módulo, executar os módulos em simulador e monitorizar as mensagens enviadas ao IoT Hub. 

### <a name="visual-studio-code-extension"></a>Extensão do Visual Studio Code

A extensão Azure IoT Edge para Visual Studio Code fornece do IoT Edge modelos de módulo criados sobre a programação de linguagens, incluindo C, C#, Java, node. js e Python, bem como as funções do Azure no C#. 

Para obter mais informações e para transferir, veja [ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Além de extensões do IoT Edge, talvez seja útil instalar extensões adicionais para o desenvolvimento. Por exemplo, pode usar [suporte do Docker para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para gerir imagens de contentores e registos. Além disso, todos os idiomas suportados principais têm extensões para Visual Studio Code, que podem ajudar quando estiver a desenvolver módulos. 

#### <a name="prerequisites"></a>Pré-requisitos

Os modelos de módulo para alguns idiomas e serviços têm os pré-requisitos necessários criar as pastas de projeto no computador de desenvolvimento com o Visual Studio Code.

| Modelo de módulo | Pré-requisito |
| --------------- | ------------ |
| Funções do Azure | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Kit de desenvolvimento Sudeste do Java 10](https://aka.ms/azure-jdks) <li> [Defina a variável de ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Gerador de módulo de node. js do IoT Edge do Azure](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Extensão do Visual Studio 2017

As ferramentas do Azure IoT Edge para Visual Studio fornecem um IoT Edge modelo de módulo criado C#. 

Para obter mais informações e para transferir, veja [ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

### <a name="iot-edge-dev-tool"></a>Ferramenta de programador do IoT Edge

A ferramenta de desenvolvimento do Azure IoT Edge simplifica o desenvolvimento de IoT Edge com capacidades de linha de comandos. Essa ferramenta fornece comandos da CLI para desenvolver, depurar e testar módulos. A ferramenta de desenvolvimento do IoT Edge funciona com o seu sistema de desenvolvimento, se instalou manualmente as dependências no seu computador ou estiver a utilizar o contentor de desenvolvimento do IoT Edge. 

Para obter mais informações e para começar a utilizar, veja [wiki de ferramenta de desenvolvimento do IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Ferramentas de teste

Existem várias ferramentas de teste para ajudar a simular dispositivos IoT Edge ou depurar módulos com mais eficiência. A tabela seguinte mostra uma comparação entre as ferramentas de alto nível e, em seguida, as secções individuais descrevem cada ferramenta mais especificamente. 

Apenas o runtime do IoT Edge é suportado para implementações de produção, mas as seguintes ferramentas permitem simular ou criar facilmente dispositivos para fins de testes e de desenvolvimento do IoT Edge. Essas ferramentas não são mutuamente exclusivas, mas podem trabalhar em conjunto para uma experiência de desenvolvimento completo. 

| Ferramenta | Também conhecido como | Plataformas suportadas | Melhor para |
| ---- | ------------- | ------------------- | --------- |
| Ferramenta de desenvolvimento IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simular um dispositivo para depurar módulos. |
| Contentor de desenvolvimento do IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Desenvolvendo sem instalação de dependências. |
| Runtime do IoT Edge num contentor | iotedgec | Windows, Linux, MacOS, ARM | Testes num dispositivo que pode não suportar o tempo de execução. |
| Contentor de dispositivo do IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Um cenário de teste com vários dispositivos de IoT Edge em escala. |

### <a name="iot-edgehub-dev-tool"></a>Ferramenta de desenvolvimento IoT EdgeHub

A ferramenta de desenvolvimento do Azure IoT EdgeHub fornece uma experiência de desenvolvimento e depuração local. O início de ajuda a ferramenta, módulos do IoT Edge sem o runtime do IoT Edge, de modo a que possa criar, desenvolveram, testar, executarem e depurar os módulos do IoT Edge e soluções localmente. Não precisa enviar imagens para um registo de contentores e implementá-las a um dispositivo para fins de teste.

A ferramenta de desenvolvimento IoT EdgeHub foi concebida para trabalhar em conjunto com as extensões do Visual Studio e Visual Studio Code, bem como com a ferramenta de desenvolvimento do IoT Edge. Ele suporta o desenvolvimento de ciclo interno, bem como testes de loop externo, então, integra-se com as ferramentas de DevOps muito. 

Para obter mais informações e para instalar, veja [ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Contentor de desenvolvimento do IoT Edge

O contentor de desenvolvimento do Azure IoT Edge é um contentor de Docker que tem todas as dependências que precisa para desenvolvimento do IoT Edge. Este contentor torna mais fácil começar a trabalhar com qualquer linguagem que pretende desenvolver, incluindo C#, Python, node. js e Java. Tudo o que precisa para instalar é um motor de contentor, como o Docker ou Moby, para extrair o contentor para o seu computador de desenvolvimento. 

Para obter mais informações, consulte [contentor de desenvolvimento do Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Runtime do IoT Edge num contentor

O runtime do IoT Edge num contentor fornece um tempo de execução completo que usa a cadeia de ligação do dispositivo como uma variável de ambiente. Este contentor permite-lhe testar módulos do IoT Edge e os cenários num sistema que podem não suportar o tempo de execução de forma nativa, como o MacOS. Quaisquer módulos que implementar serão iniciados fora do contentor de tempo de execução. Se pretender que o tempo de execução e quaisquer módulos implementados como existentes no mesmo contentor, considere em vez disso, o contentor de dispositivo do IoT Edge.

Para obter mais informações, consulte [a executar o Azure IoT Edge num contentor](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Contentor de dispositivo do IoT Edge

O contentor de dispositivo do IoT Edge é um dispositivo de IoT Edge concluído, pronto para ser iniciada com qualquer máquina com um motor de contentor. O contentor de dispositivo inclui o tempo de execução do IoT Edge e um mecanismo de contentor em si. Cada instância do contentor é um dispositivo de IoT Edge de autoprovisionamento totalmente funcional. O contentor de dispositivo oferece suporte a depuração remota de módulos, desde que exista uma rota de rede para o módulo. O contentor de dispositivo é bom para criar rapidamente um grande número de dispositivos do IoT Edge para testar cenários de à escala ou pipelines de DevOps. Também suporta a implementação no kubernetes por meio do helm. 

Para obter mais informações, consulte [contentor de dispositivo do Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Ferramentas DevOps

Quando estiver pronto para desenvolver soluções à escala para cenários de produção extensa, tire partido dos princípios de DevOps modernos incluindo automação, monitorização e os processos de engenharia de software simplificada. IoT Edge tem extensões para oferecer suporte a ferramentas de DevOps, incluindo Azure DevOps, projetos de DevOps do Azure e o Jenkins. Se pretender personalizar um pipeline existente ou usar uma ferramenta de DevOps diferente como CircleCI ou o TravisCI, pode fazê-lo com os recursos CLI incluídos na ferramenta de desenvolvimento do IoT Edge.

Para obter mais informações, diretrizes e exemplos, consulte as seguintes páginas:
* [Integração contínua e implementação contínua para o Azure IoT Edge](how-to-ci-cd.md)
* [Criar um pipeline CI/CD para o IoT Edge com projetos de DevOps do Azure](how-to-devops-project.md)
* [Plug-in Jenkins do IoT Edge do Azure](https://plugins.jenkins.io/azure-iot-edge)
* [Repositório do GitHub de DevOps do IoT Edge](https://github.com/toolboc/IoTEdge-DevOps)
