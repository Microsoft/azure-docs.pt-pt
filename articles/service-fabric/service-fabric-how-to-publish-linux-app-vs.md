---
title: Crie e publique a.Net core para um cluster linux remoto
description: Crie e publique aplicações .Net Core direcionadas a um cluster linux remoto do Estúdio Visual
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614354"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Use o Visual Studio para criar e publicar aplicações .Net Core direcionadas para um cluster de tecido de serviço linux remoto
Com a ferramenta Visual Studio pode desenvolver e publicar aplicações service Fabric .Net Core direcionadas para um cluster de Tecido de Serviço Linux. A versão SDK deve ser 3.4 ou superior para implementar uma aplicação .Net Core direcionada a clusters de Tecido de Serviço Linux do Estúdio Visual.

> [!Note]
> O Visual Studio não suporta aplicações de depuração de tecido sinuoso que visam o Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Criar uma aplicação de tecido de serviço direcionada .Net Core
1. Lançar o Estúdio Visual como **administrador.**
2. Crie um projeto com **o Projeto New->>file.**
3. No diálogo **new project,** escolha **Cloud -> Aplicação**de Tecido de Serviço .
![criar aplicação]
4. Nomeie a aplicação e clique em **Ok**.
5. Na página **new service Fabric Service,** selecione o tipo de serviço que deseja criar sob a **secção .Net Core**.
![criar serviço]

## <a name="deploy-to-a-remote-linux-cluster"></a>Desloque-se para um aglomerado de Linux remoto
1. No explorador de solução, clique à direita na aplicação e selecione **Build**.
![construção-aplicação]
2. Uma vez concluído o processo de construção da aplicação, clique no serviço e selecione editar o **ficheiro csproj**.
![editar-csproj]
3. Editar a propriedade UpdateServiceFabricManifestEnabled de True to **False** se o serviço for do **tipo de projeto ator**. Se a sua aplicação não tiver um serviço de ator, salte para o passo 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Definição De ActualizaçãoServiceFabricManifestEnabled to false, irá desativar as atualizações para o ServiceManifest.xml durante uma construção. Qualquer alteração como adicionar, remover ou mudar o nome para o serviço não se refletirá no ServiceManifest.xml. Se forem feitas alterações, deve atualizar manualmente ou temporariamente o UpdateServiceFabricManifestEnabled para o verdadeiro e construir o serviço que irá atualizar o ServiceManifest.xml e, em seguida, revertê-lo de volta para falso.
>

4. Atualize o RuntimeIndetifier do win7-x64 para a plataforma-alvo no projeto de serviço.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. No ServiceManifest, atualize o programa de ponto de entrada para remover .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. No Solution Explorer, clique à direita na aplicação e **selecione Publicar**. É apresentada a caixa de diálogo **Publicar**.
7. No Ponto final de **Ligação,** selecione o ponto final para o cluster de tecido de serviço remoto que gostaria de atingir.
![publicação-aplicação]

<!--Image references-->
[criar aplicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[criar serviço]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[construção-aplicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[editar-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publicação-aplicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre começar com tecido de [serviço com .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
