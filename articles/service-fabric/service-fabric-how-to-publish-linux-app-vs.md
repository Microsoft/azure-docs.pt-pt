---
title: Criar e publicar o aplicativo a.Net Core em um cluster Linux remoto
description: Criar e publicar .Net Core aplicativos direcionados a um cluster Linux remoto do Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614354"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Usar o Visual Studio para criar e publicar .Net Core aplicativos direcionados a um cluster remoto de Service Fabric do Linux
Com as ferramentas do Visual Studio, você pode desenvolver e publicar Service Fabric .Net Core aplicativos direcionados a um cluster de Service Fabric do Linux. A versão do SDK deve ser 3,4 ou superior para implantar um aplicativo .Net Core destinado a clusters do Linux Service Fabric do Visual Studio.

> [!Note]
> O Visual Studio não dá suporte à depuração de Service Fabric aplicativos que se destinam ao Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Criar um aplicativo Service Fabric direcionamento .Net Core
1. Inicie o Visual Studio como **administrador**.
2. Crie um projeto com **arquivo-> projeto de novo >** .
3. Na caixa de diálogo **novo projeto** , escolha **nuvem-> aplicativo Service Fabric**.
![create-application]
4. Nomeie o aplicativo e clique em **OK**.
5. Na página **novo serviço Service Fabric** , selecione o tipo de serviço que você deseja criar na **seção .NET Core**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Implantar em um cluster Linux remoto
1. No Gerenciador de soluções, clique com o botão direito do mouse no aplicativo e selecione **Compilar**.
![Build-] de aplicativos
2. Depois que o processo de compilação para o aplicativo for concluído, clique com o botão direito do mouse no serviço e selecione Editar o **arquivo csproj**.
![edit-csproj]
3. Edite a propriedade UpdateServiceFabricManifestEnabled de true para **false** se o serviço for um **tipo de projeto de ator**. Se seu aplicativo não tiver um serviço de ator, pule para a etapa 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Definir UpdateServiceFabricManifestEnabled como false desativará as atualizações para o manifesto. xml durante uma compilação. Qualquer alteração como adicionar, remover ou renomear para o serviço não será refletida no Service manifest. xml. Se forem feitas alterações, você deverá atualizar o Service manifest manualmente ou temporariamente definir UpdateServiceFabricManifestEnabled como true e criar o serviço que atualizará o manifest. xml e, em seguida, revertê-lo novamente para false.
>

4. Atualize o RuntimeIndetifier do Win7-x64 para a plataforma de destino no projeto de serviço.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. No manifesto, atualize o programa EntryPoint para Remove. exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. Em Gerenciador de Soluções, clique com o botão direito do mouse no aplicativo e selecione **publicar**. É apresentada a caixa de diálogo **Publicar**.
7. Em **ponto de extremidade de conexão**, selecione o ponto de extremidade para o cluster remoto Service Fabric Linux que você deseja direcionar.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[Compilar aplicativo]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [começar a usar o Service Fabric com .NET Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
