---
title: Implementar uma aplicação web com um modelo - Azure Cosmos DB
description: Saiba como implementar uma conta DB Azure Cosmos, aplicações web do Azure App Service e uma aplicação web de amostras usando um modelo de Gestor de Recursos Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 7d1080abb35e556e97c34e77fdce4d553c169ee9
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266870"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Implemente aplicações web Azure Cosmos DB e Azure App Service web usando um modelo de gestor de recursos Azure
Este tutorial mostra-lhe como usar um modelo de Gestor de Recursos Azure para implementar e integrar [o Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [app web Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) e uma aplicação web de amostra.

Utilizando modelos de Gestor de Recursos Azure, pode automatizar facilmente a implementação e configuração dos seus recursos Azure.  Este tutorial mostra como implementar uma aplicação web e configurar automaticamente informações de ligação à conta Azure Cosmos DB.

Após completar este tutorial, poderá responder às seguintes perguntas:  

* Como posso utilizar um modelo de Gestor de Recursos Azure para implementar e integrar uma conta DB Azure Cosmos e uma aplicação web no Azure App Service?
* Como posso utilizar um modelo de Gestor de Recursos Azure para implementar e integrar uma conta DB Azure Cosmos, uma aplicação web em Aplicações Web do Serviço de Aplicações e uma aplicação Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos
> [!TIP]
> Embora este tutorial não assuma experiência prévia com modelos de Gestor de Recursos Azure ou JSON, caso deseje modificar os modelos ou opções de implementação referenciados, então é necessário saber de cada uma dessas áreas.
> 
> 

Antes de seguir as instruções deste tutorial, certifique-se de que tem uma assinatura Azure. A Azure é uma plataforma baseada em subscrições.  Para obter mais informações sobre a obtenção de uma subscrição, consulte [Opções de Compra,](https://azure.microsoft.com/pricing/purchase-options/) [Ofertas de Membros](https://azure.microsoft.com/pricing/member-offers/)ou [Teste Gratuito.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Passo 1: Descarregue os ficheiros do modelo
Vamos começar por descarregar os ficheiros de modelo que este tutorial requer.

1. Descarregue a **conta Create a Azure Cosmos DB, Web Apps e implemente um modelo de aplicação de demonstração** `https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json` () para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta DB Azure Cosmos, uma aplicação web do Serviço de Aplicações e uma aplicação web.  Também configura automaticamente a aplicação web para ligar à conta DB do Azure Cosmos.
2. Descarregue o modelo **Create a Azure Cosmos DB e Web Apps** `https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json` () para uma pasta local (por exemplo, C:\Azure Cosmos DBTemplates). Este modelo implementa uma conta DB Azure Cosmos, uma aplicação web do Serviço de Aplicações, e modifica as definições de aplicação do site para facilmente surgir informações de ligação Azure Cosmos DB, mas não inclui uma aplicação web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Passo 2: Implementar a conta DB do Azure Cosmos, app web do Serviço de Aplicações e amostra de aplicação de demonstração
Agora vamos implementar o seu primeiro modelo.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome da conta DB Azure Cosmos introduzidos no modelo seguinte são a) válidos e b) disponíveis.  É altamente recomendável verificar a disponibilidade dos nomes que pretende fornecer antes de submeter a implementação.
> 
> 

1. Faça login no [Portal Azure,](https://portal.azure.com)clique em Novo e procure por "Implementação do Modelo".
    ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de implementação do modelo e clique em **Criar** ![ Screenshot da versão do modelo UI](./media/create-website/TemplateDeployment2.png)
3. Clique **no modelo de edição,** cole o conteúdo do ficheiro modelo DocDBWebsiteTodo.json e clique em **Guardar**.
   ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique **em Editar parâmetros,** forneça valores para cada um dos parâmetros obrigatórios e clique **em OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: Especifica o nome da aplicação web do Serviço de Aplicações e é utilizado para construir o URL que utiliza para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", então o URL pelo qual acede à aplicação web `mydemodocdbwebapp.azurewebsites.net` é).
   2. HOSTINGPLANNAME: Especifica o nome do plano de hospedagem do Serviço de Aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização Azure na qual criar o Azure Cosmos DB e os recursos de aplicações web.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta DB Azure Cosmos para criar.   
      
      ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de Recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique **em Rever termos legais,** **Comprar**e, em seguida, clique em **Criar** para iniciar a implementação.  Selecione **Pin para painel de instrumentos** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a colocação terminar, abre-se o painel de grupo de recursos.
   ![Screenshot do painel de grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Para utilizar a aplicação, navegue para o URL da aplicação web (no exemplo acima, o URL seria `http://mydemodocdbwebapp.azurewebsites.net` ).  Verá a seguinte aplicação web:
   
   ![Aplicação de Amostra Todo](./media/create-website/image2.png)
9. Vá em frente e crie algumas tarefas na aplicação web e, em seguida, volte ao painel do grupo de Recursos no portal Azure. Clique no recurso de conta DB Azure Cosmos na lista de Recursos e, em seguida, clique em **Data Explorer**.
10. Executar a consulta predefinitiva, "SELECT * FROM c" e inspecionar os resultados.  Note que a consulta recuperou a representação JSON dos itens que criou no passo 7 acima.  Sinta-se livre para experimentar consultas; por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = verdadeiro para devolver todos os itens que foram marcados como completos.
11. Sinta-se livre para explorar a experiência do portal Azure Cosmos DB ou modificar a aplicação de toda a amostra.  Quando estiver pronto, vamos implementar outro modelo.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Passo 3: Implementar a conta documental e a amostra de aplicativos web
Agora vamos implementar o seu segundo modelo.  Este modelo é útil para mostrar como pode injetar informações de conexão DB Azure Cosmos, tais como o ponto final da conta e a chave principal numa aplicação web como configurações de aplicação ou como uma cadeia de ligação personalizada. Por exemplo, talvez tenha a sua própria aplicação web que gostaria de implementar com uma conta DB Azure Cosmos e ter a informação de ligação automaticamente preenchida durante a implementação.

> [!TIP]
> O modelo não valida que o nome da aplicação web e o nome da conta DB Azure Cosmos introduzidos abaixo são a) válido e b) disponíveis.  É altamente recomendável verificar a disponibilidade dos nomes que pretende fornecer antes de submeter a implementação.
> 
> 

