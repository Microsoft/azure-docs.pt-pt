---
title: O Azure Service Fabric Docker Compose pré-visualização de implementação
description: O Azure Service Fabric aceita o formato do Docker Compose para tornar mais fácil orquestrar contentores existentes através do Service Fabric. Este suporte está atualmente em pré-visualização.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 6e03ada8c1a3aaa86ffcb11799dbe2b61852c987
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806679"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Suporte de implementação do docker Compose no Azure Service Fabric (pré-visualização)

Docker utiliza o [docker-Compose](https://docs.docker.com/compose) ficheiro para definir aplicações com vários contentores. Para facilitar para os clientes familiarizados com o Docker para orquestrarem as aplicações de contentor no Azure Service Fabric, incluímos suporte de pré-visualização para a implementação do Docker Compose nativamente na plataforma. Service Fabric pode aceitar a versão 3 e posterior do `docker-compose.yml` ficheiros. 

Uma vez que este suporte está em pré-visualização, apenas um subconjunto das diretivas é suportado. Por exemplo, as atualizações de aplicações não são suportadas. No entanto, pode sempre remover e implementar aplicações em vez de atualizá-las.

Para utilizar esta pré-visualização, crie o cluster com a versão 5.7 ou maior de runtime do Service Fabric através do portal do Azure juntamente com o SDK correspondente. 

> [!NOTE]
> Esta funcionalidade está em pré-visualização e não é suportada na produção.
> Os exemplos abaixo baseiam-se a versão de runtime 6.0 e versão 2.8 do SDK.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implementar um ficheiro do Docker Compose no Service Fabric

Os comandos seguintes criam uma aplicação do Service Fabric (com o nome `fabric:/TestContainerApp`), que pode monitorizar e gerir como qualquer outra aplicação do Service Fabric. Pode utilizar o nome da aplicação especificada para consultas de estado de funcionamento.
Service Fabric reconhece "DeploymentName" como o identificador da implementação de composição.

### <a name="use-powershell"></a>Utilizar o PowerShell

Crie uma implementação de composição do Service Fabric a partir de um ficheiro docker-Compose yml executando o seguinte comando no PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` consulte o nome de utilizador de registo de contentor e a palavra-passe. Depois de concluir a implementação, pode verificar o estado, utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para eliminar a implementação de composição através do PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização de implementação de composição através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Reverter a implementação de composição de atualização através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Após a atualização for aceita, o progresso da atualização pode ser controlado com o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Utilizar a CLI do Azure Service Fabric (sfctl)

Em alternativa, pode utilizar o seguinte comando da CLI do Service Fabric:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Depois de criar a implementação, pode verificar o estado, utilizando o seguinte comando:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para eliminar a implementação de composição, utilize o seguinte comando:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização de implementação de composição, utilize o seguinte comando:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Reverter a implementação de composição de atualização, utilize o seguinte comando:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Após a atualização for aceita, o progresso da atualização pode ser controlado com o seguinte comando:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas suportadas

Esta pré-visualização suporta um subconjunto das opções de configuração do formato de versão 3 composição, incluindo os primitivos seguintes:

* Serviços > Implementar > réplicas
* Serviços > Implementar > colocação > restrições
* Serviços > Implementar > recursos > limites
    * -cpu-shares
    * -memory
    * -memory-swap
* Serviços > comandos
* Serviços > ambiente
* Serviços > portas
* Serviços > imagem
* Serviços > isolamento (apenas para Windows)
* Serviços > registo > controlador
* Serviços > registo > controlador > Opções
* Volume & implementar > Volume

Como configurar o cluster para impor limites de recursos, conforme descrito em [governação de recursos do Service Fabric](service-fabric-resource-governance.md). Todas as outras diretivas Docker Compose não são suportadas para esta pré-visualização.

### <a name="ports-section"></a>Secção de portas

Especifique o protocolo http ou https na secção portas que será utilizada pelo serviço de escuta de serviço do Service Fabric. Isto irá garantir que o protocolo de ponto de extremidade é publicado o corretamente com o serviço de nomenclatura para permitir que o proxy inverso reencaminhar os pedidos:
* Para encaminhar aos serviços de compor do Service Fabric não seguros, especifique **/http**. Por exemplo,- **"80:80 / http"**.
* Para encaminhar aos serviços de composição do Service Fabric seguros, especifique **/https**. Por exemplo,- **"443:443 / https"**.

> [!NOTE]
> A sintaxe de secção /http e portas de /https é específica para o Service Fabric para registar o URL de serviço de escuta do Service Fabric correto.  Se o Docker compose sintaxe do arquivo por meio de programação é validado, pode fazer com que um erro de validação.

## <a name="servicednsname-computation"></a>Computação de ServiceDnsName

Se o nome do serviço que especificou num arquivo de composição é um nome de domínio completamente qualificado (ou seja, ele contém um ponto [.]), o nome DNS registado ao Service Fabric é `<ServiceName>` (incluindo o ponto). Caso contrário, cada segmento de caminho no nome do aplicativo torna-se uma etiqueta de domínio no nome DNS do serviço, com o primeiro segmento de caminho, tornando-se a etiqueta de domínio de nível superior.

Por exemplo, se o nome da aplicação especificada for `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` seria o nome DNS registado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Aplicação de implementação (definição de instância) versus modelo de aplicação do Service Fabric (definição de tipo)

Um ficheiro docker-Compose yml descreve um conjunto implementável de contentores, incluindo as propriedades e configurações.
Por exemplo, o ficheiro pode conter variáveis de ambiente e portas. Também pode especificar parâmetros de implementação, tais como restrições de posicionamento, limites de recursos e nomes DNS, o ficheiro docker-Compose yml.

O [modelo de aplicação do Service Fabric](service-fabric-application-model.md) utiliza tipos e tipos de aplicação, onde pode ter muitas instâncias da aplicação do mesmo tipo de serviço. Por exemplo, pode ter uma instância da aplicação por cliente. Este modelo baseado em tipo oferece suporte a várias versões do mesmo tipo de aplicação que está registado com o tempo de execução.

Por exemplo, o cliente A pode ter um aplicativo instanciado com 1.0 typu AppTypeA e o cliente B pode ter outro aplicativo instanciado com o mesmo tipo e versão. Definir os tipos de aplicativo nos manifestos de aplicativo e especificar os parâmetros de nome e a implementação da aplicação ao criar a aplicação.

Embora esse modelo oferece flexibilidade, estamos também a planear oferecer suporte a um modelo de implementação mais simples, com base na instância em que os tipos são implícitos do arquivo de manifesto. Nesse modelo, cada aplicativo obtém seu próprio manifesto independente. Estamos a disponibilizar pré-visualização esse esforço, adicionando suporte para docker-Compose, que é um formato de implantação com base na instância.

## <a name="next-steps"></a>Passos Seguintes

* Ler sobre o [modelo de aplicação do Service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
