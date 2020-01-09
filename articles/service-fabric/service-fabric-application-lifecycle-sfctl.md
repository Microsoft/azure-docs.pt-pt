---
title: Gerenciar aplicativos de Service Fabric do Azure usando o sfctl
description: Saiba como implantar e remover aplicativos de um Cluster Service Fabric do Azure usando a CLI do Service Fabric do Azure
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: db271d479fd84e5338d53cc25ecc0122d856c442
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610238"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Gerenciar um aplicativo de Service Fabric do Azure usando a CLI do Service Fabric do Azure (sfctl)

Saiba como criar e excluir aplicativos que estão sendo executados em um cluster de Service Fabric do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o Service Fabric CLI. Em seguida, selecione o Cluster Service Fabric. Para obter mais informações, consulte Introdução [à CLI do Service Fabric](service-fabric-cli.md).

* Ter um pacote de aplicativo Service Fabric pronto para ser implantado. Para obter mais informações sobre como criar e empacotar um aplicativo, leia sobre o [modelo de aplicativo Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Visão geral

Para implantar um novo aplicativo, conclua estas etapas:

1. Carregue um pacote de aplicativos no repositório de imagens Service Fabric.
2. Provisionar um tipo de aplicativo.
3. Exclua o conteúdo do repositório de imagens.
4. Especifique e crie um aplicativo.
5. Especifique e crie serviços.

Para remover um aplicativo existente, conclua estas etapas:

1. Exclua o aplicativo.
2. Desprovisionar o tipo de aplicativo associado.

## <a name="deploy-a-new-application"></a>Implantar um novo aplicativo

Para implantar um novo aplicativo, conclua as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregar um novo pacote de aplicativos no repositório de imagens

Antes de criar um aplicativo, carregue o pacote de aplicativos no repositório de imagens Service Fabric.

Por exemplo, se o pacote de aplicativo estiver no diretório `app_package_dir`, use os seguintes comandos para carregar o diretório:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Para pacotes de aplicativos grandes, você pode especificar a opção `--show-progress` para exibir o progresso do upload.

### <a name="provision-the-application-type"></a>Provisionar o tipo de aplicativo

Quando o upload for concluído, provisione o aplicativo. Para provisionar o aplicativo, use o seguinte comando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

O valor de `application-type-build-path` é o nome do diretório em que você carregou o pacote de aplicativos.

### <a name="delete-the-application-package"></a>Excluir o pacote de aplicativos

É recomendável que você remova o pacote de aplicativos depois que o aplicativo for registrado com êxito.  A exclusão de pacotes de aplicativos do repositório de imagens libera os recursos do sistema.  Manter pacotes de aplicativos não utilizados consome armazenamento em disco e leva a problemas de desempenho de aplicativos. 

Para excluir o pacote de aplicativos do repositório de imagens, use o seguinte comando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` deve ser o nome do diretório que você carregou ao criar o aplicativo.

### <a name="create-an-application-from-an-application-type"></a>Criar um aplicativo a partir de um tipo de aplicativo

Depois de provisionar o aplicativo, use o seguinte comando para nomear e criar seu aplicativo:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` é o nome que você deseja usar para a instância do aplicativo. Você pode obter parâmetros adicionais do manifesto do aplicativo provisionado anteriormente.

O nome do aplicativo deve começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para o novo aplicativo

Depois de criar um aplicativo, crie serviços a partir do aplicativo. No exemplo a seguir, criamos um novo serviço sem estado de nosso aplicativo. Os serviços que você pode criar por meio de um aplicativo são definidos em um manifesto de serviço no pacote de aplicativos provisionado anteriormente.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificar a implantação e a integridade do aplicativo

Para verificar se tudo está íntegro, use os seguintes comandos de integridade:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Para verificar se o serviço está íntegro, use comandos semelhantes para recuperar a integridade do serviço e do aplicativo:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Os serviços e aplicativos íntegros têm um valor `HealthState` de `Ok`.

## <a name="remove-an-existing-application"></a>Remover um aplicativo existente

Para remover um aplicativo, conclua as seguintes tarefas:

### <a name="delete-the-application"></a>Excluir o aplicativo

Para excluir o aplicativo, use o seguinte comando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Desprovisionar o tipo de aplicativo

Depois de excluir o aplicativo, você poderá desprovisionar o tipo de aplicativo se não precisar mais dele. Para desprovisionar o tipo de aplicativo, use o seguinte comando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome do tipo e a versão do tipo devem corresponder ao nome e à versão no manifesto do aplicativo provisionado anteriormente.

## <a name="upgrade-application"></a>Atualizar aplicativo

Depois de criar seu aplicativo, você pode repetir o mesmo conjunto de etapas para provisionar uma segunda versão do seu aplicativo. Em seguida, com uma atualização Service Fabric aplicativo, você pode fazer a transição para a execução da segunda versão do aplicativo. Para obter mais informações, consulte a documentação sobre [atualizações Service Fabric aplicativo](service-fabric-application-upgrade.md).

Para executar uma atualização, primeiro Provisione a próxima versão do aplicativo usando os mesmos comandos que antes:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Em seguida, é recomendável executar uma atualização automática monitorada, iniciar a atualização executando o seguinte comando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

As atualizações substituem os parâmetros existentes por qualquer conjunto especificado. Os parâmetros do aplicativo devem ser passados como argumentos para o comando de atualização, se necessário. Os parâmetros do aplicativo devem ser codificados como um objeto JSON.

Para recuperar todos os parâmetros especificados anteriormente, você pode usar o comando `sfctl application info`.

Quando uma atualização de aplicativo está em andamento, o status pode ser recuperado usando o comando `sfctl application upgrade-status`.

Por fim, se uma atualização estiver em andamento e precisar ser cancelada, você poderá usar o `sfctl application upgrade-rollback` para reverter a atualização.

## <a name="next-steps"></a>Passos seguintes

* [Noções básicas do Service Fabric CLI](service-fabric-cli.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
* [Iniciando uma atualização de aplicativo Service Fabric](service-fabric-application-upgrade.md)
