---
title: Configurar o gMSA para serviços de contentor do Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar o gMSA para um contentor em execução no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: 8f0c569495775dcda6dec189d223067435368249
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370215"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar o gMSA para contentores do Windows em execução no Service Fabric

Para configurar a gMSA (grupo de contas de serviço gerida), um ficheiro de especificação de credenciais (`credspec`) é colocada em todos os nós do cluster. O ficheiro pode ser copiado em todos os nós com uma extensão da VM.  O `credspec` ficheiro tem de conter as informações de conta gMSA. Para obter mais informações sobre o `credspec` de ficheiros, consulte [criar uma especificação de credenciais](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação de credenciais e o `Hostname` marca são especificados no manifesto do aplicativo. O `Hostname` etiqueta tem de corresponder o nome da conta gMSA que o contentor é executado.  O `Hostname` etiqueta permite que o contêiner autenticar-se a outros serviços no domínio utilizando a autenticação Kerberos.  Um exemplo para especificar a `Hostname` e o `credspec` no aplicativo de manifesto é mostrado no seguinte fragmento:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Como passo seguinte, leia os artigos seguintes:

* [Implementar um contentor do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementar um contentor de Docker para o Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
