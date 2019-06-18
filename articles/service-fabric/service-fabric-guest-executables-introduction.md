---
title: Implementar um executável existente no Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre como empacotar um aplicativo existente como convidado executável, para que possa ser implementado num cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946676"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implementar um executável existente no Service Fabric
Pode executar qualquer tipo de código, como o C++, Java ou node. js no Azure Service Fabric como um serviço. Service Fabric se refere a esses tipos de serviços como executáveis convidados.

Executáveis convidados são tratadas pelo Service Fabric, como serviços sem estado. Como resultado, eles são colocados em nós num cluster, com base na disponibilidade e de outras métricas. Este artigo descreve como empacotar e implementar um executável de convidado para um cluster do Service Fabric, utilizando o Visual Studio ou um utilitário de linha de comandos.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios da execução de um convidado executável no Service Fabric
Existem diversas vantagens em executar um convidado executável num cluster do Service Fabric:

* Elevada disponibilidade. Aplicações que são executadas no Service Fabric são feitas elevada disponibilidade. Service Fabric garante que instâncias de uma aplicação estão em execução.
* Monitorização de estado de funcionamento. Monitorização de estado de funcionamento do Service Fabric Deteta se um aplicativo está em execução e fornece informações de diagnóstico se houver uma falha.   
* Gerenciamento de ciclo de vida de aplicativos. Além de fornecer atualizações sem tempo de inatividade, Service Fabric proporciona Reversão automática para a versão anterior se ocorrer um evento de estado de funcionamento incorreto comunicado durante uma atualização.    
* Densidade. Pode executar vários aplicativos num cluster, o que elimina a necessidade de cada aplicativo seja executado em seu próprio hardware.
* Capacidade de Deteção: Com o REST é possível chamar o serviço de nomenclatura de recursos de infraestrutura do serviço de mensagens em fila para localizar outros serviços do cluster. 

## <a name="samples"></a>Exemplos
* [Exemplo para o empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomenclatura com REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Descrição geral da aplicação e os ficheiros de manifesto de serviço
Como parte da implementação de um executável convidado, é útil para entender o modelo de empacotamento e a implantação de Service Fabric, conforme descrito em [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento do Service Fabric baseia-se em dois arquivos XML: os manifestos de aplicações e serviços. A definição de esquema para os ficheiros applicationmanifest. XML e servicemanifest. xml é instalada com o SDK do Service Fabric em *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** o manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que compõem este e outros parâmetros que são utilizados para definir como um ou mais serviços devem ser implementados, como o número de instâncias.

  No Service Fabric, um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma única unidade em que são geridos os potenciais falhas e reversões potenciais. Service Fabric garante que o processo de atualização é concluída com êxito, ou, se a atualização falhar, não deixa o aplicativo num Estado desconhecido ou instável.
* **Manifesto do serviço** manifesto do serviço descreve os componentes de um serviço. Ele inclui dados, como o nome e tipo de serviço e seu código e a configuração. O manifesto de serviço também inclui alguns parâmetros adicionais que podem ser utilizados para configurar o serviço após sua implantação.

## <a name="application-package-file-structure"></a>Estrutura de ficheiros do pacote de aplicação
Para implementar uma aplicação no Service Fabric, o aplicativo deve seguir uma estrutura de diretórios predefinidos. Segue-se um exemplo dessa estrutura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o ficheiro Applicationmanifest XML que define o aplicativo. Um subdiretório para cada serviço incluído na aplicação é utilizado para conter todos os artefatos que requer que o serviço. Esses subdiretórios são o servicemanifest. XML e, normalmente, o seguinte:

* *Código*. Este diretório contém o código de serviço.
* *Config*. Esse diretório contém um arquivo Settings XML (e outros arquivos, se necessário) que o serviço pode acessar no tempo de execução para obter as definições de configuração específicos.
* *Dados*. Este é um diretório adicional para armazenar dados de locais adicionais que poderá ter o serviço. Dados devem ser utilizados para armazenar apenas os dados efémeros. Service Fabric não copie ou replicar as alterações para o diretório de dados se o serviço precisa de ser realocada (por exemplo, durante a ativação pós-falha).

> [!NOTE]
> Não precisa de criar o `config` e `data` diretórios se não precisar delas.
>
>

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para informações relacionadas e tarefas.
* [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
* [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
* [Criar a sua primeira aplicação executável de convidado com o Visual Studio](quickstart-guest-app.md)
* [Exemplo para o empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo uma ligação para a versão de pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomenclatura com REST](https://github.com/Azure-Samples/service-fabric-containers)

