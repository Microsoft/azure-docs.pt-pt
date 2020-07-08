---
title: Configuração gMSA para serviços de contentores de tecido de serviço Azure
description: Aprenda agora a configurar o grupo Contas de Serviço Gerido (gMSA) para um contentor em funcionamento em Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75639212"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurar gMSA para contentores Windows em execução em Tecido de Serviço

Para configurar o gMSA (contas de serviço geridos em grupo), é colocado um ficheiro de especificações credenciais `credspec` em todos os nós do cluster. O ficheiro pode ser copiado em todos os nós utilizando uma extensão VM.  O `credspec` ficheiro deve conter as informações da conta gMSA. Para obter mais informações sobre o `credspec` ficheiro, consulte [Criar uma Especificação credencial](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação de credencial e a `Hostname` etiqueta estão especificadas no manifesto de aplicação. A `Hostname` etiqueta deve corresponder ao nome da conta gMSA que o recipiente passa por baixo.  A `Hostname` etiqueta permite que o recipiente se autentica a outros serviços no domínio utilizando a autenticação Kerberos.  No seguinte corte é apresentada uma amostra para especificar `Hostname` o e o manifesto de `credspec` aplicação:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Como passo seguinte, leia os seguintes artigos:

* [Implementar um contentor Windows para o tecido de serviço no Windows Server 2016](service-fabric-get-started-containers.md)
* [Colocar um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
