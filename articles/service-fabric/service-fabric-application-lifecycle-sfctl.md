---
title: Gerir aplicações de tecido de serviço Azure usando sfctl
description: Saiba como implementar e remover aplicações de um cluster de tecido de serviço Azure utilizando o CLI do Tecido de Serviço Azure
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711039"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Gerir uma aplicação de tecido de serviço Azure utilizando o CLI do tecido de serviço Azure (sfctl)

Saiba como criar e eliminar aplicações que estão a ser executadas num cluster de Tecido de Serviço Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Instalar O Tecido de Serviço CLI. Em seguida, selecione o seu cluster de Tecido de Serviço. Para mais informações, consulte [Começar com o Service Fabric CLI.](service-fabric-cli.md)

* Tenha um pacote de aplicação de Tecido de Serviço pronto para ser implantado. Para obter mais informações sobre como autor e embalar uma aplicação, leia sobre o modelo de [aplicação de Tecido de Serviço.](service-fabric-application-model.md)

## <a name="overview"></a>Descrição Geral

Para implementar uma nova aplicação, complete estes passos:

1. Faça o upload de um pacote de aplicações para a loja de imagens Service Fabric.
2. Provisionar um tipo de aplicação.
3. Elimine o conteúdo da loja de imagens.
4. Especifique e crie uma aplicação.
5. Especifique e crie serviços.

Para remover uma aplicação existente, complete estes passos:

1. Apague a aplicação.
2. Desprovisionar o tipo de aplicação associado.

## <a name="deploy-a-new-application"></a>Implementar uma nova aplicação

Para implementar uma nova aplicação, preencha as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Faça o upload de um novo pacote de aplicações para a loja de imagens

Antes de criar uma aplicação, faça o upload do pacote de aplicações para a loja de imagens Service Fabric.

Por exemplo, se o seu pacote de candidatura estiver no `app_package_dir` diretório, utilize os seguintes comandos para carregar o diretório:

```shell
sfctl application upload --path ~/app_package_dir
```

Para pacotes de aplicações grandes, pode especificar `--show-progress` a opção de visualizar o progresso do upload.

### <a name="provision-the-application-type"></a>Disposição do tipo de aplicação

Quando o upload estiver concluído, disposi o pedido. Para disposir o pedido, utilize o seguinte comando:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

O valor `application-type-build-path` para o qual é o nome do diretório onde fez o upload do seu pacote de candidaturas.

### <a name="delete-the-application-package"></a>Eliminar o pacote de candidaturas

Recomenda-se que remova o pacote de aplicações após o registo da aplicação com sucesso.  A eliminação de pacotes de aplicações da loja de imagens liberta recursos do sistema.  Manter pacotes de aplicações não reutilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação. 

Para eliminar o pacote de aplicações da loja de imagens, utilize o seguinte comando:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` deve ser o nome do diretório que carregou quando criou a aplicação.

### <a name="create-an-application-from-an-application-type"></a>Criar uma aplicação a partir de um tipo de aplicação

Depois de apresentar a aplicação, utilize o seguinte comando para nomear e criar a sua aplicação:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` é o nome que pretende usar para a aplicação. Pode obter parâmetros adicionais do manifesto de aplicação previamente previsto.

O nome da aplicação deve começar pelo `fabric:/` prefixo.

### <a name="create-services-for-the-new-application"></a>Criar serviços para a nova aplicação

Depois de ter criado uma aplicação, crie serviços a partir da aplicação. No exemplo seguinte, criamos um novo serviço apátrida a partir da nossa aplicação. Os serviços que pode criar a partir de uma aplicação são definidos num manifesto de serviço no pacote de candidaturas previamente a provisionado.

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

Para verificar se o serviço é saudável, utilize comandos semelhantes para recuperar a saúde tanto do serviço como da aplicação:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Serviços e aplicações saudáveis têm um `HealthState` valor `Ok` de.

## <a name="remove-an-existing-application"></a>Remover uma aplicação existente

Para remover uma aplicação, complete as seguintes tarefas:

### <a name="delete-the-application"></a>Eliminar a aplicação

Para eliminar a aplicação, utilize o seguinte comando:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Desprovisionar o tipo de aplicação

Depois de eliminar a aplicação, pode desprovisionar o tipo de aplicação se já não precisar. Para desprovisionar o tipo de aplicação, utilize o seguinte comando:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome-tipo e a versão do tipo devem corresponder ao nome e versão no manifesto de aplicação previamente previsto.

## <a name="upgrade-application"></a>Aplicação de upgrade

Depois de criar a sua aplicação, pode repetir o mesmo conjunto de passos para obter uma segunda versão da sua aplicação. Em seguida, com uma atualização de aplicação Service Fabric pode transitar para executar a segunda versão da aplicação. Para mais informações, consulte a documentação sobre [as atualizações da aplicação Service Fabric](service-fabric-application-upgrade.md).

Para realizar uma atualização, primeiro fori a próxima versão da aplicação utilizando os mesmos comandos que antes:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Recomenda-se então a realização de uma atualização automática monitorizada, lançar a atualização executando o seguinte comando:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

As atualizações substituem os parâmetros existentes com qualquer conjunto especificado. Os parâmetros de aplicação devem ser passados como argumentos para o comando de atualização, se necessário. Os parâmetros de aplicação devem ser codificados como um objeto JSON.

Para recuperar quaisquer parâmetros previamente especificados, pode utilizar o `sfctl application info` comando.

Quando uma atualização da aplicação está em andamento, o estado pode ser recuperado usando o `sfctl application upgrade-status` comando.

Finalmente, se uma atualização estiver em andamento e precisar de ser cancelada, pode utilizar `sfctl application upgrade-rollback` a atualização para reverter.

## <a name="next-steps"></a>Passos seguintes

* [Básicos CLI de Tecido de Serviço](service-fabric-cli.md)
* [Começando com tecido de serviço em Linux](service-fabric-get-started-linux.md)
* [Lançamento de uma atualização de aplicação de Tecido de Serviço](service-fabric-application-upgrade.md)
