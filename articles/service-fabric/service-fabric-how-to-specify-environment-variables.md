---
title: Como especificar variáveis de ambiente para serviços no Azure Service Fabric | Microsoft Docs
description: Mostra como usar variáveis de ambiente para aplicativos no Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703531"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Como especificar variáveis de ambiente para serviços no Service Fabric

Este artigo mostra como especificar variáveis de ambiente para um serviço ou contêiner em Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimento para especificar variáveis de ambiente para serviços

Neste exemplo, você define uma variável de ambiente para um contêiner. O artigo pressupõe que você já tem um manifesto de aplicativo e serviço.

1. Abra o arquivo manifest. xml.
2. No elemento `CodePackage`, adicione um novo elemento `EnvironmentVariables` e um elemento `EnvironmentVariable` para cada variável de ambiente.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   As variáveis de ambiente podem ser substituídas no manifesto do aplicativo.

3. Para substituir as variáveis de ambiente no manifesto do aplicativo, use o elemento `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Especificando variáveis de ambiente dinamicamente usando Docker Compose

Service Fabric dá suporte à capacidade de [usar Docker Compose para implantação](service-fabric-docker-compose.md#supported-compose-directives). Os arquivos de composição podem ser variáveis de ambiente de origem do Shell. Esse comportamento pode ser usado para substituir dinamicamente os valores de ambiente desejados:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos principais conceitos discutidos neste artigo, consulte os [artigos gerenciar aplicativos para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outros recursos de gerenciamento de aplicativo que estão disponíveis no Visual Studio, consulte [gerenciar seus aplicativos de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
