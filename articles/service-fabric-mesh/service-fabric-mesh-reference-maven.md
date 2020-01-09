---
title: Referência do Maven da malha de Service Fabric do Azure
description: Contém a referência sobre como usar o plug-in do Maven para a malha de Service Fabric
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459015"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Plug-in do Maven para malha de Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

- Java SDK
- Maven
- CLI do Azure com extensão de malha
- CLI do Service Fabric

## <a name="goals"></a>Objetivos

### `azure-sfmesh:init`
- Cria uma pasta `servicefabric` que contém uma pasta `appresources` que tem o arquivo `application.yaml`. 

### `azure-sfmesh:addservice`
- Cria uma pasta dentro de `servicefabric` pasta com o nome do serviço e cria o arquivo YAML do serviço. 

### `azure-sfmesh:addnetwork`
- Gera um `network` YAML com o nome de rede fornecido na pasta `appresources` 

### `azure-sfmesh:addgateway`
- Gera um `gateway` YAML com o nome de gateway fornecido na pasta `appresources` 

#### `azure-sfmesh:addvolume`
- Gera um `volume` YAML com o nome de volume fornecido na pasta `appresources`.

### `azure-sfmesh:addsecret`
- Gera um `secret` YAML com o nome de segredo fornecido na pasta `appresources` 

### `azure-sfmesh:addsecretvalue`
- Gera um `secretvalue` YAML com o segredo fornecido e o nome do valor secreto na pasta `appresources` 

### `azure-sfmesh:deploy`
- Mescla o yamls da pasta `servicefabric` e cria um Azure Resource Manager modelo JSON na pasta atual.
- Implanta todos os recursos no ambiente de malha de Service Fabric do Azure 

### `azure-sfmesh:deploytocluster`
- Cria uma pasta (`meshDeploy`) que contém os JSON de implantação gerados de yamls que são aplicáveis para clusters Service Fabric
- Implanta todos os recursos no Cluster Service Fabric
 

## <a name="usage"></a>Utilização

Para usar o plug-in do Maven em seu aplicativo Java do Maven, adicione o seguinte trecho ao arquivo pom. xml:

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

O plug-in do Maven atualmente não dá suporte a configurações comuns de plug-ins do Maven para o Azure.

## <a name="how-to"></a>Procedimentos

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializar o projeto Maven para a malha de Service Fabric do Azure
Execute o comando a seguir para criar o arquivo YAML do recurso do aplicativo.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Cria uma pasta chamada `servicefabric->appresources` na pasta raiz que contém um aplicativo YAML chamado `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Adicionar recurso ao seu aplicativo

#### <a name="add-a-new-network-to-your-application"></a>Adicionar uma nova rede ao seu aplicativo
Execute o comando a seguir para criar um recurso de rede YAML. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Cria uma YAML de rede na pasta `servicefabric->appresources` chamada `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Adicionar um novo serviço ao seu aplicativo
Execute o comando a seguir para criar um YAML de serviço. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Cria um YAML de serviço na pasta `servicefabric->helloworldservice` chamado `service_helloworldservice` que faz referência `helloworldservicenetwork` & aplicativo `helloworldserver`
- O serviço escutaria na porta 8080
- O serviço estaria usando ***helloworldserver: mais recente*** como a imagem de contêiner.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Adicionar um novo recurso de gateway ao seu aplicativo
Execute o comando a seguir para criar um recurso de gateway YAML. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Cria um novo YAML de gateway na pasta `servicefabric->appresources` chamada `gateway_helloworldgateway`
- Faz referência `helloworldservicelistener` como o ouvinte de serviço que está escutando chamadas desse gateway. Também faz referência à `helloworldservice` como o serviço, `helloworldservicenetwork` como a rede e `helloworldserver` como o aplicativo. 
- Escuta solicitações na porta 80

#### <a name="add-a-new-volume-to-your-application"></a>Adicionar um novo volume ao seu aplicativo
Execute o comando a seguir para criar um recurso de volume YAML. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Cria um volume YAML na pasta `servicefabric->appresources` chamada `volume_vol1`
- Define propriedades para os parâmetros obrigatórios, `volumeAccountKey`e `volumeShareName` como acima
- Para obter mais informações sobre como fazer referência a esse volume criado, visite o seguinte, [implantar aplicativo usando o volume de arquivos do Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Adicionar um novo recurso secreto ao seu aplicativo
Execute o comando a seguir para criar um recurso secreto YAML. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Cria um YAML secreto na pasta `servicefabric->appresources` chamada `secret_secret1`
- Para obter mais informações sobre como fazer referência a esse segredo criado, visite o seguinte, [gerenciar segredos](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Adicionar um novo recurso secretovalue ao seu aplicativo
Execute o comando a seguir para criar um recurso secretovalue YAML. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Criar um nome de segredo YAML na pasta `servicefabric->appresources` chamada `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Executar a aplicação localmente

Com a ajuda do `azure-sfmesh:deploytocluster`de metas, você pode executar o aplicativo localmente usando o comando a seguir:

```cmd
mvn azure-sfmesh:deploytocluster
```

Por padrão, essa meta implanta recursos no cluster local. Se você estiver implantando no cluster local, ele pressupõe que você tenha um Cluster Service Fabric local em funcionamento. O cluster de Service Fabric local para recursos atualmente tem suporte apenas no [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Cria JSONs de yamls que se aplicam a clusters Service Fabric
- Implanta então no ponto de extremidade do cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Implantar aplicativo na malha de Service Fabric do Azure

Com a ajuda do `azure-sfmesh:deploy`de metas, você pode implantar o em Service Fabric ambiente de malha executando o comando a seguir:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Cria um grupo de recursos chamado `todoapprg` se ele não existir.
- Cria um Azure Resource Manager de modelo JSON mesclando o YAMLs. 
- Implanta o JSON no ambiente de malha de Service Fabric do Azure.
