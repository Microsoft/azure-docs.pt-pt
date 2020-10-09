---
title: Especifique o número da porta de um serviço usando parâmetros
description: Mostra-lhe como usar parâmetros para especificar a porta para uma aplicação em Tecido de Serviço
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75609864"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Como especificar o número de porta de um serviço usando parâmetros em Tecido de Serviço

Este artigo mostra-lhe como especificar o número de porta de um serviço usando parâmetros em Tecido de Serviço usando o Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedimento para especificar o número de porta de um serviço utilizando parâmetros

Neste exemplo, define o número de porta para o seu asp.net API web core utilizando um parâmetro.

1. Abra o Estúdio Visual e crie uma nova aplicação de Tecido de Serviço.
1. Escolha o modelo de núcleo ASP.NET apátrida.
1. Escolha web API.
1. Abra o ficheiro ServiceManifest.xml.
1. Note o nome do ponto final especificado para o seu serviço. A predefinição é `ServiceEndpoint`.
1. Abra o ficheiro ApplicationManifest.xml
1. No `ServiceManifestImport` elemento, adicione um novo `RessourceOverrides` elemento com uma referência ao ponto final no seu ficheiro ServiceManifest.xml.

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

1. No `Endpoint` elemento, pode agora substituir qualquer atributo utilizando um parâmetro. Neste exemplo, `Port` especifica-se e define-o para um nome de parâmetro utilizando suportes quadrados - por exemplo, `[MyWebAPI_PortNumber]`

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

1. Ainda no ficheiro ApplicationManifest.xml, então especifique o parâmetro no `Parameters` elemento

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

1. Abra a pasta ApplicationParameters e o `Cloud.xml` ficheiro
1. Para especificar uma porta diferente a ser utilizada ao publicar num cluster remoto, adicione o parâmetro com o número de porta a este ficheiro.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Ao publicar a sua aplicação no Visual Studio utilizando o perfil de publicação Cloud.xml, o seu serviço está configurado para utilizar a porta 80. Se implementar a aplicação sem especificar o parâmetro MyWebAPI_PortNumber, o serviço utiliza a porta 8080.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre alguns dos conceitos fundamentais que são discutidos neste artigo, consulte as [aplicações Manage para vários artigos de ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

Para obter informações sobre outras capacidades de gestão de aplicações que estão disponíveis no Visual Studio, consulte [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
