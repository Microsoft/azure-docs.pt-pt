---
title: Criar aplicação web de Java - serviço de aplicações do Azure
description: Saiba como executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação Java básica.
services: app-service\web
documentationcenter: ''
author: rmcmurray
manager: routlaw
editor: ''
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: cephalin;robmcm
ms.custom: seodec18
ms.openlocfilehash: 6bf0562d222bce07f74ef5355ad0e3d6b8844c33
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096627"
---
# <a name="create-your-first-java-web-app-in-azure"></a>Criar a primeira aplicação Web Java no Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este guia de introdução mostra como implementar uma aplicação Web Java no Serviço de Aplicações com o [IDE do Eclipse para Programadores de Java EE](https://www.eclipse.org/).

> [!NOTE]
>
> Os passos neste início rápido mostram como utilizar o IDE Eclipse para publicar uma aplicação Web Java no Serviço de Aplicações, mas pode utilizar o IntelliJ IDEA Ultimate Edition ou a Community Edition. Para obter mais informações, veja [Create a Hello World web app for Azure using IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) (Criar uma aplicação Web Hello World para o Azure com IntelliJ).
>

Quando tiver concluído este guia de introdução, a aplicação terá um aspeto semelhante à ilustração seguinte quando a vir num browser:

!["Olá, Azure!” aplicação Web de exemplo](./media/app-service-web-get-started-java/browse-web-app-1.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de introdução, instale:

* O <a href="https://www.eclipse.org/downloads/" target="_blank">IDE do Eclipse para Programadores de Java EE</a> gratuito. Este guia de introdução utiliza o Eclipse Neon.
* O <a href="/java/azure/eclipse/azure-toolkit-for-eclipse-installation" target="_blank">Azure Toolkit para Eclipse</a>.

> [!NOTE]
>
> Para concluir os passos deste início rápido, terá de iniciar sessão na sua conta do Azure com o Azure Toolkit for Eclipse. Para tal, veja [Azure Sign In Instructions for the Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-sign-in-instructions) (Instruções de Início de Sessão do Azure para o Azure Toolkit for Eclipse).
>

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Criar um projeto Web dinâmico no Eclipse

No Eclipse, selecione **Ficheiro** > **Novo** > **Projeto Web Dinâmico**.

Na caixa de diálogo **Novo Projeto Web Dinâmico**, dê o nome **MyFirstJavaOnAzureWebApp** ao projeto e selecione **Concluir**.
   
![Caixa de diálogo Novo Projeto Web Dinâmico](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

### <a name="add-a-jsp-page"></a>Adicionar uma página JSP

Se o Explorador de Projeto não for apresentado, restaure-o.

![Área de trabalho Java EE para Eclipse](./media/app-service-web-get-started-java/pe.png)

No Explorador de Projeto, expanda o projeto **MyFirstJavaOnAzureWebApp**.
Clique com botão direito do rato em **WebContent**e, em seguida, selecione **Novo** > **Ficheiro JSP**.

![Menu de um novo ficheiro JSP no Explorador de Projeto](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

Na caixa de diálogo **Novo Ficheiro JSP**:

* Dê o nome **index.jsp** ao ficheiro.
* Selecione **Concluir**.

  ![Caixa de diálogo Novo Ficheiro JSP](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

No ficheiro index.jsp, substitua o elemento `<body></body>` pela seguinte marcação:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Guarde as alterações.

> [!NOTE]
>
> Se vir um erro na linha 1 que se refere a uma classe Java Servlet em falta, pode ignorá-lo.
> 
> ![Erro Java servlet benigno](./media/app-service-web-get-started-java/java-servlet-benign-error.png)
>

## <a name="publish-the-web-app-to-azure"></a>Publicar a aplicação Web no Azure

No Project Explorer, clique com o botão direito do rato no projeto e, em seguida, selecione **Azure** > **Publish as Azure Web App** (Publicar como Aplicação Web do Azure).

![Menu de contexto Publicar como Aplicação Web do Azure](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

Se surgir a caixa de diálogo **Início de Sessão do Azure**, terá de seguir os passos no artigo [Azure Sign In Instructions for the Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-sign-in-instructions) (Instruções de Início de Sessão do Azure para o Azure Toolkit for Eclipse) para introduzir as suas credenciais.

### <a name="deploy-web-app-dialog-box"></a>Caixa de diálogo Implementar Aplicação Web

Depois de iniciar sessão na conta do Azure, é apresentada a caixa de diálogo **Implementar Aplicação Web**.

Selecione **Criar**.

![Caixa de diálogo Implementar Aplicação Web](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

### <a name="create-app-service-dialog-box"></a>Caixa de diálogo Criar Serviço de Aplicações

A caixa de diálogo **Criar Serviço de Aplicações** é apresentada, com os valores predefinidos. O número **170602185241** mostrado na seguinte imagem é diferente na sua caixa de diálogo.

![Caixa de diálogo Criar Serviço de Aplicações](./media/app-service-web-get-started-java/cas1.png)

Na caixa de diálogo **Criar Serviço de Aplicações**:

* Introduza um nome exclusivo para a sua aplicação Web ou mantenha o que foi gerado. Este nome tem de ser exclusivo em todo o Azure. O nome faz parte do endereço de URL da aplicação Web. Por exemplo: se o nome da aplicação Web for **MyJavaWebApp**, o URL é *myjavawebapp.azurewebsites.net*.
* Neste início rápido, mantenha o contentor Web predefinido.
* Selecione uma subscrição do Azure.
* No separador **Plano do serviço de aplicações**:

  * **Criar um novo**: Mantenha a predefinição, o que é o nome do plano do serviço de aplicações.
  * **Localização**: Selecione **Europa Ocidental** ou para uma localização perto de si.
  * **Escalão de preço**: Selecione a opção gratuita. Para as funcionalidades, veja [Preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

    ![Caixa de diálogo Criar Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

### <a name="resource-group-tab"></a>Separador grupo de recursos

Selecione o separador **Grupo de recursos**. Mantenha o valor predefinido gerado para o grupo de recursos.

![Separador grupo de recursos](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Selecione **Criar**.

<!--
### The JDK tab

Select the **JDK** tab. Keep the default, and then select **Create**.

![Create App Service plan](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)
-->

O Azure Toolkit cria a aplicação Web e apresenta uma caixa de diálogo de progresso.

![Caixa de diálogo Progresso de Criar Serviço de Aplicações](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

### <a name="deploy-web-app-dialog-box"></a>Caixa de diálogo Implementar Aplicação Web

Na caixa de diálogo **Implementar Aplicação Web**, selecione **Implementar na raiz**. Se tiver um serviço de aplicações em *wingtiptoys.azurewebsites.net* e não implementar na raiz, a sua aplicação Web chamada **MyFirstJavaOnAzureWebApp** é implementada em *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Caixa de diálogo Implementar Aplicação Web](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

A caixa de diálogo mostra as seleções do Azure, JDK e contentor Web.

Selecione **Implementar** para publicar a aplicação Web no Azure.

Quando a publicação estiver concluída, selecione a ligação **Publicado** na caixa de diálogo **Registo de Atividade do Azure**.

![Caixa de diálogo de Registo de Atividade do Azure](./media/app-service-web-get-started-java/aal.png)

Parabéns! Implementou com êxito a sua aplicação Web no Azure. 

!["Olá, Azure!” aplicação Web de exemplo](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="update-the-web-app"></a>Atualizar a aplicação Web

Altere o código JSP de exemplo para uma mensagem diferente.

```jsp
<body>
<h1><% out.println("Hello again Azure!"); %></h1>
</body>
```

Guarde as alterações.

No Explorador de Projeto, clique com o botão direito do rato no projeto e, em seguida, selecione **Azure** > **Publicar como Aplicação Web do Azure**.

A caixa de diálogo **Implementar Aplicação Web** é apresentada e mostra o serviço de aplicações que criou anteriormente. 

> [!NOTE] 
> Selecione **Implementar na raiz** sempre que publicar. 
> 

Selecione a aplicação Web e selecione **Implementar**, que publica as alterações.

Quando a ligação **Publicação** for apresentada, selecione-a para navegar até à aplicação Web e ver as alterações.

## <a name="manage-the-web-app"></a>Gerir a aplicação Web

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para ver a aplicação Web que criou.

No menu à esquerda, selecione **Grupos de Recursos**.

![Navegação no portal para grupos de recursos](media/app-service-web-get-started-java/rg.png)

Selecione o grupo de recursos. A página mostra os recursos que criou neste guia de introdução.

![Grupo de recursos](media/app-service-web-get-started-java/rg2.png)

Selecione a aplicação Web (**webapp-170602193915** na imagem anterior).

É apresentada a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias configurações que pode abrir. 

![Página Serviço de Aplicações no portal do Azure](media/app-service-web-get-started-java/web-app-blade.png)

[!INCLUDE [clean-up-section-portal-web-app](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
