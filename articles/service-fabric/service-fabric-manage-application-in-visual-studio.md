---
title: Gerir aplicações no Visual Studio
description: Utilize o Estúdio Visual para criar, desenvolver, embalar, implementar e depurar as suas aplicações e serviços azure Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614337"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Use o Estúdio Visual para simplificar a escrita e gerir as suas aplicações de Tecido de Serviço
Você pode gerir as suas aplicações e serviços Azure Service Fabric através do Visual Studio. Depois de [configurar o seu ambiente](service-fabric-get-started.md)de desenvolvimento, pode utilizar o Estúdio Visual para criar aplicações de Tecido de Serviço, adicionar serviços ou pacote, registar e implementar aplicações no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implementar a sua aplicação De Serviço Tecido
Por padrão, a implementação de uma aplicação combina os seguintes passos numa simples operação:

1. Criação do pacote de candidaturas
2. Envio do pacote de aplicação para a loja de imagens
3. Registar o tipo de candidatura
4. Eliminação de quaisquer instâncias de candidatura em execução
5. Criação de uma instância de candidatura

No Estúdio Visual, premir **f5** implementa a sua aplicação e anexa o debugger a todas as instâncias de aplicação. Pode utilizar **o Ctrl+F5** para implementar uma aplicação sem depurar, ou pode publicar num cluster local ou remoto utilizando o perfil de publicação.

### <a name="application-debug-mode"></a>Modo de depuração de aplicações
O Visual Studio fornece uma propriedade chamada **Application Debug Mode,** que controla a forma como pretende que os Estúdios Visuais lidem com a implementação da Aplicação como parte da depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade do Modo Debug da Aplicação
1. No menu de atalho de atalho do projeto de aplicação Service Fabric (*.sfproj), escolha **Propriedades** (ou prima a tecla **F4).**
2. Na janela **Propriedades,** detete a propriedade do **Modo Debug** application.

![Definir propriedade do modo de depuração de aplicação][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuração de aplicações

1. **Aplicação de atualização** Este modo permite-lhe alterar e depurar rapidamente o seu código e suporta a edição de ficheiros web estáticos enquanto depura. Este modo só funciona se o seu cluster de desenvolvimento local estiver no modo 1-Nó. Este é o modo de depuração de aplicação predefinido.
2. **Remover a aplicação** faz com que a aplicação seja removida quando a sessão de depuração terminar.
3. **Atualização automática** A aplicação continua a funcionar quando a sessão de depuração termina. A próxima sessão de depuração tratará a implementação como uma atualização. O processo de atualização preserva todos os dados que tenha introduzido numa sessão anterior de depuração.
4. **Manter aplicação** A aplicação continua a funcionar no cluster quando a sessão de depuração termina. No início da próxima sessão de depuração, a aplicação será removida.

Para os dados de **atualização automática** é preservado aplicando as capacidades de atualização de aplicações do Tecido de Serviço. Para obter mais informações sobre a atualização de aplicações e como poderá realizar uma atualização num ambiente real, consulte a atualização da [aplicação Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicione um serviço à sua aplicação Service Fabric
Pode adicionar novos serviços à sua aplicação para alargar a sua funcionalidade. Para garantir que o serviço está incluído no seu pacote de aplicações, adicione o serviço através do novo artigo do **Serviço de Tecidos...** menu.

![Adicione um novo serviço de Tecido de Serviço][newservice]

Selecione um tipo de projeto Service Fabric para adicionar à sua aplicação e especifique um nome para o serviço.  Consulte [A Escolha de uma estrutura para o seu serviço para](service-fabric-choose-framework.md) o ajudar a decidir que tipo de serviço utilizar.

![Selecione um tipo de projeto de serviço Service Fabric para adicionar à sua aplicação][addserviceproject]

O novo serviço é adicionado à sua solução e pacote de aplicações existente. As referências de serviço e uma instância de serviço padrão serão adicionadas ao manifesto de aplicação, fazendo com que o serviço seja criado e iniciado da próxima vez que implementar a aplicação.

![O novo serviço é adicionado ao seu manifesto de candidatura][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Embalem a sua aplicação De Tecido de Serviço
Para implementar a aplicação e os seus serviços para um cluster, é necessário criar um pacote de aplicações.  O pacote organiza o manifesto de aplicação, manifestos de serviço e outros ficheiros necessários num layout específico.  O Visual Studio configura e gere o pacote na pasta do projeto de aplicação, no diretório 'pkg'.  Clicar **em pacote** a partir do menu de contexto **da Aplicação** cria ou atualiza o pacote de aplicações.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicações e tipos de aplicações usando o Cloud Explorer
Pode realizar operações básicas de gestão de clusters a partir do Visual Studio utilizando o Cloud Explorer, que pode lançar a partir do menu **'Ver'.** Por exemplo, pode eliminar aplicações e tipos de aplicações não provisionando em clusters locais ou remotos.

![Remover uma aplicação][removeapplication]

> [!TIP]
> Para obter uma funcionalidade de gestão de cluster mais rica, consulte [Visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Modelo de aplicação de tecido de serviço](service-fabric-application-model.md)
* [Implantação de aplicações de tecido de serviço](service-fabric-deploy-remove-applications.md)
* [Gestão de parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurando a sua aplicação de Tecido de Serviço](service-fabric-debugging-your-application.md)
* [Visualizar o seu cluster utilizando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
