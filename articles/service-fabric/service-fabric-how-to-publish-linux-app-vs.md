---
title: Saiba como criar e publicar o .net Core aplicações num cluster remoto do Azure Service Fabric do Linux | Documentos da Microsoft
description: Criar e publicar o .net Core aplicações orientadas para um cluster do Linux remoto a partir do Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078667"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Utilizar o Visual Studio para criar e publicar o .net Core aplicativos destinados um cluster do Service Fabric do Linux remoto
Com o Visual Studio ferramentas podem desenvolver e publicar .net do Service Fabric principais aplicativos destinados um cluster do Service Fabric do Linux. A versão do SDK tem de ser 3.4 ou superior para implementar um .net clusters do aplicativo principal para o Service Fabric do Linux a partir do Visual Studio.

> [!Note]
> Visual Studio não suporta a depuração aplicações do Service Fabric que Linux de destino.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Criar uma aplicação do Service Fabric visando o .net Core
1. Inicie o Visual Studio como **administrador**.
2. Crie um projeto com **ficheiro -> New -> projeto**.
3. Na **novo projeto** caixa de diálogo, escolha **Cloud -> aplicação do Service Fabric**.
![create-application]
4. Nome da aplicação e clique em **Ok**.
5. No **novo serviço do Service Fabric** , selecione o tipo de serviço que pretende criar sob a **.Net Core secção**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Implementar um cluster do Linux remoto
1. No Explorador de soluções, clique com botão direito na aplicação e selecione **criar**.
![build-application]
2. Depois do processo de compilação para a aplicação concluída, clique com o botão direito do rato no serviço e selecione editar a **ficheiro csproj**.
![edit-csproj]
3. Editar a propriedade UpdateServiceFabricManifestEnabled verdadeiro a partir **False** se o serviço é um **tipo de projeto de ator**. Se seu aplicativo não tiver um serviço de ator, avance para o passo 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Definir UpdateServiceFabricManifestEnabled como false, irá desativar as atualizações para o servicemanifest. xml durante uma compilação. Qualquer alteração, tais como adicionar, remover ou mudar o nome para o serviço não será refletida no servicemanifest. XML. Se todas as alterações forem feitas, deve atualizar que o ServiceManifest manualmente ou temporariamente definir UpdateServiceFabricManifestEnabled como true e criar o serviço que irá atualizar o servicemanifest. XML e, em seguida, revertê-lo fazer uma cópia como false.
>

4. Atualize o RuntimeIndetifier do win7 x64 para a plataforma de destino do projeto de serviço.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. O servicemanifest, atualize o programa de ponto de entrada para remover .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. No Solution Explorer, clique com botão direito na aplicação e selecione **publicar**. É apresentada a caixa de diálogo **Publicar**.
7. Na **ponto final de ligação**, selecione o ponto final para o cluster do Service Fabric do Linux remoto que gostaria de destino.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [introdução ao Service Fabric com .net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
