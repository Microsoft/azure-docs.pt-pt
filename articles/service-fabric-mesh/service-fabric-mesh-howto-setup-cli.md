---
title: Configurar o CLI de malha de tecido de serviço Azure
description: A Interface de Linha de Rede de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e na Malha de Tecido de Serviço Azure. Eis como preparar isto.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613346"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

A Interface de Linha de Rede de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e na Malha de Tecido de Serviço Azure. Eis como preparar isto.

Existem três tipos de CLI que podem ser usados e são resumidos na tabela abaixo.

| Módulo CLI | Ambiente-alvo |  Description | 
|---|---|---|
| malha az | Malha de tecido de serviço Azure | O CLI primário que lhe permite implementar as suas aplicações e gerir recursos contra o ambiente de malha de tecido de serviço Azure. 
| sfctl | Aglomerados locais | CLI de Tecido de Serviço que permite a implantação e teste dos recursos do Tecido de Serviço contra clusters locais.  
| Maven CLI | Aglomerados locais & Malha de Tecido de Serviço Azure | Um invólucro ao redor `az mesh` e que permite que os `sfctl` desenvolvedores de Java usem uma experiência familiar de linha de comando para experiência de desenvolvimento local e Azure.  

Para a pré-visualização, a CLI do Azure Service Fabric Mesh é escrita como uma extensão da CLI do Azure. Pode instalá-la no Azure Cloud Shell ou numa instalação local da CLI do Azure. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.67 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalar o Azure Service Fabric Mesh CLI

Se ainda não o fez, instale o módulo de extensão CLI da malha de tecido de serviço Azure utilizando o seguinte comando: 
 
```azurecli-interactive
az extension add --name mesh
```

Se já estiver instalado, atualize o módulo CLI da malha de tecido de serviço Azure existente utilizando o seguinte comando:

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
