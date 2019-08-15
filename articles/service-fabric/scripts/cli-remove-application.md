---
title: Exemplo de Remoção de Script da CLI do Azure Service Fabric (sfctl)
description: Remover uma aplicação de um cluster do Azure Service Fabric com a CLI do Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: faec3a519333eab1774989804002e339e509ebba
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035294"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover uma aplicação de um cluster do Service Fabric Explorer

Este script de exemplo elimina uma instância de aplicação do Service Fabric em execução e anula o registo de um tipo de aplicação e a versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Em seguida, são eliminados os ficheiros da aplicação do arquivo de imagens. 

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, veja a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
