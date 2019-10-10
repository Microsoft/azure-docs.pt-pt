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
ms.author: atsenthi
ms.openlocfilehash: 45fc4c924a8fb794ad81529de74b98ee812f46c5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170431"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar gMSA para contêineres do Windows em execução no Service Fabric

Para configurar o gMSA (contas de serviço gerenciado de grupo), um arquivo de especificação de credencial (`credspec`) é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O arquivo `credspec` deve conter as informações da conta gMSA. Para obter mais informações sobre o arquivo `credspec`, consulte [criar uma especificação de credencial](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação de credencial e a marca `Hostname` são especificadas no manifesto do aplicativo. A marca `Hostname` deve corresponder ao nome da conta gMSA em que o contêiner é executado.  A marca `Hostname` permite que o contêiner se autentique a outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no trecho a seguir:

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
