---
title: Criar e publicar a.Net aplicação Core para um cluster linux remoto
description: Criar e publicar aplicações .Net Core direcionadas para um cluster Linux remoto do Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614354"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Utilize o Visual Studio para criar e publicar aplicações .Net Core direcionadas para um cluster remoto de tecido de serviço Linux
Com a ferramenta Visual Studio, pode desenvolver e publicar aplicações Service Fabric .Net Core direcionadas para um cluster de tecido de serviço Linux. A versão SDK deve ser 3.4 ou superior para implementar uma aplicação .Net Core direcionada a clusters de Tecido de Serviço Linux do Visual Studio.

> [!Note]
> O Visual Studio não suporta aplicações de depuragem de tecido de serviço que visam o Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Criar uma aplicação de tecido de serviço direcionada a .Net Core
1. Lançar o Visual Studio como **administrador.**
2. Crie um projeto com **o Projeto De >De Arquivos de Nova >.**
3. No diálogo New **Project,** escolha **cloud-> Service Fabric Application**.
![criar aplicação]
4. Nomeie a aplicação e clique **em Ok**.
5. Na página **New Service Fabric Service,** selecione o tipo de serviço que pretende criar na **secção .Net Core**.
![criar serviço]

## <a name="deploy-to-a-remote-linux-cluster"></a>Desdobre-se para um cluster linux remoto
1. No explorador de solução, clique no botão direito na aplicação e selecione **Construir**.
![counstroem aplicação]
2. Uma vez concluído o processo de construção da aplicação, clique no serviço e selecione editar o **ficheiro csproj**.
![edit-csproj]
3. Editar a propriedade UpdateServiceFabricManifestEnabled de True a **False** se o serviço for um **tipo de projeto de ator**. Se a sua aplicação não tiver um serviço de ator, salte para o passo 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Configurar UpdateServiceFabricManifestEnabled para falso, irá desativar as atualizações para o ServiceManifest.xml durante uma construção. Qualquer alteração como adicionar, remover ou renomear o serviço não será refletida no ServiceManifest.xml. Se forem feitas alterações, deve atualizar o ServiceManifest manualmente ou definir temporariamente UpdateServiceFabricManifestEnabled para verdadeiro e construir o serviço que irá atualizar o ServiceManifest.xml e depois revertê-lo de volta para falso.
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
6. No Solution Explorer, clique com o botão direito na aplicação e **selecione Publicar**. É apresentada a caixa de diálogo **Publicar**.
7. Em **Connection Endpoint**, selecione o ponto final para o cluster linux de serviço remoto que pretende atingir.
![aplicação de publicação]

<!--Image references-->
[criar aplicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[criar serviço]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[counstroem aplicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[aplicação de publicação]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passos seguintes
* Saiba [como começar com o Tecido de Serviço com .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
