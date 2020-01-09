---
title: Armazenando e usando os segredos do aplicativo de malha de Service Fabric do Azure
description: A malha Service Fabric dá suporte a segredos como recursos do Azure. Veja como armazenar e gerenciar segredos com seus aplicativos de Service Fabric malha.
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: d45b9e98c1f325e5dbd656c85655a4ac72e4c3e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459126"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos do aplicativo Service Fabric mesh
A malha Service Fabric dá suporte a segredos como recursos do Azure. Um segredo de malha Service Fabric pode ser qualquer informação de texto confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos com segurança.

![Visão geral dos segredos da malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos de segredos de malha
Um segredo de aplicativo de malha consiste em:
* Um recurso de **segredos** , que é um contêiner que armazena segredos de texto. Os segredos contidos no recurso de **segredos** são armazenados e transmitidos com segurança.
* Um ou mais recursos de **segredos/valores** que são armazenados no contêiner de recursos de **segredos** . Cada recurso de **segredos/valores** é diferenciado por um número de versão.

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre os segredos de malha Service Fabric, consulte:
- [Gerenciar segredos do aplicativo de malha Service Fabric](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao modelo de recurso Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
