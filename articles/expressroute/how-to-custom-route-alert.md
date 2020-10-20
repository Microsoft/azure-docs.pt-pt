---
title: 'ExpressRoute: Como configurar alertas personalizados para rotas anunciadas'
description: Este artigo mostra-lhe como utilizar a Azure Automation and Logic Apps para monitorizar o número de rotas anunciadas a partir da porta de entrada ExpressRoute para redes no local, de forma a evitar atingir o limite de 200 rotas.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: b812a727a555f8c4c95389f2ba0cc5ccea2f608a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205564"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Configurar alertas personalizados para monitorizar rotas anunciadas

Este artigo ajuda-o a utilizar a Azure Automation and Logic Apps para monitorizar constantemente o número de rotas anunciadas a partir da porta de entrada ExpressRoute para redes no local. A monitorização pode ajudar a evitar atingir o limite de [200 rotas.](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)

**A Azure Automation** permite automatizar a execução do script personalizado PowerShell armazenado num *livro de bordo.* Ao utilizar a configuração deste artigo, o livro contém um script PowerShell que consulta um ou mais gateways ExpressRoute. Recolhe um conjunto de dados contendo o grupo de recursos, o nome de gateway ExpressRoute e o número de prefixos de rede anunciados no local.

**A Azure Logic Apps** programa um fluxo de trabalho personalizado que chama o runbook da Azure Automation. A execução do livro de execução é feita usando um trabalho. Após a recolha de dados, o fluxo de trabalho Azure Logic Apps classifica os dados e, com base em critérios de correspondência no número de prefixos de rede acima ou abaixo de um limiar pré-fino, envia informações para um endereço de e-mail de destino.

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

A criação de um alerta personalizado baseia-se em três passos principais:

1. Criar uma Conta de Automação com uma conta "Executar As" e permissões.

2. Criar e configurar livros de execução.

3. Crie uma aplicação lógica que dispare a Conta Demômótica e envie um e-mail de alerta se o número for maior do que um limiar (por exemplo, 160).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem pelo menos uma porta de entrada ExpressRoute na sua implantação.

* Tem uma compreensão básica da [Run As accounts](../automation/manage-runas-account.md) in Azure Automation.

* Está familiarizado com [as Aplicações Lógicas Azure.](../logic-apps/logic-apps-overview.md)

* Está familiarizado com a utilização do Azure PowerShell. A Azure PowerShell é necessária para recolher os prefixos da rede no gateway ExpressRoute. Para obter mais informações sobre a Azure PowerShell em geral, consulte a [documentação Azure PowerShell](/powershell/azure/?view=azps-4.1.0).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Notas e limitações

* O alerta personalizado discutido neste artigo é um complemento para conseguir um melhor funcionamento e controlo. Não é um substituto para os alertas nativos no ExpressRoute.
* A recolha de dados para gateways ExpressRoute é em segundo plano. O tempo de execução pode ser mais longo do que o esperado. Para evitar a fila de trabalho, o fluxo de trabalho deve ser corretamente configurado.
* As implementações por scripts ou modelos ARM podem acontecer mais rapidamente do que o gatilho de alarme personalizado. Isto poderia resultar num aumento do número de prefixos de rede no gateway ExpressRoute acima do limite de 200 rotas.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Criar e configurar contas

