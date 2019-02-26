---
title: Exemplo de implementação do Script da CLI do Service Fabric do Azure (sfctl)
description: Implementar uma aplicação num cluster do Service Fabric do Azure com a CLI do Service Fabric do Azure
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 1723bdd29bf85b11bf0a5d86d51cb813abd6e6b4
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804098"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação num cluster do Service Fabric

Este script de exemplo copia um pacote de aplicações para um arquivo de imagens do cluster, regista o tipo de aplicação no cluster e cria uma instância de aplicação no tipo de aplicação. Todos os serviços predefinidos também são criados nesta fase.

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Quando terminar, o script [remover](cli-remove-application.md) pode ser utilizado para remover a aplicação. O script remover elimina a instância de aplicação, anula o registo do tipo de aplicação e elimina o pacote de aplicações do arquivo de imagens.

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, veja a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
