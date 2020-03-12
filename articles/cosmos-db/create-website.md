---
title: Implementar uma aplicação web com um modelo - Azure Cosmos DB
description: Saiba como implementar uma conta do Azure Cosmos DB, aplicações de Web do serviço de aplicações do Azure e um exemplo de aplicação web com um modelo Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128365"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implementar o Azure Cosmos DB e o Azure App Service Web Apps com um modelo de Gestor de recursos do Azure
Este tutorial mostra-lhe como usar um modelo de Gestor de Recursos Azure para implementar e integrar a aplicação web [do Microsoft Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) e uma aplicação web de amostra.

Utilizar modelos do Azure Resource Manager, pode facilmente automatizar a implementação e configuração de recursos do Azure.  Este tutorial mostra como implementar uma aplicação web e configurar automaticamente as informações de ligação de conta do Azure Cosmos DB.

Depois de concluir este tutorial, será capaz de responder às seguintes perguntas:  

* Como posso utilizar um modelo Azure Resource Manager para implementar e integrar uma conta do Azure Cosmos DB e uma aplicação web no App Service do Azure?
* Como posso utilizar um modelo Azure Resource Manager para implementar e integrar uma conta do Azure Cosmos DB, uma aplicação web no App Service Web Apps e uma aplicação Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos
> [!TIP]
> Embora este tutorial não assume experiência anterior com modelos Azure Resource Manager ou JSON, caso queira modificar as opções de implementação, ou modelos referenciados, em seguida, dados de conhecimento de cada uma dessas áreas é necessário.
> 
> 

Antes de seguir as instruções neste tutorial, certifique-se de que tem a uma subscrição do Azure. O Azure é uma plataforma baseada na subscrição.  Para obter mais informações sobre a obtenção de uma subscrição, consulte Opções de [Compra,](https://azure.microsoft.com/pricing/purchase-options/) [Ofertas de Membros](https://azure.microsoft.com/pricing/member-offers/)ou [Teste Gratuito.](https://azure.microsoft.com/pricing/free-trial/)

## <a id="CreateDB"></a>Passo 1: Descarregue os ficheiros do modelo
Vamos começar por transferir os ficheiros de modelo que este tutorial requer.

1. Descarregue a [conta Create a Azure Cosmos DB, Web Apps, e implemente um](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) modelo de amostra de aplicação de demonstração para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta do Azure Cosmos DB, uma aplicação do serviço de aplicações web e uma aplicação web.  Configura também automaticamente a aplicação web para ligar à conta do Azure Cosmos DB.
2. Descarregue o modelo de [amostra de Create a Cosmos DB e Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta do Azure Cosmos DB, uma aplicação web do serviço de aplicações e modifica as definições da aplicação do site para descobrir facilmente informações de ligação de Azure Cosmos DB, mas não inclui um aplicativo web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Passo 2: Implementar a conta do Azure Cosmos DB, a aplicação do serviço de aplicações web e o exemplo de aplicação de demonstração
Agora vamos implementar seu primeiro modelo.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome de conta do Azure Cosmos DB introduzida no modelo seguinte estão a) válido e b) disponível.  É altamente recomendável que verifique a disponibilidade dos nomes de que pretende fornecer antes de submeter a implementação.
> 
> 

1. Inicie sessão no [Portal Azure,](https://portal.azure.com)clique em Novo e procure "Implementação do Modelo".
    ![Screenshot do](./media/create-website/TemplateDeployment1.png) de implantação do modelo
2. Selecione o item de implementação do modelo e clique **em Criar** ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment2.png)
3. Clique no **modelo Editar,** reexa o conteúdo do ficheiro do modelo DocDBWebsiteTodo.json e clique em **Guardar**.
   ![Screenshot do](./media/create-website/TemplateDeployment3.png) de implantação do modelo
4. Clique em **parâmetros Editar,** forneça valores para cada um dos parâmetros obrigatórios e clique EM **OK**.  Os parâmetros são os seguintes:
   
   1. NOME DO SITE: Especifica o nome da aplicação web do App Service e é utilizado para construir o URL que utiliza para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", então o URL pelo qual acede à aplicação web é `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: Especifica o nome do plano de alojamento do serviço de aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização do Azure na qual se cria, o Azure Cosmos DB e a web, recursos de aplicação.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta do Azure Cosmos DB para criar.   
      
      ![Captura de ecrã da implementação do modelo da interface do Usuário](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Captura de ecrã da implementação do modelo da interface do Usuário](./media/create-website/TemplateDeployment5.png)
