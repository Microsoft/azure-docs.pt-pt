---
title: Referência de Maven de malha de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Contém a referência para saber como utilizar o plug-in do Maven para a malha do Service Fabric
services: service-fabric-mesh
keywords: maven, java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811630"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Plug-in do maven para a malha de recursos de infraestrutura do serviço

## <a name="prerequisites"></a>Pré-requisitos

- SDK Java
- Maven
- CLI do Azure com a extensão de malha
- CLI do Service Fabric

## <a name="goals"></a>Objetivos

### `azure-sfmesh:init`
- Cria um `servicefabric` pasta que contém um `appresources` pasta que tem o `application.yaml` ficheiro. 

### `azure-sfmesh:addservice`
- Cria uma pasta dentro `servicefabric` pasta com o nome do serviço e cria o ficheiro do serviço YAML. 

### `azure-sfmesh:addnetwork`
- Gera uma `network` YAML com o nome de rede fornecido no `appresources` pasta 

### `azure-sfmesh:addgateway`
- Gera uma `gateway` YAML com o nome de gateway fornecido no `appresources` pasta 

### `azure-sfmesh:addsecret`
- Gera uma `secret` YAML com o nome secreto fornecido no `appresources` pasta 

### `azure-sfmesh:addsecretvalue`
- Gera uma `secretvalue` YAML com o nome do valor do segredo e secreta fornecido no `appresources` pasta 

### `azure-sfmesh:deploy`
- Intercala yamls do `servicefabric` pasta e cria um modelo do Azure Resource Manager JSON na pasta atual.
- Implementa todos os recursos no ambiente de malha do Azure Service Fabric 

### `azure-sfmesh:deploytocluster`
- Cria uma pasta (`meshDeploy`) que contém a implementação JSONs gerados a partir de yamls que são aplicáveis para clusters do Service Fabric
- Implementa todos os recursos de cluster do Service Fabric
 

## <a name="usage"></a>Utilização

Para utilizar o plug-in do Maven na sua aplicação Java do Maven, adicione o fragmento seguinte ao seu ficheiro pom. XML:

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

O plug-in do Maven atualmente não suporta configurações comuns de plug-ins do Maven para o Azure.

## <a name="how-to"></a>Procedimentos

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializar o projeto Maven para o modo de malha do Azure Service Fabric
Execute o seguinte comando para criar o ficheiro YAML de recursos do aplicativo.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Cria uma pasta chamada `servicefabric->appresources` na sua pasta de raiz que contém um aplicativo YAML com o nome `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Adicionar recursos à sua aplicação

#### <a name="add-a-new-network-to-your-application"></a>Adicionar uma nova rede à sua aplicação
Execute o comando abaixo para criar um yaml de recursos de rede. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Cria uma rede YAML na pasta `servicefabric->appresources` com o nome `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Adicionar um novo serviço à sua aplicação
Execute o comando abaixo para criar um yaml de serviço. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Cria um serviço YAML na pasta `servicefabric->helloworldservice` com o nome `service_helloworldservice` que faz referência `helloworldservicenetwork` & o `helloworldserver` aplicação
- O serviço deve escutar na porta 8080
- O serviço usará ***helloworldserver:latest*** como ele é a imagem de contentor.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Adicionar um novo recurso de gateway para a sua aplicação
Execute o comando abaixo para criar um yaml de recurso de gateway. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Cria um novo gateway YAML na pasta `servicefabric->appresources` com o nome `gateway_helloworldgateway`
- Referências `helloworldservicelistener` como o serviço de escuta do serviço está escutando para chamadas a partir deste gateway. Também faz referência a `helloworldservice` como o serviço `helloworldservicenetwork` que a rede e `helloworldserver` que a aplicação. 
- Está à escuta de pedidos na porta 80

#### <a name="add-a-new-volume-to-your-application"></a>Adicionar um novo volume à sua aplicação
Execute o comando abaixo para criar um yaml de recurso de volume. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Cria um volume YAML na pasta `servicefabric->appresources` com o nome `volume_vol1`
- Define as propriedades para os parâmetros necessários, `volumeAccountKey`, e `volumeShareName` como anteriormente
- Para obter mais informações sobre como fazer referência criada volume, visite o seguinte, [implementar aplicação com o Volume de ficheiros do Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Adicionar um novo recurso secreto à sua aplicação
Execute o comando abaixo para criar um recurso secreta yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Cria um YAML secreta na pasta `servicefabric->appresources` com o nome `secret_secret1`
- Para obter mais informações sobre como fazer referência a este segredo criado, visite o seguinte, [gerir segredos](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Adicionar um novo recurso de secretvalue à sua aplicação
Execute o comando abaixo para criar um yaml de recurso secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Criar um secretvalue YAML na pasta `servicefabric->appresources` com o nome `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Executar a aplicação localmente

Com a ajuda do objetivo `azure-sfmesh:deploytocluster`, pode executar a aplicação localmente, utilizando o comando abaixo:

```cmd
mvn azure-sfmesh:deploytocluster
```

Por predefinição este objetivo implementa recursos no local cluster. Se estiver a implementar no local cluster, parte do princípio que tem um cluster do Service Fabric local em funcionamento. Cluster do Service Fabric local para os recursos é atualmente suportado apenas no [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Cria JSONs de yamls que são aplicáveis para clusters do Service Fabric
- Em seguida, implementa para o ponto final de Cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Implementar a aplicação para o modo de malha do Azure Service Fabric

Com a ajuda do objetivo `azure-sfmesh:deploy`, pode implementar em ambiente de malha de recursos de infraestrutura do serviço ao executar o comando abaixo:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Cria um grupo de recursos denominado `todoapprg` se não existir.
- Cria um modelo do Azure Resource Manager JSON, mesclando as YAMLs. 
- Implementa o JSON para o ambiente de malha do Azure Service Fabric.