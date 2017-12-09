---
title: "Exemplo de Script da CLI de recursos de infraestrutura de serviço - atualização de uma aplicação num cluster"
description: "Exemplo de Script da CLI de recursos de infraestrutura de serviço - atualização de uma aplicação com uma nova versão. Este exemplo também atualiza uma aplicação implementada com os bits de novo."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: 059022a7f447fe2b0decaacf44ef7bc2e9a241d1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2017
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Adicionar um certificado de aplicação para um cluster do Service Fabric

Este script de exemplo carrega uma nova versão da aplicação existente e, em seguida, atualiza uma aplicação implementada com os bits de novo.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte o [documentação do Service Fabric CLI](../service-fabric-cli.md).

Amostras de CLI de recursos de infraestrutura de serviço adicionais para o Azure Service Fabric podem ser encontradas no [exemplos de Service Fabric CLI](../samples-cli.md).
