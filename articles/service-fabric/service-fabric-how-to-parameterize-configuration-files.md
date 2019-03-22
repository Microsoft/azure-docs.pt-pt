---
title: Parametrizar os ficheiros de configuração no Azure Service Fabric | Documentos da Microsoft
description: Saiba como parametrizar os ficheiros de configuração no Service Fabric.
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
ms.openlocfilehash: 0ab6e3f189d4a2e7e8f3bc96108d7979c99fffa8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102674"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar os ficheiros de configuração no Service Fabric

Este artigo mostra-lhe como parametrizar um ficheiro de configuração no Service Fabric.  Se não já estiver familiarizado com conceitos básicos de gestão de aplicações para vários ambientes, leia [gerir aplicações para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrização de ficheiros de configuração

Neste exemplo, substituir um valor de configuração usando parâmetros na implantação do seu aplicativo.

1. Abra o  *<MyService>\PackageRoot\Config\Settings.xml* ficheiro no seu projeto de serviço.
1. Defina um nome de parâmetro de configuração e um valor, por exemplo o tamanho da cache igual a 25, adicionando o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Guarde e feche o ficheiro.
1. Abra o  *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* ficheiro.
1. No ficheiro applicationmanifest. XML, declare um valor de parâmetro e o padrão no `Parameters` elemento.  Recomenda-se que o nome do parâmetro contém o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na `ServiceManifestImport` secção do ficheiro applicationmanifest. XML, adicione um `ConfigOverride` elemento, que referencia o pacote de configuração, a seção e o parâmetro.

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
> No caso em que adicionar um ConfigOverride, o Service Fabric escolhe sempre os parâmetros da aplicação ou o valor predefinido especificado no manifesto do aplicativo.
>
>

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [gerir as suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).