---
title: Especificar variáveis ambientais para serviços
description: Mostra-lhe como usar variáveis ambientais para aplicações em Tecido de Serviço
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576745"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Como especificar variáveis ambientais para serviços em Tecido de Serviço

Este artigo mostra-lhe como especificar variáveis ambientais para um serviço ou recipiente em Tecido de Serviço.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimento de especificação de variáveis ambientais para serviços

Neste exemplo, você define uma variável ambiental para um recipiente. O artigo assume que já tem um manifesto de aplicação e serviço.

1. Abra o ficheiro ServiceManifest.xml.
2. No `CodePackage` elemento, adicione um novo `EnvironmentVariables` elemento e um elemento para cada `EnvironmentVariable` variável ambiente.

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

3. Para anular as variáveis ambientais no manifesto de aplicação, utilize o `EnvironmentOverrides` elemento.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Especificar variáveis ambientais dinamicamente usando Docker Compose

O Tecido de Serviço suporta a capacidade [de utilizar o Docker Compose para implantação.](service-fabric-docker-compose.md#supported-compose-directives) Os ficheiros de composição podem obter variáveis ambientais a partir da concha. Este comportamento pode ser usado para substituir os valores ambientais desejados dinamicamente:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos conceitos fundamentais que são discutidos neste artigo, consulte as [aplicações Manage para vários artigos de ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
