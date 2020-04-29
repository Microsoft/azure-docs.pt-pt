---
title: Gerir aplicações de tecido de serviço Azure usando sfctl
description: Saiba como implementar e remover aplicações de um cluster de tecido de serviço Azure utilizando o Tecido de Serviço Azure CLI
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259074"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Gerir uma aplicação Azure Service Fabric utilizando o Tecido de Serviço Azure CLI (sfctl)

Aprenda a criar e apagar aplicações que estejam a funcionar num cluster Azure Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o tecido de serviço CLI. Em seguida, selecione o seu cluster De Tecido de Serviço. Para mais informações, consulte [Iniciar com o Tecido de Serviço CLI](service-fabric-cli.md).

* Tenha um pacote de aplicação Service Fabric pronto para ser implantado. Para mais informações sobre como autor e embalar uma aplicação, leia sobre o modelo de [aplicação Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Descrição geral

Para implementar uma nova aplicação, complete estas etapas:

1. Faça upload de um pacote de aplicações para a loja de imagens Service Fabric.
2. Provisão de um tipo de candidatura.
3. Elimine o conteúdo da loja de imagens.
4. Especifique e crie uma aplicação.
5. Especificar e criar serviços.

Para remover uma aplicação existente, preencha estes passos:

1. Apagar a aplicação.
2. Não fornecer o tipo de aplicação associado.

## <a name="deploy-a-new-application"></a>Implementar uma nova aplicação

Para implementar uma nova aplicação, complete as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Faça upload de um novo pacote de aplicações para a loja de imagens

Antes de criar uma aplicação, faça o upload do pacote de aplicações para a loja de imagens Service Fabric.

Por exemplo, se o `app_package_dir` seu pacote de candidatura estiver no diretório, utilize os seguintes comandos para fazer o upload do diretório:

```shell
sfctl application upload --path ~/app_package_dir
```

Para grandes pacotes de aplicações, pode especificar a opção `--show-progress` de mostrar o progresso do upload.

### <a name="provision-the-application-type"></a>Prever o tipo de candidatura

Quando o upload estiver concluído, forme o pedido. Para fornecer o pedido, utilize o seguinte comando:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

O valor `application-type-build-path` para o nome do diretório onde fez o upload do seu pacote de candidaturas.

### <a name="delete-the-application-package"></a>Eliminar o pacote de candidaturas

Recomenda-se que remova o pacote de aplicação após a inscrição registada com sucesso.  A apagar pacotes de aplicações da loja de imagens liberta recursos do sistema.  Manter pacotes de aplicação não utilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação. 

Para eliminar o pacote de aplicação da loja de imagens, utilize o seguinte comando:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`deve ser o nome do diretório que carregou quando criou a aplicação.

### <a name="create-an-application-from-an-application-type"></a>Criar uma aplicação a partir de um tipo de aplicação

Depois de fornecer a aplicação, utilize o seguinte comando para nomear e crie a sua aplicação:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`é o nome que pretende utilizar para a instância de candidatura. Pode obter parâmetros adicionais do manifesto de candidatura previamente previsto.

O nome da aplicação `fabric:/`deve começar com o prefixo .

### <a name="create-services-for-the-new-application"></a>Criar serviços para a nova aplicação

Depois de ter criado uma aplicação, crie serviços a partir da aplicação. No exemplo seguinte, criamos um novo serviço apátrida a partir da nossa aplicação. Os serviços que pode criar a partir de uma aplicação são definidos num manifesto de serviço no pacote de candidatura previamente provisionado.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificar a implementação e a saúde da aplicação

Para verificar se está tudo saudável, utilize os seguintes comandos de saúde:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Para verificar se o serviço é saudável, utilize comandos semelhantes para recuperar a saúde do serviço e da aplicação:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Serviços e aplicações `HealthState` saudáveis têm um valor de `Ok`.

## <a name="remove-an-existing-application"></a>Remover uma aplicação existente

Para remover uma aplicação, complete as seguintes tarefas:

### <a name="delete-the-application"></a>Eliminar o pedido

Para eliminar a aplicação, utilize o seguinte comando:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Não fornecer o tipo de candidatura

Depois de eliminar a aplicação, pode desfornecer o tipo de aplicação se já não precisar. Para desfornecer o tipo de aplicação, utilize o seguinte comando:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome tipo e a versão tipo devem coincidir com o nome e a versão no manifesto de aplicação previamente provisionado.

## <a name="upgrade-application"></a>Aplicação de upgrade

Depois de criar a sua aplicação, pode repetir o mesmo conjunto de passos para fornecer uma segunda versão da sua aplicação. Em seguida, com uma atualização de aplicação Service Fabric pode transitar para executar a segunda versão da aplicação. Para mais informações, consulte a documentação sobre as atualizações da [aplicação Service Fabric.](service-fabric-application-upgrade.md)

Para efetuar uma atualização, primeira disposição da próxima versão da aplicação utilizando os mesmos comandos que antes:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Recomenda-se então que efetue uma atualização automática monitorizada, inicie a atualização executando o seguinte comando:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

As atualizações sobrepõem-se aos parâmetros existentes com qualquer conjunto especificado. Os parâmetros de aplicação devem ser passados como argumentos para o comando de atualização, se necessário. Os parâmetros de aplicação devem ser codificados como um objeto JSON.

Para recuperar quaisquer parâmetros previamente especificados, pode utilizar o `sfctl application info` comando.

Quando uma atualização da aplicação está em `sfctl application upgrade-status` andamento, o estado pode ser recuperado utilizando o comando.

Finalmente, se uma atualização estiver em andamento e precisar `sfctl application upgrade-rollback` de ser cancelada, pode utilizar a atualização para reverter a atualização.

## <a name="next-steps"></a>Passos seguintes

* [Básicos CLI de tecido de serviço](service-fabric-cli.md)
* [Começando com tecido de serviço em Linux](service-fabric-get-started-linux.md)
* [Lançamento de uma atualização de aplicação service Fabric](service-fabric-application-upgrade.md)
