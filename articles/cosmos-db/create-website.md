---
title: Implemente uma aplicação web com um modelo - Azure Cosmos DB
description: Saiba como implementar uma conta Azure Cosmos DB, Aplicações Web do Serviço de Aplicações de Aplicações de Aplicações de Aplicações de Aplicações de Aplicação de Aplicações de Aplicação de Aplicações e uma aplicação web de amostra utilizando um modelo de Gestor de Recursos Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128365"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implemente web apps web do serviço de aplicações Azure Cosmos DB e Azure usando um modelo de gestor de recursos azure
Este tutorial mostra-lhe como usar um modelo de Gestor de Recursos Azure para implementar e integrar a aplicação web [do Microsoft Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) e uma aplicação web de amostra.

Utilizando modelos de Gestor de Recursos Azure, pode facilmente automatizar a implementação e configuração dos seus recursos Azure.  Este tutorial mostra como implementar uma aplicação web e configurar automaticamente as informações de conexão da conta Azure Cosmos DB.

Depois de completar este tutorial, poderá responder às seguintes perguntas:  

* Como posso usar um modelo de Gestor de Recursos Azure para implementar e integrar uma conta Azure Cosmos DB e uma aplicação web no Azure App Service?
* Como posso usar um modelo de Gestor de Recursos Azure para implementar e integrar uma conta Azure Cosmos DB, uma aplicação web em Aplicações Web do Serviço de Aplicações e uma aplicação webimplement?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos
> [!TIP]
> Embora este tutorial não assuma experiência prévia com modelos de Gestor de Recursos Azure ou JSON, caso deseje modificar os modelos ou opções de implementação referenciados, então é necessário conhecer cada uma destas áreas.
> 
> 

Antes de seguir as instruções deste tutorial, certifique-se de que tem uma assinatura Azure. O Azure é uma plataforma baseada em subscrições.  Para obter mais informações sobre a obtenção de uma subscrição, consulte Opções de [Compra,](https://azure.microsoft.com/pricing/purchase-options/) [Ofertas de Membros](https://azure.microsoft.com/pricing/member-offers/)ou [Teste Gratuito.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Passo 1: Descarregue os ficheiros do modelo
Vamos começar por descarregar os ficheiros de modelo que este tutorial requer.

1. Descarregue a [conta Create a Azure Cosmos DB, Web Apps, e implemente um](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) modelo de amostra de aplicação de demonstração para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta Azure Cosmos DB, uma aplicação web do App Service e uma aplicação web.  Também configura automaticamente a aplicação web para ligar à conta Azure Cosmos DB.
2. Descarregue o modelo de [amostra de Create a Cosmos DB e Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta Azure Cosmos DB, uma aplicação web do App Service, e modifica as definições de aplicação do site para facilmente superfícier informações de conexão Azure Cosmos DB, mas não inclui uma aplicação web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Passo 2: Implementar a conta Azure Cosmos DB, app web do App Service e amostra de aplicação de demonstração
Agora vamos implementar o seu primeiro modelo.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome da conta Azure Cosmos DB inseridos no seguinte modelo são a) válidos e b) disponíveis.  É altamente recomendável que verifique a disponibilidade dos nomes que pretende fornecer antes de submeter a implementação.
> 
> 

1. Inicie sessão no [Portal Azure,](https://portal.azure.com)clique em Novo e procure "Implementação do Modelo".
    ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de implementação do modelo e clique **em Criar** ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment2.png)