Quando cria uma conta Automation no portal Azure, uma conta [Run As](../automation/automation-security-overview.md#run-as-accounts) é criada automaticamente. Esta conta tem a seguintes ações:

* Cria um pedido de Diretório Ativo Azure (Azure AD) com um certificado auto-assinado. A conta Run As em si tem um certificado que precisa ser renovado por defeito todos os anos.

* Cria uma conta principal de serviço para a aplicação em Azure AD.

* Atribui-se a função contribuinte (RBAC) na Subscrição Azure em uso. Esta função gere os recursos do Azure Resource Manager utilizando livros de execução.

Para criar uma conta Automation, precisa de privilégios e permissões. Para obter informações, consulte [permissões necessárias para criar uma conta Demôm automação.](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Criar uma conta de automação

Criar uma conta De automação com permissões run-as. Para obter instruções, consulte [Criar uma conta Azure Automation](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Atribuir a execução como conta uma função

Por predefinição, a **função Contribuinte** é atribuída ao titular de serviço que é utilizado pela sua conta **Run As.** Pode manter a função predefinida atribuída ao principal de serviço, ou pode restringir permissões atribuindo uma [função incorporada](../role-based-access-control/built-in-roles.md) (por exemplo, Leitor) ou uma [função personalizada](../active-directory/users-groups-roles/roles-create-custom.md).

 Utilize os seguintes passos para determinar a função atribuída ao titular de serviço que é utilizado pela sua conta Run As:

1. Navegue para a sua conta Automation. Navegar para **definições de conta**e, em seguida, selecionar **Executar como contas**.

2. Selecione **Funções** para ver a definição de função que está a ser usada.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Adicionar conta de automação":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Criar e configurar livros de execução

### <a name="1-install-modules"></a><a name="install-modules"></a>1. Instalar módulos

Para executar os cmdlets PowerShell nos runbooks da Azure Automation, é necessário instalar alguns módulos Azure PowerShell Az. Utilize os seguintes passos para instalar os módulos:

1. Abra a sua Conta de Automação Azure e navegue para **Módulos.**

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Adicionar conta de automação":::

2. Pes faça uma pesquisa na Galeria e importe os seguintes **módulos: Az.Accounts**, **Az.Network,** **Az.Automation**e **Az.Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. Criar um livro de recortes

1. Para criar o seu livro de bordo PowerShell, navegue para a sua Conta de Automação. Em **Automatização de Processos,** selecione o **azulejo runbooks** e, em seguida, selecione **Criar um livro de execução**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Adicionar conta de automação":::

2. Selecione **Criar** para criar o livro de execução.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Adicionar conta de automação":::

3. Selecione o livro de execução recém-criado e, em seguida, **selecione Editar**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Adicionar conta de automação":::

4. Em **Editar,** cole o script PowerShell. O [script Exemplo](#script) pode ser modificado e utilizado para monitorizar os gateways ExpressRoute em um ou mais grupos de recursos.

   No roteiro de exemplo, note as seguintes definições:

    * A matriz **$rgList** contém a lista de grupos de recursos com gateways ExpressRoute. Pode personalizar os gateways ExpressRoute baseados na lista.
    * A variável **$thresholdNumRoutes** definir o limiar do número de prefixos de rede anunciados a partir de uma porta de entrada ExpressRoute para as redes no local.

#### <a name="example-script"></a><a name="script"></a>Script de exemplo

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Guarde e publique o livro de recortes

1. **Selecione Guardar** para guardar uma cópia do rascunho do livro de recortes.
2. Selecione **Publicar** para publicar o livro de recortes como a versão oficial do runbook na conta de automação.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Adicionar conta de automação":::

Quando executam o script PowerShell, é recolhida uma lista de valores:
 
* Grupo de recursos

* Nome do gateway ExpressRoute

* Endereço IP do primeiro par BGP (peer1)

* Endereço IP do segundo par BGP (peer2)

* Número de prefixos de rede anunciados a partir da porta de entrada ExpressRoute para o primeiro par BGP (peer1)

* Número de prefixos de rede anunciados a partir da porta de entrada ExpressRoute para o segundo par BGP (peer2)

* Timestamp

* Estado, classificado como:

  * 'OK' se o número de rotas for inferior a um valor limiar
  * 'ALERTA' se o número de rotas se ultrapassar um valor limiar
  * 'AVISO' se o número de prefixos de rede anunciados aos dois pares BGP for diferente

* Mensagem de alerta, para uma descrição verbosa do estado (OK, ALERTA, AVISO)

O script PowerShell converte a informação recolhida numa saída JSON. O livro de bordo utiliza o cmdlet PowerShell [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  como fluxo de saída para comunicar informações ao cliente.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. Validar o livro de recortes

Uma vez criado o livro de bordo, este deve ser validado. Selecione **Iniciar** e verificar a saída e os erros para os diferentes fluxos de trabalho.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Criar e configurar uma aplicação lógica

A Azure Logic Apps é a orquestradora de todo o processo de recolha e ações. Nas secções seguintes, constrói-se um fluxo de trabalho utilizando uma aplicação lógica.

### <a name="workflow"></a>Fluxo de trabalho

Neste fluxo de trabalho, você constrói uma aplicação lógica que monitoriza regularmente os gateways ExpressRoute. Se existirem novos itens, a aplicação lógica envia uma mensagem de e-mail para cada item. Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Adicionar conta de automação":::

### <a name="1-create-a-logic-app"></a>1. Criar uma aplicação lógica

No **designer de aplicativos Logic,** crie uma aplicação lógica utilizando o modelo **de App Blank Logic.** Para etapas, consulte [Create Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Adicionar conta de automação":::

### <a name="2-add-a-trigger"></a>2. Adicione um gatilho

Todas as aplicações lógicas são iniciadas por um gatilho. Um gatilho dispara quando um evento específico acontece, ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Azure Logic Apps cria uma instância lógica de aplicações que inicia e executa o seu fluxo de trabalho.

Para executar regularmente uma aplicação lógica que se baseia num horário pré-fino, adicione o **incorporado Recorrence: Agendar** ao seu fluxo de trabalho. Na caixa de pesquisa, **escreva o horário.** Selecione **Triggers**. Na lista 'Triggers', selecione **Recorrence Schedule**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Adicionar conta de automação":::

No gatilho da Agenda de Recorrência, pode definir o fuso horário e uma recorrência para repetir o fluxo de trabalho. Em conjunto, o intervalo e a frequência definem o agendamento para o acionador da aplicação lógica. Para estabelecer uma frequência mínima de recorrência razoável, considere os seguintes fatores:

* O script PowerShell no livro de formulários Automation leva tempo a ser concluído. O tempo de funcionação depende do número de gateways ExpressRoute para monitorizar. Uma frequência de recorrência demasiado curta causará filas de trabalho.

* O script PowerShell funciona como um trabalho em segundo plano. Não começa imediatamente. corre depois de um atraso variável.

* Uma frequência de recorrência demasiado curta gerará carga não precisa nos seus gateways Azure ExpressRoute.

No final da configuração do fluxo de trabalho, pode verificar a consistência da frequência de recorrência executando o fluxo de trabalho algumas vezes e, em seguida, verificar o resultado na história do **Runs**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. Criar um emprego

Uma aplicação lógica acede a outras aplicações, serviços e à plataforma através de conectores. O próximo passo neste fluxo de trabalho é selecionar um conector para aceder à conta Azure Automation que foi definida anteriormente.

1. In **Logic Apps Designer**, abaixo da **Recorrência,** selecione **Novo passo**. Em **Escolha uma ação** e a caixa de pesquisa, selecione **All**.
2. Na caixa de pesquisa, **digite Azure Automation** e procure. Selecione **Criar trabalho**. **Criar trabalho** será usado para disparar o runbook de automação que foi criado anteriormente.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Adicionar conta de automação":::

3. Inscreva-se usando um diretor de serviço. Pode usar um principal de serviço existente, ou pode criar um novo. Para criar um novo diretor de serviço, consulte [Como usar o portal para criar um diretor de serviço AD Azure que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md) Selecione **Connect com o principal de serviço.**

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Adicionar conta de automação":::

4. Digite um **Nome de Ligação,** adicione o seu **ID do Cliente** (ID de aplicação), Segredo de **Cliente,** e o seu **ID do Inquilino.** Em seguida, selecione **Criar**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Adicionar conta de automação" na Conta **Demôm automação.** Além disso, verifique se adicionou o **Nome runbook** como um novo parâmetro.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. Obter a saída de emprego

1. Selecione **Novo passo**. Procure "Azure Automation". Na lista **de Ações,** selecione **Obter saída de emprego**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Adicionar conta de automação":::

2. Na página de saída de **trabalho Get,** especifique as informações necessárias para aceder à conta de automação. Selecione a **Conta de Subscrição, Recursos**e **Automação** que pretende utilizar. Clique dentro da caixa **de identificação de trabalho.** Quando aparecer a lista **de conteúdos Dynamic,** selecione **Job ID**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. Parse o JSON

A informação contida na saída da "Azure Automation Create job action" (etapas anteriores) gera um objeto JSON. Logic Apps **Parse JSON** é uma ação incorporada para criar fichas fáceis de usar a partir das propriedades e seus valores em conteúdo JSON. Em seguida, pode utilizar essas propriedades no seu fluxo de trabalho.

1. Adicione uma ação. No âmbito da **ação Get job output ->**, selecione Novo **passo**.
2. Na caixa de pesquisa de ação Escolha uma caixa de pesquisa de **ação,** escreva "parse json" para procurar conectores que ofereçam esta ação. Na lista **de Ações,** selecione a ação **Parse JSON** para as operações de dados que pretende utilizar.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Adicionar conta de automação":::

3. Clique dentro da caixa **de conteúdo.** Quando aparecer a lista de conteúdos Dynamic, selecione **Conteúdo**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Analisar um JSON requer um esquema. O esquema pode ser gerado utilizando a saída do livro de bordo da Automação. Abra uma nova sessão de navegador web, execute o runbook Automation e agarre a saída. Voltar à ação **de Operações de Dados da Logic Apps Parse JSON.** Na parte inferior da página, **selecione Utilize a carga útil da amostra para gerar esquema**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Adicionar conta de automação":::

5. Para **introduzir ou colar uma amostra de carga JSON,** cole a saída do livro de saída da Automatização e selecione **Feito**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Um esquema é gerado automaticamente através da análise da carga útil de entrada JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. Definir e inicializar uma variável

Neste passo do fluxo de trabalho, criamos uma condição para enviar um alarme por e-mail. Para uma formatação flexível e personalizada de uma mensagem corporal de e-mail, uma variável auxiliar é introduzida no fluxo de trabalho.

1. No âmbito da ação **de saída de emprego**Get , selecione Novo **passo**. Na caixa de pesquisa, encontre e selecione **Variáveis.**

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Adicionar conta de automação":::

2. Na lista **de Ações,** selecione a ação **variável Initialize.**

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Adicionar conta de automação":::

3. Especificar o nome da variável. Para **tipo**, selecione **String**. O **Valor** da variável será atribuído mais tarde no fluxo de trabalho.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. Criar uma ação "Para cada"

Uma vez analisado o JSON, a ação **Parse JSON Data Operations** armazena o conteúdo na saída *do Corpo.* Para processar a saída, pode criar um laço "Para cada um" repetindo uma ou mais ações em cada item da matriz.

1. Sob **a variável Initialize,** selecione **Adicione uma ação**. Na caixa de pesquisa, escreva "para cada um" como filtro.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Adicionar conta de automação":::

2. Na lista **de Ações,** selecione a ação **Para cada um - Controlo**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Adicionar conta de automação":::

3. Clique na versão Selecionar uma saída da caixa de texto **dos passos anteriores.** Quando aparecer a lista **de conteúdo dinâmico,** selecione o **Corpo**, que é a saída do JSON analisado.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Adicionar conta de automação":::

4. Para cada elemento do corpo JSON, queremos definir uma condição. Do grupo de ação, selecione **Control**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Adicionar conta de automação":::

5. Na lista **de Ações,** selecione **Controlo de Condições.** O Condition-Control é uma estrutura de controlo que compara os dados do seu fluxo de trabalho com valores ou campos específicos. Em seguida, pode especificar diferentes ações que são executadas com base em se os dados cumprem ou não a condição.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Adicionar conta de automação":::

6. Na raiz da **ação da Condição,** altere a operação lógica para **Ou**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Verifique o valor do número de prefixos de rede que um gateway ExpressRoute anuncia aos dois pares BGP. O número de rotas está disponível em "numRoutePeer1" e "numRoutePeer2" em **conteúdo Dinâmico**. Na caixa de valor, digite o valor **para numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="Adicionar conta de automação":::

8. Para adicionar mais uma linha à sua condição, escolha **Adicionar -> Adicionar linha**. Na segunda caixa, a partir de **conteúdo Dinâmico,** selecione **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="Adicionar conta de automação":::

9. A condição lógica é verdadeira quando uma de duas variáveis dinâmicas, numRoute1 ou numRoute2, é maior do que o limiar. Neste exemplo, o limiar é fixado para 160 (80% do valor máximo de 200 rotas). Pode alterar o valor limiar para se adaptar às suas necessidades. Para obter consistência, o valor deve ser o mesmo utilizado no script PowerShell do runbook.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Adicionar conta de automação":::

10. Em **Se for verdade,** formato e criar as ações para enviar o alerta por e-mail. Em **Escolha uma ação, procure e selecione **Variáveis.**

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Adicionar conta de automação":::

11. Em Variáveis, **selecione Adicionar uma ação**. Na lista **de Ações,** selecione **'Definir variável'.**

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Adicionar conta de automação":::

12. Em **Nome**, selecione a variável chamada **EmailBody** que criou anteriormente. Para **Valor**, cole o script HTML necessário para formatar o e-mail de alerta. Utilize o **conteúdo Dinâmico** para incluir os valores do corpo JSON. Depois de configurar estas definições, o resultado é que o **emailbody** variável contém todas as informações relacionadas com o alerta, em formato HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Adicionar conta de automação":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Adicione o conector de e-mail

A Logic Apps fornece muitos conectores de e-mail. Neste exemplo, adicionamos um conector Outlook para enviar o alerta por e-mail. Na **variável set**, selecione **Adicione uma ação**. Em **Escolha uma ação,** escreva "enviar e-mail" na caixa de pesquisa.

1. Selecione **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Adicionar conta de automação":::

2. Na lista **de Ações,** selecione **Enviar por email o artigo V2.**

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Adicionar conta de automação":::

3. Inscreva-se para criar uma ligação ao Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Adicionar conta de automação":::

4. No campo **Corpo,** clique em **Adicionar conteúdo dinâmico.** A partir do painel de conteúdo dinâmico, adicione o **emailcorpo**variável . Preencha o **Assunto** e **Os** Campos.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Adicionar conta de automação":::

5. A **ação Enviar um e-mail (v2)** completa a configuração do fluxo de trabalho.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. Validação do fluxo de trabalho

O passo final é a validação do fluxo de trabalho. No **Visão geral das aplicações lógicas**, selecione **'Executar Trigger'.** Selecione **Recorrência**. O fluxo de trabalho pode ser monitorizado e verificado na história do **Runs**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Adicionar conta de automação":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como personalizar o fluxo de trabalho, consulte [a Azure Logic Apps.](../logic-apps/logic-apps-overview.md)