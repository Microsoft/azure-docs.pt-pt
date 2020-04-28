---
title: Implementar aplicativos previsivelmente com a ARM
description: Aprenda a implementar várias aplicações do Azure App Service como uma única unidade e de forma previsível usando modelos de Gestão de Recursos Azure e scripts PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75967385"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Fornecimento e implantação de microserviços previsivelmente em Azure
Este tutorial mostra como fornecer e implementar uma aplicação composta por [microserviços](https://en.wikipedia.org/wiki/Microservices) no Serviço de [Aplicações Azure](https://azure.microsoft.com/services/app-service/) como uma única unidade e de forma previsível usando modelos de grupo de recursos JSON e scripts PowerShell. 

Ao fornecer e implementar aplicações de alta escala compostas por microserviços altamente dissociados, a repetível e a previsibilidade são cruciais para o sucesso. [O Azure App Service](https://azure.microsoft.com/services/app-service/) permite-lhe criar microserviços que incluam aplicações web, back ends móveis e aplicações API. [O Azure Resource Manager](../azure-resource-manager/management/overview.md) permite-lhe gerir todos os microserviços como uma unidade, juntamente com dependências de recursos, tais como bases de dados e configurações de controlo de fontes. Agora, você também pode implementar tal aplicação usando modelos JSON e scripts powerShell simples. 

## <a name="what-you-will-do"></a>O que vai fazer
No tutorial, irá implementar uma aplicação que inclui:

* Duas aplicações do App Service (ou é, dois microserviços)
* Uma base de dados SQL de backend
* Definições de aplicativos, cordas de ligação e controlo de fontes
* Insights de aplicação, alertas, definições de autoscalcificação

## <a name="tools-you-will-use"></a>Ferramentas que vai usar
Neste tutorial, utilizará as seguintes ferramentas. Como não é uma discussão abrangente sobre ferramentas, vou manter-me no cenário de ponta a ponta e dar-vos uma breve introdução a cada um, e onde podem encontrar mais informações sobre isso. 

### <a name="azure-resource-manager-templates-json"></a>Modelos de Gestor de Recursos Azure (JSON)
Sempre que cria uma aplicação no Azure App Service, por exemplo, o Gestor de Recursos Azure usa um modelo JSON para criar todo o grupo de recursos com os recursos componentes. Um modelo complexo do [Azure Marketplace](/azure/marketplace) pode incluir a base de dados, contas de armazenamento, o plano do Serviço de Aplicações, a própria app, regras de alerta, definições de aplicações, configurações de escala automática e muito mais, e todos estes modelos estão disponíveis para si através do PowerShell. Para obter mais informações sobre os modelos do Gestor de Recursos Azure, consulte os modelos de [gestor de recursos do Azure](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 para Estúdio Visual
O mais recente SDK contém melhorias no suporte do modelo de Gestor de Recursos no editor da JSON. Você pode usá-lo para criar rapidamente um modelo de grupo de recursos de raiz ou abrir um modelo JSON existente (como um modelo de galeria descarregado) para modificação, povoar o arquivo de parâmetros e até mesmo implementar o grupo de recursos diretamente a partir de uma solução do Grupo de Recursos Azure.

Para mais informações, consulte [Azure SDK 2.6 para Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 ou mais tarde
A partir da versão 0.8.0, a instalação Azure PowerShell inclui o módulo Azure Resource Manager, além do módulo Azure. Este novo módulo permite-lhe escrever a implantação de grupos de recursos.

Para mais informações, consulte [A Utilização de PowerShell Azure com O Gestor](../powershell-azure-resource-manager.md) de Recursos Azure

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Esta [ferramenta de pré-visualização](https://resources.azure.com) permite-lhe explorar as definições JSON de todos os grupos de recursos na sua subscrição e recursos individuais. Na ferramenta, pode editar as definições JSON de um recurso, eliminar toda uma hierarquia de recursos e criar novos recursos.  A informação prontamente disponível nesta ferramenta é muito útil para a autoria do modelo porque mostra quais as propriedades que precisa definir para um determinado tipo de recurso, os valores corretos, etc. Pode até criar o seu grupo de recursos no [Portal Azure,](https://portal.azure.com/)depois inspecionar as definições da JSON na ferramenta exploradora para o ajudar a templatizar o grupo de recursos.

### <a name="deploy-to-azure-button"></a>Desdobrar para o botão Azure
Se utilizar o GitHub para controlo de fontes, pode colocar um [botão Deploy para Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) na sua README. MD, que permite uma chave de mão UI para Azure. Embora possa fazê-lo para qualquer aplicação simples, pode estender isto para permitir a implementação de todo um grupo de recursos colocando um ficheiro azuredeploy.json na raiz do repositório. Este ficheiro JSON, que contém o modelo de grupo de recursos, será utilizado pelo botão Deploy to Azure para criar o grupo de recursos. Por exemplo, consulte a amostra [do ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) que utilizará neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obtenha o modelo de grupo de recursos da amostra
Então, agora vamos direto ao que isso.

1. Navegue para a amostra do Serviço de Aplicações [ToDoApp.](https://github.com/azure-appservice-samples/ToDoApp)
2. Em readme.md, clique em **Implementar para Azure**.
3. Foi levado para o local de [implantação](https://deploy.azure.com) para o azul e pediu para inserir parâmetros de implantação. Note que a maioria dos campos estão povoados com o nome do repositório e algumas cordas aleatórias para si. Pode alterar todos os campos se quiser, mas as únicas coisas que tem de introduzir são o login administrativo do SQL Server e a palavra-passe, em seguida, clique em **Next**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **Implementar** para iniciar o processo de implementação. Uma vez que o processo http://todoappse esgote, clique no link *XXXX*.azurewebsites.net para navegar na aplicação implementada. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   O UI seria um pouco lento quando se navega pela primeira vez porque as aplicações estão apenas a começar, mas convença-se de que é uma aplicação totalmente funcional.
5. De volta à página 'Enviar', clique no link **Gerir** para ver a nova aplicação no Portal Azure.
6. No dropdown **Essentials,** clique no link do grupo de recursos. Note também que a aplicação já está ligada ao repositório GitHub no âmbito do **Projeto Externo**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Na lâmina do grupo de recursos, note que já existem duas apps e uma Base de Dados SQL no grupo de recursos.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que viu em poucos minutos é uma aplicação de dois microserviços totalmente implantada, com todos os componentes, dependências, configurações, base de dados e publicação contínua, criada por uma orquestração automatizada no Azure Resource Manager. Tudo isto foi feito por duas coisas:

* O botão Deploy para Azure
* azuredeploy.json na raiz repo

Pode implementar esta mesma aplicação dezenas, centenas ou milhares de vezes e ter sempre a mesma configuração. A repetível e a previsibilidade desta abordagem permitem-lhe implementar aplicações de alta escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examinar (ou editar) AZUREDEPLOY. Rio JSON
Agora vamos ver como o repositório GitHub foi criado. Estará a utilizar o editor da JSON no Azure .NET SDK, por isso, se ainda não tiver instalado [o Azure .NET SDK 2.6,](https://azure.microsoft.com/downloads/)faça-o agora.

1. Clone o repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) utilizando a sua ferramenta git favorita. Na imagem abaixo, estou a fazer isto no Team Explorer no Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Da raiz do repositório, azuredeploy.json aberto no Estúdio Visual. Se não vir o painel de contorno jSON, precisa de instalar o Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não vou descrever todos os detalhes do formato JSON, mas a secção [Mais Recursos](#resources) tem links para aprender a linguagem do modelo do grupo de recursos. Aqui, vou mostrar-lhe as funcionalidades interessantes que podem ajudá-lo a começar a fazer o seu próprio modelo personalizado para implementação de apps.

### <a name="parameters"></a>Parâmetros
Dê uma olhada na secção de parâmetros para ver se a maioria destes parâmetros são o que o botão **Deploy to Azure** o leva a inserir. O local por trás do botão **Deploy to Azure** povoa o UI de entrada utilizando os parâmetros definidos em azuredeploy.json. Estes parâmetros são utilizados ao longo das definições de recursos, tais como nomes de recursos, valores de propriedade, etc.

### <a name="resources"></a>Recursos
No nó de recursos, você pode ver que 4 recursos de alto nível são definidos, incluindo uma instância SQL Server, um plano de Serviço de Aplicações e duas aplicações. 

#### <a name="app-service-plan"></a>Plano do App Service
Vamos começar com um simples recurso de nível de raiz no JSON. No contorno jSON, clique no plano de serviço de aplicações nomeado **[hostingPlanName]** para destacar o código JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Note que `type` o elemento especifica a cadeia para um plano de Serviço de Aplicações (foi chamado de quinta de servidores há muito, muito tempo), e outros elementos e propriedades são preenchidos usando os parâmetros definidos no ficheiro JSON, e este recurso não tem recursos aninhados.

> [!NOTE]
> Note também que `apiVersion` o valor do diz ao Azure com que versão da API REST utilizar a definição `{}`de recursos JSON, e pode afetar a forma como o recurso deve ser formatado dentro da . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso SQL Server chamado **SQLServer** no contorno JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Note o seguinte sobre o código JSON realçado:

* A utilização de parâmetros garante que os recursos criados são nomeados e configurados de uma forma que os torna consistentes uns com os outros.
* O recurso SQLServer tem dois recursos aninhados, cada um tem um valor diferente para `type`.
* Os recursos aninhados no interior, `“resources”: […]`onde a `dependsOn` base de dados e as regras de firewall são definidas, têm um elemento que especifica o ID de recurso do recurso SQLServer de nível raiz. Isto diz ao Azure Resource Manager, "antes de criar este recurso, esse outro recurso já deve existir; e se esse outro recurso for definido no modelo, então crie-o primeiro".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como utilizar a `resourceId()` função, consulte funções de modelo de gestor de recursos do [Azure](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* O efeito `dependsOn` do elemento é que o Gestor de Recursos Azure pode saber quais os recursos que podem ser criados em paralelo e quais os recursos que devem ser criados sequencialmente. 

#### <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Agora, vamos passar para as próprias aplicações, que são mais complicadas. Clique na aplicação [variáveis('apiSiteName')] no Contorno JSON para destacar o seu código JSON. Vais reparar que as coisas estão a ficar muito mais interessantes. Para este fim, falarei sobre as características, uma a uma:

##### <a name="root-resource"></a>Recurso de raiz
A aplicação depende de dois recursos diferentes. Isto significa que o Gestor de Recursos Azure só criará a app depois de criado o plano de Serviço de Aplicações e a instância do Servidor SQL.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Definições da aplicação
As definições da aplicação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No `properties` elemento `config/appsettings`para , tem duas definições de aplicação no formato `"<name>" : "<value>"`.

* `PROJECT`é um [cenário KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que diz à implantação do Azure qual o projeto a utilizar numa solução de estúdio visual multi-projeto. Mostrar-lhe-ei mais tarde como o controlo de fontes está configurado, mas como o código ToDoApp está numa solução de estúdio visual multi-projeto, precisamos desta definição.
* `clientUrl`é simplesmente uma definição de aplicação que o código de aplicação utiliza.

##### <a name="connection-strings"></a>Cadeias de ligação
As cordas de ligação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No `properties` elemento `config/connectionstrings`para , cada corda de ligação também é definida `"<name>" : {"value": "…", "type": "…"}`como um nome:par de valor, com o formato específico de . Para `type` o elemento, `MySql`os `SQLServer` `SQLAzure`valores `Custom`possíveis são, e .

> [!TIP]
> Para obter uma lista definitiva dos tipos de cordas de \[ligação, execute o seguinte comando em Azure PowerShell: Enum:GetNames ("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controlo de código fonte
As definições de controlo de origem também são definidas como um recurso aninhado. O Azure Resource Manager utiliza este recurso para configurar a publicação contínua (ver ressalva mais `IsManualIntegration` tarde) e também para iniciar a implementação do código de aplicação automaticamente durante o processamento do ficheiro JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`e `branch` deve ser bastante intuitivo e deve apontar para o repositório Git e o nome da sucursal para publicar. Mais uma vez, estes são definidos por parâmetros de entrada. 

Note no `dependsOn` elemento que, além do próprio `sourcecontrols/web` recurso da `config/appsettings` `config/connectionstrings`aplicação, também depende e . Isto porque `sourcecontrols/web` uma vez configurado, o processo de implementação do Azure tentará automaticamente implementar, construir e iniciar o código de aplicação. Por isso, inserir esta dependência ajuda-o a certificar-se de que a aplicação tem acesso às definições de aplicação e às cordas de ligação necessárias antes de o código de aplicação ser executado. 

> [!NOTE]
> Nota também `IsManualIntegration` que `true`está definido para . Esta propriedade é necessária neste tutorial porque você realmente não é dono do repositório GitHub, e assim não pode realmente conceder permissão ao Azure para configurar a publicação contínua do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (isto é, impulsionar atualizações automáticas de repositório para Azure). Só pode utilizar `false` o valor predefinido para o repositório especificado se tiver configurado as credenciais GitHub do proprietário no [portal Azure](https://portal.azure.com/) antes. Por outras palavras, se tiver configurado o controlo de origem para o GitHub ou BitBucket para qualquer aplicação no [Portal Azure](https://portal.azure.com/) anteriormente, utilizando as credenciais de utilizador, então o Azure lembrar-se-á das credenciais e usá-las-á sempre que implementar qualquer aplicação do GitHub ou BitBucket no futuro. No entanto, se ainda não o fez, a implementação do modelo JSON falhará quando o Gestor de Recursos do Azure tentar configurar as definições de controlo de fonte da aplicação, porque não consegue entrar no GitHub ou bitBucket com as credenciais do proprietário do repositório.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Compare o modelo JSON com o grupo de recursos implantados
Aqui, pode ver todas as lâminas da aplicação no [Portal Azure](https://portal.azure.com/), mas há outra ferramenta que é igualmente útil, se não mais. Vá à ferramenta de pré-visualização do [Azure Resource Explorer,](https://resources.azure.com) que lhe dá uma representação JSON de todos os grupos de recursos nas suas subscrições, uma vez que existem realmente no backend Azure. Você também pode ver como a hierarquia JSON do grupo de recursos em Azure corresponde com a hierarquia no ficheiro de modelo que é usado para criá-lo.

Por exemplo, quando vou à ferramenta [do Azure Resource Explorer](https://resources.azure.com) e expandi os nós do explorador, posso ver o grupo de recursos e os recursos de nível raiz que são recolhidos sob os respetivos tipos de recursos.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se perfurar uma aplicação, deverá ser capaz de ver detalhes de configuração da aplicação semelhantes ao screenshot abaixo:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Mais uma vez, os recursos aninhados devem ter uma hierarquia muito semelhante às do seu ficheiro de modelo JSON, e você deve ver as definições da aplicação, cordas de ligação, etc., devidamente refletidas no painel JSON. A ausência de definições aqui pode indicar um problema com o seu ficheiro JSON e pode ajudá-lo a resolver o seu ficheiro de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implante o modelo do grupo de recursos
O botão **Deploy to Azure** é ótimo, mas permite-lhe implementar o modelo de grupo de recursos em azuredeploy.json apenas se já tiver empurrado azuredeploy.json para GitHub. O Azure .NET SDK também fornece as ferramentas para que possa implementar qualquer ficheiro de modelo JSON diretamente da sua máquina local. Para isso, siga os passos abaixo:

1. No Estúdio Visual, clique em **File** > **New** > **Project**.
2. Clique no **Visual C#** > **Cloud** > **Azure Resource Group**e, em seguida, clique em **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. No **modelo De Selecione Azure,** selecione **o modelo em branco** e clique **em OK**.
4. Arraste azuredeploy.json para a pasta **Modelo** do seu novo projeto.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Do Solution Explorer, abra o azuredeploy.json copiado.
6. Só para o bem da demonstração, vamos adicionar alguns recursos padrão de Insight de aplicação ao nosso ficheiro JSON, clicando em **Adicionar Recurso**. Se estiver apenas interessado em implementar o ficheiro JSON, salte para os passos de implantação.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Insights de aplicação para aplicações web,** em seguida, certifique-se de que um plano e app de serviço de aplicação existentes é selecionado e, em seguida, clique em **Adicionar**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Agora poderá ver vários novos recursos que, dependendo do recurso e do que faz, têm dependências do plano de Serviço de Aplicações ou da app. Estes recursos não são ativados pela sua definição existente e tu vais mudar isso.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. No Contorno JSON, clique em **appInsights AutoScale** para destacar o seu código JSON. Este é o ajuste de escala para o seu plano de Serviço de Aplicações.
9. No código JSON destacado, `location` localize as propriedades e `enabled` coloque-as como mostrado abaixo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. No Contorno JSON, clique em **CPUHigh appInsights** para destacar o seu código JSON. Isto é um alerta.
11. Localize `location` `isEnabled` as propriedades e coloque-as como mostrado abaixo. Faça o mesmo com os outros três alertas (lâmpadas roxas).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Está pronto para partir. Clique no direito do projeto e selecione **Implementar** > **Nova Implementação**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Inicie sessão na sua conta Azure se ainda não o fez.
14. Selecione um grupo de recursos existente na sua subscrição ou crie um novo, **selecione azuredeploy.json**e, em seguida, clique em **Editar Parâmetros**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora poderá editar todos os parâmetros definidos no ficheiro do modelo numa mesa bonita. Os parâmetros que definem os incumprimentos já terão os seus valores predefinidos, e os parâmetros que definem uma lista de valores permitidos serão mostrados como downdowns.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha todos os parâmetros vazios e utilize o [endereço de repo GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) no **repoUrl**. Em seguida, clique em **Guardar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > A autoscalcificação é uma funcionalidade oferecida no nível **Standard** ou superior, e os alertas de nível de plano são funcionalidades oferecidas em nível **Básico** ou superior, terá de definir o parâmetro **sku** para **Standard** ou **Premium** para ver todos os seus novos recursos app Insights iluminarem-se.
    > 
    > 
16. Clique em **Implementar**. Se selecionar **Guardar palavras-passe,** a palavra-passe será guardada no ficheiro do parâmetro **em texto simples**. Caso contrário, será-lhe pedido que insera a palavra-passe da base de dados durante o processo de implementação.

Já está! Agora basta ir ao [Portal Azure](https://portal.azure.com/) e à ferramenta [Azure Resource Explorer](https://resources.azure.com) para ver os novos alertas e configurações de escala automática adicionadas à sua aplicação implantada pela JSON.

Os seus passos nesta secção realizaram principalmente o seguinte:

1. Preparou o ficheiro do modelo
2. Criou um ficheiro de parâmetros para acompanhar o ficheiro do modelo
3. Implementou o ficheiro do modelo com o ficheiro parâmetro

O último passo é facilmente feito por um cmdlet PowerShell. Para ver o que o Visual Studio fez quando implementou a sua aplicação, abra scripts\Deploy-AzureResourceGroup.ps1. Há lá muito código, mas vou destacar todo o código pertinente que precisa para implementar o ficheiro do modelo com o ficheiro do parâmetro.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup`é aquele que realmente realiza a ação. Tudo isto deve demonstrar-lhe que, com a ajuda da ferramenta, é relativamente simples implementar a sua aplicação na nuvem previsivelmente. Sempre que executar o cmdlet no mesmo modelo com o mesmo ficheiro de parâmetro, terá o mesmo resultado.

## <a name="summary"></a>Resumo
Em DevOps, a repetível e a previsibilidade são chaves para qualquer implementação bem sucedida de uma aplicação de alta escala composta por microserviços. Neste tutorial, implementou uma aplicação de dois microserviços para o Azure como um único grupo de recursos utilizando o modelo do Gestor de Recursos Azure. Esperemos que tenha lhe dado o conhecimento de que precisa para começar a converter a sua aplicação em Azure num modelo e pode fortí-la e implantá-la previsivelmente. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Linguagem do modelo do gestor de recursos azure](../azure-resource-manager/templates/template-syntax.md)
* [Autor de modelos de gestor de recursos azure](../azure-resource-manager/templates/template-syntax.md)
* [Funções de modelo de gestor de recursos azure](../azure-resource-manager/templates/template-functions.md)
* [Implementar uma aplicação com o modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Implantações de grupos de recursos de resolução de problemas em Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Passos seguintes

Para conhecer a sintaxe da JSON e propriedades para tipos de recursos implantados neste artigo, consulte:

* [Microsoft.Sql/servidores](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servidores/bases de dados](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servidores/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)