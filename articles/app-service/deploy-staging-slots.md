---
title: Configurar ambientes de encenação
description: Aprenda a implementar aplicações para uma ranhura de não produção e a swap automático para a produção. Aumente a fiabilidade e elimine o tempo de inatividade da aplicação a partir de implementações.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 11e133a24ff728cc864e50e898e9db982b186337
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597926"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de teste no Serviço de Aplicações do Azure
<a name="Overview"></a>

Quando implementa a sua aplicação web, aplicação web no Linux, back back back móvel ou aplicação API para [o Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)pode utilizar uma ranhura de implementação separada em vez da ranhura de produção padrão quando estiver a funcionar no nível de plano **de**plano standard , **Premium**ou **Isolated** App Service. As ranhuras de implementação são aplicações ao vivo com os seus próprios nomes de anfitriões. Os elementos de conteúdo e configuração da aplicação podem ser trocados entre duas ranhuras de implementação, incluindo a ranhura de produção. 

A implementação da sua aplicação para uma ranhura não produtiva tem os seguintes benefícios:

* Pode validar as alterações de aplicações numa ranhura de implementação de encenação antes de trocá-la com a ranhura de produção.
* A implementação de uma aplicação para uma ranhura primeiro e a sua troca em produção certifica-se de que todos os casos da ranhura são aquecidos antes de serem trocados em produção. Isto elimina o tempo de inatividade quando implementa a sua aplicação. A reorientação do tráfego é perfeita, e nenhum pedido é retirado por causa de operações de permuta. Pode automatizar todo este fluxo de trabalho configurando a troca de [automóveis](#Auto-Swap) quando não é necessária a validação pré-swap.
* Depois de uma troca, a ranhura com aplicação previamente encenada tem agora a app de produção anterior. Se as alterações trocadas na ranhura de produção não forem como espera, pode realizar a mesma troca imediatamente para recuperar o seu "último bom site conhecido".

Cada nível de plano de app service suporta um número diferente de slots de implementação. Não há nenhuma taxa adicional para usar ranhuras de implantação. Para saber o número de slots dos suportes de nível da sua aplicação, consulte [os limites](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)do Serviço de Aplicações . 

Para escalar a sua aplicação para um nível diferente, certifique-se de que o nível de destino suporta o número de slots que a sua aplicação já utiliza. Por exemplo, se a sua aplicação tiver mais de cinco slots, não pode escaloná-la para o nível **Standard,** porque o nível **Standard** suporta apenas cinco slots de implementação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um bloco
A aplicação deve estar a funcionar no nível **Standard**, **Premium**ou **Isolado** para que possa ativar várias ranhuras de implementação.


1. no [portal Azure,](https://portal.azure.com/)procure e selecione **Serviços de Aplicações** e selecione a sua aplicação. 
   
    ![Pesquisa de Serviços de Aplicações](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. No painel esquerdo, selecione **ranhuras** > de implantação**Adicione ranhuras**.
   
    ![Adicionar um novo bloco de implementação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se a aplicação ainda não estiver no nível **Standard**, **Premium,** ou **Isolado,** recebe uma mensagem que indica os níveis suportados para permitir a publicação encenada. Neste ponto, tem a opção de selecionar **upgrade** e ir ao separador **Escala** da sua aplicação antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar,** dar um nome à ranhura e selecionar se clonar uma configuração de aplicação a partir de outra ranhura de implementação. Selecione **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Pode clonar uma configuração a partir de qualquer ranhura existente. As definições que podem ser clonadas incluem definições de aplicativos, cordas de ligação, versões de enquadramento de idioma, tomadas web, versão HTTP e bitness da plataforma.

4. Depois de adicionada a ranhura, selecione **Feche** para fechar a caixa de diálogo. A nova ranhura está agora mostrada na página de **ranhuras de implantação.** Por predefinição, o **tráfego %** está definido para 0 para a nova ranhura, com todo o tráfego do cliente encaminhado para a ranhura de produção.

5. Selecione a nova ranhura de implementação para abrir a página de recursos da ranhura.
   
    ![Título de ranhura de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    A slot de encenação tem uma página de gestão como qualquer outra app do App Service. Pode alterar a configuração da ranhura. Para lembrá-lo que está a visualizar a ranhura de implementação, o nome da aplicação é mostrado como ** \<nome de aplicação>/\<nome de slot>**, e o tipo de aplicação é App Service **(Slot)**. Também pode ver a ranhura como uma aplicação separada no seu grupo de recursos, com as mesmas designações.

6. Selecione o URL da aplicação na página de recursos da ranhura. A ranhura de implementação tem o seu próprio nome de anfitrião e é também uma aplicação ao vivo. Para limitar o acesso do público à ranhura de implantação, consulte as restrições IP do Serviço de [Aplicações Azure](app-service-ip-restrictions.md).

A nova ranhura de implementação não tem conteúdo, mesmo que clone as definições de uma ranhura diferente. Por exemplo, pode [publicar nesta ranhura com Git](app-service-deploy-local-git.md). Você pode implantar para a ranhura a partir de um ramo de repositório diferente ou de um repositório diferente.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma troca

### <a name="swap-operation-steps"></a>Trocar etapas de operação

Quando troca duas ranhuras (geralmente de uma ranhura de preparação para a ranhura de produção), o App Service faz o seguinte para garantir que a ranhura-alvo não experimenta tempo de inatividade:

1. Aplique as seguintes definições a partir da ranhura-alvo (por exemplo, a ranhura de produção) em todas as instâncias da ranhura de origem: 
    - Definições [de aplicativos específicos para slot](#which-settings-are-swapped) e cordas de ligação, se aplicável.
    - [Definições de implantação contínuas,](deploy-continuous-deployment.md) se ativadas.
    - [Definições de autenticação do Serviço de Aplicações,](overview-authentication-authorization.md) se ativadas.
    
    Qualquer um destes casos desencadeia todos os casos na ranhura de origem para reiniciar. Durante [a troca com pré-visualização,](#Multi-Phase)isto marca o fim da primeira fase. A operação de permuta é interrompida e pode validar que a ranhura de origem funciona corretamente com as definições da ranhura do alvo.

1. Aguarde por cada instância na ranhura de origem para completar o seu reinício. Se alguma instância não reiniciar, a operação de permuta reverte todas as alterações à ranhura de origem e para a operação.

1. Se a [cache local](overview-local-cache.md) estiver ativada, desencadeie a inicialização da cache local fazendo um pedido http para a raiz de aplicação ("/") em cada instância da ranhura de origem. Aguarde até que cada instância retorne qualquer resposta HTTP. A inicialização local do cache provoca um novo reinício em cada instância.

1. Se a [troca automática](#Auto-Swap) for ativada com [aquecimento personalizado,](#Warm-up)desencadeie o início da [aplicação](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) fazendo um pedido HTTP para a raiz de aplicação ("/") em cada instância da ranhura de origem.

    Se `applicationInitialization` não for especificado, desencadeie um pedido HTTP para a raiz de aplicação da ranhura de origem em cada instância. 
    
    Se uma instância devolver qualquer resposta HTTP, é considerado como aquecido.

1. Se todas as instâncias na ranhura de origem forem aquecidas com sucesso, troque as duas ranhuras trocando as regras de encaminhamento para as duas ranhuras. Após este passo, a ranhura-alvo (por exemplo, a ranhura de produção) tem a app que já foi aquecida na ranhura de origem.

1. Agora que a ranhura de origem tem a aplicação de pré-permuta anteriormente na ranhura-alvo, execute a mesma operação aplicando todas as definições e reiniciando as instâncias.

Em qualquer ponto da operação de troca, todo o trabalho de inicialização das aplicações trocadas acontece na ranhura de origem. A ranhura-alvo permanece on-line enquanto a ranhura de origem está a ser preparada e aquecida, independentemente de onde a permuta tenha sucesso ou falhe. Para trocar uma ranhura de preparação com a ranhura de produção, certifique-se de que a ranhura de produção é sempre a ranhura-alvo. Desta forma, a operação de swap não afeta a sua aplicação de produção.

### <a name="which-settings-are-swapped"></a>Que configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma definição de aplicação ou uma cadeia de ligação para se agarrar a uma ranhura específica (não trocada), vá à página de **Configuração** para essa ranhura. Adicione ou edite uma definição e, em seguida, selecione **a definição**de ranhura de implementação . A seleção desta caixa de verificação diz ao Serviço de Aplicações que a definição não é permutável. 

![Definição de ranhura](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Troque duas ranhuras 
Pode trocar slots de implementação na página de **slots** de implementação da sua aplicação e na página **'Overview'.** Para obter detalhes técnicos sobre a troca de slot, consulte [o que acontece durante a troca](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar uma aplicação de uma ranhura de implementação em produção, certifique-se de que a produção é o seu slot alvo e que todas as configurações na ranhura de origem estão configuradas exatamente como você quer tê-las em produção.
> 
> 

Para trocar as ranhuras de implantação:

1. Vá à página de ranhuras de **implementação** da sua aplicação e selecione **Swap**.
   
    ![Botão de troca](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **Swap** mostra as definições na fonte selecionada e as ranhuras de destino que serão alteradas.

2. Selecione as ranhuras de **Origem** e **Alvo** desejadas. Normalmente, o alvo é a ranhura de produção. Além disso, selecione os separadores de **Alterações** de Origem e **Alterações** de Destino e verifique se as alterações de configuração são esperadas. Quando terminar, pode trocar imediatamente as ranhuras **selecionando Swap**.

    ![Concluir a troca](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o seu slot de destino funcionaria com as novas definições antes da troca realmente acontecer, não selecione **Swap**, mas siga as instruções em [Troca com pré-visualização](#Multi-Phase).

3. Quando terminar, feche a caixa de diálogo selecionando **Fechar**.

Se tiver algum problema, consulte as trocas de [Troubleshoot.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troque com pré-visualização (troca de várias fases)

Antes de trocar em produção como o slot alvo, valide que a aplicação funciona com as definições trocadas. A ranhura de origem também é aquecida antes da conclusão da permuta, o que é desejável para aplicações críticas de missão.

Quando realiza uma troca com pré-visualização, o Serviço de Aplicações realiza a mesma operação de [swap,](#AboutConfiguration) mas faz pausas após o primeiro passo. Em seguida, pode verificar o resultado da ranhura de preparação antes de concluir a troca. 

Se cancelar a troca, o Serviço de Aplicações reaplica elementos de configuração à ranhura de origem.

Para trocar com pré-visualização:

1. Siga os passos nas ranhuras de [implementação swap,](#Swap) mas selecione **Executar a troca com pré-visualização**.

    ![Troque com pré-visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra-lhe como a configuração da ranhura de origem muda na fase 1 e como a fonte e a ranhura do alvo mudam na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Iniciar a troca**.

    Quando a fase 1 terminar, é notificado na caixa de diálogo. Pré-visualizar a troca na `https://<app_name>-<source-slot-name>.azurewebsites.net`ranhura de origem indo para . 

3. Quando estiver pronto para completar o swap pendente, selecione **'Swap' completo** na **ação Swap** e selecione **'Swap Completo**' .

    Para cancelar uma troca pendente, **selecione Cancelar Trocar** em vez disso.

4. Quando terminar, feche a caixa de diálogo selecionando **Fechar**.

Se tiver algum problema, consulte as trocas de [Troubleshoot.](#troubleshoot-swaps)

Para automatizar uma troca de várias fases, consulte [Automatizar com PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Recue uma troca
Se ocorrerem erros na ranhura-alvo (por exemplo, na ranhura de produção) após uma troca de faixas horárias, restaure as ranhuras para os seus estados de pré-swap trocando imediatamente as mesmas duas ranhuras.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> A troca de automóveis não é suportada em aplicações web no Linux.

A troca de automóveis dinamiza cenários Azure DevOps onde pretende implementar a sua aplicação continuamente com zero arranques a frio e zero tempo de inatividade para os clientes da app. Quando a troca automática é ativada a partir de uma ranhura para a produção, sempre que empurra slot alterações de código para essa ranhura, o Serviço de [Aplicações troca](#swap-operation-steps) automaticamente a app em produção depois de aquecida na ranhura de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para a ranhura de produção, considere testar a troca automática numa ranhura-alvo não produtiva.
   > 

Para configurar a troca automática:

1. Vá à página de recursos da sua aplicação. Selecione >  **ranhuras** > *\<de origem desejadas>*  > **configurações gerais**de **configuração**.
   
2. Para **troca automática ativada,** selecione **On**. Em seguida, selecione a ranhura de destino desejada para a ranhura de implantação de **swap supor automaticamente**e selecione **Guardar** na barra de comando. 
   
    ![Seleções para configurar troca de automóveis](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um impulso de código para a ranhura de origem. A troca de automóveis acontece após um curto período de tempo e a atualização reflete-se no URL da sua ranhura-alvo.

Se tiver algum problema, consulte as trocas de [Troubleshoot.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especificar aquecimento personalizado

Algumas aplicações podem exigir ações de aquecimento personalizadas antes da troca. O `applicationInitialization` elemento de configuração em web.config permite especificar ações de inicialização personalizadas. A [operação de troca](#AboutConfiguration) aguarda que este aquecimento personalizado termine antes de trocar com a ranhura-alvo. Aqui está uma amostra web.config fragmento.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações sobre a personalização do `applicationInitialization` elemento, consulte as falhas mais comuns de troca de [ranhuras de implantação e como corrigi-las](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Também pode personalizar o comportamento de aquecimento com uma ou ambas as [seguintes definições de aplicação:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para o ping para aquecer o seu site. Adicione esta definição de aplicação especificando um caminho personalizado que começa com um corte como o valor. Um exemplo é `/statuscheck`. O valor predefinido é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para o funcionamento do aquecimento. Adicione esta definição de aplicação com uma lista separada de códigos HTTP. Um exemplo `200,202` é. Se o código de estado devolvido não estiver na lista, as operações de aquecimento e troca são interrompidas. Por predefinição, todos os códigos de resposta são válidos.

> [!NOTE]
> O `<applicationInitialization>` elemento de configuração faz parte de cada start-up de cada aplicação, enquanto as duas definições de aplicativos de comportamento de aquecimento aplicam-se apenas a swaps de slot.

Se tiver algum problema, consulte as trocas de [Troubleshoot.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Monitorize uma troca

Se a operação de [swap](#AboutConfiguration) demorar muito tempo a ser concluída, poderá obter informações sobre o funcionamento do swap no registo de [atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos da sua aplicação no portal, no painel esquerdo, selecione **registo de atividade**.

Uma operação de troca aparece na `Swap Web App Slots`consulta de registo como . Pode expandi-lo e selecionar uma das suboperações ou erros para ver os detalhes.

## <a name="route-traffic"></a>Tráfego de rota

Por padrão, todos os pedidos do cliente`http://<app_name>.azurewebsites.net`para o URL de produção da aplicação são encaminhados para a ranhura de produção. Pode saquear uma parte do tráfego para outra ranhura. Esta funcionalidade é útil se precisar de feedback do utilizador para uma nova atualização, mas não está pronto para lançá-la para produção.

### <a name="route-production-traffic-automatically"></a>Tráfego de produção de rotas automaticamente

Para encaminhar automaticamente o tráfego de produção:

1. Vá à página de recursos da sua aplicação e selecione ranhuras de **implementação**.

2. Na coluna **Tráfego %** da ranhura que pretende encaminhar para, especificar uma percentagem (entre 0 e 100) para representar a quantidade total de tráfego que pretende encaminhar. Selecione **Guardar**.

    ![Definição de uma percentagem de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Após a definição ser guardada, a percentagem especificada de clientes é aleatoriamente encaminhada para a ranhura de não produção. 

Depois de um cliente ser automaticamente encaminhado para uma vaga específica, é "fixado" para aquela ranhura para a vida daquela sessão de cliente. No navegador cliente, pode ver qual a ranhura a que `x-ms-routing-name` a sua sessão está fixada olhando para o cookie nos seus cabeçalhos HTTP. Um pedido que é encaminhado para a ranhura de `x-ms-routing-name=staging`"encenação" tem o cookie. Um pedido que é encaminhado para a ranhura `x-ms-routing-name=self`de produção tem o cookie.

   > [!NOTE]
   > Junto ao portal Azure, também [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) pode utilizar o comando no Azure CLI para definir as percentagens de encaminhamento de ferramentas CI/CD, como os gasodutos DevOps ou outros sistemas de automação.
   > 

### <a name="route-production-traffic-manually"></a>Tráfego de produção de rota manualmente

Além do encaminhamento automático de tráfego, o Serviço de Aplicações pode encaminhar pedidos para uma ranhura específica. Isto é útil quando deseja que os seus utilizadores possam optar ou optar pela aplicação beta. Para encaminhar manualmente o `x-ms-routing-name` tráfego de produção, utilize o parâmetro de consulta.

Para que os utilizadores optem por não sair da sua aplicação beta, por exemplo, pode colocar este link na sua página web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A `x-ms-routing-name=self` corda especifica a ranhura de produção. Depois de o navegador cliente aceder ao link, é redirecionado para a ranhura de produção. Cada pedido subsequente `x-ms-routing-name=self` tem o cookie que coloca a sessão na ranhura de produção.

Para permitir que os utilizadores optem pela sua aplicação beta, defina o mesmo parâmetro de consulta para o nome da ranhura de não produção. Segue-se um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, são dadas novas ranhuras com uma regra de encaminhamento de `0%`, mostrada em cinza. Quando define explicitamente `0%` este valor (mostrado em texto preto), os seus utilizadores `x-ms-routing-name` podem aceder manualmente à ranhura de paragem utilizando o parâmetro de consulta. Mas não serão encaminhados automaticamente para a ranhura porque a percentagem de encaminhamento está definida para 0. Este é um cenário avançado onde pode "esconder" a sua ranhura de encenação do público, permitindo que as equipas internas testem alterações na ranhura.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Apagar uma ranhura

Procure e selecione a sua aplicação. Selecione > *\<ranhuras *de **implantação**para eliminar> >  **visão geral**. O tipo de aplicação é mostrado como **App Service (Slot)** para lembrá-lo que está a ver uma ranhura de implementação. **Selecione Excluir** na barra de comando.  

![Eliminar uma ranhura de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerir o Azure através do Windows PowerShell, incluindo suporte para gerir slots de implementação no Serviço de Aplicações Azure.

Para obter informações sobre a instalação e configuração do Azure PowerShell e sobre a autenticação do Azure PowerShell com a sua subscrição Azure, consulte como instalar e configurar o [Microsoft Azure PowerShell](/powershell/azure/overview).  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Iniciar uma troca com uma pré-visualização (permuta de várias fases) e aplicar a configuração da ranhura de destino na ranhura de origem
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

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorizar eventos de permuta no registo de atividade
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Apagar uma ranhura
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizar com modelos de Gestor de Recursos

Os modelos do [Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) são ficheiros JSON declarativos usados para automatizar a implementação e configuração dos recursos Azure. Para trocar slots utilizando modelos de Gestor de Recursos, irá definir duas propriedades nos recursos *Microsoft.Web/sites/slots* e *recursos microsoft.Web/sites:*

- `buildVersion`: trata-se de uma propriedade de cadeia que representa a versão atual da aplicação implantada na ranhura. Por exemplo: "v1", "1.0.0.1", ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: esta é uma propriedade `buildVersion` de cordas que especifica o que a ranhura deve ter. Se o targetBuildVersion não `buildVersion`for igual à corrente, isto irá desencadear a operação `buildVersion`de permuta encontrando a ranhura especificada .

### <a name="example-resource-manager-template"></a>Modelo de gestor de recursos de exemplo

O seguinte modelo de `buildVersion` Gestor de Recursos atualizará `targetBuildVersion` a ranhura de preparação e definirá a ranhura de produção. Isto vai trocar as duas ranhuras. O modelo assume que já tem um webapp criado com uma ranhura chamada "staging".

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

Este modelo de Gestor de Recursos é idempotente, o que significa que pode ser executado repetidamente e produzir o mesmo estado das ranhuras. Após a primeira `targetBuildVersion` execução, `buildVersion`corresponderá à corrente, pelo que não será desencadeada uma troca.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizar com o CLI

Para comandos [Azure CLI](https://github.com/Azure/azure-cli) para ranhuras de implantação, consulte a ranhura de [implementação do webapp az](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Trocas de resolução de problemas

Se ocorrer algum erro durante uma troca de [slot,](#AboutConfiguration)está registado em *D:\home\LogFiles\eventlog.xml*. Também está registado no registo de erros específico da aplicação.

Aqui estão alguns erros comuns de troca:

- Um pedido HTTP para a raiz de aplicação é cronometrado. A operação swap aguarda 90 segundos por cada pedido http, e volta a tentar até 5 vezes. Se todas as tentativas forem cronometradas, a operação de troca é interrompida.

- A inicialização local do cache pode falhar quando o conteúdo da aplicação exceder a quota de disco local especificada para a cache local. Para mais informações, consulte a visão geral do [cache local](overview-local-cache.md).

- Durante o [aquecimento personalizado,](#Warm-up)os pedidos HTTP são feitos internamente (sem passar pelo URL externo). Podem falhar com certas regras de reescrita de URL em *Web.config*. Por exemplo, as regras para redirecionar nomes de domínio ou impor HTTPS podem impedir que os pedidos de aquecimento cheguem ao código da aplicação. Para resolver este problema, modifique as suas regras de reescrita adicionando as seguintes duas condições:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de reescrita de URL ainda podem bloquear pedidos HTTP. Para resolver este problema, modifique as suas regras de reescrita adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Algumas regras de [restrição IP](app-service-ip-restrictions.md) podem impedir que a operação de swap envie pedidos HTTP para a sua aplicação. Os intervalos de endereços `10.` `100.` IPv4 que começam e são internos à sua implantação. Deve permitir que se conectem à sua aplicação.

- Após trocas de slot, a aplicação pode experimentar reiniciações inesperadas. Isto porque, após uma troca, a configuração de ligação do nome de anfitrião fica dessincronizada, o que por si só não causa reinícios. No entanto, certos eventos de armazenamento subjacentes (tais como falhas no volume de armazenamento) podem detetar estas discrepâncias e forçar todos os processos dos trabalhadores a reiniciar. Para minimizar este tipo de reinícios, defina a [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` definição](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) da aplicação em *todas as ranhuras*. No entanto, esta definição de aplicações *não* funciona com aplicações da Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Passos seguintes
[Bloquear o acesso a faixas horárias não produtivas](app-service-ip-restrictions.md)
