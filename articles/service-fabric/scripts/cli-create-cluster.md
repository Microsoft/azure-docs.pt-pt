---
title: Exemplo de Implementação do Script da CLI do Azure
description: Como criar um cluster de sistema de serviço seguro Linux em Azure utilizando a Interface da Linha de Comando Azure (CLI).
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
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8a6f23a5f4ce497c38358f06394f0f0aa97f9889
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739666"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Criar um cluster do Linux do Service Fabric seguro no Azure

Este comando cria um certificado autoassinado, adiciona-o a um cofre de chaves e transfere o certificado localmente.  O novo certificado é utilizado para proteger o cluster quando é implementado.  Também pode utilizar um certificado existente em vez de criar um novo.  De qualquer forma, o nome de requerente do certificado tem de corresponder ao domínio que utiliza para aceder ao cluster do Service Fabric. Esta partida é necessária para fornecer TLS para os pontos finais de gestão HTTPS do cluster e Explorador de Tecidos de Serviço. Não é possível obter um certificado TLS/SSL de um CA para o `.cloudapp.azure.com` domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando pedir um certificado de uma AC, o nome de requerente do certificado tem de corresponder ao nome do domínio personalizado que utilizar para o cluster.

Se necessário, instale a [CLI do Azure](cli/azure/install-azure-cli).

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
| [az sf cluster create](/cli/azure/sf/cluster) | Cria um novo cluster do Service Fabric.  |

## <a name="next-steps"></a>Passos seguintes

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
