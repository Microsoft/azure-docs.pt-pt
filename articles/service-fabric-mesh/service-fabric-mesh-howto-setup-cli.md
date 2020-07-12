---
title: Configurar o CLI de malha de tecido de serviço Azure
description: A Interface de Linha de Rede de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e na Malha de Tecido de Serviço Azure. Eis como preparar isto.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 3ce2dbcefa63cc55a66e712664d1436ca24d3a1d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248548"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh
A Interface de Linha de Rede de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e na Malha de Tecido de Serviço Azure. Eis como preparar isto.

Existem três tipos de CLI que podem ser usados e são resumidos na tabela abaixo.

| Módulo CLI | Ambiente-alvo |  Descrição | 
|---|---|---|
| malha az | Malha de tecido de serviço Azure | O CLI primário que lhe permite implementar as suas aplicações e gerir recursos contra o ambiente de malha de tecido de serviço Azure. 
| sfctl | Aglomerados locais | CLI de Tecido de Serviço que permite a implantação e teste dos recursos do Tecido de Serviço contra clusters locais.  
| Maven CLI | Aglomerados locais & Malha de Tecido de Serviço Azure | Um invólucro ao redor `az mesh` e que permite que os `sfctl` desenvolvedores de Java usem uma experiência familiar de linha de comando para experiência de desenvolvimento local e Azure.  

Para a pré-visualização, a CLI do Azure Service Fabric Mesh é escrita como uma extensão da CLI do Azure. Pode instalá-la no Azure Cloud Shell ou numa instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalar o Azure Service Fabric Mesh CLI
1. Tem de instalar a versão Azure CLI 2.0.67 ou posterior. Executar `az --version` para localizar a versão. Para instalar ou atualizar para a versão mais recente da CLI, veja [Instalar a CLI do Azure][azure-cli-install].

2. Instale o módulo de extensão CLI da malha de malha de tecido de serviço Azure utilizando o seguinte comando. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Atualize um módulo CLI de malha de malha de tecido de serviço Azure existente utilizando o seguinte comando.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instale o CLI de Tecido de Serviço (sfctl) 

Siga as instruções sobre [configurar o Tecido de Serviço CLI](../service-fabric/service-fabric-cli.md). O módulo **sfctl** pode ser utilizado para a implementação de aplicações com base no modelo de recursos contra clusters de Tecido de Serviço na sua máquina local. 

## <a name="install-the-maven-cli"></a>Instale o Maven CLI 

Para utilizar o Maven CLI, é necessário instalar na sua máquina o seguinte: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (malha az) - Para visar a malha de tecido de serviço Azure 
* SFCTL (sfctl) - Para direcionar os aglomerados locais 

O Maven CLI para o Tecido de Serviço ainda está em pré-visualização. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Leia a secção [de referência do Maven CLI](service-fabric-mesh-reference-maven.md) para saber mais sobre o uso detalhado.

## <a name="next-steps"></a>Passos seguintes

Pode também configurar o seu [ambiente de desenvolvimento no Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas a [perguntas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
