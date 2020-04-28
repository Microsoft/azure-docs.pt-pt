---
title: Empreba um executável existente para o Tecido de Serviço Azure
description: Aprenda sobre a embalagem de uma aplicação existente como um hóspede executável, para que possa ser implantado para um cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75457826"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implementar um executável existente para o Tecido de Serviço
Pode executar qualquer tipo de código, como Node.js, Java ou C++ em Azure Service Fabric como serviço. Service Fabric refere-se a este tipo de serviços como executáveis de hóspedes.

Os executáveis dos hóspedes são tratados pela Service Fabric como serviços apátridas. Como resultado, são colocados em nós em um cluster, com base na disponibilidade e outras métricas. Este artigo descreve como embalar e implementar um hóspede executável para um cluster de Tecido de Serviço, utilizando o Visual Studio ou um utilitário de linha de comando.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios de executar um hóspede executável em Tecido de Serviço
Existem várias vantagens em executar um hóspede executável em um cluster de Tecido de Serviço:

* Elevada disponibilidade. As aplicações que funcionam em Tecido de Serviço são disponibilizadas de forma altamente disponível. O Service Fabric garante que os casos de uma aplicação estão em execução.
* Monitorização da saúde. A monitorização sanitária do serviço Fabric deteta se uma aplicação está em execução e fornece informações de diagnóstico se houver uma falha.   
* Gestão de ciclo de vida de aplicação. Além de fornecer upgrades sem tempo de inatividade, o Service Fabric fornece reversão automática para a versão anterior se houver um mau evento de saúde relatado durante uma atualização.    
* A densidade. Pode executar várias aplicações num cluster, o que elimina a necessidade de cada aplicação funcionar no seu próprio hardware.
* Descubra: Utilizando o REST pode ligar para o serviço de nomeação de tecidos de serviço para encontrar outros serviços no cluster. 

## <a name="samples"></a>Amostras
* [Amostra para embalagem e implantação de um hóspede executável](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Amostra de dois executáveis convidados (C# e nodejs) comunicando através do serviço naming usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral dos ficheiros manifesto de aplicação e serviço
Como parte da implementação de um hóspede executável, é útil compreender o modelo de embalagem e implementação de Tecido de Serviço, conforme descrito no modelo de [aplicação.](service-fabric-application-model.md) O modelo de embalagem Service Fabric baseia-se em dois ficheiros XML: a aplicação e os manifestos de serviço. A definição de esquema para os ficheiros ApplicationManifest.xml e ServiceManifest.xml está instalada com o SDK de Tecido de Serviço em *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto de candidatura** O manifesto de aplicação é usado para descrever a aplicação. Ele enumera os serviços que o compõem, e outros parâmetros que são usados para definir como um ou mais serviços devem ser implantados, como o número de instâncias.

  No Tecido de Serviço, uma aplicação é uma unidade de implantação e atualização. Uma aplicação pode ser atualizada como uma única unidade onde potenciais falhas e potenciais recuos são geridos. O Service Fabric garante que o processo de atualização é bem sucedido ou, se a atualização falhar, não deixa a aplicação num estado desconhecido ou instável.
* **Manifesto de serviço** O manifesto de serviço descreve os componentes de um serviço. Inclui dados, como o nome e o tipo de serviço, e o seu código e configuração. O manifesto de serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço uma vez implementado.

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

O ApplicationPackageRoot contém o ficheiro ApplicationManifest.xml que define a aplicação. Um subdiretório para cada serviço incluído na aplicação é utilizado para conter todos os artefactos que o serviço necessita. Estes subdiretórios são o ServiceManifest.xml e, tipicamente, os seguintes:

* *Código.* Este diretório contém o código de serviço.
* *Config.* Este diretório contém um ficheiro Definições.xml (e outros ficheiros, se necessário) a que o serviço pode aceder no prazo de execução para obter configurações de configuração específicas.
* *Dados*. Este é um diretório adicional para armazenar dados locais adicionais que o serviço pode precisar. Os dados devem ser utilizados para armazenar apenas dados efémeros. Serviço O Tecido não copia nem replica alterações ao diretório de dados se o serviço precisar de ser recolocado (por exemplo, durante a falha).

> [!NOTE]
> Não tens de criar `config` os `data` e diretórios se não precisares deles.
>
>

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter informações e tarefas relacionadas.
* [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
* [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
* [Crie a sua primeira aplicação executável de hóspedes usando o Visual Studio](quickstart-guest-app.md)
* [Amostra para embalagem e implantação de um hóspede executável,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)incluindo uma ligação ao pré-lançamento da ferramenta de embalagem
* [Amostra de dois executáveis convidados (C# e nodejs) comunicando através do serviço naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)

