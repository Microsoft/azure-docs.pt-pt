---
title: Parametrizar ficheiros de configuração no Tecido de Serviço Azure
description: Aprenda a parametrizar ficheiros de configuração em Service Fabric, uma técnica útil para gerir vários ambientes.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75644635"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar ficheiros de configuração em Tecido de Serviço

Este artigo mostra-lhe como parametrizar um ficheiro de configuração no Tecido de Serviço.  Se ainda não está familiarizado com os conceitos fundamentais de gestão de aplicações para vários ambientes, leia [Gerir aplicações para vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar ficheiros de configuração

Neste exemplo, substitui-se um valor de configuração utilizando parâmetros na implementação da sua aplicação.

1. Abra o ficheiro * \<MyService>\PackageRoot\Config\Settings.xml* no seu projeto de serviço.
1. Desa ajuste o nome e o valor do parâmetro de configuração, por exemplo, tamanho da cache igual a 25, adicionando o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Guarde e feche o ficheiro.
1. Abra o ficheiro * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. No ficheiro ApplicationManifest.xml, declare um parâmetro e valor predefinido no `Parameters` elemento.  Recomenda-se que o nome do parâmetro contenha o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na `ServiceManifestImport` secção do ficheiro ApplicationManifest.xml, adicione um `ConfigOverrides` e `ConfigOverride` elemento, referindo o pacote de configuração, a secção e o parâmetro.

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
> No caso de adicionar um ConfigOverride, o Service Fabric escolhe sempre os parâmetros de aplicação ou o valor predefinido especificado no manifesto de aplicação.
>
>

## <a name="next-steps"></a>Próximos passos
Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
