---
title: Referência de Malha maven de malha de tecido de serviço Azure
description: Contém a referência para como usar o plugin Maven para malha de tecido de serviço
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625588"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Plugin Maven para malha de tecido de serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

## <a name="prerequisites"></a>Pré-requisitos

- SDK Java
- Maven
- CLI azul com extensão de malha
- CLI do Service Fabric

## <a name="goals"></a>Objetivos

### `azure-sfmesh:init`
- Cria uma `servicefabric` pasta que contém uma pasta que tem o `appresources` `application.yaml` ficheiro. 

### `azure-sfmesh:addservice`
- Cria uma pasta interior `servicefabric` com o nome de serviço e cria o ficheiro YAML do serviço. 

### `azure-sfmesh:addnetwork`
- Gera um `network` YAML com o nome de rede fornecido na `appresources` pasta 

### `azure-sfmesh:addgateway`
- Gera um `gateway` YAML com o nome de gateway fornecido na `appresources` pasta 

#### `azure-sfmesh:addvolume`
- Gera um `volume` YAML com o nome de volume fornecido na `appresources` pasta.

### `azure-sfmesh:addsecret`
- Gera um `secret` YAML com o nome secreto fornecido na `appresources` pasta 

### `azure-sfmesh:addsecretvalue`
- Gera um `secretvalue` YAML com o nome de valor secreto e secreto fornecido na `appresources` pasta 

### `azure-sfmesh:deploy`
- Funde os yamls da `servicefabric` pasta e cria um modelo de Gestor de Recursos Azure JSON na pasta atual.
- Implanta todos os recursos para o ambiente de malha de tecido de serviço Azure 

### `azure-sfmesh:deploytocluster`
- Cria uma pasta que `meshDeploy` contém a implantação JSONs gerada a partir de yamls que são aplicáveis para clusters de tecido de serviço
- Implanta todos os recursos para o cluster de Tecidos de Serviço
 

## <a name="usage"></a>Utilização

Para utilizar o plugin Maven na sua aplicação Maven Java, adicione o seguinte corte ao seu ficheiro pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Configuração comum

O plugin Maven não suporta atualmente configurações comuns de Plugins Maven para Azure.

## <a name="how-to"></a>Procedimentos

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Projeto Inicialize Maven para Azure Service Fabric Mesh
Executar o seguinte comando para criar o ficheiro YAML de recurso de aplicação.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Cria uma pasta chamada `servicefabric->appresources` na sua pasta raiz que contém uma aplicação chamada YAML `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Adicionar recurso à sua aplicação

#### <a name="add-a-new-network-to-your-application"></a>Adicione uma nova rede à sua aplicação
Executar o comando abaixo para criar um yaml de recurso de rede. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Cria uma rede YAML em pasta `servicefabric->appresources` chamada `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Adicione um novo serviço à sua aplicação
Executar o comando abaixo para criar um serviço yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Cria um serviço YAML em pasta `servicefabric->helloworldservice` com o nome de `service_helloworldservice` referências & a `helloworldservicenetwork` `helloworldserver` app
- O serviço ouviria no porto 8080
- O serviço estaria a usar ***helloworldserver:o mais recente*** como a sua imagem de contentor.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Adicione um novo recurso de gateway à sua aplicação
Executar o comando abaixo para criar um recurso de gateway yaml. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Cria uma nova porta DE ENTRADA YAML em pasta `servicefabric->appresources` chamada `gateway_helloworldgateway`
- Referências `helloworldservicelistener` como o ouvinte de serviço que está a ouvir chamadas deste gateway. Também refere o `helloworldservice` serviço como o serviço, como a rede `helloworldservicenetwork` e como a `helloworldserver` aplicação. 
- Escutas para pedidos no porto 80

#### <a name="add-a-new-volume-to-your-application"></a>Adicione um novo volume à sua aplicação
Executar o comando abaixo para criar um yaml de recurso de volume. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Cria um volume YAML em pasta `servicefabric->appresources` nomeada `volume_vol1`
- Define propriedades para parâmetros `volumeAccountKey` necessários, e como `volumeShareName` acima
- Para obter mais informações sobre como fazer referência a este volume criado, visite o seguinte, [Implementar App usando volume de ficheiros Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Adicione um novo recurso secreto à sua aplicação
Executar o comando abaixo para criar um recurso secreto yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Cria um YAML secreto em pasta `servicefabric->appresources` chamada `secret_secret1`
- Para mais informações sobre como fazer referência a este segredo criado, visite o seguinte, [Gerir Segredos](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Adicione um novo recurso de valor secreto à sua aplicação
Executar o comando abaixo para criar um recurso secretvalue yaml. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Criar um valor secreto YAML em pasta `servicefabric->appresources` nomeada `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Executar a aplicação localmente

Com a ajuda do `azure-sfmesh:deploytocluster` objetivo, pode executar a aplicação localmente usando o comando abaixo:

```cmd
mvn azure-sfmesh:deploytocluster
```

Por defeito, este objetivo implanta recursos para o cluster local. Se estiver a implantar para o cluster local, assume que tem um cluster local de Tecido de Serviço a funcionar. O cluster de tecido de serviço local para recursos é atualmente suportado apenas no [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Cria JSONs a partir de yamls que são aplicáveis para clusters de tecido de serviço
- Implanta então para o ponto final do Cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Implementar aplicação para a malha de tecido de serviço Azure

Com a ajuda do `azure-sfmesh:deploy` objetivo, pode implementar para o Ambiente de Malha de Tecido de Serviço, executando o comando abaixo:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Cria um grupo de recursos chamado `todoapprg` se não existir.
- Cria um modelo de Gestor de Recursos Azure JSON fundindo os YAMLs. 
- Implanta o JSON no ambiente de malha de tecido de serviço Azure.
