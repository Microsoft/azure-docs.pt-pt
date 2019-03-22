---
title: Criar a sua primeira aplicação do Azure Service Fabric no Linux com c# | Documentos da Microsoft
description: Saiba como criar e implementar uma aplicação de Service Fabric com c# e .NET Core 2.0.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: f044aa006727fafb52c2c5e6c015c3e4cc70edf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57882018"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar a sua primeira aplicação do Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java - Linux (Pré-visualização)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux (Pré-visualização)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

O Service Fabric disponibiliza SDKs para criar serviços no Linux em .NET Core e Java. Neste tutorial, vamos ver como pode criar uma aplicação para Linux e compilar um serviço com C# no .NET Core 2.0.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de uma caixa do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

Também deve instalar a [CLI do Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Instalar e configurar os geradores para C#
O Service Fabric fornece ferramentas estruturais que o ajudam a criar aplicações do Service Fabric a partir de um terminal com os geradores de modelos Yeoman. Siga estes passos para configurar os geradores de modelos Yeoman do Service Fabric para C#:

1. Instalar nodejs e NPM no seu computador

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Instalar o gerador de modelos [Yeoman](https://yeoman.io/) no seu computador a partir do NPM

   ```bash
   npm install -g yo
   ```
3. Instalar o gerador de aplicações C# Yeoman do Service Fabric a partir do NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O gerador [Yeoman](https://yeoman.io/) do Service Fabric para C#, que instalou no passo anterior, facilita a criação do seu primeiro serviço e a adição posterior de outros. Vamos utilizar o Yeoman para criar uma aplicação com um único serviço.

1. Num terminal, escreva o seguinte comando para começar a criar a estrutura: `yo azuresfcsharp`
2. Dê um nome à aplicação.
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para os objetivos deste tutorial, escolhemos um Serviço Reliable Actor.

   ![Gerador Yeoman do Service Fabric para C#][sf-yeoman]

> [!NOTE]
> Para obter mais informações sobre as opções, veja [Service Fabric programming model overview (Descrição geral do modelo de programação Service Fabric)](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação que pode utilizar para criar a aplicação a partir do terminal (depois de navegar para a pasta da aplicação).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Implementar a aplicação

Depois de criada a aplicação, pode implementá-la no cluster local.

1. Ligue ao cluster do Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Execute o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

A implementação da aplicação criada é igual à de qualquer outra aplicação do Service Fabric. Veja a documentação sobre como [gerir uma aplicação do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros desses comandos encontram-se nos manifestos gerados dentro do pacote de aplicação.

Após a implementação da aplicação, abra um browser e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implementar a aplicação a um cluster Linux seguro no Azure, terá de configurar um certificado para validar a sua aplicação com o tempo de execução do Service Fabric. Se o fizer, permite que os seus serviços de Reliable Services comunicar com o tempo de execução do Service Fabric subjacente APIs. Para obter mais informações, consulte [configurar uma aplicação de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Iniciar o cliente de teste e executar uma ativação pós-falha
Os projetos de ator não fazem nada só por si. Precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.

   Em caso de MAC OS X, precisa copiar a pasta de myactorsvcTestClient para a algum local dentro do contentor ao executar os seguintes comandos adicionais.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]
3. Clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia um nó no seu cluster local e força uma ativação pós-falha num réplica secundária em execução noutro nó. Quando realizar esta ação, tenha atenção à saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## <a name="adding-more-services-to-an-existing-application"></a>Adicionar mais serviços a uma aplicação existente

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos:
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Execute `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Passos seguintes

* [Interagir com os clusters do Service Fabric através da CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
