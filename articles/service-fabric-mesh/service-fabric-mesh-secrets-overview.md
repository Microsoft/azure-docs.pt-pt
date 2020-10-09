---
title: Armazenar e usar segredos de aplicação de malha de malha de tecido de serviço Azure
description: A Malha de Tecido de Serviço suporta segredos como recursos Azure. Aqui está como armazenar e gerir segredos com as suas aplicações de Malha de Tecido de Serviço.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76277623"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos de aplicação de malha de tecido de serviço
A Malha de Tecido de Serviço suporta segredos como recursos Azure. Um segredo de malha de tecido de serviço pode ser qualquer informação de texto sensível, como cadeias de ligação de armazenamento, palavras-passe ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Visão geral dos segredos de malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos de segredos de malha
Uma aplicação de malha Secreta consiste em:
* Um recurso **Secrets,** que é um recipiente que armazena segredos de texto. Os segredos **contidos** no recurso Secrets são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Secrets/Values** que estão armazenados no contentor de recursos **Secrets.** Cada recurso **Segredos/Valores** distingue-se por um número de versão.

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre os Segredos de Malha de Tecido de Serviço, consulte:
- [Gerir segredos de aplicação de malha de tecido de serviço](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao Modelo de Recursos de Tecido de Serviço](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
