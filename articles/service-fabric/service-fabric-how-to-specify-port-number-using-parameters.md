---
title: Como especificar o número de porta de um serviço usando parâmetros no Azure Service Fabric | Documentos da Microsoft
description: Mostra-lhe como utilizar os parâmetros para especificar a porta para uma aplicação no Service Fabric
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
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720251"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Como especificar o número de porta de um serviço usando parâmetros no Service Fabric

Este artigo mostra como especificar o número de porta de um serviço usando parâmetros no Service Fabric com o Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedimento para especificar o número de porta de um serviço com parâmetros

Neste exemplo, define o número de porta de Web do asp.net core web API com um parâmetro.

1. Abra o Visual Studio e crie uma nova aplicação de Service Fabric.
1. Escolha o modelo sem estado ASP.NET Core.
1. Escolha a Web API.
1. Abra o ficheiro servicemanifest. XML.
1. Tenha em atenção o nome do ponto final especificado para o seu serviço. A predefinição é `ServiceEndpoint`.
1. Abra o ficheiro Applicationmanifest.
1. Na `ServiceManifestImport` elemento, adicionar um novo `RessourceOverrides` elemento com uma referência para o ponto final no ficheiro servicemanifest. XML.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Na `Endpoint` elemento, agora pode substituir qualquer atributo com um parâmetro. Neste exemplo, especificar `Port` e defini-lo como um nome de parâmetro utilizando parênteses Retos - por exemplo, `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Ainda no ficheiro applicationmanifest. XML, em seguida, especifique o parâmetro no `Parameters` elemento

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. E definir um `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Abra a pasta de ApplicationParameters e o `Cloud.xml` ficheiro
1. Para especificar uma porta diferente para ser utilizado quando publicar num cluster remoto, adicione o parâmetro com o número de porta para este ficheiro.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Quando a publicação da sua aplicação a partir do Visual Studio, o ficheiro a utilizar um perfil de publicação, o seu serviço está configurado para utilizar a porta 80. Se implementar a aplicação sem especificar o parâmetro MyWebAPI_PortNumber, o serviço utiliza a porta 8080.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre alguns dos conceitos básicos discutidos neste artigo, consulte a [gerir as aplicações para vários artigos de ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [gerir as suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
