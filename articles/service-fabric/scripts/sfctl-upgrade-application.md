---
title: Atualizar uma aplicação em um cluster em sfctl
description: Exemplo de Script da CLI do Service Fabric - Atualizar uma aplicação com uma nova versão. Este exemplo também atualiza uma aplicação implementada com as novas partes.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614728"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Atualizar uma aplicação usando o Service Fabric CLI

Este script de exemplo carrega uma nova versão de uma aplicação existente e então atualiza uma aplicação implementada com as novas partes.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Passos seguintes

Para mais informações, veja a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Podem ser encontrados exemplos adicionais da CLI do Service Fabric para o Azure Service Fabric em [Exemplos da CLI do Service Fabric](../samples-cli.md).
