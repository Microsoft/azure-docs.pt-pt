---
title: Como especificar variáveis de ambiente para serviços no Azure Service Fabric | Documentos da Microsoft
description: Mostra-lhe como utilizar variáveis de ambiente para aplicações no Service Fabric
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
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720234"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Como especificar variáveis de ambiente para serviços no Service Fabric

Este artigo mostra como especificar variáveis de ambiente para um serviço ou o contentor no Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimento para especificar variáveis de ambiente para serviços

Neste exemplo, define uma variável de ambiente de um contentor. O artigo pressupõe que já tem um manifesto de aplicações e serviço.

1. Abra o ficheiro servicemanifest. XML.
1. Na `CodePackage` elemento, adicionar um novo `EnvironmentVariables` elemento e um `EnvironmentVariable` elemento para cada variável de ambiente.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Variáveis de ambiente podem ser substituídas no manifesto do aplicativo.

1. Para substituir as variáveis de ambiente no manifesto da aplicação, utilize o `EnvironmentOverrides` elemento.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre alguns dos conceitos básicos discutidos neste artigo, consulte a [gerir as aplicações para vários artigos de ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [gerir as suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).