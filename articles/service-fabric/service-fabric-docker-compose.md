---
title: Pré-visualização de implantação de composição de tecido de serviço Azure
description: A Azure Service Fabric aceita o formato Docker Compose para facilitar a orquestração dos recipientes existentes utilizando o Tecido de Serviço. Este suporte está atualmente em pré-visualização.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84691293"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker Compose suporte de implantação em Azure Service Fabric (Preview)

Docker usa o ficheiro [docker-compose.yml](https://docs.docker.com/compose) para definir aplicações multi-contentores. Para facilitar aos clientes que conhecem o Docker orquestrar as aplicações de contentores existentes no Azure Service Fabric, incluímos suporte de pré-visualização para a implementação do Docker Compose na plataforma. O Service Fabric pode aceitar a versão 3 e posterior dos `docker-compose.yml` ficheiros. 

Dado que este apoio está em pré-visualização, apenas é apoiado um subconjunto de diretivas composições.

Para utilizar esta pré-visualização, crie o seu cluster com a versão 5.7 ou maior do tempo de funcionamento do Tecido de Serviço através do portal Azure juntamente com o SDK correspondente. 

> [!NOTE]
> Esta funcionalidade está em pré-visualização e não é suportada na produção.
> Os exemplos abaixo baseiam-se na versão 6.0 e na versão SDK 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implementar um ficheiro Docker Compose no Tecido de Serviço

Os seguintes comandos criam uma aplicação De Tecido de Serviço `fabric:/TestContainerApp` (nomeado), que pode monitorizar e gerir como qualquer outra aplicação de Tecido de Serviço. Pode utilizar o nome de aplicação especificado para consultas de saúde.
O Tecido de Serviço reconhece o "Nome de Implantação" como o identificador da implantação compose.

### <a name="use-powershell"></a>Utilizar o PowerShell

Criar uma implementação de Composição de Tecido de Serviço a partir de um ficheiro docker-compose.yml executando o seguinte comando em PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` consulte o nome de utilizador do registo do contentor e a palavra-passe. Depois de ter concluído a implantação, pode verificar o seu estado utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para eliminar a implementação de Compose através do PowerShell, utilize o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização de implementação composição através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Para reverter a atualização de implementação composição através do PowerShell, utilize o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Após a aceitação da atualização, o progresso da atualização poderá ser rastreado utilizando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Use O Tecido de Serviço Azure CLI (sfctl)

Em alternativa, pode utilizar o seguinte comando CLI do Tecido de Serviço:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Depois de ter criado a implementação, pode verificar o seu estado utilizando o seguinte comando:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para eliminar a implantação composição, utilize o seguinte comando:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização de implementação composição, utilize o seguinte comando:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Para reverter a atualização de implementação composição, utilize o seguinte comando:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Após a aceitação da atualização, o progresso da atualização poderá ser rastreado utilizando o seguinte comando:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas de composição apoiadas

Esta pré-visualização suporta um subconjunto das opções de configuração do formato Compose versão 3, incluindo os seguintes primitivos:

* Serviços > implementar réplicas de >
* Serviços > implementar > > restrições de colocação
* Serviços > implementar limites > de recursos >
    * -cpu-acções
    * -memória
    * -troca de memórias
* Comandos > de Serviços
* Serviços > Ambiente
* Serviços > Portos
* Serviços > Imagem
* Serviços > Isolamento (apenas para Windows)
* Serviços > controlador de > de registo
* Serviços > > opções de > do condutor de registo
* Volume & Implementar volume >

Configurar o cluster para a aplicação dos limites de recursos, conforme descrito na [governação dos recursos do Service Fabric](service-fabric-resource-governance.md). Todas as outras diretivas da Docker Compose não são apoiadas para esta pré-visualização.

### <a name="ports-section"></a>Secção de portas

Especifique o protocolo http ou https na secção Ports que será utilizado pelo ouvinte de serviço Service Fabric. Isto garantirá que o protocolo de ponto final seja publicado corretamente com o serviço de nomeação para permitir que o representante inverso reencaminhasse os pedidos:
* Para encaminhar para serviços de composição de tecido de serviço não seguro, especifique **/http**. Por exemplo, - **"80:80/http"**.
* Para encaminhar para garantir serviços de Composição de Tecido de Serviço, especifique **/https**. Por exemplo, - **"443:443/https"**.

> [!NOTE]
> A sintaxe da secção portas /https e /https é específica do Tecido de Serviço para registar o URL correto do ouvinte do tecido de serviço.  Se a sintaxe do ficheiro de composição do Docker for validada programáticamente, pode causar um erro de validação.

## <a name="servicednsname-computation"></a>Computação de Nomes de Serviço

Se o nome de serviço que especifica num ficheiro Compose for um nome de domínio totalmente qualificado (isto é, contém um ponto [.]), o nome DNS registado pela Service Fabric é `<ServiceName>` (incluindo o ponto). Caso contrário, cada segmento de caminho no nome da aplicação torna-se um rótulo de domínio no nome DNS de serviço, com o primeiro segmento de caminho a tornar-se o rótulo de domínio de nível superior.

Por exemplo, se o nome de aplicação especificado for `fabric:/SampleApp/MyComposeApp` , será o nome `<ServiceName>.MyComposeApp.SampleApp` DNS registado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Composição de implantação (definição de instância) contra modelo de aplicação de tecido de serviço (definição de tipo)

Um ficheiro docker-compose.yml descreve um conjunto de contentores implantáveis, incluindo as suas propriedades e configurações.
Por exemplo, o ficheiro pode conter variáveis e portas ambientais. Também pode especificar parâmetros de implementação, tais como restrições de colocação, limites de recursos e nomes DNS, no ficheiro docker-compose.yml.

O [modelo de aplicação Service Fabric](service-fabric-application-model.md) utiliza tipos de serviço e tipos de aplicação, onde pode ter muitas instâncias de aplicação do mesmo tipo. Por exemplo, pode ter uma instância de aplicação por cliente. Este modelo baseado em tipo suporta várias versões do mesmo tipo de aplicação que está registada com o tempo de execução.

Por exemplo, o cliente A pode ter uma aplicação instantânea com o tipo 1.0 do AppTypeA, e o cliente B pode ter outra aplicação instantânea com o mesmo tipo e versão. Define os tipos de aplicação nos manifestos de aplicação e especifica o nome da aplicação e os parâmetros de implementação quando cria a aplicação.

Embora este modelo ofereça flexibilidade, também estamos a planear apoiar um modelo de implementação mais simples e baseado em instâncias onde os tipos estão implícitos a partir do ficheiro manifesto. Neste modelo, cada aplicação obtém o seu próprio manifesto independente. Estamos a visualizar este esforço adicionando suporte para docker-compose.yml, que é um formato de implementação baseado em exemplo.

## <a name="next-steps"></a>Passos seguintes

* Leia no modelo de [aplicação do Tecido de Serviço](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
