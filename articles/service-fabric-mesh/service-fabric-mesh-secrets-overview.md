---
title: Armazenando e usando os segredos do aplicativo de malha de Service Fabric do Azure
description: A malha Service Fabric dá suporte a segredos como recursos do Azure. Veja como armazenar e gerenciar segredos com seus aplicativos de Service Fabric malha.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277623"
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
