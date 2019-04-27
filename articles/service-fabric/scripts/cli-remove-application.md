---
title: Exemplo de Remoção de Script da CLI do Azure Service Fabric (sfctl)
description: Remover uma aplicação de um cluster do Azure Service Fabric com a CLI do Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 12/06/2017
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 4e0f4f7f4b8bfc643f0680b7fa1d1fc5b063eb5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621969"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover uma aplicação de um cluster do Service Fabric Explorer

Este script de exemplo elimina uma instância de aplicação do Service Fabric em execução e anula o registo de um tipo de aplicação e a versão do cluster.  A eliminação da instância da aplicação também elimina todas as instâncias do serviço em execução associadas a essa aplicação. Em seguida, são eliminados os ficheiros da aplicação do arquivo de imagens. 

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Delete the application
sfctl application delete \
    --application-id svcfab_app \
    --timeout 500

# Unprovision the application type
sfctl application unprovision \
    --application-type-name svcfab_appType \
    --application-type-version 1.0.0 \
    --timeout 500

# Delete the application files from the image store
sfctl store delete \
    --content-path myappfolder
```

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, veja a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).

<!--Update_Description: update meta properties, update link -->