---
title: Configurar gMSA para os serviços de contêiner do Azure Service Fabric | Microsoft Docs
description: Aprenda agora a configurar o gMSA para um contêiner em execução no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: 09994c7676de8470efff1707598ddf32a48e41a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599183"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar gMSA para contêineres do Windows em execução no Service Fabric

Para configurar o gMSA (contas de serviço gerenciado de grupo), um arquivo de`credspec`especificação de credencial () é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O `credspec` arquivo deve conter as informações da conta do gMSA. Para obter mais informações sobre `credspec` o arquivo, consulte [criar uma especificação](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)de credencial. A especificação de credencial `Hostname` e a marca são especificadas no manifesto do aplicativo. A `Hostname` marca deve corresponder ao nome da conta do gMSA em que o contêiner é executado.  A `Hostname` marca permite que o contêiner se autentique a outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no trecho a seguir:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Como uma próxima etapa, leia os seguintes artigos:

* [Implantar um contêiner do Windows para Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
