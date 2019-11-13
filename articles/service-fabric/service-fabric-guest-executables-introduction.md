---
title: Empacotar um executável existente no Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre como empacotar um aplicativo existente como um executável convidado, para que ele possa ser implantado em um Cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: atsenthi
ms.openlocfilehash: bd6984db67a8a7b9c38988558ada51e12d337f52
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013265"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implantar um executável existente no Service Fabric
Você pode executar qualquer tipo de código, como node. js, Java ou C++ no Azure Service Fabric como um serviço. Service Fabric se refere a esses tipos de serviços como executáveis convidados.

Os executáveis convidados são tratados por Service Fabric como serviços sem estado. Como resultado, eles são colocados em nós em um cluster, com base na disponibilidade e outras métricas. Este artigo descreve como empacotar e implantar um executável convidado em um Cluster Service Fabric, usando o Visual Studio ou um utilitário de linha de comando.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios da execução de um executável convidado no Service Fabric
Há várias vantagens em executar um executável convidado em um Cluster Service Fabric:

* Elevada disponibilidade. Os aplicativos executados no Service Fabric se tornam altamente disponíveis. Service Fabric garante que as instâncias de um aplicativo estejam em execução.
* Monitoramento de integridade. Service Fabric monitoramento de integridade detecta se um aplicativo está em execução e fornece informações de diagnóstico se houver uma falha.   
* Gerenciamento do ciclo de vida do aplicativo. Além de fornecer atualizações sem tempo de inatividade, Service Fabric fornece Reversão automática para a versão anterior se houver um evento de integridade insatisfatório relatado durante uma atualização.    
* Dens. Você pode executar vários aplicativos em um cluster, o que elimina a necessidade de cada aplicativo ser executado em seu próprio hardware.
* Descoberta: usando o REST, você pode chamar o serviço de nomenclatura de Service Fabric para localizar outros serviços no cluster. 

## <a name="samples"></a>Amostras
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e NodeJS) se comunicando por meio do serviço de cadastramento usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral dos arquivos de manifesto do aplicativo e do serviço
Como parte da implantação de um executável convidado, é útil entender o modelo de empacotamento e implantação Service Fabric conforme descrito em [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento Service Fabric se baseia em dois arquivos XML: os manifestos de aplicativo e serviço. A definição de esquema para os arquivos ApplicationManifest. xml e manifest. xml é instalada com o SDK do Service Fabric em *c:\Arquivos de Programas\microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** O manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que o compõem e outros parâmetros que são usados para definir como um ou mais serviços devem ser implantados, como o número de instâncias.

  No Service Fabric, um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma única unidade onde possíveis falhas e reversões potenciais são gerenciadas. Service Fabric garante que o processo de atualização seja bem-sucedido ou, se a atualização falhar, não deixará o aplicativo em um estado desconhecido ou instável.
* **Manifesto do serviço** O manifesto do serviço descreve os componentes de um serviço. Ele inclui dados, como o nome e o tipo de serviço, e seu código e configuração. O manifesto do serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço quando ele é implantado.

## <a name="application-package-file-structure"></a>Estrutura do arquivo do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, o aplicativo deve seguir uma estrutura de diretório predefinida. Veja a seguir um exemplo dessa estrutura.

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

O ApplicationPackageRoot contém o arquivo ApplicationManifest. XML que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos que o serviço requer. Esses subdiretórios são o manifest. xml e, normalmente, o seguinte:

* *Código*. Esse diretório contém o código do serviço.
* *Configuração*. Esse diretório contém um arquivo Settings. XML (e outros arquivos, se necessário) que o serviço pode acessar em tempo de execução para recuperar definições de configuração específicas.
* *Dados*. Esse é um diretório adicional para armazenar dados locais adicionais que podem ser necessários para o serviço. Os dados devem ser usados para armazenar somente dados efêmeros. Service Fabric não copiar ou replicar as alterações no diretório de dados se o serviço precisar ser realocado (por exemplo, durante o failover).

> [!NOTE]
> Você não precisa criar os diretórios `config` e `data` se não precisar deles.
>
>

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos a seguir para obter informações e tarefas relacionadas.
* [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
* [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
* [Criar seu primeiro aplicativo executável convidado usando o Visual Studio](quickstart-guest-app.md)
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo um link para o pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois executáveis convidados (C# e NodeJS) se comunicando por meio do serviço de cadastramento usando REST](https://github.com/Azure-Samples/service-fabric-containers)

