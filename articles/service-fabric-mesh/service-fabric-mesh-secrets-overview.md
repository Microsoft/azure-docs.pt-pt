---
title: Segredos da aplicação de malha de armazenar e utilizar o Azure Service Fabric | Documentos da Microsoft
description: Armazenar e utilizar segredos de malha do Service Fabric.
services: service-fabric-mesh
keywords: segredos
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: b6db9b6cb2ccbf1c8d29edb817d35677109ae755
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768626"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos da aplicação Mesh do Service Fabric
Malha do Service Fabric suporta segredos como recursos do Azure. Um segredo de malha do Service Fabric pode ser qualquer informação de texto confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Descrição geral de segredos de malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos de segredos de malha
Um aplicativo de malha segredo consiste em:
* R **segredos** recurso, o que é um contentor que armazena os segredos de texto. Segredos contidos a **segredos** recursos são armazenados e transmitidos de forma segura.
* Um ou mais **segredos/valores** recursos que estão armazenados no **segredos** contentor de recursos. Cada **segredos/valores** recursos distingue-se por um número de versão.

## <a name="inline-or-stored-in-azure-key-vault"></a>Inline ou armazenado no Azure Key Vault
- Malha de aplicativos e recursos de serviço incluem Managed Service Identity (MSI) com o Azure Active Directory (AAD) para poder aceder aos segredos no Azure Key Vault.
- Segredos e certificados podem ser automaticamente revertida a ativação pós-falha com as políticas.

## <a name="next-steps"></a>Passos Seguintes 
Para saber mais sobre os segredos de malha do Service Fabric, veja:
- [Gerir segredos da aplicação do Service Fabric malha](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao modelo de recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
