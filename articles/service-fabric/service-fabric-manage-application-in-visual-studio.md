---
title: Gerir as suas aplicações do Azure Service Fabric no Visual Studio | Documentos da Microsoft
description: Utilize o Visual Studio para criar, desenvolver, empacotar, implementar e depurar as suas aplicações do Azure Service Fabric e serviços.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 4744858869e10094389be58ddd3960cb8cc2773a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720100"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilizar o Visual Studio para simplificar a criação e gestão das suas aplicações do Service Fabric
Pode gerenciar seus aplicativos de Azure Service Fabric e serviços através do Visual Studio. Assim que tiver [configurar o ambiente de desenvolvimento](service-fabric-get-started.md), pode utilizar o Visual Studio para criar aplicações do Service Fabric, serviços, ou pacote, registre-se de adicionar e implementar aplicações no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implementar a sua aplicação do Service Fabric
Por predefinição, a implantação de um aplicativo combina as etapas a seguir numa operação simple:

1. Criar o pacote de aplicação
2. Carregar o pacote de aplicação para o armazenamento de imagens
3. Registar o tipo de aplicação
4. Remoção de nenhum aplicativo instâncias em execução
5. Criar uma instância de aplicação

No Visual Studio, premindo **F5** implanta seu aplicativo e anexar o depurador para todas as instâncias da aplicação. Pode usar **Ctrl + F5** para implementar uma aplicação sem depuração ou pode publicar num cluster local ou remoto com o perfil de publicação.

### <a name="application-debug-mode"></a>Modo de depuração da aplicação
Visual Studio fornecem uma propriedade chamada **modo de depuração do aplicativo**, que controla a forma como pretende que o Visual Studio para lidar com a implementação de aplicações como parte de depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade do modo de depuração da aplicação
1. No Service Fabric application do projeto (*. sfproj) menu de atalho, escolha **propriedades** (ou prima o **F4** chave).
2. Na **propriedades** janela, definida a **modo de depuração do aplicativo** propriedade.

![Definir a propriedade de modo de depuração do aplicativo][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuração da aplicação

1. **Atualizar aplicação** este modo permite-lhe rapidamente alterar e depurar seu código e suporta edição de arquivos de web estática durante a depuração. Este modo só funciona se o cluster de desenvolvimento local estiver no modo de 1 nó. Esta é a predefinição do modo de depuração do aplicativo.
2. **Remover aplicação** faz com que o aplicativo a serem removidos quando termina a sessão de depuração.
3. **Atualizar de automático** a aplicação continua a ser executada quando termina a sessão de depuração. A próxima sessão de depuração tratará da implementação como uma atualização. O processo de atualização preserva todos os dados que introduziu numa sessão de depuração anterior.
4. **Manter o aplicativo** o aplicativo permanece em execução no cluster quando termina a sessão de depuração. No início da próxima sessão de depuração, o aplicativo será removido.

Para **atualizar de automático** os dados são preservados aplicando os recursos de atualização de aplicação do Service Fabric. Para obter mais informações sobre a atualização de aplicativos e como pode efetuar uma atualização num ambiente real, consulte [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicionar um serviço a sua aplicação do Service Fabric
Pode adicionar novos serviços à sua aplicação para expandir a sua funcionalidade. Para garantir que o serviço está incluído no seu pacote de aplicação, adicione o serviço por meio do **novo serviço de recursos de infraestrutura...**  item de menu.

![Adicionar um novo serviço do Service Fabric][newservice]

Selecione um tipo de projeto do Service Fabric para adicionar à sua aplicação e especifique um nome para o serviço.  Ver [escolher uma estrutura para o seu serviço](service-fabric-choose-framework.md) para ajudar a decidir o tipo de serviço a utilizar.

![Selecione um tipo de projeto de serviço do Service Fabric para adicionar à sua aplicação][addserviceproject]

O novo serviço é adicionado à sua solução e o pacote de aplicação existente. As referências de serviço e uma instância de serviço predefinida serão adicionados ao manifesto do aplicativo, fazendo com que o serviço ser criada e iniciada na próxima vez que a aplicação é implementada.

![O novo serviço é adicionado para o manifesto da aplicação][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empacotar a sua aplicação do Service Fabric
Para implementar a aplicação e os respetivos serviços num cluster, terá de criar um pacote de aplicação.  O pacote organiza o manifesto do aplicativo, manifestos de serviço e outros arquivos necessários num layout específico.  Visual Studio configura e gere o pacote na pasta do projeto de aplicativo, no diretório 'pkg'.  Clicar **pacote** partir a **aplicação** menu de contexto cria ou atualiza o pacote de aplicação.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicações e tipos de aplicação com o Cloud Explorer
Pode efetuar operações de gestão de cluster básico no Visual Studio com o Cloud Explorer, o que pode iniciar a partir da **vista** menu. Por exemplo, pode eliminar aplicações e anular o aprovisionamento de tipos de aplicações em clusters locais ou remotos.

![Remover uma aplicação][removeapplication]

> [!TIP]
> Para uma funcionalidade de gestão de cluster mais avançada, consulte [visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes
* [Modelo de aplicação do Service Fabric](service-fabric-application-model.md)
* [Implementação de aplicação do Service Fabric](service-fabric-deploy-remove-applications.md)
* [Gerir parâmetros da aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurar a aplicação do Service Fabric](service-fabric-debugging-your-application.md)
* [Visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
