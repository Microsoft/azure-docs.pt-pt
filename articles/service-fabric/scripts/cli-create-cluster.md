---
title: Exemplo de Implementação do Script da CLI do Azure
description: Criar um cluster do Linux do Service Fabric seguro no Azure com a CLI do Azure.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1386be8093cac4f5271886ff736b24acd483d64e
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032813"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Criar um cluster do Linux do Service Fabric seguro no Azure

Este comando cria um certificado autoassinado, adiciona-o a um cofre de chaves e transfere o certificado localmente.  O novo certificado é utilizado para proteger o cluster quando é implementado.  Também pode utilizar um certificado existente em vez de criar um novo.  De qualquer forma, o nome de requerente do certificado tem de corresponder ao domínio que utiliza para aceder ao cluster do Service Fabric. Esta correspondência é necessária para fornecer um SSL para os pontos finais de gestão HTTPS do cluster e o Service Fabric Explorer. Não pode obter um certificado SSL a partir de uma AC para o domínio `.cloudapp.azure.com`. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Se necessário, instale a [CLI do Azure](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos, o cluster e todos os recursos relacionados.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Cria um novo cluster do Service Fabric.  |

## <a name="next-steps"></a>Passos seguintes

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
