---
title: Implementar aplicativos previsivelmente com a ARM
description: Aprenda a implementar várias aplicações do Azure App Service como uma única unidade e de forma previsível usando modelos de Gestão de Recursos Azure e scripts PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 6c45d2da8658740b5e5e7e3dceb7478ea28d712c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962031"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Provisão e implantação de microserviços previsivelmente em Azure
Este tutorial mostra como providenciar e implementar uma aplicação composta por [microserviços](https://en.wikipedia.org/wiki/Microservices) no [Azure App Service](https://azure.microsoft.com/services/app-service/) como uma única unidade e de forma previsível usando modelos de grupo de recursos JSON e scripts PowerShell. 

Quando o provisionamento e a implantação de aplicações de alta escala que são compostas por microserviços altamente dissociados, a repetibilidade e a previsibilidade são cruciais para o sucesso. [O Azure App Service](https://azure.microsoft.com/services/app-service/) permite criar microserviços que incluem aplicações web, back ends móveis e aplicações API. [O Azure Resource Manager](../azure-resource-manager/management/overview.md) permite-lhe gerir todos os microserviços como unidade, juntamente com dependências de recursos, tais como bases de dados e definições de controlo de fontes. Agora, também pode implementar tal aplicação usando modelos JSON e scripts simples PowerShell. 

## <a name="what-you-will-do"></a>O que vai fazer
No tutorial, irá implementar uma aplicação que inclui:

* Duas aplicações do Serviço de Aplicações (ou seja, dois microserviços)
* Uma base de dados SQL de backend
* Definições de aplicativos, cadeias de ligação e controlo de origem
* Insights de aplicações, alertas, definições de autoscalagem

## <a name="tools-you-will-use"></a>Ferramentas que vai usar
Neste tutorial, utilizará as seguintes ferramentas. Como não é uma discussão abrangente sobre ferramentas, vou manter-me no cenário de ponta a ponta e dar-te uma breve introdução a cada um, e onde podes encontrar mais informações sobre isso. 

### <a name="azure-resource-manager-templates-json"></a>Modelos de Gestor de Recursos Azure (JSON)
Sempre que cria uma aplicação no Azure App Service, por exemplo, o Azure Resource Manager utiliza um modelo JSON para criar todo o grupo de recursos com os recursos componentes. Um modelo complexo do [Azure Marketplace](../marketplace/index.yml) pode incluir a base de dados, contas de armazenamento, o plano de Serviço de Aplicações, a própria app, regras de alerta, configurações de aplicações, configurações de autoescalação e muito mais, e todos estes modelos estão disponíveis para você através do PowerShell. Para obter mais informações sobre os modelos do Gestor de Recursos Azure, consulte [modelos de gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 para Visual Studio
O mais recente SDK contém melhorias no suporte do modelo do Gestor de Recursos no editor JSON. Pode usá-lo para criar rapidamente um modelo de grupo de recursos a partir do zero ou abrir um modelo JSON existente (como um modelo de galeria descarregado) para modificação, preencher o ficheiro de parâmetros e até mesmo implementar o grupo de recursos diretamente a partir de uma solução do Grupo de Recursos Azure.

Para mais informações, consulte [Azure SDK 2.6 para o Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 ou mais tarde
A partir da versão 0.8.0, a instalação Azure PowerShell inclui o módulo Azure Resource Manager para além do módulo Azure. Este novo módulo permite-lhe scripts a implementação de grupos de recursos.

Para obter mais informações, consulte [utilizar a Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Esta [ferramenta de pré-visualização](https://resources.azure.com) permite-lhe explorar as definições JSON de todos os grupos de recursos na sua subscrição e nos recursos individuais. Na ferramenta, pode editar as definições JSON de um recurso, eliminar toda uma hierarquia de recursos e criar novos recursos.  As informações prontamente disponíveis nesta ferramenta são muito úteis para a autoria do modelo porque mostra-lhe quais as propriedades que precisa de definir para um determinado tipo de recurso, os valores corretos, etc. Pode até criar o seu grupo de recursos no [Portal Azure,](https://portal.azure.com/)depois inspecione as definições de JSON na ferramenta exploradora para o ajudar a terpluário do grupo de recursos.

### <a name="deploy-to-azure-button"></a>Botão Implementar no Azure
Se utilizar o GitHub para controlo de origem, pode colocar um [botão Implementar para Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) na sua README. MD, que permite uma colocação de chave de turno uI para Azure. Embora possa fazê-lo para qualquer aplicação simples, pode estender isto para permitir a implementação de um grupo inteiro de recursos, colocando uma azuredeploy.jsem ficheiros na raiz do repositório. Este ficheiro JSON, que contém o modelo de grupo de recursos, será utilizado pelo botão Implementar para Azure para criar o grupo de recursos. Por exemplo, consulte a amostra [do ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) que utilizará neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obtenha o modelo do grupo de recursos de amostra
Então, agora vamos ao que é.

1. Navegue para a amostra do Serviço de Aplicações [ToDoApp.](https://github.com/azure-appservice-samples/ToDoApp)
2. Em readme.md, clique **em Implementar para Azure**.
3. É levado para o local [de implantação](https://deploy.azure.com) e azul e pediu para inserir parâmetros de implementação. Note que a maioria dos campos são povoados com o nome do repositório e algumas cordas aleatórias para si. Pode alterar todos os campos se quiser, mas as únicas coisas que tem de introduzir são o login administrativo do SQL Server e a palavra-passe, e depois clique em **Seguinte**.
   
   ![Mostra os parâmetros de implantação da entrada no local de implantação-azul.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **Implementar** para iniciar o processo de implementação. Assim que o processo terminar, clique na http://todoapp ligação *XXXX*.azurewebsites.net para navegar na aplicação implementada. 
   
   ![Mostra o processo de implementação da sua aplicação.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A UI seria um pouco lenta quando se navega pela primeira vez porque as aplicações estão apenas a começar, mas convença-se de que se trata de uma aplicação totalmente funcional.
5. De volta à página Implementar, clique no link **Gerir** para ver a nova aplicação no Portal Azure.
6. No **dropdown Essentials,** clique na ligação do grupo de recursos. Note também que a aplicação já está ligada ao repositório GitHub no âmbito **do Projeto Externo.** 
   
   ![Mostra a ligação do grupo De Recursos na secção Dedesemesta Essentials.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Na lâmina do grupo de recursos, note que já existem duas apps e uma Base de Dados SQL no grupo de recursos.
   
   ![Mostra os recursos disponíveis no seu grupo de recursos.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que acabou de ver em poucos minutos é uma aplicação de dois microserviços totalmente implantada, com todos os componentes, dependências, configurações, base de dados e publicação contínua, criada por uma orquestração automatizada em Azure Resource Manager. Tudo isto foi feito por duas coisas:

* O botão Implementar para Azure
* azuredeploy.jsna raiz do repo

Pode implementar esta mesma aplicação dezenas, centenas ou milhares de vezes e ter sempre a mesma configuração. A repetibilidade e a previsibilidade desta abordagem permitem-lhe implementar aplicações de alta escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examinar (ou editar) AZUREDEPLOY.JSON
Agora vamos ver como o repositório do GitHub foi criado. Vai utilizar o editor JSON no Azure .NET SDK, por isso, se ainda não instalou [o Azure .NET SDK 2.6,](https://azure.microsoft.com/downloads/)faça-o agora.

1. Clone o repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) usando a sua ferramenta de git favorita. Na imagem abaixo, estou a fazer isto no Team Explorer no Visual Studio 2013.
   
   ![Mostra como usar uma ferramenta de git para clonar o repositório ToDoApp.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. A partir da raiz do repositório, abra azuredeploy.jsno Estúdio Visual. Se não vir o painel de contorno json, tem de instalar o Azure .NET SDK.
   
   ![Mostra o painel de contorno json no Estúdio Visual.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não vou descrever todos os detalhes do formato JSON, mas a secção [Mais Recursos](#resources) tem ligações para aprender a linguagem do modelo de grupo de recursos. Aqui, vou apenas mostrar-lhe as funcionalidades interessantes que podem ajudá-lo a começar a fazer o seu próprio modelo personalizado para implementação de aplicações.

### <a name="parameters"></a>Parâmetros
Veja a secção de parâmetros para ver se a maioria destes parâmetros são o que o botão **Implementar para Azure** lhe pede para inserir. O site por trás do botão **Implementar para Azul** povoa a UI de entrada utilizando os parâmetros definidos em azuredeploy.jsligados. Estes parâmetros são usados ao longo das definições de recursos, tais como nomes de recursos, valores de propriedade, etc.

### <a name="resources"></a>Recursos
No nó de recursos, pode ver que são definidos 4 recursos de alto nível, incluindo uma instância sql Server, um plano de Serviço de Aplicações e duas aplicações. 

#### <a name="app-service-plan"></a>Plano do Serviço de Aplicações
Vamos começar com um simples recurso de nível de raiz no JSON. No Contorno JSON, clique no plano de Serviço de Aplicações denominado **[hospedagem Do Dia do Planeta]** para realçar o código JSON correspondente. 

![Mostra a secção [hostingPlanName] do código JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Note que o `type` elemento especifica a cadeia para um plano de Serviço de Aplicações (foi chamado de fazenda de servidor há muito, muito tempo), e outros elementos e propriedades são preenchidos usando os parâmetros definidos no ficheiro JSON, e este recurso não tem quaisquer recursos aninhados.

> [!NOTE]
> Note também que o valor dos `apiVersion` tells Azure com que versão da API REST para utilizar a definição de recurso JSON, e pode afetar a forma como o recurso deve ser formatado dentro do `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso SQL Server chamado **SQLServer** no Contorno JSON.

![Mostra o recurso SQL Server chamado SQLServer no Contorno JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Note o seguinte sobre o código JSON realçado:

* A utilização de parâmetros garante que os recursos criados são nomeados e configurados de uma forma que os torna consistentes uns com os outros.
* O recurso SQLServer tem dois recursos aninhados, cada um tem um valor diferente para `type` .
* Os recursos aninhados no interior `“resources”: […]` , onde a base de dados e as regras de firewall são definidas, têm um elemento que especifica o `dependsOn` ID de recurso do recurso SQLServer de nível de raiz. Isto diz ao Azure Resource Manager, "antes de criar este recurso, que outros recursos já devem existir; e se esse outro recurso for definido no modelo, então crie esse primeiro".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como utilizar a `resourceId()` função, consulte as [funções do modelo do Gestor de Recursos Azure](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* O efeito do elemento é que o Gestor de `dependsOn` Recursos Azure pode saber quais os recursos que podem ser criados em paralelo e quais os recursos que devem ser criados sequencialmente. 

#### <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Agora, vamos passar para as próprias aplicações, que são mais complicadas. Clique na aplicação [variáveis('apiSiteName')] no Contorno JSON para destacar o seu código JSON. Vais reparar que as coisas estão a ficar muito mais interessantes. Para este fim, falarei sobre as características, uma a uma:

##### <a name="root-resource"></a>Recurso de raiz
A aplicação depende de dois recursos diferentes. Isto significa que o Azure Resource Manager só criará a aplicação depois de criado o plano de Serviço de Aplicações e a instância do SQL Server.

![Mostra as dependências da aplicação no plano de Serviço de Aplicações e na instância do SQL Server.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Definições da aplicação
As definições da aplicação também são definidas como um recurso aninhado.

![Mostra as definições de aplicação definidas como um recurso aninhado no código JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No `properties` elemento para `config/appsettings` , tem duas configurações de aplicações no formato `"<name>" : "<value>"` .

* `PROJECT` é uma [definição kudu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que diz à implementação do Azure qual projeto usar numa solução de Estúdio Visual multi-projecto. Vou mostrar-lhe mais tarde como o controlo de origem está configurado, mas como o código ToDoApp está numa solução de Estúdio Visual multi-projecto, precisamos desta configuração.
* `clientUrl` é simplesmente uma definição de aplicação que o código de aplicação utiliza.

##### <a name="connection-strings"></a>Cadeias de ligação
As cordas de ligação também são definidas como um recurso aninhado.

![Mostra como as cadeias de ligação são definidas como um recurso aninhado no código JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No `properties` elemento para `config/connectionstrings` , cada fio de ligação também é definido como um nome:par de valor, com o formato específico de `"<name>" : {"value": "…", "type": "…"}` . Para o `type` elemento, os valores possíveis `MySql` `SQLServer` `SQLAzure` são, `Custom` e.

> [!TIP]
> Para uma lista definitiva dos tipos de cadeias de ligação, executar o seguinte comando em Azure PowerShell: \[ Enum]::GetNames ("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controlo de código fonte
As definições de controlo de origem também são definidas como um recurso aninhado. O Azure Resource Manager utiliza este recurso para configurar a publicação contínua (ver ressalva `IsManualIntegration` mais tarde) e também para iniciar a implementação do código de aplicação automaticamente durante o processamento do ficheiro JSON.

![Mostra como as definições de controlo de origem são definidas como um recurso aninhado no código JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` e `branch` deve ser bastante intuitivo e deve apontar para o repositório git e o nome da filial para publicar de. Mais uma vez, estes são definidos por parâmetros de entrada. 

Note no `dependsOn` elemento que, além do próprio recurso da app, `sourcecontrols/web` também depende e `config/appsettings` `config/connectionstrings` . Isto porque uma vez `sourcecontrols/web` configurado, o processo de implementação do Azure tentará automaticamente implementar, construir e iniciar o código de aplicação. Por isso, inserir esta dependência ajuda-o a certificar-se de que a aplicação tem acesso às definições de aplicação e cadeias de ligação necessárias antes de o código de aplicação ser executado. 

> [!NOTE]
> Nota também que `IsManualIntegration` está definido para `true` . Esta propriedade é necessária neste tutorial porque você não é realmente dono do repositório GitHub, e assim não pode realmente conceder permissão à Azure para configurar a publicação contínua do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (ou seja, empurrar atualizações automáticas de repositório para Azure). Só pode utilizar o valor predefinido `false` para o repositório especificado se tiver configurado as credenciais gitHub do proprietário no [portal Azure.](https://portal.azure.com/) Por outras palavras, se tiver configurado controlo de origem para GitHub ou BitBucket para qualquer aplicação no [Portal Azure](https://portal.azure.com/) anteriormente, usando as credenciais do seu utilizador, então a Azure irá lembrar-se das credenciais e usá-las sempre que implementar qualquer app do GitHub ou BitBucket no futuro. No entanto, se ainda não o fez, a implementação do modelo JSON falhará quando o Azure Resource Manager tentar configurar as definições de controlo de origem da aplicação, uma vez que não consegue entrar no GitHub ou no BitBucket com as credenciais do proprietário do repositório.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Compare o modelo JSON com o grupo de recursos implantado
Aqui podes ver todas as lâminas da aplicação no [Portal Azure](https://portal.azure.com/), mas há outra ferramenta que é igualmente útil, se não mais. Aceda à ferramenta de pré-visualização do [Azure Resource Explorer,](https://resources.azure.com) que lhe dá uma representação JSON de todos os grupos de recursos nas suas subscrições, uma vez que existem na parte de trás do Azure. Também pode ver como a hierarquia JSON do grupo de recursos em Azure corresponde à hierarquia no ficheiro que é usado para criá-lo.

Por exemplo, quando vou à ferramenta [Azure Resource Explorer](https://resources.azure.com) e expando os nós no explorador, posso ver o grupo de recursos e os recursos de nível de raiz que são recolhidos sob os respetivos tipos de recursos.

![Consulte o grupo de recursos e os recursos de nível de raiz na ferramenta expandida do Azure Resources Explorer.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se perfurar para uma aplicação, deverá ser capaz de ver detalhes de configuração de aplicações semelhantes ao screenshot abaixo:

![Faça um exercício para ver os detalhes da configuração na aplicação.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Mais uma vez, os recursos aninhados devem ter uma hierarquia muito semelhante à do seu ficheiro de modelo JSON, e deve ver as definições da aplicação, cordas de conexão, etc., devidamente refletidas no painel JSON. A ausência de configurações aqui pode indicar um problema com o seu ficheiro JSON e pode ajudá-lo a resolver problemas no seu ficheiro de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implemente o modelo de grupo de recursos
O botão **Implementar para Azure** é ótimo, mas permite-lhe implementar o modelo de grupo de recursos em azuredeploy.jsapenas se já tiver pressionado azuredeploy.jspara o GitHub. O Azure .NET SDK também fornece as ferramentas para que possa implementar qualquer ficheiro de modelo JSON diretamente da sua máquina local. Para isso, siga os passos abaixo:

1. No Estúdio Visual, clique em **File**  >  **New**  >  **Project**.
2. Clique **em Visual C#**  >  **Cloud**  >  **Azure Resource Group**e, em seguida, clique em **OK**.
   
   ![Criar um novo projeto como Grupo de Recursos Azure no Azure .NET SDK.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. No **modelo de Azure select,** selecione **O Modelo em Branco** e clique em **OK**.
4. Arraste azuredeploy.jspara a pasta **Modelo** do seu novo projeto.
   
   ![Mostra o resultado de arrastar a azuredeploy.jsno ficheiro para a pasta Modelo do seu projeto.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A partir do Solution Explorer, abra a azuredeploy.jscopiada.
6. Apenas para o bem da demonstração, vamos adicionar alguns recursos padrão de Insight de Aplicação ao nosso ficheiro JSON, clicando em **Adicionar Recurso**. Se estiver apenas interessado em implantar o ficheiro JSON, salte para as escadas de implantação.
   
   ![Mostra o botão 'Adicionar Recursos' que pode utilizar para adicionar os recursos padrão de Insight de Aplicação ao seu ficheiro JSON.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Insights de Aplicações para Aplicações Web**, em seguida, certifique-se de que um plano e aplicação de Serviço de Aplicações existentes é selecionado e, em seguida, clique em **Adicionar**.
   
   ![Mostra a seleção de Insights de Aplicações para Aplicações Web, Nome, Plano de Serviço de Aplicações e Web App.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Agora poderá ver vários novos recursos que, dependendo do recurso e do que faz, têm dependências quer do plano do Serviço de Aplicações, quer da app. Estes recursos não são ativados pela definição existente e tu vais mudar isso.
   
   ![Veja os novos recursos que têm dependências do plano ou app do Serviço de Aplicações.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. No Contorno JSON, clique em **appInsights AutoScale** para destacar o seu código JSON. Esta é a configuração de escala para o seu plano de Serviço de Aplicações.
9. No código JSON realçado, localize as `location` propriedades e `enabled` desatresse-as como mostrado abaixo.
   
   ![Mostra a localização e as propriedades ativadas na appInsights Código JSON autoScale e os valores a que deve defini-las.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. No Contorno JSON, clique em **app CPUHighInsights** para destacar o seu código JSON. Isto é um alerta.
11. Localize as `location` propriedades e `isEnabled` coloque-as como mostrado abaixo. Faça o mesmo para os outros três alertas (lâmpadas roxas).
    
    ![Mostra a localização e as propriedades isEnabled na app CPUHighInsights código JSON e os valores a que deve defini-las.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Está pronto para partir. Clique com o botão direito no projeto e selecione **Implementar**  >  **Nova Implementação**.
    
    ![Mostra como implementar o seu novo projeto.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Inicie sessão na sua conta Azure se ainda não o fez.
14. Selecione um grupo de recursos existente na sua subscrição ou crie um novo, selecioneazuredeploy.jse, em seguida, clique ** em** **Editar Parâmetros**.
    
    ![Mostra como editar os parâmetros do azuredeploy.jsno ficheiro.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora poderá editar todos os parâmetros definidos no ficheiro do modelo numa mesa agradável. Os parâmetros que definem os padrão já terão os seus valores padrão, e os parâmetros que definem uma lista de valores permitidos serão mostrados como recuos.
    
    ![Mostra parâmetros que definem uma lista de valores permitidos como listas de abandono.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha todos os parâmetros vazios e use o [endereço de repo DoHub GitHub para o ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) no **repoUrl**. Em seguida, clique em **Guardar**.
    
    ![Mostra os parâmetros recentemente preenchidos para o azuredeploy.jsarquivado.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > A autoscalagem é uma funcionalidade oferecida no nível **Standard** ou superior, e os alertas de nível de plano são funcionalidades oferecidas em nível **básico** ou superior, terá de definir o parâmetro **sku** para **Standard** ou **Premium** para ver todos os seus novos recursos app Insights iluminados.
    > 
    > 
16. Clique em **Implementar**. Se selecionar **Guarde as palavras-passe,** a palavra-passe será guardada no ficheiro do parâmetro **em texto simples**. Caso contrário, será solicitado que introduza a palavra-passe da base de dados durante o processo de implementação.

Já está! Agora basta ir ao [Portal Azure](https://portal.azure.com/) e à ferramenta [Azure Resource Explorer](https://resources.azure.com) para ver os novos alertas e definições de autoescala adicionadas à aplicação implementada pelo JSON.

Os seus passos nesta secção realizaram principalmente os seguintes:

1. Preparou o arquivo do modelo
2. Criei um ficheiro de parâmetro para acompanhar o ficheiro do modelo
3. Implementou o ficheiro do modelo com o ficheiro de parâmetro

O último passo é facilmente feito por um cmdlet PowerShell. Para ver o que o Visual Studio fez quando implementou a sua aplicação, abra Scripts\Deploy-AzureResourceGroup.ps1. Há muito código lá, mas vou apenas realçar todo o código pertinente que precisa para implementar o ficheiro de modelo com o ficheiro de parâmetros.

![Mostra o código pertinente no script que precisa de ser utilizado para implantar o ficheiro do modelo com o ficheiro de parâmetro.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup` é aquele que realmente realiza a ação. Tudo isto deve demonstrar-lhe que, com a ajuda de ferramentas, é relativamente simples implementar a sua aplicação em nuvem previsivelmente. Sempre que executar o cmdlet no mesmo modelo com o mesmo ficheiro de parâmetro, terá o mesmo resultado.

## <a name="summary"></a>Resumo
Em DevOps, a repetibilidade e a previsibilidade são chaves para qualquer implementação bem sucedida de uma aplicação de alta escala composta por microserviços. Neste tutorial, implementou uma aplicação de dois microserviços para o Azure como um único grupo de recursos utilizando o modelo Azure Resource Manager. Esperemos que lhe tenha dado o conhecimento de que precisa para começar a converter a sua aplicação em Azure num modelo e possa provisivelmente prosciê-la e implantá-la de forma previsível. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Linguagem do modelo do gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)
* [Modelos de gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)
* [Funções do modelo do gestor de recursos Azure](../azure-resource-manager/templates/template-functions.md)
* [Implementar uma aplicação com o modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)
* [Resolução de problemas de recursos de recursos implantações em Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Passos seguintes

Para conhecer a sintaxe JSON e as propriedades para os tipos de recursos implantados neste artigo, consulte:

* [Microsoft.Sql/servidores](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servidores/bases de dados](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servidores/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoescalações](/azure/templates/microsoft.insights/autoscalesettings)