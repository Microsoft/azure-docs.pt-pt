---
title: Configurar ambientes de encenação
description: Aprenda a implementar aplicativos para uma ranhura não-produção e autoswap em produção. Aumente a fiabilidade e elimine o tempo de inatividade da aplicação das implementações.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b12b85a2248d7709066ba3218327e0a5d52a0192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962167"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de teste no Serviço de Aplicações do Azure
<a name="Overview"></a>

Quando implementar a sua aplicação web, web app no Linux, back end móvel ou app API para [o Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)pode utilizar uma faixa de implementação separada em vez da ranhura de produção padrão quando estiver a executar o nível de plano de aplicação **standard,** **premium**ou **isolado.** As slots de implementação são aplicações ao vivo com os seus próprios nomes de anfitrião. Os elementos de conteúdo e configurações da aplicação podem ser trocados entre duas ranhuras de implantação, incluindo a ranhura de produção. 

A implementação da sua aplicação numa faixa horária não-produção tem os seguintes benefícios:

* Pode validar alterações de aplicações numa ranhura de implementação de encenação antes de a trocar com a ranhura de produção.
* A implementação de uma aplicação para uma ranhura primeiro e a sua troca em produção garante que todas as instâncias da ranhura são aquecidas antes de serem trocadas para a produção. Isto elimina o tempo de inatividade quando implementa a sua aplicação. A reorientação do tráfego é perfeita, e nenhum pedido é retirado por causa de operações de troca. Pode automatizar todo este fluxo de trabalho configurando [o swap automático](#Auto-Swap) quando não é necessária validação de pré-troca.
* Depois de uma troca, a ranhura com app previamente encenada tem agora a aplicação de produção anterior. Se as alterações trocadas na ranhura de produção não forem como espera, pode efetuar a mesma troca imediatamente para recuperar o seu "último bom site conhecido".

Cada nível de plano de serviço de aplicações suporta um número diferente de slots de implementação. Não há nenhuma taxa adicional para usar slots de implantação. Para saber o número de slots suporta os suportes de nível da sua aplicação, consulte [os limites do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Para escalar a sua aplicação para um nível diferente, certifique-se de que o nível alvo suporta o número de slots que a sua aplicação já utiliza. Por exemplo, se a sua aplicação tiver mais de cinco slots, não pode escaloná-la para o nível **Standard,** porque o nível **Standard** suporta apenas cinco slots de implementação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um bloco
A aplicação deve estar a ser executada no nível **Standard,** **Premium**ou **Isolado** para poder ativar várias faixas de implantação.


1. no [portal Azure,](https://portal.azure.com/)procure e selecione **Serviços de Aplicações** e selecione a sua aplicação. 
   
    ![Pesquisa de Serviços de Aplicações](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. No painel esquerdo, selecione **slots de implementação**  >  **Adicionar Ranhura**.
   
    ![Adicionar um novo bloco de implementação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se a aplicação ainda não se realizar no nível **Standard,** **Premium**ou **Isolado,** recebe uma mensagem que indica os níveis suportados para permitir a publicação encenada. Neste momento, tem a opção de selecionar **Upgrade** e ir ao separador **Escala** da sua aplicação antes de continuar.
   > 

3. Na caixa de diálogo **de ranhura,** dê um nome à ranhura e selecione se clone uma configuração de aplicação a partir de outra ranhura de implementação. **Selecione Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Pode clonar uma configuração a partir de qualquer ranhura existente. As definições que podem ser clonadas incluem configurações de aplicações, cadeias de conexão, versões de estrutura linguística, tomadas web, versão HTTP e bitness de plataforma.

4. Depois de adicionar a ranhura, selecione **Perto** para fechar a caixa de diálogo. A nova ranhura é agora mostrada na página **de slots de implementação.** Por predefinição, **o tráfego %** está definido para 0 para a nova ranhura, com todo o tráfego de clientes encaminhado para a ranhura de produção.

5. Selecione a nova ranhura de implementação para abrir a página de recursos da ranhura.
   
    ![Título de slot de implementação](./media/web-sites-staged-publishing/StagingTitle.png)

    A ranhura de encenação tem uma página de gestão como qualquer outra aplicação do Serviço de Aplicações. Pode alterar a configuração da ranhura. Para lembrá-lo que está a visualizar a ranhura de implementação, o nome da aplicação é mostrado como **\<app-name>/\<slot-name>** , e o tipo de aplicação é App Service **(Slot)**. Também pode ver a ranhura como uma aplicação separada no seu grupo de recursos, com as mesmas designações.

6. Selecione o URL da aplicação na página de recursos da ranhura. A slot de implementação tem o seu próprio nome de anfitrião e é também uma aplicação ao vivo. Para limitar o acesso do público à ranhura de implementação, consulte as [restrições IP do Serviço de Aplicações Azure](app-service-ip-restrictions.md).

A nova ranhura de implementação não tem conteúdo, mesmo que clone as definições de uma ranhura diferente. Por exemplo, pode [publicar nesta slot com o Git.](./deploy-local-git.md) Você pode implantar para a ranhura a partir de um ramo de repositório diferente ou um repositório diferente.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma troca

### <a name="swap-operation-steps"></a>Trocar etapas de operação

Quando troca duas ranhuras (normalmente de uma ranhura de paragem para a ranhura de produção), o Serviço de Aplicações faz o seguinte para garantir que a ranhura do alvo não experimenta tempo de inatividade:

1. Aplicar as seguintes definições da ranhura-alvo (por exemplo, a ranhura de produção) a todas as instâncias da ranhura de origem: 
    - Definições de aplicações [específicas para slots](#which-settings-are-swapped) e cadeias de ligação, se aplicável.
    - [Definições de implementação contínua,](deploy-continuous-deployment.md) se ativadas.
    - [Definições de autenticação do Serviço de Aplicações,](overview-authentication-authorization.md) se ativadas.
    
    Qualquer um destes casos desencadeia todas as instâncias na ranhura de origem para reiniciar. Durante [a troca com pré-visualização,](#Multi-Phase)isto marca o fim da primeira fase. A operação de troca é interrompida e pode validar que a ranhura de origem funciona corretamente com as definições da ranhura do alvo.

1. Aguarde por todas as instâncias da ranhura de origem para completar o seu reinício. Se alguma instância não reiniciar, a operação de troca reverte todas as alterações na ranhura de origem e interrompe o funcionamento.

1. Se [a cache local](overview-local-cache.md) estiver ativada, desencadeie a inicialização da cache local fazendo um pedido HTTP à raiz da aplicação ("/") em cada instância da ranhura de origem. Aguarde até que cada instância retorne qualquer resposta HTTP. A inicialização da cache local provoca um novo recomeço em cada instância.

1. Se [a troca automática](#Auto-Swap) estiver ativada com aquecimento [personalizado,](#Warm-up)acionar o Início da [Aplicação](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) fazendo um pedido HTTP à raiz da aplicação ("/") em cada instância da ranhura de origem.

    Se `applicationInitialization` não for especificado, desencadeie um pedido HTTP para a raiz de aplicação da ranhura de origem em cada instância. 
    
    Se um caso devolver qualquer resposta HTTP, é considerado como aquecido.

1. Se todas as instâncias da ranhura de origem forem aquecidas com sucesso, troque as duas ranhuras trocando as regras de encaminhamento para as duas ranhuras. Após este passo, a ranhura-alvo (por exemplo, a ranhura de produção) tem a aplicação que já foi previamente aquecida na ranhura de origem.

1. Agora que a ranhura de origem possui a aplicação de pré-troca anteriormente na ranhura alvo, execute a mesma operação aplicando todas as definições e reiniciando as instâncias.

Em qualquer ponto da operação de troca, todo o trabalho de inicialização das aplicações trocadas acontece na ranhura de origem. A ranhura do alvo permanece on-line enquanto a ranhura de origem está a ser preparada e aquecida, independentemente de onde a troca tenha sucesso ou falhe. Para trocar uma ranhura de encenação com a ranhura de produção, certifique-se de que a ranhura de produção é sempre a ranhura-alvo. Desta forma, a operação de troca não afeta a sua aplicação de produção.

### <a name="which-settings-are-swapped"></a>Que configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma definição de aplicação ou uma cadeia de ligação para se colar a uma ranhura específica (não trocada), aceda à página **de Configuração** para essa ranhura. Adicione ou edite uma definição e, em seguida, selecione **a definição de ranhura de implementação**. A seleção desta caixa de verificação diz ao Serviço de Aplicações que a definição não é permutável. 

![Definição de slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Trocar duas ranhuras 
Pode trocar slots de implementação na página de **slots** de implementação da sua aplicação e na página **'Vista Geral'.** Para obter detalhes técnicos sobre a permuta de slot, consulte [o que acontece durante a troca](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar uma aplicação de uma ranhura de implantação para a produção, certifique-se de que a produção é o seu slot alvo e que todas as definições na ranhura de origem estão configuradas exatamente como você quer ter em produção.
> 
> 

Para trocar slots de implementação:

1. Aceda à página de slots de **implementação** da sua aplicação e selecione **Swap**.
   
    ![Botão de troca](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **Swap** mostra as definições nas faixas de origem e alvo selecionadas que serão alteradas.

2. Selecione as ranhuras **de Origem** e **Destino** desejadas. Normalmente, o alvo é a ranhura de produção. Além disso, selecione os separadores **'Alterações de Origem'** e **alterações de destino** e verifique se as alterações de configuração são esperadas. Quando terminar, pode trocar as ranhuras imediatamente selecionando **Swap**.

    ![Concluir a troca](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como a sua ranhura-alvo funcionaria com as novas definições antes de a troca realmente acontecer, não selecione **Swap**, mas siga as instruções em [Swap com pré-visualização](#Multi-Phase).

3. Quando terminar, feche a caixa de diálogo selecionando **Close**.

Se tiver algum problema, consulte [as trocas de resolução de problemas.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com pré-visualização (troca de várias fases)

Antes de trocar para a produção como a ranhura alvo, valide que a aplicação funciona com as definições trocadas. A ranhura de origem também é aquecida antes da conclusão do swap, o que é desejável para aplicações críticas da missão.

Quando executa uma troca com pré-visualização, o Serviço de Aplicações executa a mesma [operação de troca,](#AboutConfiguration) mas faz uma pausa após o primeiro passo. Em seguida, pode verificar o resultado da ranhura de paragem antes de concluir a troca. 

Se cancelar a troca, o Serviço de Aplicações reaplica elementos de configuração para a ranhura de origem.

Para trocar com pré-visualização:

1. Siga os passos nas [ranhuras de implementação swap,](#Swap) mas selecione **'Executar swap' com pré-visualização**.

    ![Trocar com pré-visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra-lhe como a configuração na ranhura de origem muda na fase 1 e como a origem e a ranhura do alvo mudam na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Start Swap**.

    Quando a fase 1 terminar, é notificado na caixa de diálogo. Pré-visualizar a troca na ranhura de origem indo para `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Quando estiver pronto para concluir a troca pendente, selecione **Complete Swap** in **Swap action** e selecione Complete **Swap**.

    Para cancelar uma troca pendente, selecione **Cancelar Swap.**

4. Quando terminar, feche a caixa de diálogo selecionando **Close**.

Se tiver algum problema, consulte [as trocas de resolução de problemas.](#troubleshoot-swaps)

Para automatizar uma troca em várias fases, consulte [Automatismo com PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverta uma troca
Se ocorrerem erros na ranhura-alvo (por exemplo, a ranhura de produção) após uma troca de ranhuras, restaure as ranhuras nos seus estados de pré-troca troca trocando imediatamente as mesmas duas ranhuras.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> A troca automática não é suportada em aplicações web no Linux.

A troca automática simplifica os cenários do Azure DevOps onde pretende implementar a sua aplicação continuamente com zero arranques frios e zero tempo de inatividade para os clientes da app. Quando a troca automática é ativada a partir de uma ranhura para a produção, sempre que empurra as alterações de código para essa ranhura, o Serviço de Aplicações troca automaticamente [a aplicação em produção](#swap-operation-steps) depois de ser aquecida na ranhura de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para a ranhura de produção, considere testar a troca automática numa ranhura de alvo não produtivo.
   > 

Para configurar a troca automática:

1. Aceda à página de recursos da sua aplicação. Selecione **configurações de**  >  *\<desired source slot>*  >  **configuração**  >  **configuração configurações gerais**.
   
2. Para **troca automática ativada**, selecione **On**. Em seguida, selecione a ranhura de destino desejada para **a ranhura de implementação de troca automática**e selecione **Guardar** na barra de comando. 
   
    ![Seleções para configurar a troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um impulso de código para a ranhura de origem. A troca automática ocorre após um curto período de tempo e a atualização é refletida no URL da sua ranhura alvo.

Se tiver algum problema, consulte [as trocas de resolução de problemas.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especifique o aquecimento personalizado

Algumas aplicações podem necessitar de ações de aquecimento personalizadas antes da troca. O `applicationInitialization` elemento de configuração no web.config permite especificar as ações de inicialização personalizadas. A [operação de troca](#AboutConfiguration) aguarda que este aquecimento personalizado termine antes de trocar com a ranhura do alvo. Aqui está uma amostra web.config fragmento.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Para obter mais informações sobre a personalização do `applicationInitialization` elemento, consulte [as falhas de troca de slot de implementação mais comuns e como corrigi-las](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Também pode personalizar o comportamento de aquecimento com uma ou ambas as [seguintes definições de aplicações:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para o ping para aquecer o seu site. Adicione esta definição de aplicação especificando um caminho personalizado que começa com um corte como o valor. Um exemplo é `/statuscheck`. O valor predefinido é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para o funcionamento do aquecimento. Adicione esta definição de aplicação com uma lista separada por vírgula de códigos HTTP. Um exemplo `200,202` é. Se o código de estado devolvido não estiver na lista, as operações de aquecimento e troca são interrompidas. Por predefinição, todos os códigos de resposta são válidos.

> [!NOTE]
> O `<applicationInitialization>` elemento de configuração faz parte de cada arranque de aplicações, enquanto as duas configurações de aplicações de comportamento de aquecimento aplicam-se apenas a trocas de slots.

Se tiver algum problema, consulte [as trocas de resolução de problemas.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Monitorizar uma troca

Se a [operação de troca](#AboutConfiguration) demorar muito tempo a ser concluída, poderá obter informações sobre a operação de troca no registo de [atividades](../azure-monitor/platform/platform-logs-overview.md).

Na página de recursos da sua aplicação no portal, no painel esquerdo, selecione **Registo de Atividade**.

Uma operação de troca aparece na consulta de registo como `Swap Web App Slots` . Pode expandi-lo e selecionar uma das suboperações ou erros para ver os detalhes.

## <a name="route-traffic"></a>Tráfego de rotas

Por padrão, todos os pedidos do cliente para o URL de produção da aplicação `http://<app_name>.azurewebsites.net` são encaminhados para a ranhura de produção. Pode encaminhar uma parte do tráfego para outra ranhura. Esta funcionalidade é útil se precisar de feedback do utilizador para uma nova atualização, mas não está pronto para a lançar para a produção.

### <a name="route-production-traffic-automatically"></a>Tráfego de produção de rotas automaticamente

Para encaminhar automaticamente o tráfego de produção:

1. Vá à página de recursos da sua aplicação e selecione **slots de implementação**.

2. Na coluna **Traffic %** da faixa horária para a qual pretende encaminhar, especifique uma percentagem (entre 0 e 100) para representar a quantidade total de tráfego que pretende encaminhar. Selecione **Guardar**.

    ![Definição de uma percentagem de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Após a definição ser guardada, a percentagem especificada de clientes é encaminhada aleatoriamente para a ranhura de não produção. 

Depois de um cliente ser automaticamente encaminhado para uma faixa específica, é "fixado" para essa ranhura durante a vida dessa sessão de clientes. No navegador cliente, pode ver a ranhura a que a sua sessão está fixada, olhando para o `x-ms-routing-name` cookie nos seus cabeçalhos HTTP. Um pedido que é encaminhado para a ranhura de "encenação" tem o cookie `x-ms-routing-name=staging` . Um pedido que é encaminhado para a ranhura de produção tem o `x-ms-routing-name=self` cookie.

   > [!NOTE]
   > Junto ao portal Azure, também pode utilizar o [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) comando no CLI Azure para definir as percentagens de encaminhamento de ferramentas CI/CD como os oleodutos DevOps ou outros sistemas de automação.
   > 

### <a name="route-production-traffic-manually"></a>Tráfego de produção de rotas manualmente

Além do encaminhamento automático de tráfego, o Serviço de Aplicações pode encaminhar os pedidos para uma faixa horária específica. Isto é útil quando pretende que os seus utilizadores possam optar ou optar pela sua aplicação beta. Para encaminhar o tráfego de produção manualmente, utilize o `x-ms-routing-name` parâmetro de consulta.

Para permitir que os utilizadores optem pela sua aplicação beta, por exemplo, pode colocar este link na sua página web:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia `x-ms-routing-name=self` especifica a ranhura de produção. Depois de o navegador cliente aceder ao link, é redirecionado para a ranhura de produção. Cada pedido subsequente tem o `x-ms-routing-name=self` cookie que fixa a sessão para a ranhura de produção.

Para permitir que os utilizadores optem pela sua aplicação beta, desaprova o mesmo parâmetro de consulta para o nome da ranhura de não produção. Eis um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por predefinição, são dadas novas ranhuras de `0%` , mostrada em cinza. Quando definir explicitamente este valor `0%` para (mostrado em texto preto), os seus utilizadores podem aceder manualmente à ranhura de preparação utilizando o `x-ms-routing-name` parâmetro de consulta. Mas não serão encaminhados automaticamente para a ranhura porque a percentagem de encaminhamento está definida para 0. Este é um cenário avançado onde podes "esconder" a tua ranhura de encenação do público, ao mesmo tempo que permite que as equipas internas testem alterações na ranhura.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Eliminar uma ranhura

Procure e selecione a sua aplicação. Selecione **Slots**  >  *\<slot to delete>*  >  **Desaguisamento Geral**. O tipo de aplicação é mostrado como **Serviço de Aplicações (Slot)** para lembrá-lo que está a visualizar uma ranhura de implementação. **Selecione Excluir** na barra de comando.  

![Eliminar uma ranhura de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell é um módulo que fornece cmdlets para gerir o Azure através do Windows PowerShell, incluindo suporte para gerir slots de implementação no Azure App Service.

Para obter informações sobre a instalação e configuração do Azure PowerShell e sobre a autenticação do Azure PowerShell com a sua subscrição Azure, consulte [Como instalar e configurar o Microsoft Azure PowerShell](/powershell/azure/).  

---
### <a name="create-a-web-app"></a>Criar uma aplicação Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Criar uma ranhura
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Iniciar um swap com uma pré-visualização (troca de várias fases) e aplicar a configuração do slot de destino na ranhura de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancelar uma permuta pendente (trocar com revisão) e restaurar a configuração da ranhura de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Troca de blocos de implementação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorize eventos de troca no registo de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Eliminar uma ranhura
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatize com modelos de Gestor de Recursos

[Os modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) são ficheiros JSON declarativos utilizados para automatizar a implementação e configuração dos recursos Azure. Para trocar slots utilizando modelos de Gestor de Recursos, irá definir duas propriedades no *Microsoft.Web/sites/slots* e nos recursos *Microsoft.Web/sites:*

- `buildVersion`: trata-se de uma propriedade de cordas que representa a versão atual da aplicação implantada na ranhura. Por exemplo: "v1", "1.0.0.1", ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: esta é uma propriedade de cordas que especifica o que `buildVersion` a ranhura deve ter. Se o targetBuildVersion não for igual à `buildVersion` corrente, isto irá desencadear a operação de troca encontrando a ranhura que tem a especificada `buildVersion` .

### <a name="example-resource-manager-template"></a>Modelo de gestor de recursos de exemplo

O modelo seguinte do Gestor de Recursos atualizará `buildVersion` a ranhura de paragem e definirá `targetBuildVersion` a ranhura de produção. Isto vai trocar as duas ranhuras. O modelo assume que já tem um webapp criado com uma ranhura chamada "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Este modelo de Gestor de Recursos é idempotente, o que significa que pode ser executado repetidamente e produzir o mesmo estado das ranhuras. Após a primeira execução, `targetBuildVersion` corresponderá à `buildVersion` corrente, para que uma troca não seja desencadeada.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatize com o CLI

Para os comandos [Azure CLI](https://github.com/Azure/azure-cli) para slots de implementação, consulte [a ranhura de implementação do webapp az](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Trocas de resolução de problemas

Se ocorrer algum erro durante uma [permuta](#AboutConfiguration)de ranhuras, é registado no *D:\home\LogFiles\eventlog.xml*. Também está registado no registo de erros específico da aplicação.

Aqui estão alguns erros de troca comuns:

- Um pedido HTTP para a raiz da aplicação é cronometrado. A operação de troca aguarda 90 segundos por cada pedido HTTP e retrição até 5 vezes. Se todas as recauchuções forem cronometradas, a operação de troca é interrompida.

- A inicialização de cache local pode falhar quando o conteúdo da aplicação exceder a quota de disco local especificada para a cache local. Para mais informações, consulte [a visão geral da cache local.](overview-local-cache.md)

- Durante o [aquecimento personalizado,](#Warm-up)os pedidos HTTP são feitos internamente (sem passar pelo URL externo). Podem falhar com certas regras de reescrita de URL em *Web.config*. Por exemplo, as regras para redirecionar nomes de domínio ou fazer cumprir HTTPS podem impedir que os pedidos de aquecimento cheguem ao código da aplicação. Para contornar esta questão, modifique as suas regras de reescrita adicionando as seguintes duas condições:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de reescrita de URL ainda podem bloquear pedidos HTTP. Para contornar esta questão, modifique as suas regras de reescrita adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Após trocas de slot, a aplicação pode experimentar recomeços inesperados. Isto porque depois de uma troca, a configuração de ligação do nome anfitrião sai de sincronização, o que por si só não causa recomeços. No entanto, certos eventos de armazenamento subjacentes (tais como falhas no volume de armazenamento) podem detetar estas discrepâncias e forçar todos os processos dos trabalhadores a reiniciar. Para minimizar este tipo de reinícios, defina a definição da [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` aplicação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) em *todas as ranhuras*. No entanto, esta configuração da aplicação *não* funciona com aplicações da Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Passos seguintes
[Bloquear o acesso a faixas horárias não produção](app-service-ip-restrictions.md)