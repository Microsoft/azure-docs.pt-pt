---
title: Pré-visualização de implantação de estivador de tecido de serviço azure
description: A Azure Service Fabric aceita o formato Docker Compose para facilitar a orquestração de contentores existentes utilizando o Tecido de Serviço. Este suporte encontra-se atualmente em pré-visualização.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282461"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Suporte de implantação Docker Compose em Tecido de Serviço Azure (Pré-visualização)

O Docker usa o ficheiro [docker-compose.yml](https://docs.docker.com/compose) para definir aplicações multi-contentores. Para facilitar aos clientes familiarizados com o Docker a orquestrar as aplicações de contentores existentes no Tecido de Serviço Azure, incluímos suporte de pré-visualização para a implantação de Docker Compose nativamente na plataforma. O Serviço Tecido pode aceitar `docker-compose.yml` a versão 3 e posterior dos ficheiros. 

Uma vez que este apoio está em pré-visualização, apenas um subconjunto de diretivas compor é apoiado.

Para utilizar esta pré-visualização, crie o seu cluster com a versão 5.7 ou maior do tempo de execução do Tecido de Serviço através do portal Azure juntamente com o SDK correspondente. 

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização e não é suportada na produção.
> Os exemplos abaixo são baseados na versão runtime 6.0 e sdK versão 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implementar um ficheiro Docker Compose no Tecido de Serviço

Os seguintes comandos criam uma `fabric:/TestContainerApp`aplicação Service Fabric (nomeada), que pode monitorizar e gerir como qualquer outra aplicação de Tecido de Serviço. Pode utilizar o nome de candidatura especificado para consultas de saúde.
O Tecido de Serviço reconhece o "DeploymentName" como o identificador da implantação compor.

### <a name="use-powershell"></a>Utilizar o PowerShell

Crie uma implantação de composição de tecido de serviço a partir de um ficheiro docker-compose.yml executando o seguinte comando no PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`e `RegistryPassword` consulte o nome de utilizador e senha do registo de contentores. Depois de concluída a implantação, pode verificar o seu estado utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para eliminar a implantação compor através do PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização de implementação compor através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Para reverter a atualização de implementação compor através da PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Após a atualização ser aceite, o progresso da atualização poderia ser rastreado utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Utilizar tecido de serviço Azure CLI (sfctl)

Em alternativa, pode utilizar o seguinte comando CLI de tecido de serviço:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Depois de ter criado a implantação, pode verificar o seu estado utilizando o seguinte comando:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para eliminar a implantação de Compor, utilize o seguinte comando:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização de implementação compor, utilize o seguinte comando:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Para reverter a atualização de implementação do Compose, utilize o seguinte comando:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Após a atualização ser aceite, o progresso da atualização poderia ser rastreado utilizando o seguinte comando:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas de composição apoiada

Esta pré-visualização suporta um subconjunto das opções de configuração do formato compor versão 3, incluindo os seguintes primitivos:

* Serviços > Implementar réplicas >
* Serviços > implementar constrangimentos > de colocação de >
* Serviços > Implementar limites de > de recursos >
    * -cpu-shares
    * -memória
    * -troca de memória
* Comandos > de Serviços
* Serviços > Ambiente
* Serviços > Portos
* Serviços > Imagem
* Serviços > Isolamento (apenas para Windows)
* Serviços > Motorista de > de exploração de madeira
* Serviços > opções de > de motorista de >
* Volume & implantação > Volume de >

Criar o cluster para a aplicação dos limites de recursos, tal como descrito na governação dos [recursos do Tecido de Serviço.](service-fabric-resource-governance.md) Todas as outras diretivas do Docker Compose não são apoiadas para esta pré-visualização.

### <a name="ports-section"></a>Secção de portos

Especifique o protocolo http ou https na secção Portos que será utilizado pelo ouvinte do serviço Fabric. Isto garantirá que o protocolo de ponto final seja publicado corretamente com o serviço de nomeação para permitir que o representante inverso reencaminha os pedidos:
* Para encaminhar para serviços de composição de tecido de serviço não seguros, especifique **/http**. Por exemplo, **- "80:80/http".**
* Para encaminhar para assegurar serviços de composição de tecido de serviço, especifique **/https**. Por exemplo, - **"443:443/https".**

> [!NOTE]
> A sintaxe da secção /http e /https portos é específica para o Tecido de Serviço para registar o URL de escuta de tecido de serviço correto.  Se a sintaxe de ficheiros compor o Docker for validada programáticamente, pode causar um erro de validação.

## <a name="servicednsname-computation"></a>Computação ServiceDnsName

Se o nome de serviço que especifica num ficheiro Compor for um nome de domínio totalmente qualificado (isto é, `<ServiceName>` contém um ponto [.]), o nome DNS registado pelo Service Fabric é (incluindo o ponto). Caso contrário, cada segmento de caminho no nome da aplicação torna-se uma etiqueta de domínio no nome DNS de serviço, com o primeiro segmento de caminho a tornar-se o rótulo de domínio de nível superior.

Por exemplo, se o nome `fabric:/SampleApp/MyComposeApp` `<ServiceName>.MyComposeApp.SampleApp` de pedido especificado for, seria o nome DNS registado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Compor implementação (definição de exemplo) versus modelo de aplicativo de tecido de serviço (definição de tipo)

Um ficheiro docker-compose.yml descreve um conjunto de contentores implantáveis, incluindo as suas propriedades e configurações.
Por exemplo, o ficheiro pode conter variáveis ambientais e portas. Também pode especificar parâmetros de implantação, tais como restrições de colocação, limites de recursos e nomes DNS, no ficheiro docker-compose.yml.

O modelo de [aplicação Service Fabric](service-fabric-application-model.md) utiliza tipos de serviço e tipos de aplicação, onde pode ter muitas instâncias de aplicação do mesmo tipo. Por exemplo, pode ter uma instância de aplicação por cliente. Este modelo baseado em tipo suporta várias versões do mesmo tipo de aplicação que está registada com o tempo de execução.

Por exemplo, o cliente A pode ter uma aplicação instantânea com o tipo 1.0 do AppTypeA, e o cliente B pode ter outra aplicação instantânea com o mesmo tipo e versão. Define os tipos de aplicação nos manifestos de aplicação e especifica o nome da aplicação e os parâmetros de implementação quando cria a aplicação.

Embora este modelo ofereça flexibilidade, estamos também a planear apoiar um modelo de implementação mais simples e baseado em exemplos, onde os tipos estão implícitos a partir do ficheiro manifesto. Neste modelo, cada aplicação recebe o seu próprio manifesto independente. Estamos a visualizar este esforço adicionando suporte para docker-compose.yml, que é um formato de implementação baseado em instâncias.

## <a name="next-steps"></a>Passos seguintes

* Leia o modelo de [aplicação service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
