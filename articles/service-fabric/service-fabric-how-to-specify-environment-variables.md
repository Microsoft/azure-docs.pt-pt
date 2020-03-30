---
title: Especificar variáveis ambientais para serviços
description: Mostra-lhe como usar variáveis ambientais para aplicações em Tecido de Serviço
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614320"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Como especificar variáveis ambientais para serviços em Tecido de Serviço

Este artigo mostra-lhe como especificar variáveis ambientais para um serviço ou contentor em Tecido de Serviço.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimento para especificar variáveis ambientais para serviços

Neste exemplo, você estabelece uma variável ambiental para um recipiente. O artigo assume que já tem um manifesto de inscrição e serviço.

1. Abra o ficheiro ServiceManifest.xml.
2. No `CodePackage` elemento, adicione `EnvironmentVariables` um novo `EnvironmentVariable` elemento e um elemento para cada variável ambiental.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   As variáveis ambientais podem ser ultrapassadas no manifesto de aplicação.

3. Para anular as variáveis ambientais no `EnvironmentOverrides` manifesto de aplicação, utilize o elemento.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Especificando variáveis ambientais dinamicamente usando Docker Compose

O Tecido de Serviço suporta a capacidade de utilizar o [Docker Compose para a implantação](service-fabric-docker-compose.md#supported-compose-directives). Os ficheiros de composição podem obter variáveis ambientais a partir da concha. Este comportamento pode ser usado para substituir os valores ambientais desejados de forma dinâmica:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos conceitos fundamentais que são discutidos neste artigo, consulte as [aplicações de Gestão para artigos de vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Estúdio Visual, consulte [Gerir as suas aplicações de Tecido de Serviço no Estúdio Visual](service-fabric-manage-application-in-visual-studio.md).
