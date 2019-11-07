---
title: Configurar ambientes de preparo para aplicativos Web no serviço Azure App | Microsoft Docs
description: Saiba como usar a publicação em etapas para aplicativos Web no serviço Azure App.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/19/2019
ms.author: cephalin
ms.openlocfilehash: f9b1af14bd986f1fa6fb5feb398a7f1fdf982f77
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669102"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no serviço Azure App
<a name="Overview"></a>

Ao implantar seu aplicativo Web, aplicativo Web no Linux, back-end móvel ou aplicativo de API para [Azure app serviço](https://go.microsoft.com/fwlink/?LinkId=529714), você pode usar um slot de implantação separado em vez do slot de produção padrão quando estiver executando no **Standard**, **Premium**ou **isolado** Camada do plano do serviço de aplicativo. Os slots de implantação são aplicativos ao vivo com seus próprios nomes de host. Elementos de configuração e conteúdo do aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. 

Implantar seu aplicativo em um slot de não produção tem os seguintes benefícios:

* Você pode validar as alterações do aplicativo em um slot de implantação de preparo antes de trocá-lo pelo slot de produção.
* A implantação de um aplicativo em um slot primeiro e sua troca na produção garante que todas as instâncias do slot sejam ativadas antes de serem trocadas na produção. Isso elimina o tempo de inatividade quando você implanta seu aplicativo. O redirecionamento de tráfego é contínuo e nenhuma solicitação é descartada devido a operações de permuta. Você pode automatizar todo esse fluxo de trabalho configurando a [troca automática](#Auto-Swap) quando a validação de pré-atualização não for necessária.
* Após uma troca, o slot com o aplicativo preparado anteriormente agora tem o aplicativo de produção anterior. Se as alterações trocadas no slot de produção não forem as esperadas, você poderá executar a mesma troca imediatamente para obter o "último site bom conhecido" de volta.

Cada camada de plano do serviço de aplicativo dá suporte a um número diferente de Slots de implantação. Não há nenhum custo adicional para usar os slots de implantação. Para descobrir o número de Slots com suporte na camada do aplicativo, consulte [limites do serviço de aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Para dimensionar seu aplicativo para uma camada diferente, verifique se a camada de destino dá suporte ao número de slots que seu aplicativo já usa. Por exemplo, se seu aplicativo tiver mais de cinco slots, você não poderá dimensioná-lo para a camada **Standard** , pois a camada **Standard** oferece suporte a apenas cinco slots de implantação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um slot
O aplicativo deve estar em execução na camada **Standard**, **Premium**ou **Isolated** para que você habilite vários slots de implantação.

1. Na [portal do Azure](https://portal.azure.com/), abra a [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources)do aplicativo.

2. No painel esquerdo, selecione **Slots de implantação** > **Adicionar slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver na camada **Standard**, **Premium**ou **Isolated** , você receberá uma mensagem que indica as camadas com suporte para habilitar a publicação em etapas. Neste ponto, você tem a opção de selecionar **Atualizar** e ir para a guia **escala** do seu aplicativo antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar um slot** , dê um nome ao slot e selecione se deseja clonar uma configuração de aplicativo de outro slot de implantação. Selecione **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Você pode clonar uma configuração de qualquer slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, cadeias de conexão, versões de estrutura de linguagem, soquetes Web, versão HTTP e bit de bits de plataforma.

4. Depois que o slot for adicionado, selecione **fechar** para fechar a caixa de diálogo. O novo slot agora é mostrado na página **Slots de implantação** . Por padrão, o **tráfego%** é definido como 0 para o novo slot, com todo o tráfego do cliente roteado para o slot de produção.

5. Selecione o novo slot de implantação para abrir a página de recursos do slot.
   
    ![Título do slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de preparo tem uma página de gerenciamento assim como qualquer outro aplicativo do serviço de aplicativo. Você pode alterar a configuração do slot. O nome do slot é mostrado na parte superior da página para lembrá-lo de que você está exibindo o slot de implantação.

6. Selecione a URL do aplicativo na página de recursos do slot. O slot de implantação tem seu próprio nome de host e também é um aplicativo ativo. Para limitar o acesso público ao slot de implantação, consulte [Azure app restrições de IP do serviço](app-service-ip-restrictions.md).

O novo slot de implantação não tem conteúdo, mesmo se você clonar as configurações de um slot diferente. Por exemplo, você pode [publicar neste slot com o Git](app-service-deploy-local-git.md). Você pode implantar no slot de uma ramificação de repositório diferente ou de um repositório diferente. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma permuta

### <a name="swap-operation-steps"></a>Etapas da operação de permuta

Quando você troca dois slots (geralmente de um slot de preparo no slot de produção), o serviço de aplicativo faz o seguinte para garantir que o slot de destino não experimente tempo de inatividade:

1. Aplique as seguintes configurações do slot de destino (por exemplo, o slot de produção) a todas as instâncias do slot de origem: 
    - Configurações do aplicativo e cadeias [de conexão específicas do slot](#which-settings-are-swapped) , se aplicável.
    - Configurações de [implantação contínua](deploy-continuous-deployment.md) , se habilitadas.
    - Configurações de [autenticação do serviço de aplicativo](overview-authentication-authorization.md) , se habilitadas.
    
    Qualquer um desses casos disparará todas as instâncias no slot de origem para reiniciar. Durante a [troca com visualização](#Multi-Phase), isso marca o final da primeira fase. A operação de permuta está pausada e você pode validar que o slot de origem funciona corretamente com as configurações do slot de destino.

1. Aguarde até que cada instância no slot de origem conclua sua reinicialização. Se alguma instância não for reiniciada, a operação de permuta reverterá todas as alterações no slot de origem e interromperá a operação.

1. Se o [cache local](overview-local-cache.md) estiver habilitado, dispare a inicialização do cache local fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem. Aguarde até que cada instância retorne qualquer resposta HTTP. A inicialização do cache local causa outra reinicialização em cada instância.

1. Se a [troca automática](#Auto-Swap) estiver habilitada com a inicialização [personalizada](#Warm-up)do [aplicativo](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) de disparo, fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem.

    Se `applicationInitialization` não for especificado, dispare uma solicitação HTTP para a raiz do aplicativo do slot de origem em cada instância. 
    
    Se uma instância retornar qualquer resposta HTTP, ela será considerada como ativada.

1. Se todas as instâncias no slot de origem forem ativadas com êxito, troque os dois slots alternando as regras de roteamento para os dois slots. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que foi anteriormente ativado no slot de origem.

1. Agora que o slot de origem tem o aplicativo de pré-permuta anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reiniciando as instâncias.

Em qualquer ponto da operação de permuta, todo o trabalho de inicializar os aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está sendo preparado e ativado, independentemente de onde a troca tenha êxito ou falhe. Para trocar um slot de preparo pelo slot de produção, verifique se o slot de produção é sempre o slot de destino. Dessa forma, a operação de permuta não afeta seu aplicativo de produção.

### <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma cadeia de conexão ou configuração de aplicativo para se conectar a um slot específico (não trocado), vá para a página de **configuração** desse slot. Adicione ou edite uma configuração e, em seguida, selecione **configuração do slot de implantação**. Marcar essa caixa de seleção informa ao serviço de aplicativo que a configuração não é intercambiável. 

![Configuração de slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots 
Você pode alternar os slots de implantação na página de **Slots de implantação** do aplicativo e na página **visão geral** . Para obter detalhes técnicos sobre a troca de slot, consulte [o que acontece durante a permuta](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar um aplicativo de um slot de implantação em produção, verifique se a produção é o slot de destino e se todas as configurações no slot de origem estão configuradas exatamente como você deseja tê-las em produção.
> 
> 

Para permutar slots de implantação:

1. Vá para a página **Slots de implantação** do aplicativo e selecione **alternar**.
   
    ![Botão Alternar](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **alternar** mostra as configurações nos slots de origem e destino selecionados que serão alterados.

2. Selecione os slots de **origem** e de **destino** desejados. Normalmente, o destino é o slot de produção. Além disso, selecione as guias **alterações de origem** e **alterações de destino** e verifique se as alterações de configuração são esperadas. Quando tiver terminado, você poderá trocar os slots imediatamente selecionando **alternar**.

    ![Concluir a troca](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o slot de destino seria executado com as novas configurações antes que a permuta realmente aconteça, não selecione **alternar**, mas siga as instruções em [alternar com visualização](#Multi-Phase).

3. Quando tiver terminado, feche a caixa de diálogo selecionando **fechar**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Alternar com visualização (troca de várias fases)

Antes de trocar para produção como o slot de destino, valide se o aplicativo é executado com as configurações trocadas. O slot de origem também é ativado antes da conclusão da permuta, o que é desejável para aplicativos de missão crítica.

Quando você executa uma troca com visualização, o serviço de aplicativo executa a mesma [operação de permuta](#AboutConfiguration) , mas pausa após a primeira etapa. Em seguida, você pode verificar o resultado no slot de preparo antes de concluir a permuta. 

Se você cancelar a troca, o serviço de aplicativo reaplicará os elementos de configuração ao slot de origem.

Para alternar com a visualização:

1. Siga as etapas em [trocar slots de implantação](#Swap) , mas selecione **executar permuta com visualização**.

    ![Alternar com visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem muda na fase 1 e como a origem e o slot de destino são alterados na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Iniciar permuta**.

    Quando a fase 1 for concluída, você será notificado na caixa de diálogo. Visualize a troca no slot de origem acessando `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando estiver pronto para concluir a permuta pendente, selecione **concluir permuta** na **ação de permuta** e selecione **concluir permuta**.

    Para cancelar uma permuta pendente, selecione **Cancelar permuta** em vez disso.

4. Quando tiver terminado, feche a caixa de diálogo selecionando **fechar**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

Para automatizar uma troca de várias fases, consulte [automatizar com o PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverter uma permuta
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma troca de slot, restaure os slots para seus Estados de pré-atualização alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar troca automática

> [!NOTE]
> A troca automática não tem suporte em aplicativos Web no Linux.

A troca automática simplifica os cenários de DevOps do Azure em que você deseja implantar seu aplicativo continuamente com inícios inativos sem interrupção e sem tempo de inatividade para clientes do aplicativo. Quando a troca automática está habilitada de um slot para produção, sempre que você envia por push as alterações de código para esse slot, [o serviço de aplicativo troca automaticamente o aplicativo em produção](#swap-operation-steps) depois que ele é ativado no slot de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para o slot de produção, considere testar a troca automática em um slot de destino de não produção.
   > 

Para configurar a troca automática:

1. Vá para a página de recursos do aplicativo. Selecione os **Slots de implantação** >  *\<slot de origem desejado >* configurações de > **configuração** de > **geral**.
   
2. Para a **troca automática habilitada**, selecione **ativado**. Em seguida, selecione o slot de destino desejado para o **slot de implantação de permuta automática**e selecione **salvar** na barra de comandos. 
   
    ![Seleções para configurar a troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. A troca automática ocorre após um curto período de tempo e a atualização é refletida na URL do slot de destino.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especificar aquecimento personalizado

Alguns aplicativos podem exigir ações de aquecimento personalizadas antes da troca. O elemento de configuração `applicationInitialization` no Web. config permite que você especifique ações de inicialização personalizadas. A [operação de permuta](#AboutConfiguration) aguarda que esse aquecimento personalizado seja concluído antes de alternar com o slot de destino. Aqui está um fragmento Web. config de exemplo.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações sobre como personalizar o elemento `applicationInitialization`, consulte [falhas de permuta de slot de implantação mais comuns e como corrigi-los](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma ou ambas as seguintes configurações de [aplicativo](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: o caminho para o ping para aquecimento do seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começa com uma barra como o valor. Um exemplo é `/statuscheck`. O valor padrão é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista separada por vírgulas de códigos HTTP. Um exemplo é `200,202`. Se o código de status retornado não estiver na lista, as operações aquecimento e swap serão interrompidas. Por padrão, todos os códigos de resposta são válidos.

> [!NOTE]
> O elemento de configuração `<applicationInitialization>` faz parte de cada inicialização de aplicativo, enquanto as duas configurações de aplicativo de comportamento de aquecimento se aplicam somente a trocas de slot.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorar uma troca

Se a [operação de permuta](#AboutConfiguration) levar muito tempo para ser concluída, você poderá obter informações sobre a operação de permuta no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos do aplicativo no portal, no painel esquerdo, selecione log de **atividades**.

Uma operação de permuta é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-lo e selecionar uma das suboperações ou erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear tráfego

Por padrão, todas as solicitações de cliente para a URL de produção do aplicativo (`http://<app_name>.azurewebsites.net`) são roteadas para o slot de produção. Você pode rotear uma parte do tráfego para outro slot. Esse recurso será útil se você precisar de comentários do usuário para uma nova atualização, mas não estiver pronto para liberá-lo para produção.

### <a name="route-production-traffic-automatically"></a>Rotear tráfego de produção automaticamente

Para rotear o tráfego de produção automaticamente:

1. Vá para a página de recursos do aplicativo e selecione **Slots de implantação**.

2. Na coluna **tráfego%** do slot para o qual você deseja rotear, especifique uma porcentagem (entre 0 e 100) para representar a quantidade de tráfego total que você deseja rotear. Selecione **Guardar**.

    ![Definindo um percentual de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração é salva, a porcentagem especificada de clientes é roteada aleatoriamente para o slot de não produção. 

Depois que um cliente é roteado automaticamente para um slot específico, ele é "fixado" a esse slot durante a vida útil dessa sessão de cliente. No navegador do cliente, você pode ver em qual slot sua sessão está fixa examinando o `x-ms-routing-name` cookie em seus cabeçalhos HTTP. Uma solicitação que é roteada para o slot de "preparo" tem o cookie `x-ms-routing-name=staging`. Uma solicitação que é roteada para o slot de produção tem o cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Rotear tráfego de produção manualmente

Além do roteamento automático de tráfego, o serviço de aplicativo pode rotear solicitações para um slot específico. Isso é útil quando você deseja que os usuários possam aceitar ou recusar seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de consulta `x-ms-routing-name`.

Para permitir que os usuários recusem seu aplicativo beta, por exemplo, você pode colocar esse link em sua página da Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` especifica o slot de produção. Depois que o navegador do cliente acessa o link, ele é redirecionado para o slot de produção. Cada solicitação subsequente tem o `x-ms-routing-name=self` cookie que fixa a sessão ao slot de produção.

Para permitir que os usuários aceitem seu aplicativo beta, defina o mesmo parâmetro de consulta como o nome do slot de não produção. Segue-se um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, novos slots recebem uma regra de roteamento de `0%`, mostrados em cinza. Quando você define explicitamente esse valor como `0%` (mostrado em texto preto), os usuários podem acessar o slot de preparo manualmente usando o parâmetro de consulta `x-ms-routing-name`. Mas eles não serão roteados para o slot automaticamente porque a porcentagem de roteamento é definida como 0. Esse é um cenário avançado em que você pode "Ocultar" o slot de preparo do público, permitindo que as equipes internas testem as alterações no slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Excluir um slot

Vá para a página de recursos do aplicativo. Selecione **Slots de implantação** > *slot de\<para excluir >* **visão geral**de > . Selecione **excluir** na barra de comandos.  

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte para gerenciar slots de implantação no serviço Azure App.

Para obter informações sobre como instalar e configurar Azure PowerShell e sobre como autenticar Azure PowerShell com sua assinatura do Azure, consulte [como instalar e configurar o Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Criar uma aplicação Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Criar um slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Iniciar uma troca com uma visualização (troca de várias fases) e aplicar a configuração do slot de destino ao slot de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancelar uma permuta pendente (trocar com revisão) e restaurar a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Trocar slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorar eventos de permuta no log de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Excluir um slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-arm-templates"></a>Automatizar com modelos ARM

Os [modelos do ARM](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) são arquivos JSON declarativos usados para automatizar a implantação e a configuração dos recursos do Azure. Para trocar os slots usando modelos ARM, você definirá duas propriedades nos recursos *Microsoft. Web/sites/Slots* e *Microsoft. Web/sites* :

- `buildVersion`: essa é uma propriedade de cadeia de caracteres que representa a versão atual do aplicativo implantado no slot. Por exemplo: "v1", "1.0.0.1" ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: essa é uma propriedade de cadeia de caracteres que especifica o que `buildVersion` o slot deve ter. Se o targetBuildVersion não for igual ao `buildVersion`atual, isso disparará a operação de permuta encontrando o slot que tem o `buildVersion`especificado.

### <a name="example-arm-template"></a>Exemplo de modelo ARM

O modelo ARM a seguir atualizará a `buildVersion` do slot de preparo e definirá o `targetBuildVersion` no slot de produção. Isso mudará os dois slots. O modelo pressupõe que você já tenha um webapp criado com um slot chamado "preparo".

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

Esse modelo de ARM é idempotente, o que significa que ele pode ser executado repetidamente e produzir o mesmo estado dos slots. Após a primeira execução, `targetBuildVersion` corresponderá à `buildVersion`atual, de modo que uma troca não será disparada.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizar com a CLI

Para [CLI do Azure](https://github.com/Azure/azure-cli) comandos para slots de implantação, consulte [AZ webapp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Solucionar problemas de trocas

Se ocorrer algum erro durante uma [troca de slot](#AboutConfiguration), ele será registrado em *D:\home\LogFiles\eventlog.xml*. Ele também é registrado no log de erros específico do aplicativo.

Aqui estão alguns erros de permuta comuns:

- Uma solicitação HTTP para a raiz do aplicativo é cronometrada. A operação de permuta aguarda por 90 segundos para cada solicitação HTTP e tenta novamente até 5 vezes. Se todas as novas tentativas atingirem o tempo limite, a operação de permuta será interrompida.

- A inicialização do cache local poderá falhar quando o conteúdo do aplicativo exceder a cota de disco local especificada para o cache local. Para obter mais informações, consulte [visão geral do cache local](overview-local-cache.md).

- Durante o [aquecimento personalizado](#Warm-up), as solicitações HTTP são feitas internamente (sem passar pela URL externa). Eles podem falhar com determinadas regras de regravação de URL em *Web. config*. Por exemplo, regras para redirecionar nomes de domínio ou impor HTTPS podem impedir que solicitações de aquecimento atinjam o código do aplicativo. Para contornar esse problema, modifique suas regras de reescrita adicionando as duas condições a seguir:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de regravação de URL ainda podem bloquear solicitações HTTP. Para contornar esse problema, modifique suas regras de reescrita adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Algumas [regras de restrição de IP](app-service-ip-restrictions.md) podem impedir que a operação de permuta envie solicitações HTTP para seu aplicativo. Os intervalos de endereços IPv4 que começam com `10.` e `100.` são internos à sua implantação. Você deve permitir que eles se conectem ao seu aplicativo.

- Após trocas de slot, o aplicativo pode experimentar reinicializações inesperadas. Isso ocorre porque, após uma troca, a configuração de associação de nome de host fica fora de sincronia, o que por si só não causa reinicializações. No entanto, determinados eventos de armazenamento subjacentes (como failovers de volume de armazenamento) podem detectar essas discrepâncias e forçar a reinicialização de todos os processos de trabalho. Para minimizar esses tipos de reinicializações, defina a [configuração do aplicativo`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1`](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) em *todos os slots*. No entanto, essa configuração de aplicativo *não funciona com* aplicativos Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Passos seguintes
[Bloquear o acesso a slots de não produção](app-service-ip-restrictions.md)
