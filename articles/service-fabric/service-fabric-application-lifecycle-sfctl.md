---
title: Gerir aplicações do Azure Service Fabric com a CLI do Azure Service Fabric (sfctl)
description: Saiba como implementar e remover aplicações a partir de um cluster do Azure Service Fabric, utilizando a CLI do Azure Service Fabric
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662997"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Gerir uma aplicação do Azure Service Fabric com a CLI do Azure Service Fabric (sfctl)

Saiba como criar e eliminar aplicações em execução num cluster do Azure Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

* Instale a CLI do Service Fabric. Em seguida, selecione o cluster do Service Fabric. Para obter mais informações, consulte [introdução à CLI do Service Fabric](service-fabric-cli.md).

* Tem um pacote de aplicação do Service Fabric, pronto para ser implementada. Para obter mais informações sobre como autor e pacote de uma aplicação, leia sobre o [modelo de aplicação do Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Descrição geral

Para implementar um novo aplicativo, conclua estes passos:

1. Carregar um pacote de aplicação para o armazenamento de imagens do Service Fabric.
2. Aprovisione um tipo de aplicação.
3. Elimine o conteúdo do arquivo de imagem.
4. Especificar e criar uma aplicação.
5. Especifique e criar serviços.

Para remover uma aplicação existente, conclua estes passos:

1. Elimine a aplicação.
2. Anular o aprovisionamento do tipo de aplicação associados.

## <a name="deploy-a-new-application"></a>Implementar uma nova aplicação

Para implementar um novo aplicativo, conclua as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregar um novo pacote de aplicação para o armazenamento de imagens

Antes de criar uma aplicação, carregar o pacote de aplicação para o armazenamento de imagens do Service Fabric.

Por exemplo, se o seu pacote de aplicação está no `app_package_dir` directory, utilize os seguintes comandos para carregar o diretório:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Para pacotes de aplicações grande, pode especificar o `--show-progress` opção para apresentar o progresso do carregamento.

### <a name="provision-the-application-type"></a>Aprovisionar o tipo de aplicação

Quando o carregamento estiver concluído, Aprovisione a aplicação. Para aprovisionar a aplicação, utilize o seguinte comando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

O valor para `application-type-build-path` é o nome do diretório onde o seu pacote de aplicação que carregou.

### <a name="delete-the-application-package"></a>Eliminar o pacote de aplicação

É recomendado que remova o pacote de aplicação depois do aplicativo for registado com êxito.  A eliminação de pacotes de aplicação do arquivo de imagens liberta os recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho do aplicativo. 

Para eliminar o pacote de aplicação a partir do arquivo de imagem, utilize o seguinte comando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` tem de ser o nome do diretório que carregou quando criou a aplicação.

### <a name="create-an-application-from-an-application-type"></a>Criar uma aplicação a partir de um tipo de aplicação

Depois de aprovisionar a aplicação, utilize o seguinte comando para nomear e criar a sua aplicação:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` é o nome que pretende utilizar para a instância de aplicação. Pode obter parâmetros adicionais a partir do manifesto do aplicativo aprovisionados anteriormente.

O nome da aplicação tem de começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para a nova aplicação

Depois de criar um aplicativo, crie serviços da aplicação. No exemplo a seguir, criamos um novo serviço sem monitoração de estado de nosso aplicativo. Os serviços que podem ser criados a partir de um aplicativo são definidos no manifesto do serviço no pacote de aplicação previamente aprovisionado.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Certifique-se a implementação da aplicação e o estado de funcionamento

Para verificar se tudo o que está em bom estado, utilize os seguintes comandos do Estado de funcionamento:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Para verificar que o serviço está em bom estado, utilize comandos semelhantes para obter o estado de funcionamento do serviço e o aplicativo:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Aplicações e serviços em bom estado de ter uma `HealthState` valor de `Ok`.

## <a name="remove-an-existing-application"></a>Remover uma aplicação existente

Para remover uma aplicação, conclua as seguintes tarefas:

### <a name="delete-the-application"></a>Eliminar a aplicação

Para eliminar a aplicação, utilize o seguinte comando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anular o aprovisionamento do tipo de aplicação

Depois de eliminar o aplicativo, pode anular o aprovisionamento do tipo de aplicação se já não precisam dele. Para anular o aprovisionamento do tipo de aplicação, utilize o seguinte comando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome do tipo e a versão de tipo tem de corresponder o nome e a versão no manifesto do aplicativo aprovisionados anteriormente.

## <a name="upgrade-application"></a>Atualizar aplicação

Depois de criar seu aplicativo, pode repetir o mesmo conjunto de passos para Aprovisionar uma segunda versão do seu aplicativo. Em seguida, com uma atualização de aplicação do Service Fabric é possível passar para executar a segunda versão da aplicação. Para obter mais informações, consulte a documentação sobre [as atualizações de aplicações do Service Fabric](service-fabric-application-upgrade.md).

Para efetuar uma atualização, primeiro Aprovisione a próxima versão da aplicação com os mesmos comandos como antes:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Recomenda-se, em seguida, para executar uma atualização automática monitorizada, inicie a atualização ao executar o seguinte comando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Atualizações substituem parâmetros existentes com qualquer conjunto especificado. Parâmetros de aplicação devem ser transmitidos como argumentos para o comando de atualização, se necessário. Parâmetros de aplicação devem ser codificados como um objeto JSON.

Para obter todos os parâmetros especificados anteriormente, pode usar o `sfctl application info` comando.

Quando uma atualização da aplicação está em curso, o estado pode ser obtido com o `sfctl application upgrade-status` comando.

Por fim, se uma atualização está em progresso e precisa ser cancelada, pode utilizar o `sfctl application upgrade-rollback` para reverter a atualização.

## <a name="next-steps"></a>Passos Seguintes

* [Noções básicas da CLI do Service Fabric](service-fabric-cli.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
* [Iniciar uma atualização de aplicação do Service Fabric](service-fabric-application-upgrade.md)
