---
title: Configuração gMSA para serviços de contentores azure service Fabric
description: Aprenda agora a configurar o grupo De contas de serviço geridas (gMSA) para um contentor em funcionamento em Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639212"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar gMSA para recipientes Windows em execução em Tecido de Serviço

Para configurar o gMSA (contas de serviço geridas`credspec`pelo grupo), é colocado um ficheiro de especificação credencial () em todos os nós do cluster. O ficheiro pode ser copiado em todos os nós utilizando uma extensão VM.  O `credspec` ficheiro deve conter as informações da conta gMSA. Para obter mais `credspec` informações sobre o ficheiro, consulte [Criar uma Especificação Credencial](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação credencial `Hostname` e a etiqueta estão especificadas no manifesto de aplicação. A `Hostname` etiqueta deve coincidir com o nome da conta gMSA a que o recipiente passa.  A `Hostname` etiqueta permite que o recipiente se autenticaa a outros serviços do domínio utilizando a autenticação Kerberos.  Uma amostra para `Hostname` especificar `credspec` o e o manifesto de aplicação é mostrada no seguinte corte:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Como próximo passo, leia os seguintes artigos:

* [Implemente um recipiente Windows para o Serviço de Tecido no Windows Server 2016](service-fabric-get-started-containers.md)
* [Desloque um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
