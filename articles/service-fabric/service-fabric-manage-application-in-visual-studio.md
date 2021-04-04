---
title: Gerir aplicações no Visual Studio
description: Utilize o Visual Studio para criar, desenvolver, embalar, implementar e depurar as suas aplicações e serviços de Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574552"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilize o Visual Studio para simplificar a escrita e gerir as suas aplicações de Tecido de Serviço
Pode gerir as suas aplicações e serviços Azure Service Fabric através do Visual Studio. Uma vez [configurado o seu ambiente de desenvolvimento,](service-fabric-get-started.md)pode utilizar o Visual Studio para criar aplicações de Tecido de Serviço, adicionar serviços ou pacotes, registar e implementar aplicações no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implemente a sua aplicação de Tecido de Serviço
Por predefinição, a implementação de uma aplicação combina os seguintes passos numa simples operação:

1. Criação do pacote de aplicações
2. O upload do pacote de aplicações para a loja de imagens
3. Registo do tipo de candidatura
4. Eliminação de quaisquer instâncias de aplicação em execução
5. Criar uma instância de aplicação

No Visual Studio, pressionar **o F5** implementa a sua aplicação e anexa o depurante a todas as instâncias de aplicação. Pode utilizar **o Ctrl+F5** para implementar uma aplicação sem depuração, ou pode publicar num cluster local ou remoto utilizando o perfil de publicação.

### <a name="application-debug-mode"></a>Modo de Depuramento de Aplicações
O Visual Studio fornece uma propriedade chamada **Application Debug Mode**, que controla a forma como quer que os Estúdios Visuais lidem com a implementação da aplicação como parte da depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade Application Debug Mode
1. No menu de atalho do projeto de aplicação Service Fabric (*.sfproj), escolha **Propriedades** (ou prima a tecla **F4).**
2. Na janela **Propriedades,** desapedace a propriedade **Application Debug Mode.**

![Definir Propriedade do Modo Debug de aplicação][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de Depuramento de Aplicações

1. **Aplicação de Atualização** Este modo permite-lhe alterar rapidamente e depurar o seu código e suporta a edição de ficheiros web estáticos enquanto desativa. Este modo só funciona se o seu cluster de desenvolvimento local estiver no modo 1-Nó. Este é o modo de depuração de aplicação predefinido.
2. **Remover Aplicação** faz com que a aplicação seja removida quando a sessão de depurar termina.
3. **Atualização automática** A aplicação continua a decorrer quando a sessão de depurar termina. A próxima sessão de depuração tratará a implementação como uma atualização. O processo de atualização preserva todos os dados que tenha introduzido numa sessão de depuração anterior.
4. **Manter a aplicação** A aplicação continua a funcionar no cluster quando a sessão de depuração termina. No início da próxima sessão de depurar, a aplicação será removida.

Para os dados **de upgrade automático** é preservado aplicando as capacidades de atualização de aplicação do Tecido de Serviço. Para obter mais informações sobre a atualização de aplicações e como pode realizar uma atualização num ambiente real, consulte o [upgrade da aplicação Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicione um serviço à sua aplicação Service Fabric
Pode adicionar novos serviços à sua aplicação para alargar a sua funcionalidade. Para garantir que o serviço está incluído no seu pacote de candidaturas, adicione o serviço através do novo serviço **de tecido...** item do menu.

![Adicione um novo serviço de Tecido de Serviço][newservice]

Selecione um tipo de projeto de Tecido de Serviço para adicionar à sua aplicação e especifique um nome para o serviço.  Consulte [escolher uma estrutura para o seu serviço](service-fabric-choose-framework.md) para ajudá-lo a decidir que tipo de serviço usar.

![Selecione um tipo de projeto de serviço de serviço de Tecido de Serviço para adicionar à sua aplicação][addserviceproject]

O novo serviço é adicionado à sua solução e pacote de aplicações existente. As referências de serviço e uma instância de serviço predefinido serão adicionadas ao manifesto de aplicação, fazendo com que o serviço seja criado e iniciado da próxima vez que implementar a aplicação.

![O novo serviço é adicionado ao manifesto de aplicação][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Embale a sua aplicação de Tecido de Serviço
Para implementar a aplicação e os seus serviços num cluster, é necessário criar um pacote de aplicações.  O pacote organiza o manifesto de aplicação, manifestos de serviço e outros ficheiros necessários num layout específico.  O Visual Studio configura e gere o pacote na pasta do projeto de aplicação, no diretório 'pkg'.  Clicar em **Pacote** a partir do menu de contexto **de aplicação** cria ou atualiza o pacote de aplicações.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicações e tipos de aplicações usando o Cloud Explorer
Pode executar operações básicas de gestão de clusters a partir do Estúdio Visual utilizando o Cloud Explorer, que pode lançar a partir do menu **Ver.** Por exemplo, pode eliminar aplicações e tipos de aplicações não-provisão em clusters locais ou remotos.

![Remover uma aplicação][removeapplication]

> [!TIP]
> Para obter uma funcionalidade de gestão de cluster mais rica, consulte [visualizar o seu cluster com o Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Modelo de aplicação de tecido de serviço](service-fabric-application-model.md)
* [Implementação de aplicações de tecido de serviço](service-fabric-deploy-remove-applications.md)
* [Gestão de parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurar a sua aplicação de Tecido de Serviço](service-fabric-debugging-your-application.md)
* [Visualizar o seu cluster utilizando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