6. Clique em **Rever os termos legais,** **comprar,** e depois clique em **Criar** para iniciar a implementação.  Selecione **Pin para dashboard** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot do](./media/create-website/TemplateDeployment6.png) de implantação do modelo
7. Quando a implementação estiver concluída, é aberto o painel do grupo de recursos.
   ![Screenshot do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Para utilizar a aplicação, navegue para o URL da aplicação web (no exemplo acima, o URL seria `http://mydemodocdbwebapp.azurewebsites.net`).  Verá a seguinte aplicação web:
   
   ![Exemplo de aplicação de lista de tarefas](./media/create-website/image2.png)
9. Vá em frente e criar algumas das tarefas na aplicação web e, em seguida, regressar ao painel do grupo de recursos no portal do Azure. Clique no recurso de conta Azure Cosmos DB na lista de Recursos e, em seguida, clique em **Data Explorer**.
10. Executar a consulta predefinida, "SELECIONAR * FROM c" e inspecionar os resultados.  Tenha em atenção que a consulta tem de obter a representação JSON de itens de afazeres que criou no passo 7 acima.  Fique à vontade experimentar consultas; Por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = true para retornar todos os itens de afazeres que foram marcados como concluídos.
11. Fique à vontade explorar a experiência do portal do Azure Cosmos DB ou modificar o exemplo de aplicativo de lista de tarefas.  Quando estiver pronto, vamos implementar outro modelo.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Passo 3: Implementar o exemplo de aplicação de web e de conta de documento
Agora vamos implementar o modelo de segundo.  Este modelo é útil para mostrar como pode injetar informações de ligação do Azure Cosmos DB, como o ponto final da conta e a chave mestra numa aplicação web como as definições da aplicação ou como uma cadeia de caracteres de conexão personalizada. Por exemplo, talvez tenha seu próprio aplicativo da web que gostaria de implementar com uma conta do Azure Cosmos DB e tem as informações de ligação são preenchidas automaticamente durante a implementação.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome da conta do Azure Cosmos DB introduzida abaixo são a) válido e b) estão disponíveis.  É altamente recomendável que verifique a disponibilidade dos nomes de que pretende fornecer antes de submeter a implementação.
> 
> 

1. No [Portal Azure,](https://portal.azure.com)clique em Novo e procure "Implementação do Modelo".
    ![Screenshot do](./media/create-website/TemplateDeployment1.png) de implantação do modelo
2. Selecione o item de implementação do modelo e clique **em Criar** ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment2.png)
3. Clique no **modelo Editar,** reexa o conteúdo do ficheiro de modelo DocDBWebSite.json e clique em **Guardar**.
   ![Screenshot do](./media/create-website/TemplateDeployment3.png) de implantação do modelo
4. Clique em **parâmetros Editar,** forneça valores para cada um dos parâmetros obrigatórios e clique EM **OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: Especifica o nome da aplicação do serviço de aplicações web e é utilizado para criar o URL que irá utilizar para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", em seguida, o URL através do qual acessar a aplicação web é mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Especifica o nome do plano de alojamento do serviço de aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização do Azure na qual se cria, o Azure Cosmos DB e a web, recursos de aplicação.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta do Azure Cosmos DB para criar.   
      
      ![Captura de ecrã da implementação do modelo da interface do Usuário](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Captura de ecrã da implementação do modelo da interface do Usuário](./media/create-website/TemplateDeployment5.png)
6. Clique em **Rever os termos legais,** **comprar,** e depois clique em **Criar** para iniciar a implementação.  Selecione **Pin para dashboard** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot do](./media/create-website/TemplateDeployment6.png) de implantação do modelo
7. Quando a implementação estiver concluída, é aberto o painel do grupo de recursos.
   ![Screenshot do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Clique no recurso da Aplicação Web na lista de Recursos e, em seguida, clique nas definições de **Aplicação** ![Screenshot do grupo de recursos](./media/create-website/TemplateDeployment9.png)  
9. Observe como se existem definições de aplicação presentes para o ponto de final do Azure Cosmos DB e cada uma das chaves de mestre do Azure Cosmos DB.

    ![Captura de ecrã de definições da aplicação](./media/create-website/TemplateDeployment10.png)  
10. Sinta-se livre para continuar a explorar o Portal Azure, ou siga uma das nossas [amostras](https://go.microsoft.com/fwlink/?LinkID=402386) Azure Cosmos DB para criar a sua própria aplicação Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passos seguintes
Parabéns! Implementar o Azure Cosmos DB, aplicação web do serviço de aplicações e um exemplo de aplicação web com modelos Azure Resource Manager.

* Para saber mais sobre o Azure Cosmos DB, clique [aqui.](https://azure.microsoft.com/services/cosmos-db/)
* Para saber mais sobre as aplicações Web do Azure App Service, clique [aqui](https://go.microsoft.com/fwlink/?LinkId=325362).
* Para saber mais sobre os modelos do Gestor de Recursos Azure, clique [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

