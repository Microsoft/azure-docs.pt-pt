---
title: Parametrizar arquivos de configuração no Azure Service Fabric | Microsoft Docs
description: Saiba como parametrizar os arquivos de configuração no Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464772"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar arquivos de configuração no Service Fabric

Este artigo mostra como Parametrizar um arquivo de configuração no Service Fabric.  Se você ainda não estiver familiarizado com os principais conceitos de gerenciamento de aplicativos para vários ambientes, leia [gerenciar aplicativos para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar arquivos de configuração

Neste exemplo, você substitui um valor de configuração usando parâmetros em sua implantação de aplicativo.

1. Abra o  *\<arquivo MyService > \PackageRoot\Config\Settings.xml* em seu projeto de serviço.
1. Defina um nome de parâmetro de configuração e um valor, por exemplo, o tamanho do cache igual a 25, adicionando o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Guarde e feche o ficheiro.
1. Abra o  *\<arquivo MyApplication > \ApplicationPackageRoot\ApplicationManifest.xml*
1. No arquivo ApplicationManifest. xml, declare um parâmetro e um valor padrão no `Parameters` elemento.  É recomendável que o nome do parâmetro contenha o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na seção do arquivo ApplicationManifest. xml, adicione um `ConfigOverrides` elemento e `ConfigOverride` , referenciando o pacote de configuração, a seção e o parâmetro. `ServiceManifestImport`

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> No caso em que você adiciona um ConfigOverride, Service Fabric sempre escolhe os parâmetros do aplicativo ou o valor padrão especificado no manifesto do aplicativo.
>
>

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outros recursos de gerenciamento de aplicativo que estão disponíveis no Visual Studio, consulte [gerenciar seus aplicativos de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
