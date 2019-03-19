---
title: Configurar a CLI do Azure Service Fabric Mesh | Microsoft Docs
description: Saiba como configurar a CLI do Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7b817eefc7a17966060eb7ca05e21f70f04d0878
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885458"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh
Malha Interface de linha de comandos (CLI do Service Fabric) é necessário para implementar e gerir recursos localmente e na malha do Azure Service Fabric. 

Existem três tipos de CLI que podem ser utilizados e estes estão resumidos na tabela abaixo. 

| Módulo CLI | Ambiente de destino |  Descrição | 
|---|---|---|
| malha de AZ | Malha de recursos de infraestrutura do serviço do Azure | A CLI principal que permite-lhe implementar as suas aplicações e gerir os recursos contra o ambiente de malha do Azure Service Fabric. 
| sfctl | Clusters locais | CLI do Service Fabric que permite a implantação e teste de recursos de infraestrutura do serviço em relação a clusters locais.  
| Maven CLI | Clusters locais e o modo de malha do Azure Service Fabric | Um wrapper em torno de "malha az" e "sfctl" que permite que o Java os desenvolvedores usem uma linha de comandos familiares experiência para uma experiência de desenvolvimento local e do Azure.  

Para a pré-visualização, a CLI do Azure Service Fabric Mesh é escrita como uma extensão da CLI do Azure. Pode instalá-la no Azure Cloud Shell ou numa instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalar a malha de recursos de infraestrutura do serviço do Azure CLI
1. Tem de instalar a CLI do Azure versão 2.0.43 ou posterior. Executar `az --version` para localizar a versão. Para instalar ou atualizar para a versão mais recente da CLI, veja [Instalar a CLI do Azure][azure-cli-install].

2. Instale o módulo de extensão de CLI de malha do Azure Service Fabric com o seguinte comando. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Atualize um módulo de CLI de malha do Azure Service Fabric existente, utilizando o seguinte comando.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalar a CLI do Service Fabric (sfctl) 

Siga as instruções [configurar a CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). O **sfctl** módulo pode ser utilizado para implementação de aplicações com base no modelo de recurso em relação a clusters do Service Fabric no seu computador local. 

## <a name="install-the-maven-cli"></a>Instalar o CLI do Maven 

Para poder utilizar a CLI do Maven, a seguinte tem de ser instalado no seu computador: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* CLI do Azure de malha (malha de az -) para o modo de malha do Azure Service Fabric de destino 
* SFCTL (sfctl) - para clusters do locais de destino 

A CLI do Maven para o Service Fabric ainda está em pré-visualização. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Leitura a [referência da CLI do Maven](service-fabric-mesh-reference-maven.md) secção para saber mais sobre a utilização detalhada.

## <a name="next-steps"></a>Passos Seguintes

Pode também configurar o seu [ambiente de desenvolvimento no Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas a [perguntas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
