---
title: Exemplo de Script da CLI do Service Fabric - Listar aplicações num cluster
description: Exemplo de Script da CLI do Service Fabric - Listar as aplicações aprovisionadas num cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/13/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: ''
ms.openlocfilehash: 8fd83190f3cf92ef0f88ff0fb2a807e03199a5c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621935"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Listar as aplicações a executar num cluster do Service Fabric

Este script de exemplo liga-se a um cluster do Service Fabric e, em seguida, lista todas as aplicações aprovisionadas.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de exemplo

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Retrieve all applications from the cluster
sfctl application list
```

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, veja a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
<!--Update_Description: update meta properties -->