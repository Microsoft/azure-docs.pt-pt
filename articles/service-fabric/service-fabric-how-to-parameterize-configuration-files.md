---
title: Parametrize ficheiros de config em Tecido de Serviço Azure
description: Aprenda a parametrizar ficheiros de configuração no Tecido de Serviço, uma técnica útil na gestão de múltiplos ambientes.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644635"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar ficheiros de configuração no Tecido de Serviço

Este artigo mostra-lhe como parametrizar um ficheiro de configuração no Tecido de Serviço.  Se ainda não está familiarizado com os conceitos fundamentais de gestão de aplicações para vários ambientes, leia [Gerir aplicações para vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar ficheiros de configuração

Neste exemplo, sobrepõe-se a um valor de configuração utilizando parâmetros na implementação da sua aplicação.

1. Abra o * \<ficheiro MyService>\PackageRoot\Config\Settings.xml* no seu projeto de serviço.
1. Defina um nome e valor de parâmetro de configuração, por exemplo, o tamanho da cache igual a 25, adicionando o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Guarde e feche o ficheiro.
1. Abra o * \<ficheiro MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. No ficheiro ApplicationManifest.xml, declare um parâmetro e `Parameters` valor predefinido no elemento.  Recomenda-se que o nome do parâmetro contenha o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na `ServiceManifestImport` secção do ficheiro ApplicationManifest.xml, `ConfigOverrides` `ConfigOverride` adicione um elemento e um elemento, fazendo referência ao pacote de configuração, à secção e ao parâmetro.

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
> No caso de adicionar um ConfigOverride, o Tecido de Serviço escolhe sempre os parâmetros da aplicação ou o valor predefinido especificado no manifesto de aplicação.
>
>

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Estúdio Visual, consulte [Gerir as suas aplicações de Tecido de Serviço no Estúdio Visual](service-fabric-manage-application-in-visual-studio.md).
