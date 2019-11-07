---
title: Configurar a CLI do Azure Service Fabric Mesh | Microsoft Docs
description: A CLI (interface de linha de comando) de malha Service Fabric é necessária para implantar e gerenciar recursos localmente e na malha de Service Fabric do Azure. Veja como configurá-lo.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5c9733a5fc5aec337a326fe39e78c3e5c0b8ca4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686299"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh
A CLI (interface de linha de comando) de malha Service Fabric é necessária para implantar e gerenciar recursos localmente e na malha de Service Fabric do Azure. Veja como configurá-lo.

Há três tipos de CLI que podem ser usados e resumidos na tabela a seguir.

| Módulo da CLI | Ambiente de destino |  Descrição | 
|---|---|---|
| malha AZ | Malha de Service Fabric do Azure | A CLI principal que permite que você implante seus aplicativos e gerencie recursos no ambiente de malha de Service Fabric do Azure. 
| sfctl | Clusters locais | Service Fabric CLI que permite a implantação e o teste de Service Fabric recursos em clusters locais.  
| CLI do Maven | Clusters locais & malha de Service Fabric do Azure | Um wrapper em torno de `az mesh` e `sfctl` que permite aos desenvolvedores de Java usar uma experiência de linha de comando familiar para a experiência de desenvolvimento local e do Azure.  

Para a pré-visualização, a CLI do Azure Service Fabric Mesh é escrita como uma extensão da CLI do Azure. Pode instalá-la no Azure Cloud Shell ou numa instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalar a CLI da malha de Service Fabric do Azure
1. Você deve instalar o CLI do Azure versão 2.0.67 ou posterior. Executar `az --version` para localizar a versão. Para instalar ou atualizar para a versão mais recente da CLI, consulte [instalar o CLI do Azure][azure-cli-install].

2. Instale o módulo de extensão da CLI da malha de Service Fabric do Azure usando o comando a seguir. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Atualize um módulo CLI existente do Azure Service Fabric mesh usando o comando a seguir.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalar a CLI do Service Fabric (sfctl) 

Siga as instruções em [Configurar a CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). O módulo **sfctl** pode ser usado para implantação de aplicativos com base no modelo de recurso em relação a clusters Service Fabric em seu computador local. 

## <a name="install-the-maven-cli"></a>Instalar a CLI do Maven 

Para usar a CLI do Maven, o seguinte precisa ser instalado em seu computador: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* CLI da malha do Azure (AZ mesh)-para direcionar a malha de Service Fabric do Azure 
* SFCTL (SFCTL) – para os clusters locais de destino 

A CLI do Maven para Service Fabric ainda está em versão prévia. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Leia a seção de [referência da CLI do Maven](service-fabric-mesh-reference-maven.md) para saber mais sobre o uso detalhado.

## <a name="next-steps"></a>Passos seguintes

Pode também configurar o seu [ambiente de desenvolvimento no Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas a [perguntas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
