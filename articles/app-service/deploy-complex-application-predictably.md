---
title: Aprovisionar e implementar microsserviços de forma previsível - serviço de aplicações do Azure
description: Saiba como implementar uma aplicação compostas por microsserviços no serviço de aplicações do Azure como uma única unidade e de forma previsível, com modelos de grupo de recursos JSON e scripts do PowerShell.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6d18222e15f62f12592362827b6dbc4a3d7dfbc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820319"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Aprovisionar e implementar microsserviços de forma previsível no Azure
Este tutorial mostra como aprovisionar e implementar um aplicativo composto [microsserviços](https://en.wikipedia.org/wiki/Microservices) na [App Service do Azure](https://azure.microsoft.com/services/app-service/) como uma única unidade e de forma previsível, com modelos de grupo de recursos JSON e Script do PowerShell. 

Quando o aprovisionamento e a implantação de aplicativos de grande escala que são compostas por altamente desacoplada microsserviços, repetibilidade e previsibilidade são cruciais para êxito. [Serviço de aplicações do Azure](https://azure.microsoft.com/services/app-service/) permite-lhe criar microsserviços incluem aplicações web, móveis back-ends e aplicações API. [O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite-lhe gerir todos os microsserviços são como uma unidade, juntamente com as dependências de recursos, tais como a base de dados e definições de controlo de origem. Agora, também pode implementar uma aplicação a utilizar modelos JSON e o script do PowerShell simples. 

## <a name="what-you-will-do"></a>O que fará
O tutorial, irá implementar uma aplicação que inclui:

* Duas aplicações de serviço de aplicações (ou seja, duas microsserviços)
* Um base de dados SQL de back-end
* Definições da aplicação, as cadeias de ligação e o controlo de origem
* O Application insights, alertas, as definições de dimensionamento automático

## <a name="tools-you-will-use"></a>Ferramentas de que irá utilizar
Neste tutorial, irá utilizar as seguintes ferramentas. Uma vez que não é uma discussão abrangente sobre ferramentas, vou escolher o cenário de ponta a ponta e dar a uma breve introdução a cada um, e onde pode encontrar mais informações sobre ele. 

### <a name="azure-resource-manager-templates-json"></a>Modelos do Azure Resource Manager (JSON)
Sempre que cria uma aplicação no App Service do Azure, por exemplo, o Azure Resource Manager utiliza um modelo JSON para criar o grupo de recursos inteiro com os recursos de componente. Um complexo modelo a partir da [do Azure Marketplace](/azure/marketplace) pode incluir a base de dados, contas de armazenamento, o plano do serviço de aplicações, a aplicação em si, regras de alerta, as definições de aplicações, definições de dimensionamento automático e muito mais, e todos os estes modelos estão disponíveis para si através do PowerShell. Para obter mais informações sobre os modelos do Azure Resource Manager, consulte [criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 para Visual Studio
O SDK mais recente contém aprimoramentos para o suporte de modelo do Resource Manager no editor de JSON. Pode usá-lo para rapidamente criar um modelo de grupo de recursos do zero ou abrir um modelo JSON existente (por exemplo, um modelo de galeria transferido) para modificação, preencha o ficheiro de parâmetros e até mesmo implementar o grupo de recursos diretamente a partir de um recurso do Azure Solução de grupo.

Para obter mais informações, consulte [2.6 do SDK do Azure para Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>O Azure PowerShell 0.8.0 ou posterior
Partir da versão 0.8.0, a instalação do PowerShell do Azure inclui o módulo do Azure Resource Manager, além de módulo do Azure. Este novo módulo permite-lhe a implementação de grupos de recursos de script.

Para obter mais informações, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Isso [ferramenta de pré-visualização](https://resources.azure.com) permite-lhe explorar as definições de JSON de todos os grupos de recursos na sua subscrição e os recursos individuais. A ferramenta, pode editar as definições de JSON de um recurso, eliminar uma hierarquia completa de recursos e criar novos recursos.  As informações prontamente disponíveis para esta ferramenta são muito útil para a criação de modelo, porque ele mostra as propriedades que precisa definir para um determinado tipo de recurso, os valores corretos, etc. Pode até mesmo criar grupo de recursos dos [Portal do Azure](https://portal.azure.com/), em seguida, Inspecione as respetivas definições JSON na ferramenta do explorer para o ajudar a templatize o grupo de recursos.

### <a name="deploy-to-azure-button"></a>Implementar no botão do Azure
Se utilizar o GitHub para o controlo de origem, pode colocar um [botão implementar no Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) no seu ficheiro Leia-me. MD, que permite uma implementação de chave na mão da interface do Usuário para o Azure. Embora seja possível fazer isso para qualquer aplicação simple, pode estender esta opção para ativar a implementação de um grupo de recursos inteiro, colocando um ficheiro azuredeploy JSON na raiz do repositório. Este ficheiro JSON, que contém o modelo de grupo de recursos, será utilizado pelo botão implementar no Azure para criar o grupo de recursos. Por exemplo, veja a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exemplo, que irá utilizar neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obter o modelo de grupo de recursos de exemplo
Agora vamos certa para o mesmo.

1. Navegue para o [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exemplo de serviço de aplicações.
2. No readme.md, clique em **implementar no Azure**.
3. É levado para o [implementar no azure](https://deploy.azure.com) do site e a pedido para parâmetros de implementação de entrada. Tenha em atenção que a maioria dos campos é preenchida com o nome do repositório e algumas cadeias de caracteres aleatórias para. Pode alterar todos os campos se desejar, mas as únicas coisas que tem de introduzir o início de sessão administrativo do SQL Server e a palavra-passe, em seguida, clique em **seguinte**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **Deploy** para iniciar o processo de implantação. Depois do processo é executado para conclusão, clique nas http://todoapp *XXXX*. azurewebsites.net ligação para procurar a aplicação implementada. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A interface do Usuário teria de ser um pouco lento quando primeiro navegar para o mesmo porque as aplicações são for iniciada, mas convencer de sua que é um aplicativo totalmente funcional.
5. Na página de implementar, clique nas **gerir** ligação para ver a nova aplicação no Portal do Azure.
6. Na **Essentials** lista pendente, clique na ligação de grupo de recursos. Observe também que a aplicação já está ligada para o repositório do GitHub sob **projeto externa**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. No painel do grupo de recursos, tenha em atenção que já existem duas aplicações e uma base de dados SQL no grupo de recursos.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que acabou de ver alguns minutos é um aplicativo totalmente implementadas duas-microsserviços, com todos os componentes, dependências, definições, base de dados e publicação contínua, iremos configurar por uma orquestração automatizada no Azure Resource Manager. Tudo isso foi feito duas coisas:

* O botão implementar no Azure
* azuredeploy. JSON na raiz do repositório

Pode implementar esta aplicação mesmo dezenas, centenas ou milhares de vezes e de ter exatamente a mesma configuração cada vez. A capacidade de repetição e a previsibilidade dessa abordagem permite-lhe implementar aplicações de grande escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examinar (ou editar) AZUREDEPLOY. JSON
Agora vamos ver como era configurar o repositório do GitHub. Irá utilizar o editor de JSON no SDK de .NET do Azure, por isso, se ainda não instalou [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), fazê-lo agora.

1. Clone o [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) repositório utilizando a sua ferramenta favorita do git. A captura de ecrã abaixo, estou fazendo isso no Team Explorer no Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Na raiz do repositório, abra o azuredeploy. JSON no Visual Studio. Se não vir o painel de contorno de JSON, terá de instalar o SDK .NET do Azure.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não vou descrever todos os detalhes do formato JSON, mas a [mais recursos](#resources) secção tem ligações para a linguagem de modelo de grupo de recursos de aprendizado. Aqui, vou mostrar a recursos interessantes que podem ajudá-lo a começar a utilizar na criação de seu próprio modelo personalizado para a implementação de aplicação.

### <a name="parameters"></a>Parâmetros
Consulte a secção de parâmetros para ver que a maioria desses parâmetros é o que o **implementar no Azure** botão pede-lhe introduzir. O site por trás da **implementar no Azure** botão preenche a interface do Usuário usando os parâmetros definidos no azuredeploy. JSON de entrada. Esses parâmetros são usados em toda a definições de recursos, tais como nomes de recursos, valores de propriedade, etc.

### <a name="resources"></a>Recursos
O nó de recursos, pode ver que recursos de nível superior 4 são definidos, incluindo uma instância do SQL Server, um plano do serviço de aplicações e duas aplicações. 

#### <a name="app-service-plan"></a>Plano do App Service
Vamos começar com um simple recurso de nível de raiz no JSON. Em contorno de JSON, clique no plano de serviço de aplicações com o nome **[hostingPlanName]** para realçar o código JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Tenha em atenção que o `type` elemento Especifica a cadeia de caracteres para um plano de serviço de aplicações (ele era chamado de um farm de servidores de um tempo longo, por extenso atrás) e outros elementos e propriedades são preenchidas com os parâmetros definidos no ficheiro JSON e este recurso não tem qualquer aninhados recursos.

> [!NOTE]
> Observe também que o valor de `apiVersion` informa o Azure qual é a versão de API REST para utilizar a definição do recurso JSON com e pode afetar a como o recurso deve ser formatado dentro do `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso do SQL Server com o nome **SQLServer** no contorno de JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Tenha em atenção o seguinte sobre o código JSON realçado:

* O uso de parâmetros garante que os recursos criados estão com o nome e configurados de forma a que os torna consistente entre si.
* O recurso de SQLServer tem dois recursos aninhados, cada um tem um valor diferente para `type`.
* Os recursos aninhados dentro `“resources”: […]`, onde são definidas na base de dados e as regras de firewall, tem um `dependsOn` elemento que especifica o ID de recurso do recurso de SQLServer em nível de raiz. Isso informa ao Azure Resource Manager, "antes de criar este recurso, o que já deve existir outro recurso; e se esse outro recurso é definido no modelo, em seguida, criam essa primeiro".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como utilizar o `resourceId()` funcionar, consulte [as funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* O efeito do `dependsOn` elemento é que o Azure Resource Manager pode saber quais os recursos que podem ser criados em paralelo e quais recursos têm de ser criados em sequência. 

#### <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Agora, vamos passar para as aplicações reais, o que são mais complicadas. Clique na aplicação [variables('apiSiteName')] no contorno de JSON para realçar o código JSON. Perceberá que as coisas estão ficando muito mais interessantes. Para esse fim, falarei sobre os recursos individualmente:

##### <a name="root-resource"></a>Recurso de raiz
A aplicação depende de dois recursos diferentes. Isso significa que o Azure Resource Manager irá criar a aplicação apenas depois do plano do serviço de aplicações e a instância do SQL Server são criados.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Definições de aplicação
As definições da aplicação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Na `properties` elemento para `config/appsettings`, tem duas definições de aplicações no formato `"<name>" : "<value>"`.

* `PROJECT` é um [definição de KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que indica a implementação do Azure que utilize uma solução do Visual Studio multiprojeto do projeto. Mostrarei posteriormente como o controlo de origem estiver configurado, mas uma vez que o código de ToDoApp está numa solução do Visual Studio multiprojeto, precisamos esta definição.
* `clientUrl` é simplesmente uma aplicação que definir o código do aplicativo usa.

##### <a name="connection-strings"></a>Cadeias de ligação
As cadeias de ligação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Na `properties` elemento para `config/connectionstrings`, cada cadeia de ligação também é definida como um par de valores de nomes, com o formato específico de `"<name>" : {"value": "…", "type": "…"}`. Para o `type` elemento, os valores possíveis são `MySql`, `SQLServer`, `SQLAzure`, e `Custom`.

> [!TIP]
> Para obter uma lista definitiva dos tipos de cadeia de ligação, execute o seguinte comando no Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controlo de origem
As definições de controlo de origem também são definidas como um recurso aninhado. O Azure Resource Manager utiliza este recurso para configurar a publicação contínua (consulte a advertência sobre `IsManualIntegration` mais tarde) e também para iniciar a implementação de código do aplicativo automaticamente durante o processamento do ficheiro JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` e `branch` deve ser bastante intuitiva e deve apontar para o repositório de Git e o nome do ramo para publicar a partir de. Mais uma vez, são definidos pelo parâmetros de entrada. 

Tenha em atenção na `dependsOn` elemento que, além do recurso de aplicação em si, `sourcecontrols/web` também depende `config/appsettings` e `config/connectionstrings`. Isto acontece porque depois `sourcecontrols/web` é configurado, o processo de implementação do Azure irá automaticamente tentar implementar, criar e iniciar o código do aplicativo. Por conseguinte, a inserir esta dependência ajuda-o a certificar-se de que a aplicação tem acesso às definições da aplicação necessária e as cadeias de ligação antes do código do aplicativo é executado. 

> [!NOTE]
> Observe também que `IsManualIntegration` está definido como `true`. Esta propriedade é necessária neste tutorial porque não é, na verdade, o proprietário do repositório do GitHub e, portanto, não é possível realmente conceder permissão para o Azure para configurar a publicação contínua a partir [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (ou seja, push automáticas repositório atualizações para o Azure). Pode utilizar o valor predefinido `false` para o repositório especificado apenas se tiver configurado as credenciais do GitHub do proprietário na [portal do Azure](https://portal.azure.com/) antes. Em outras palavras, se tiver definido o controle de origem para o GitHub ou BitBucket para qualquer aplicação na [Portal do Azure](https://portal.azure.com/) anteriormente, usar o seu utilizador as credenciais, em seguida, o Azure irá lembrar as credenciais e usá-las sempre que implementar qualquer aplicação a partir de GitHub ou BitBucket no futuro. No entanto, se ainda não fez isso, a implementação do modelo JSON falhará quando tentar configurar as definições de controlo de origem da aplicação, porque ele não é possível iniciar sessão no GitHub ou BitBucket com credenciais do proprietário do repositório do Azure Resource Manager.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparar o modelo JSON com o grupo de recursos implementados
Aqui, pode avançar para os painéis de todas as aplicações no [Portal do Azure](https://portal.azure.com/), mas há outra ferramenta que tão útil, se não é muito mais. Vá para o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta de pré-visualização, que dá-lhe uma representação JSON de todos os grupos de recursos nas suas subscrições, que, na verdade, existem no back-end do Azure. Também pode ver como a hierarquia JSON de um grupo de recursos no Azure corresponde com a hierarquia no arquivo de modelo do que é utilizado para criá-lo.

Por exemplo, quando eu for para o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta e expanda os nós no explorer, posso ver o grupo de recursos e os recursos de nível de raiz que são recolhidos em seus tipos respectivos recursos.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se desagregar para uma aplicação, deverá conseguir ver os detalhes de configuração de aplicação semelhantes da captura de ecrã abaixo:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Novamente, os recursos aninhados devem ter uma hierarquia muito semelhante aos em seu arquivo de modelo JSON, e verá as definições da aplicação, as cadeias de ligação, etc., refletida adequadamente no painel de JSON. A ausência de configurações pode indicar um problema com o ficheiro JSON e pode ajudar a resolver o seu ficheiro de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implementar o modelo de grupo de recursos por conta própria
O **implementar no Azure** botão é ótimo, mas permite-lhe implementar o modelo de grupo de recursos em azuredeploy. JSON, apenas se já tiver emitida azuredeploy. JSON para o GitHub. O SDK .NET do Azure também fornece as ferramentas para a implementação de qualquer ficheiro de modelo JSON diretamente a partir do seu computador local. Para tal, siga os passos abaixo:

1. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
2. Clique em **em Visual C#** > **Cloud** > **grupo de recursos do Azure**, em seguida, clique em **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Na **selecionar modelo do Azure**, selecione **modelo em branco** e clique em **OK**.
4. Arraste o azuredeploy. JSON para o **modelo** pasta do seu projeto novo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A partir do Explorador de soluções, abra o azuredeploy. JSON copiado.
6. Apenas para fins de demonstração, vamos adicionar alguns recursos do Application Insight standard ao nosso arquivo JSON, ao clicar em **adicionar recurso**. Se estiver interessado em implementar o ficheiro JSON apenas, avance para os passos de implementação.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Application Insights para aplicações Web**, em seguida, certifique-se de um plano do serviço de aplicações existentes e a aplicação está selecionado e, em seguida, clique em **Add**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Agora poderá ver vários recursos novos que, dependendo do recurso e o que ele faz, têm dependências no plano do App Service ou da aplicação. Estes recursos não estão ativados por sua definição existente e pretender alterá-la.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Em contorno de JSON, clique em **appInsights dimensionamento automático** para realçar o código JSON. Esta é a definição de dimensionamento para o seu plano do serviço de aplicações.
9. No código JSON realçado, localize a `location` e `enabled` propriedades e defini-los, conforme mostrado abaixo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Em contorno de JSON, clique em **CPUHigh appInsights** para realçar o código JSON. Este é um alerta.
11. Localize a `location` e `isEnabled` propriedades e defini-los, conforme mostrado abaixo. Fazer o mesmo para os outros três alertas (lâmpadas roxa).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Agora, está pronto para implementar. Com o botão direito no projeto e selecione **Deploy** > **nova implementação**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Inicie sessão na conta do Azure se ainda não o tiver feito.
14. Selecione um grupo de recursos existente na sua subscrição ou crie um novo, um, selecione **azuredeploy. JSON**e, em seguida, clique em **Editar parâmetros**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora poderá editar todos os parâmetros definidos no ficheiro de modelo numa tabela de bom. Parâmetros que definem os padrões já terão seus valores padrão e parâmetros que definem uma lista de valores permitidos serão mostrados como listas pendentes.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha os parâmetros vazios e utilizar o [endereço de repositório do GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) na **repoUrl**. Em seguida, clique em **guardar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Dimensionamento automático é um recurso oferecido no **padrão** escalão ou superior e ao nível do plano de alertas são recursos oferecidos no **básica** camada ou superior, terá de definir o **sku** parâmetro para **padrão** ou **Premium** para ver todos os seus novos App Insights recursos leves cópia de segurança.
    > 
    > 
16. Clique em **implementar**. Se tiver selecionado **guardar palavras-passe**, a palavra-passe será guardada no ficheiro de parâmetros **em texto sem formatação**. Caso contrário, será solicitado para introduzir a palavra-passe da base de dados durante o processo de implantação.

Já está! Agora precisa apenas Ir para o [Portal do Azure](https://portal.azure.com/) e o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta para ver os novos alertas e as definições de dimensionamento automático adicionadas ao seu JSON implementou a aplicação.

Os passos nesta secção principalmente feito o seguinte:

1. Preparar o ficheiro de modelo
2. Criar um ficheiro de parâmetros com o ficheiro de modelo
3. Implementar o ficheiro de modelo com o ficheiro de parâmetros

A última etapa é feita facilmente por um cmdlet do PowerShell. Para ver o que o Visual Studio fez quando implementada a aplicação, abra Scripts\Deploy AzureResourceGroup.ps1. Há muitos códigos aqui, mas vou apenas para realçar o código de pertinente que necessita para implementar o ficheiro de modelo com o ficheiro de parâmetros.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup`, é o que realmente executa a ação. Tudo isso deve demonstrar a que, com a ajuda de ferramentas, é relativamente simples implementar a aplicação de cloud de forma previsível. Sempre que executar o cmdlet no mesmo modelo com o mesmo ficheiro de parâmetros, vamos obter o mesmo resultado.

## <a name="summary"></a>Resumo
Na programação e operações, repetibilidade e previsibilidade são chaves para qualquer implementação bem-sucedida de uma aplicação de larga escala compostas por microsserviços. Neste tutorial, implementou uma aplicação de duas microsserviços no Azure como um grupo de recursos com o modelo Azure Resource Manager. Espero que ele tenha dado a o conhecimento de que precisa para começar a converter a sua aplicação no Azure num modelo e que pode aprovisionar e implementá-la de forma previsível. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Linguagem de modelo do Resource Manager do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Criação de modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funções de modelo do Resource Manager do Azure](../azure-resource-manager/resource-group-template-functions.md)
* [Implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Resolver problemas de implementações do grupo de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos implementadas neste artigo, consulte:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)