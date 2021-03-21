---
title: Embale um tecido de serviço Azure existente
description: Aprenda sobre a embalagem de uma aplicação existente como um hóspede executável, para que possa ser implantado em um cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017751"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implementar um executável existente para o tecido de serviço
Pode executar qualquer tipo de código, como Node.js, Java ou C++ em Azure Service Fabric como um serviço. O Service Fabric refere-se a este tipo de serviços como executáveis de hóspedes.

Os executáveis de hóspedes são tratados pela Service Fabric como serviços apátridas. Como resultado, são colocados em nós num cluster, com base na disponibilidade e outras métricas. Este artigo descreve como embalar e implementar um hóspede executável para um cluster de Tecido de Serviço, usando o Visual Studio ou um utilitário de linha de comando.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios de executar um hóspede executável em Tecido de Serviço
Existem várias vantagens em executar um hóspede executável num cluster de Tecido de Serviço:

* Elevada disponibilidade. As aplicações que funcionam em Service Fabric são altamente disponibilizadas. A Service Fabric garante que os casos de uma aplicação estão em execução.
* Monitorização do estado de funcionamento. A monitorização de saúde do Tecido de Serviço deteta se uma aplicação está em execução e fornece informações de diagnóstico em caso de falha.   
* Gestão do ciclo de vida da aplicação. Além de fornecer upgrades sem tempo de inatividade, o Service Fabric fornece retorno automático para a versão anterior se houver um mau evento de saúde relatado durante uma atualização.    
* A densidade. Pode executar várias aplicações num cluster, o que elimina a necessidade de cada aplicação funcionar no seu próprio hardware.
* Descoberta: Utilizar o REST pode ligar para o serviço de nomeação de tecido de serviço para encontrar outros serviços no cluster. 

## <a name="samples"></a>Amostras
* [Amostra para embalagem e implantação de um hóspede executável](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Amostra de dois executáveis de hóspedes (C# e nodejs) comunicando através do serviço Naming usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral dos ficheiros manifestos de aplicação e serviço
Como parte da implementação de um hóspede executável, é útil entender a embalagem e o modelo de implementação do Service Fabric como descrito no [modelo de aplicação.](service-fabric-application-model.md) O modelo de embalagem Service Fabric baseia-se em dois ficheiros XML: a aplicação e os manifestos de serviço. A definição de esquema para os ficheiros ApplicationManifest.xml e ServiceManifest.xml é instalada com o Service Fabric SDK em *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto de aplicação** O manifesto de aplicação é usado para descrever a aplicação. Ele enumera os serviços que o compõem, e outros parâmetros que são usados para definir como um ou mais serviços devem ser implantados, como o número de casos.

  No Tecido de Serviço, uma aplicação é uma unidade de implementação e upgrade. Uma aplicação pode ser atualizada como uma única unidade onde potenciais falhas e potenciais revés são geridos. O Service Fabric garante que o processo de upgrade é bem sucedido ou, se a atualização falhar, não deixa a aplicação num estado desconhecido ou instável.
* **Manifesto de serviço** O manifesto de serviço descreve os componentes de um serviço. Inclui dados, como o nome e o tipo de serviço, e o seu código e configuração. O manifesto de serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço uma vez que é implantado.

## <a name="application-package-file-structure"></a>Estrutura de ficheiros de pacote de aplicação
Para implementar uma aplicação para o Service Fabric, a aplicação deve seguir uma estrutura de diretório predefinida. Segue-se um exemplo dessa estrutura.

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

O ApplicationPackageRoot contém o ficheiro ApplicationManifest.xml que define a aplicação. Um subdiretório para cada serviço incluído na aplicação é usado para conter todos os artefactos que o serviço requer. Estas subdiretórios são a ServiceManifest.xml e, tipicamente, as seguintes:

* *Código*. Este diretório contém o código de serviço.
* *Config.* Este diretório contém um ficheiro Settings.xml (e outros ficheiros, se necessário) que o serviço pode aceder em tempo de execução para recuperar configurações específicas de configuração.
* *Dados.* Este é um diretório adicional para armazenar dados locais adicionais que o serviço pode precisar. Os dados devem ser utilizados para armazenar apenas dados efémeros. O Tecido de Serviço não copia nem replica alterações no diretório de dados se o serviço tiver de ser deslocalizado (por exemplo, durante a falha).

> [!NOTE]
> Não tens de criar os `config` `data` diretórios se não precisares deles.
>
>

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter informações e tarefas relacionadas.
* [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
* [Implementar vários executáveis convidados](./service-fabric-deploy-existing-app.md)
* [Crie a sua primeira aplicação executável de hóspedes utilizando o Visual Studio](quickstart-guest-app.md)
* [Amostra para embalagem e implantação de um hóspede executável,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)incluindo um link para o pré-relançamento da ferramenta de embalagem
* [Amostra de dois executáveis de hóspedes (C# e nodejs) comunicando através do serviço Naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)
