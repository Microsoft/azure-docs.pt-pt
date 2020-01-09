---
title: Gerir aplicações no Visual Studio
description: Use o Visual Studio para criar, desenvolver, empacotar, implantar e depurar seus aplicativos e serviços do Azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614337"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Use o Visual Studio para simplificar a gravação e o gerenciamento de seus aplicativos Service Fabric
Você pode gerenciar seus aplicativos e serviços do Azure Service Fabric por meio do Visual Studio. Depois de [configurar seu ambiente de desenvolvimento](service-fabric-get-started.md), você pode usar o Visual Studio para criar Service Fabric aplicativos, adicionar serviços ou empacotar, registrar e implantar aplicativos em seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implantar seu aplicativo Service Fabric
Por padrão, a implantação de um aplicativo combina as seguintes etapas em uma operação simples:

1. Criando o pacote de aplicativos
2. Carregando o pacote de aplicativos no repositório de imagens
3. Registrando o tipo de aplicativo
4. Removendo todas as instâncias do aplicativo em execução
5. Criando uma instância do aplicativo

No Visual Studio, pressionar **F5** implanta seu aplicativo e anexa o depurador a todas as instâncias de aplicativo. Você pode usar **Ctrl + F5** para implantar um aplicativo sem depuração ou pode publicar em um cluster local ou remoto usando o perfil de publicação.

### <a name="application-debug-mode"></a>Modo de depuração do aplicativo
O Visual Studio fornece uma propriedade chamada **modo de depuração do aplicativo**, que controla como você deseja que o Visual estúdios manipule a implantação do aplicativo como parte da depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade do modo de depuração do aplicativo
1. No menu de atalho do projeto de aplicativo Service Fabric (*. sfproj), escolha **Propriedades** (ou pressione a tecla **F4** ).
2. Na janela **Propriedades** , defina a propriedade **modo de depuração do aplicativo** .

![Definir a propriedade do modo de depuração do aplicativo][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuração de aplicativo

1. **Atualizar aplicativo** Esse modo permite que você altere e depure rapidamente seu código e dê suporte à edição de arquivos estáticos da Web durante a depuração. Esse modo só funcionará se o cluster de desenvolvimento local estiver no modo de 1 nó. Esse é o modo de depuração de aplicativo padrão.
2. **Remover aplicativo** faz com que o aplicativo seja removido quando a sessão de depuração termina.
3. **Atualização automática** O aplicativo continua a ser executado quando a sessão de depuração termina. A próxima sessão de depuração tratará a implantação como uma atualização. O processo de atualização preserva todos os dados que você inseriu em uma sessão de depuração anterior.
4. **Manter aplicativo** O aplicativo continua em execução no cluster quando a sessão de depuração termina. No início da próxima sessão de depuração, o aplicativo será removido.

Para os dados de **atualização automática** são preservados com a aplicação dos recursos de atualização de aplicativo do Service Fabric. Para obter mais informações sobre como atualizar aplicativos e como você pode executar uma atualização em um ambiente real, consulte [Service Fabric atualização do aplicativo](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicionar um serviço ao seu aplicativo Service Fabric
Você pode adicionar novos serviços ao seu aplicativo para estender sua funcionalidade. Para garantir que o serviço seja incluído no pacote de aplicativos, adicione o serviço por meio do novo item de menu **serviço de malha...** .

![Adicionar um novo serviço de Service Fabric][newservice]

Selecione um tipo de projeto Service Fabric para adicionar ao seu aplicativo e especifique um nome para o serviço.  Consulte [escolhendo uma estrutura para seu serviço](service-fabric-choose-framework.md) para ajudá-lo a decidir qual tipo de serviço usar.

![Selecione um tipo de projeto de serviço Service Fabric para adicionar ao seu aplicativo][addserviceproject]

O novo serviço é adicionado à sua solução e ao pacote de aplicativos existente. As referências de serviço e uma instância de serviço padrão serão adicionadas ao manifesto do aplicativo, fazendo com que o serviço seja criado e iniciado na próxima vez que você implantar o aplicativo.

![O novo serviço é adicionado ao manifesto do aplicativo][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empacotar seu aplicativo Service Fabric
Para implantar o aplicativo e seus serviços em um cluster, você precisa criar um pacote de aplicativos.  O pacote organiza o manifesto do aplicativo, os manifestos do serviço e outros arquivos necessários em um layout específico.  O Visual Studio configura e gerencia o pacote na pasta do projeto de aplicativo, no diretório ' pkg '.  Clicar em **pacote** no menu de contexto do **aplicativo** cria ou atualiza o pacote de aplicativos.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicativos e tipos de aplicativos usando o Cloud Explorer
Você pode executar operações básicas de gerenciamento de cluster no Visual Studio usando o Cloud Explorer, que pode ser iniciado no menu **Exibir** . Por exemplo, você pode excluir aplicativos e desprovisionar tipos de aplicativos em clusters locais ou remotos.

![Remover uma aplicação][removeapplication]

> [!TIP]
> Para obter uma funcionalidade de gerenciamento de cluster mais rica, consulte [visualizando seu cluster com Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Modelo de aplicativo Service Fabric](service-fabric-application-model.md)
* [Implantação de aplicativo Service Fabric](service-fabric-deploy-remove-applications.md)
* [Gerenciando parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurando seu aplicativo Service Fabric](service-fabric-debugging-your-application.md)
* [Visualizando seu cluster usando Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