3. Clique no **modelo Editar,** reexa o conteúdo do ficheiro do modelo DocDBWebsiteTodo.json e clique em **Guardar**.
   ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique em **parâmetros Editar,** forneça valores para cada um dos parâmetros obrigatórios e clique EM **OK**.  Os parâmetros são os seguintes:
   
   1. NOME DO SITE: Especifica o nome da aplicação web do App Service e é utilizado para construir o URL que utiliza para aceder à aplicação `mydemodocdbwebapp.azurewebsites.net`web (por exemplo, se especificar "mydemodocdbwebapp", então o URL pelo qual acede à aplicação web é).
   2. HOSTINGPLANNAME: Especifica o nome do plano de hospedagem do Serviço de Aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização Azure para criar os recursos da Azure Cosmos DB e da aplicação web.
   4. NOME DE DADOS: Especifica o nome da conta Azure Cosmos DB para criar.   
      
      ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de Recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique em **Rever os termos legais,** **comprar,** e depois clique em **Criar** para iniciar a implementação.  Selecione **Pin para dashboard** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a implantação terminar, o painel do grupo Resource abre.
   ![Screenshot do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Para utilizar a aplicação, navegue para o URL da `http://mydemodocdbwebapp.azurewebsites.net`aplicação web (no exemplo acima, o URL seria).  Verá a seguinte aplicação web:
   
   ![Aplicação De Todo amostra](./media/create-website/image2.png)
9. Vá em frente e crie algumas tarefas na aplicação web e, em seguida, volte ao painel do grupo Recursos no portal Azure. Clique no recurso de conta Azure Cosmos DB na lista de Recursos e, em seguida, clique em **Data Explorer**.
10. Executar a consulta predefinida, "SELECT * FROM c" e inspecione os resultados.  Note que a consulta recuperou a representação json dos itens todo o que criou no passo 7 acima.  Sinta-se à vontade para experimentar consultas; por exemplo, tente executar SELECT * A partir de c ONDE c.isComplete = fiel a devolver todos os itens que foram marcados como completos.
11. Sinta-se livre para explorar a experiência do portal Azure Cosmos DB ou modificar a aplicação Todo da amostra.  Quando estiver pronto, vamos colocar outro modelo.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Passo 3: Implementar a conta documental e a amostra da aplicação web
Agora vamos implementar o seu segundo modelo.  Este modelo é útil para mostrar como pode injetar informações de ligação Azure Cosmos DB, tais como ponto final de conta e chave principal numa aplicação web como configurações de aplicação ou como uma cadeia de ligação personalizada. Por exemplo, talvez tenha a sua própria aplicação web que gostaria de implementar com uma conta Azure Cosmos DB e ter a informação de ligação automaticamente povoada durante a implementação.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome da conta Azure Cosmos DB inseridos abaixo são a) válidos e b) disponíveis.  É altamente recomendável que verifique a disponibilidade dos nomes que pretende fornecer antes de submeter a implementação.
> 
> 

1. No [Portal Azure,](https://portal.azure.com)clique em Novo e procure "Implementação do Modelo".
    ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de implementação do modelo e clique **em Criar** ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment2.png)
3. Clique no **modelo Editar,** reexa o conteúdo do ficheiro de modelo DocDBWebSite.json e clique em **Guardar**.
   ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique em **parâmetros Editar,** forneça valores para cada um dos parâmetros obrigatórios e clique EM **OK**.  Os parâmetros são os seguintes:
   
   1. NOME DO SITE: Especifica o nome da aplicação web do App Service e é utilizado para construir o URL que utilizará para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", então o URL pelo qual acede à aplicação web é mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Especifica o nome do plano de hospedagem do Serviço de Aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização Azure para criar os recursos da Azure Cosmos DB e da aplicação web.
   4. NOME DE DADOS: Especifica o nome da conta Azure Cosmos DB para criar.   
      
      ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de Recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique em **Rever os termos legais,** **comprar,** e depois clique em **Criar** para iniciar a implementação.  Selecione **Pin para dashboard** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot do UI de implementação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a implantação terminar, o painel do grupo Resource abre.
   ![Screenshot do painel do grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Clique no recurso da Aplicação Web na lista de Recursos e, em seguida, clique em **configurações** ![de aplicação Screenshot do grupo de recursos](./media/create-website/TemplateDeployment9.png)  
9. Note como existem configurações de aplicação presentes para o ponto final do Azure Cosmos DB e cada uma das chaves master Azure Cosmos DB.

    ![Screenshot das definições de aplicação](./media/create-website/TemplateDeployment10.png)  
10. Sinta-se livre para continuar a explorar o Portal Azure, ou siga uma das nossas [amostras](https://go.microsoft.com/fwlink/?LinkID=402386) Azure Cosmos DB para criar a sua própria aplicação Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passos seguintes
Parabéns! Implementou o Azure Cosmos DB, a aplicação web do App Service e uma aplicação web de amostra usando modelos do Gestor de Recursos Azure.

* Para saber mais sobre o Azure Cosmos DB, clique [aqui.](https://azure.microsoft.com/services/cosmos-db/)
* Para saber mais sobre as aplicações Web do Azure App Service, clique [aqui](https://go.microsoft.com/fwlink/?LinkId=325362).
* Para saber mais sobre os modelos do Gestor de Recursos Azure, clique [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>O que mudou
* Para um guia para a mudança de Websites para App Service ver: [Serviço de Aplicações Azure e seu impacto nos serviços azure existentes](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