1. No [Portal Azure,](https://portal.azure.com)clique em Novo e procure por "Implementação do Modelo".
    ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment1.png)
2. Selecione o item de implementação do modelo e clique em **Criar** ![ Screenshot da versão do modelo UI](./media/create-website/TemplateDeployment2.png)
3. Clique **no modelo de edição,** cole o conteúdo do ficheiro do modelo DocDBWebSite.json e clique em **Guardar**.
   ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment3.png)
4. Clique **em Editar parâmetros,** forneça valores para cada um dos parâmetros obrigatórios e clique **em OK**.  Os parâmetros são os seguintes:
   
   1. SITENAME: Especifica o nome da aplicação web do Serviço de Aplicações e é utilizado para construir o URL que utilizará para aceder à aplicação web (por exemplo, se especificar "mydemodocdbwebapp", então o URL pelo qual acede à aplicação web é mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Especifica o nome do plano de hospedagem do Serviço de Aplicações para criar.
   3. LOCALIZAÇÃO: Especifica a localização Azure na qual criar o Azure Cosmos DB e os recursos de aplicações web.
   4. DATABASEACCOUNTNAME: Especifica o nome da conta DB Azure Cosmos para criar.   
      
      ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment4.png)
5. Escolha um grupo de Recursos existente ou forneça um nome para fazer um novo grupo de recursos e escolha uma localização para o grupo de recursos.

    ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment5.png)
6. Clique **em Rever termos legais,** **Comprar**e, em seguida, clique em **Criar** para iniciar a implementação.  Selecione **Pin para painel de instrumentos** para que a implementação resultante seja facilmente visível na página inicial do portal Azure.
   ![Screenshot da UI de implementação do modelo](./media/create-website/TemplateDeployment6.png)
7. Quando a colocação terminar, abre-se o painel de grupo de recursos.
   ![Screenshot do painel de grupo de recursos](./media/create-website/TemplateDeployment7.png)  
8. Clique no recurso da Web App na lista de Recursos e, em seguida, clique em Definições de **Aplicação** ![ Screenshot do grupo de recursos](./media/create-website/TemplateDeployment9.png)  
9. Note como existem configurações de aplicação presentes para o ponto final Azure Cosmos DB e cada uma das teclas master Azure Cosmos DB.

    ![Screenshot das definições de aplicações](./media/create-website/TemplateDeployment10.png)  
10. Sinta-se livre para continuar a explorar o Portal Azure, ou siga uma das nossas [amostras](https://go.microsoft.com/fwlink/?LinkID=402386) de DB Azure Cosmos para criar a sua própria aplicação DB Azure Cosmos.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passos seguintes
Parabéns! Implementou a Azure Cosmos DB, app web do Serviço de Aplicações e uma aplicação web de amostras utilizando modelos do Azure Resource Manager.

* Para saber mais sobre a Azure Cosmos DB, clique [aqui.](https://azure.microsoft.com/services/cosmos-db/)
* Para saber mais sobre as aplicações web do Azure App Service, clique [aqui.](https://go.microsoft.com/fwlink/?LinkId=325362)
* Para saber mais sobre os modelos do Gestor de Recursos Azure, clique [aqui.](https://msdn.microsoft.com/library/azure/dn790549.aspx)

## <a name="whats-changed"></a>O que mudou
* Para um guia para a mudança de Websites para App Service consulte: [Azure App Service e o seu impacto nos serviços Azure existentes](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

