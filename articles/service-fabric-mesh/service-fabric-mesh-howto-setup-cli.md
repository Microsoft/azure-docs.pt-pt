---
title: Configurar o ClI de malha de tecido de serviço Azure
description: A Interface de Linha de Comando de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e em malha de tecido de serviço Azure. Eis como preparar tudo.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 0fb65ceeabca9331130083f8ec5b3fe8acce13b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259191"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh
A Interface de Linha de Comando de Malha de Tecido de Serviço (CLI) é necessária para implantar e gerir recursos localmente e em malha de tecido de serviço Azure. Eis como preparar tudo.

Existem três tipos de CLI que podem ser usados e são resumidos na tabela abaixo.

| Módulo CLI | Ambiente-alvo |  Descrição | 
|---|---|---|
| malha az | Malha de tecido de serviço azure | O CLI primário que lhe permite implementar as suas aplicações e gerir recursos contra o ambiente de malha de tecido de serviço Azure. 
| sfctl | Aglomerados locais | Serviço Tecido CLI que permite a implantação e teste de recursos de Tecido de Serviço contra clusters locais.  
| Maven CLI | Aglomerados locais & malha de tecido de serviço Azure | Um invólucro `az mesh` ao `sfctl` redor e que permite que os desenvolvedores de Java utilizem uma experiência familiar de linha de comando para experiência de desenvolvimento local e Azure.  

Para a pré-visualização, a CLI do Azure Service Fabric Mesh é escrita como uma extensão da CLI do Azure. Pode instalá-la no Azure Cloud Shell ou numa instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instale o CLI de malha de tecido de serviço Azure
1. Tem de instalar a versão Azure CLI 2.0.67 ou posterior. Executar `az --version` para localizar a versão. Para instalar ou atualizar para a versão mais recente da CLI, veja [Instalar a CLI do Azure][azure-cli-install].

2. Instale o módulo de extensão CLI de malha de tecido de tecido de serviço Azure utilizando o seguinte comando. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Atualize um módulo CLI de malha de tecido de tecido azure existente utilizando o seguinte comando.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instale o Tecido de Serviço CLI (sfctl) 

Siga as instruções no Conjunto de [Tecidos de Serviço CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). O módulo **sfctl** pode ser utilizado para a implementação de aplicações com base no modelo de recursos contra os clusters de tecido de serviço na sua máquina local. 

## <a name="install-the-maven-cli"></a>Instale o Maven CLI 

Para utilizar o Maven CLI, é necessário instalar na sua máquina o seguinte: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Malha Azure CLI (malha az) - Para direcionar a malha de tecido de serviço Azure 
* SFCTL (sfctl) - Para direcionar os clusters locais 

O Maven CLI para tecido de serviço ainda está em pré-visualização. 

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

Leia a secção de [referência maven CLI](service-fabric-mesh-reference-maven.md) para saber sobre o uso detalhado.

## <a name="next-steps"></a>Passos seguintes

Pode também configurar o seu [ambiente de desenvolvimento no Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas a [perguntas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
