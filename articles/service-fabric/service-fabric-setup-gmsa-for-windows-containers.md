---
title: Configurar gMSA para os serviços de contêiner de Service Fabric do Azure
description: Aprenda agora a configurar contas de serviço gerenciado de grupo (gMSA) para um contêiner em execução no Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639212"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar gMSA para contêineres do Windows em execução no Service Fabric

Para configurar o gMSA (contas de serviço gerenciado de grupo), um arquivo de especificação de credencial (`credspec`) é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O arquivo de `credspec` deve conter as informações da conta do gMSA. Para obter mais informações sobre o arquivo de `credspec`, consulte [criar uma especificação de credencial](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação de credencial e a marca de `Hostname` são especificadas no manifesto do aplicativo. A marca de `Hostname` deve corresponder ao nome da conta gMSA em que o contêiner é executado.  A marca `Hostname` permite que o contêiner se autentique em outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no trecho a seguir:

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
