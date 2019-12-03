---
title: Implantar aplicativos de modo previsível com o ARM
description: Saiba como implantar vários aplicativos de serviço de Azure App como uma única unidade e de forma previsível usando os modelos de gerenciamento de recursos do Azure e o script do PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9ec3a6b39a857f888514b0a3872ae411e1819f3a
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671814"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Provisionar e implantar os microserviços previsíveis no Azure
Este tutorial mostra como provisionar e implantar um aplicativo composto por [microservices](https://en.wikipedia.org/wiki/Microservices) no [serviço Azure app](https://azure.microsoft.com/services/app-service/) como uma única unidade e de forma previsível usando modelos de grupo de recursos JSON e scripts do PowerShell. 

Ao provisionar e implantar aplicativos de grande escala que são compostos de microserviços altamente desacoplados, a capacidade de repetição e a previsibilidade são cruciais para o sucesso. [Azure app serviço](https://azure.microsoft.com/services/app-service/) permite que você crie microserviços que incluem aplicativos Web, back-ends móveis e aplicativos de API. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite que você gerencie todos os microserviços como uma unidade, juntamente com as dependências de recursos, como configurações de controle do código-fonte e banco de dados. Agora, você também pode implantar esse aplicativo usando modelos JSON e scripts simples do PowerShell. 

## <a name="what-you-will-do"></a>O que você fará
No tutorial, você implantará um aplicativo que inclui:

* Dois aplicativos do serviço de aplicativo (ou seja, dois microserviços)
* Um banco de dados SQL de back-end
* Configurações do aplicativo, cadeias de conexão e controle do código-fonte
* Application insights, alertas, configurações de dimensionamento automático

## <a name="tools-you-will-use"></a>Ferramentas que serão usadas
Neste tutorial, você usará as ferramentas a seguir. Como não se trata de uma discussão abrangente sobre ferramentas, vou até o cenário de ponta a ponta e simplesmente lhe dá uma breve introdução a cada um deles e onde você pode encontrar mais informações sobre ele. 

### <a name="azure-resource-manager-templates-json"></a>Modelos de Azure Resource Manager (JSON)
Sempre que você cria um aplicativo no serviço Azure App, por exemplo, Azure Resource Manager usa um modelo JSON para criar o grupo de recursos inteiro com os recursos do componente. Um modelo complexo do [Azure Marketplace](/azure/marketplace) pode incluir o banco de dados, as contas de armazenamento, o plano do serviço de aplicativo, o próprio aplicativo, as regras de alerta, as configurações do aplicativo, as configurações de dimensionamento automático e muito mais, e todos esses modelos estão disponíveis para você por meio do PowerShell. Para obter mais informações sobre os modelos de Azure Resource Manager, consulte [criando modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>SDK do Azure 2,6 para Visual Studio
O SDK mais recente contém aprimoramentos no suporte ao modelo do Resource Manager no editor de JSON. Você pode usar isso para criar rapidamente um modelo de grupo de recursos do zero ou abrir um modelo JSON existente (como um modelo de galeria baixado) para modificação, preencher o arquivo de parâmetros e até mesmo implantar o grupo de recursos diretamente de um recurso do Azure Solução de grupo.

Para obter mais informações, consulte [Azure SDK 2,6 para Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 ou posterior
A partir da versão 0.8.0, a instalação do Azure PowerShell inclui o módulo Azure Resource Manager além do módulo do Azure. Esse novo módulo permite que você scripte a implantação de grupos de recursos.

Para obter mais informações, consulte [usando Azure PowerShell com Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Essa [ferramenta de visualização](https://resources.azure.com) permite que você explore as definições de JSON de todos os grupos de recursos em sua assinatura e os recursos individuais. Na ferramenta, você pode editar as definições de JSON de um recurso, excluir uma hierarquia inteira de recursos e criar novos recursos.  As informações prontamente disponíveis nesta ferramenta são muito úteis para a criação de modelos, pois mostram quais propriedades você precisa definir para um determinado tipo de recurso, os valores corretos, etc. Você pode até mesmo criar seu grupo de recursos no [portal do Azure](https://portal.azure.com/)e, em seguida, inspecionar suas definições de JSON na ferramenta do Explorer para ajudá-lo a modelar o grupo de recursos.

### <a name="deploy-to-azure-button"></a>Botão implantar no Azure
Se você usar o GitHub para controle do código-fonte, poderá colocar um [botão implantar no Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) no seu Leiame. MD, que habilita uma interface do usuário de implantação de chave para o Azure. Embora seja possível fazer isso para qualquer aplicativo simples, você pode estendê-lo para habilitar a implantação de um grupo de recursos inteiro colocando um arquivo azuredeploy. JSON na raiz do repositório. Esse arquivo JSON, que contém o modelo de grupo de recursos, será usado pelo botão implantar no Azure para criar o grupo de recursos. Para obter um exemplo, consulte o exemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que será usado neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obter o modelo de grupo de recursos de exemplo
Agora, vamos direto para ele.

1. Navegue até o exemplo de serviço de aplicativo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. Em readme.md, clique em **implantar no Azure**.
3. Você é levado para o site [implantar para o Azure](https://deploy.azure.com) e solicitou a entrada de parâmetros de implantação. Observe que a maioria dos campos é populada com o nome do repositório e algumas cadeias de caracteres aleatórias para você. Você pode alterar todos os campos, se desejar, mas as únicas coisas que você precisa inserir são o SQL Server logon administrativo e a senha, em seguida, clique em **Avançar**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **implantar** para iniciar o processo de implantação. Depois que o processo for concluído, clique no link http://todoapp*xxxx*. azurewebsites.net para procurar o aplicativo implantado. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A interface do usuário seria um pouco lenta quando você navega pela primeira vez para ela porque os aplicativos estão apenas começando, mas convencem que é um aplicativo totalmente funcional.
5. De volta à página implantar, clique no link **gerenciar** para ver o novo aplicativo no portal do Azure.
6. No menu suspenso **Essentials** , clique no link do grupo de recursos. Observe também que o aplicativo já está conectado ao repositório GitHub em **projeto externo**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Na folha do grupo de recursos, observe que já existem dois aplicativos e um banco de dados SQL no grupo de recursos.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que você acabou de ver em poucos minutos é um aplicativo de dois microserviço totalmente implantado, com todos os componentes, dependências, configurações, banco de dados e publicação contínua, configurados por uma orquestração automatizada no Azure Resource Manager. Tudo isso foi feito por duas coisas:

* O botão implantar no Azure
* azuredeploy. JSON na raiz do repositório

Você pode implantar esse mesmo aplicativo de dezenas, centenas ou milhares de vezes e ter exatamente a mesma configuração a cada vez. A capacidade de repetição e a previsibilidade dessa abordagem permitem que você implante aplicativos de grande escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examine (ou edite) AZUREDEPLOY. JSON
Agora vamos ver como o repositório GitHub foi configurado. Você usará o editor de JSON no SDK do .NET do Azure, portanto, se você ainda não tiver instalado o [SDK do .net do azure 2,6](https://azure.microsoft.com/downloads/), faça isso agora.

1. Clone o repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) usando sua ferramenta de git favorita. Na captura de tela abaixo, estou fazendo isso na Team Explorer em Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Na raiz do repositório, abra azuredeploy. JSON no Visual Studio. Se você não vir o painel estrutura de tópicos JSON, precisará instalar o SDK do .NET do Azure.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não vou descrever todos os detalhes do formato JSON, mas a seção [mais recursos](#resources) tem links para aprender a linguagem de modelo do grupo de recursos. Aqui, vou apenas mostrar os recursos interessantes que podem ajudá-lo a começar a criar seu próprio modelo personalizado para a implantação de aplicativos.

### <a name="parameters"></a>Parâmetros
Dê uma olhada na seção de parâmetros para ver que a maioria desses parâmetros é o que o botão **implantar no Azure** solicita que você insira. O site por trás do botão **implantar no Azure** popula a interface do usuário de entrada usando os parâmetros definidos em azuredeploy. JSON. Esses parâmetros são usados em todas as definições de recursos, como nomes de recursos, valores de propriedade, etc.

### <a name="resources"></a>Recursos
No nó recursos, você pode ver que 4 recursos de nível superior são definidos, incluindo uma instância SQL Server, um plano do serviço de aplicativo e dois aplicativos. 

#### <a name="app-service-plan"></a>Plano do App Service
Vamos começar com um recurso de nível raiz simples no JSON. Na estrutura de tópicos JSON, clique no plano do serviço de aplicativo denominado **[hostingPlanName]** para realçar o código JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observe que o elemento `type` especifica a cadeia de caracteres para um plano do serviço de aplicativo (ele era chamado de tempo demorado de um farm de servidores) e outros elementos e propriedades são preenchidos usando os parâmetros definidos no arquivo JSON, e esse recurso não tem recursos aninhados.

> [!NOTE]
> Observe também que o valor de `apiVersion` informa ao Azure a qual versão da API REST usar a definição de recurso JSON e pode afetar como o recurso deve ser formatado dentro do `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso SQL Server chamado **SqlServer** na estrutura de tópicos JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observe o seguinte sobre o código JSON realçado:

* O uso de parâmetros garante que os recursos criados sejam nomeados e configurados de forma a torná-los consistentes entre si.
* O recurso SQLServer tem dois recursos aninhados, cada um tem um valor diferente para `type`.
* Os recursos aninhados dentro de `“resources”: […]`, em que o banco de dados e as regras de firewall são definidos, têm um elemento `dependsOn` que especifica a ID de recurso do recurso SQLServer de nível raiz. Isso informa Azure Resource Manager "antes de criar esse recurso, esse outro recurso já deve existir; e se esse outro recurso estiver definido no modelo, crie-o primeiro ".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como usar a função `resourceId()`, consulte [Azure Resource Manager funções de modelo](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* O efeito do elemento `dependsOn` é que Azure Resource Manager pode saber quais recursos podem ser criados em paralelo e quais recursos devem ser criados sequencialmente. 

#### <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Agora, vamos passar para os próprios aplicativos propriamente ditos, que são mais complicados. Clique no aplicativo [Variables (' apiSiteName ')] na estrutura de tópicos JSON para realçar seu código JSON. Você perceberá que as coisas estão ficando muito mais interessantes. Para essa finalidade, falarei sobre os recursos um a um:

##### <a name="root-resource"></a>Recurso raiz
O aplicativo depende de dois recursos diferentes. Isso significa que Azure Resource Manager criará o aplicativo somente depois que o plano do serviço de aplicativo e a instância de SQL Server forem criados.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Configurações do aplicativo
As configurações do aplicativo também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No elemento `properties` para `config/appsettings`, você tem duas configurações de aplicativo no formato `"<name>" : "<value>"`.

* `PROJECT` é uma [configuração KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que informa à implantação do Azure qual projeto usar em uma solução de vários projetos do Visual Studio. Mostrarei mais tarde como o controle do código-fonte está configurado, mas como o código ToDoApp está em uma solução de vários projetos do Visual Studio, precisamos dessa configuração.
* `clientUrl` é simplesmente uma configuração de aplicativo usada pelo código do aplicativo.

##### <a name="connection-strings"></a>Cadeias de ligação
As cadeias de conexão também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No elemento `properties` para `config/connectionstrings`, cada cadeia de conexão também é definida como um par nome: valor, com o formato específico de `"<name>" : {"value": "…", "type": "…"}`. Para o elemento `type`, os valores possíveis são `MySql`, `SQLServer`, `SQLAzure`e `Custom`.

> [!TIP]
> Para obter uma lista definitiva dos tipos de cadeia de caracteres de conexão, execute o seguinte comando em Azure PowerShell: \[enum]:: GetNames ("Microsoft. WindowsAzure. Commands. Utilities. sites. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Controlo de código fonte
As configurações de controle do código-fonte também são definidas como um recurso aninhado. Azure Resource Manager usa esse recurso para configurar a publicação contínua (consulte a advertência sobre `IsManualIntegration` mais tarde) e também para iniciar a implantação do código do aplicativo automaticamente durante o processamento do arquivo JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` e `branch` devem ser bem intuitivos e devem apontar para o repositório git e o nome da ramificação da qual publicar. Novamente, elas são definidas pelos parâmetros de entrada. 

Observe que, no elemento `dependsOn` que, além do recurso de aplicativo em si, `sourcecontrols/web` também depende `config/appsettings` e `config/connectionstrings`. Isso ocorre porque, uma vez que `sourcecontrols/web` estiver configurado, o processo de implantação do Azure tentará automaticamente implantar, compilar e iniciar o código do aplicativo. Portanto, inserir essa dependência ajuda você a garantir que o aplicativo tenha acesso às configurações de aplicativo necessárias e às cadeias de conexão antes que o código do aplicativo seja executado. 

> [!NOTE]
> Observe também que `IsManualIntegration` está definido como `true`. Essa propriedade é necessária neste tutorial porque você não possui o repositório GitHub de fato e, portanto, não pode realmente conceder permissão ao Azure para configurar a publicação contínua do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (ou seja, enviar atualizações automáticas de repositório para o Azure). Você pode usar o valor padrão `false` para o repositório especificado somente se tiver configurado as credenciais do GitHub do proprietário no [portal do Azure](https://portal.azure.com/) antes. Em outras palavras, se você tiver configurado o controle do código-fonte para o GitHub ou o BitBucket para qualquer aplicativo no [portal do Azure](https://portal.azure.com/) anteriormente, usando suas credenciais de usuário, o Azure lembrará as credenciais e as usará sempre que você implantar qualquer aplicativo do GitHub ou do BitBucket no futuro. No entanto, se você ainda não fez isso, a implantação do modelo JSON falhará quando o Azure Resource Manager tentar definir as configurações de controle do código-fonte do aplicativo porque ele não pode fazer logon no GitHub ou BitBucket com as credenciais do proprietário do repositório.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparar o modelo JSON com o grupo de recursos implantado
Aqui, você pode percorrer todas as folhas do aplicativo no [portal do Azure](https://portal.azure.com/), mas há outra ferramenta que é tão útil, se não mais. Vá para a ferramenta de visualização de [Azure Resource Explorer](https://resources.azure.com) , que fornece uma representação JSON de todos os grupos de recursos em suas assinaturas, pois eles realmente existem no back-end do Azure. Você também pode ver como a hierarquia JSON do grupo de recursos no Azure corresponde à hierarquia no arquivo de modelo usado para criá-la.

Por exemplo, ao ir para a ferramenta de [Azure Resource Explorer](https://resources.azure.com) e expandir os nós no Explorer, posso ver o grupo de recursos e os recursos de nível raiz que são coletados em seus respectivos tipos de recursos.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se você fizer uma busca detalhada em um aplicativo, deverá ser capaz de ver os detalhes de configuração do aplicativo semelhantes à captura de tela abaixo:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Novamente, os recursos aninhados devem ter uma hierarquia muito semelhante àquela em seu arquivo de modelo JSON, e você deve ver as configurações do aplicativo, cadeias de conexão, etc., refletidas corretamente no painel JSON. A ausência de configurações aqui pode indicar um problema com o arquivo JSON e pode ajudá-lo a solucionar problemas de seu arquivo de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implantar o modelo de grupo de recursos por conta própria
O botão **implantar no Azure** é ótimo, mas permite que você implante o modelo de grupo de recursos em azuredeploy. JSON somente se você já enviou azuredeploy. JSON por push para o github. O SDK do .NET do Azure também fornece as ferramentas para você implantar qualquer arquivo de modelo JSON diretamente do seu computador local. Para fazer isso, siga as etapas abaixo:

1. No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.
2. Clique **em C# Visual** > **Cloud** > **grupo de recursos do Azure**e clique em **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Em **selecionar modelo do Azure**, selecione **modelo em branco** e clique em **OK**.
4. Arraste azuredeploy. JSON para a pasta de **modelos** do seu novo projeto.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Em Gerenciador de Soluções, abra o azuredeploy. JSON copiado.
6. Apenas para fins de demonstração, vamos adicionar alguns recursos padrão do Application Insight ao nosso arquivo JSON, clicando em **Adicionar recurso**. Se você estiver apenas interessado em implantar o arquivo JSON, pule para as etapas de implantação.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Application insights para aplicativos Web**e verifique se um plano e aplicativo do serviço de aplicativo existente está selecionado e clique em **Adicionar**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Agora você poderá ver vários novos recursos que, dependendo do recurso e o que ele faz, têm dependências no plano do serviço de aplicativo ou no aplicativo. Esses recursos não são habilitados pela definição existente e você vai alterá-lo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Na estrutura de tópicos JSON, clique em **appInsights autoescala** para realçar seu código JSON. Esta é a configuração de dimensionamento para o plano do serviço de aplicativo.
9. No código JSON realçado, localize as propriedades `location` e `enabled` e defina-as como mostrado abaixo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Na estrutura de tópicos JSON, clique em **CPUHigh appInsights** para realçar seu código JSON. Este é um alerta.
11. Localize as propriedades `location` e `isEnabled` e defina-as conforme mostrado abaixo. Faça o mesmo para os outros três alertas (lâmpadas roxas).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Agora você está pronto para implantar. Clique com o botão direito do mouse no projeto e selecione **implantar** > **nova implantação**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Faça logon em sua conta do Azure, se ainda não tiver feito isso.
14. Selecione um grupo de recursos existente em sua assinatura ou crie um novo, selecione **azuredeploy. JSON**e clique em **Editar parâmetros**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora você poderá editar todos os parâmetros definidos no arquivo de modelo em uma boa tabela. Os parâmetros que definem os padrões já terão seus valores padrão, e os parâmetros que definem uma lista de valores permitidos serão mostrados como listas suspensas.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha todos os parâmetros vazios e use o endereço do [repositório do GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) no **rederramal**. Em seguida, clique em **salvar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > O dimensionamento automático é um recurso oferecido na camada **Standard** ou superior, e os alertas de nível de plano são recursos oferecidos na camada **básica** ou superior. você precisará definir o parâmetro **SKU** como **Standard** ou **Premium** para ver todos os novos recursos do App insights acenderem.
    > 
    > 
16. Clique em **implantar**. Se você selecionou **salvar senhas**, a senha será salva no arquivo de parâmetro **em texto sem formatação**. Caso contrário, será solicitado que você insira a senha do banco de dados durante o processo de implantação.

Já está! Agora você só precisa acessar o [portal do Azure](https://portal.azure.com/) e a ferramenta de [Azure Resource Explorer](https://resources.azure.com) para ver os novos alertas e as configurações de dimensionamento automático adicionados ao seu aplicativo JSON implantado.

As etapas nesta seção realizaram principalmente o seguinte:

1. Preparado o arquivo de modelo
2. Criou um arquivo de parâmetro para ir com o arquivo de modelo
3. O arquivo de modelo foi implantado com o arquivo de parâmetro

A última etapa é facilmente feita por um cmdlet do PowerShell. Para ver o que o Visual Studio fez quando implantou seu aplicativo, abra Scripts\Deploy-AzureResourceGroup.ps1. Há muito código, mas vou apenas destacar todo o código pertinente necessário para implantar o arquivo de modelo com o arquivo de parâmetro.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup`, é aquele que realmente executa a ação. Tudo isso deve demonstrar a você que, com a ajuda das ferramentas, é relativamente simples implantar seu aplicativo em nuvem de modo previsível. Toda vez que você executar o cmdlet no mesmo modelo com o mesmo arquivo de parâmetro, você obterá o mesmo resultado.

## <a name="summary"></a>Resumo
Em DevOps, a capacidade de repetição e a previsibilidade são chaves para qualquer implantação bem-sucedida de um aplicativo de grande escala composto por microservices. Neste tutorial, você implantou um aplicativo de dois microserviço no Azure como um único grupo de recursos usando o modelo de Azure Resource Manager. Espero que você tenha dado o conhecimento de que precisa para começar a converter seu aplicativo no Azure em um modelo e pode provisioná-lo e implantá-lo de modo previsível. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Idioma do modelo de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Criando modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager funções de modelo](../azure-resource-manager/resource-group-template-functions.md)
* [Implantar um aplicativo com Azure Resource Manager modelo](../azure-resource-manager/resource-group-template-deploy.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Solucionando problemas de implantações de grupo de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a sintaxe JSON e as propriedades para tipos de recursos implantados neste artigo, consulte:

* [Microsoft. SQL/Servers](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/Servers/bancos de dados](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/Servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/sites/Slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)