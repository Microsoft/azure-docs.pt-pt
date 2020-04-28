---
title: Referência de Malha de Tecido de Serviço Azure Maven
description: Contém a referência para como usar o plugin Maven para malha de tecido de serviço
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75459015"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven Plugin para malha de tecido de serviço

## <a name="prerequisites"></a>Pré-requisitos

- SDK Java
- Maven
- Azure CLI com extensão de malha
- CLI do Service Fabric

## <a name="goals"></a>Objetivos

### `azure-sfmesh:init`
- Cria `servicefabric` uma pasta `appresources` que contém `application.yaml` uma pasta que tem o ficheiro. 

### `azure-sfmesh:addservice`
- Cria uma `servicefabric` pasta dentro de uma pasta com o nome de serviço e cria o ficheiro YAML do serviço. 

### `azure-sfmesh:addnetwork`
- Gera um `network` YAML com o nome `appresources` de rede fornecido na pasta 

### `azure-sfmesh:addgateway`
- Gera um `gateway` YAML com o nome `appresources` de gateway fornecido na pasta 

#### `azure-sfmesh:addvolume`
- Gera um `volume` YAML com o nome `appresources` de volume fornecido na pasta.

### `azure-sfmesh:addsecret`
- Gera um `secret` YAML com o nome `appresources` secreto fornecido na pasta 

### `azure-sfmesh:addsecretvalue`
- Gera um `secretvalue` YAML com o nome de `appresources` valor secreto e secreto fornecido na pasta 

### `azure-sfmesh:deploy`
- Funde os yamls `servicefabric` da pasta e cria um modelo de Gestor de Recursos Azure JSON na pasta atual.
- Implanta todos os recursos para o ambiente de malha de tecido de serviço Azure 

### `azure-sfmesh:deploytocluster`
- Cria uma`meshDeploy`pasta () que contém os JSONs de implantação gerados a partir de yamls que são aplicáveis aos clusters de tecido de serviço
- Implanta todos os recursos para o cluster de Tecido de Serviço
 

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

O plugin Maven atualmente não suporta configurações comuns de Maven Plugins para Azure.

## <a name="how-to"></a>Procedimentos

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializar projeto Maven para malha de tecido de serviço Azure
Executar o seguinte comando para criar o ficheiro YAML de recurso de aplicação.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Cria uma `servicefabric->appresources` pasta chamada na sua pasta raiz que contém uma aplicação yAML chamada`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Adicione recurso à sua aplicação

#### <a name="add-a-new-network-to-your-application"></a>Adicione uma nova rede à sua aplicação
Execute o comando abaixo para criar um yaml de recursos de rede. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Cria uma rede YAML na pasta `servicefabric->appresources` chamada`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Adicione um novo serviço à sua aplicação
Execute o comando abaixo para criar um yaml de serviço. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Cria um serviço YAML `service_helloworldservice` na `helloworldservicenetwork` pasta `servicefabric->helloworldservice` `helloworldserver` chamada que refere & app
- O serviço iria ouvir no porto 8080
- O serviço estaria a usar ***o Helloworldserver: mais recente*** como a sua imagem de contentor.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Adicione um novo recurso de gateway à sua aplicação
Execute o comando abaixo para criar um yaml de recurso gateway. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Cria uma nova gateway YAML na pasta `servicefabric->appresources` chamada`gateway_helloworldgateway`
- Referências `helloworldservicelistener` como o ouvinte de serviço que está a ouvir chamadas deste portal. Refere também `helloworldservice` o serviço, `helloworldservicenetwork` como a `helloworldserver` rede e como aplicação. 
- Escutas para pedidos no porto 80

#### <a name="add-a-new-volume-to-your-application"></a>Adicione um novo volume à sua aplicação
Executar o comando abaixo para criar um yaml de recurso de volume. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Cria um volume YAML na pasta `servicefabric->appresources` chamada`volume_vol1`
- Define propriedades para parâmetros `volumeAccountKey`necessários, e `volumeShareName` como acima
- Para obter mais informações sobre como referenciar este volume criado, visite o seguinte, Implemente app utilizando o Volume de [Ficheiros Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Adicione um novo recurso secreto à sua aplicação
Execute o comando abaixo para criar um yaml de recurso secreto. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Cria um YAML `servicefabric->appresources` secreto na pasta chamada`secret_secret1`
- Para mais informações sobre como referenciar este segredo criado, visite o seguinte, [Gerir Segredos](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Adicione um novo recurso de valor secreto à sua aplicação
Execute o comando abaixo para criar um yaml de recurso de valor secreto. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Criar um YAML de `servicefabric->appresources` valor secreto na pasta chamada`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Executar a aplicação localmente

Com a ajuda `azure-sfmesh:deploytocluster`do objetivo, pode executar a aplicação localmente utilizando o comando abaixo:

```cmd
mvn azure-sfmesh:deploytocluster
```

Por defeito, este objetivo descoloca recursos para o cluster local. Se você está implantando para cluster local, ele assume que você tem um cluster de tecido de serviço local em funcionamento. O cluster de recursos do Tecido de Serviço Local é atualmente suportado apenas no [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Cria JSONs a partir de yamls que são aplicáveis aos clusters de tecido de serviço
- Implanta-se então para o ponto final do Cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Implementar aplicação para malha de tecido de serviço Azure

Com a ajuda `azure-sfmesh:deploy`do objetivo, pode ser implantado para o Service Fabric Mesh Environment executando o comando abaixo:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Cria um grupo `todoapprg` de recursos chamado se não existir.
- Cria um modelo de Gestor de Recursos Azure JSON através da fusão dos YAMLs. 
- Implanta o JSON para o ambiente de malha de tecido de serviço Azure.
