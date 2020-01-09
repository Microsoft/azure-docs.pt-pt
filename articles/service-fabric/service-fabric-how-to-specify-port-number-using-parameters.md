---
title: Especificar o número da porta de um serviço usando parâmetros
description: Mostra como usar parâmetros para especificar a porta para um aplicativo no Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609864"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Como especificar o número da porta de um serviço usando parâmetros em Service Fabric

Este artigo mostra como especificar o número da porta de um serviço usando parâmetros em Service Fabric usando o Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedimento para especificar o número da porta de um serviço usando parâmetros

Neste exemplo, você define o número da porta para sua API Web do asp.net Core usando um parâmetro.

1. Abra o Visual Studio e crie um novo aplicativo Service Fabric.
1. Escolha o modelo de ASP.NET Core sem estado.
1. Escolha API Web.
1. Abra o arquivo manifest. xml.
1. Observe o nome do ponto de extremidade especificado para o serviço. A predefinição é `ServiceEndpoint`.
1. Abra o arquivo ApplicationManifest. xml
1. No elemento `ServiceManifestImport`, adicione um novo elemento `RessourceOverrides` com uma referência ao ponto de extremidade em seu arquivo do seu manifesto. xml.

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

1. No elemento `Endpoint`, agora você pode substituir qualquer atributo usando um parâmetro. Neste exemplo, você especifica `Port` e o define como um nome de parâmetro usando colchetes-por exemplo, `[MyWebAPI_PortNumber]`

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

1. Ainda no arquivo ApplicationManifest. xml, você especifica o parâmetro no elemento `Parameters`

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

1. Abra a pasta Applicationparameters e o arquivo `Cloud.xml`
1. Para especificar uma porta diferente a ser usada ao publicar em um cluster remoto, adicione o parâmetro com o número da porta a esse arquivo.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ao publicar seu aplicativo do Visual Studio usando o perfil de publicação Cloud. xml, o serviço é configurado para usar a porta 80. Se você implantar o aplicativo sem especificar o parâmetro MyWebAPI_PortNumber, o serviço usará a porta 8080.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos principais conceitos discutidos neste artigo, consulte os [artigos gerenciar aplicativos para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outros recursos de gerenciamento de aplicativo que estão disponíveis no Visual Studio, consulte [gerenciar seus aplicativos de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
