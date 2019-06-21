---
title: Configurar ambientes de teste para aplicações web no App Service do Azure | Documentos da Microsoft
description: Saiba como utilizar a publicação faseada para web apps no App Service do Azure.
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
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: cbf287aef2c1792033a198070da605014a7b6281
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272850"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de teste no serviço de aplicações do Azure
<a name="Overview"></a>

Ao implementar a aplicação web, aplicação web no Linux, back-end móvel e aplicação de API para [serviço de aplicações](https://go.microsoft.com/fwlink/?LinkId=529714), pode implementar num bloco de implementação separados em vez do bloco de produção predefinido ao executar o **padrão**, **Premium**, ou **Isolated** escalão do plano de serviço de aplicações. Blocos de implementação são aplicações, na verdade, em direto com os seus próprios nomes de anfitrião. Elementos de conteúdo e de configuração da aplicação podem ser trocados entre duas ranhuras de implementação, incluindo o bloco de produção. Implementar a aplicação para um bloco de não produção tem as seguintes vantagens:

* Pode validar alterações da aplicação num bloco de implementação de teste antes de a colocar com o bloco de produção.
* Implementar uma aplicação para um bloco de primeiro e colocar em produção certifica-se de que todas as instâncias do slot são preparadas antes de serem colocadas em produção. Esta ação elimina o tempo de inatividade, quando implementar a sua aplicação. O redirecionamento de tráfego é totalmente integrado, e não existem pedidos são ignorados devido a operações de troca. Este fluxo de trabalho completo pode ser automatizado através da configuração [comutação automática](#Auto-Swap) quando a validação de pré-troca de não é necessária.
* Após uma alternância de, a ranhura com a aplicação anteriormente faseada tem agora a aplicação de produção anterior. Se as alterações colocadas em bloco de produção não conforme o esperado, pode executar a mesma troca imediatamente para obter sua "última boa site conhecido" novamente.

Cada escalão do plano de serviço de aplicações suporta um número diferente de blocos de implementação e não implica custos adicionais para utilizar ranhuras de implementação. Para obter informações sobre o número de ranhuras suporta a camada da sua aplicação, consulte [limites de serviço de aplicações](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Para dimensionar a sua aplicação para um escalão diferente, a camada de destino tem de suportar o número de ranhuras a que sua aplicação já utiliza. Por exemplo, se a sua aplicação tiver mais de cinco ranhuras, não é possível dimensionar até **padrão** tier, porque **padrão** escalão suporta apenas cinco blocos de implementação. 

<a name="Add"></a>

## <a name="add-slot"></a>Adicionar ranhura
A aplicação tem de executar o **padrão**, **Premium**, ou **Isolated** escalão para que a ativação de vários blocos de implementação.

1. Na [portal do Azure](https://portal.azure.com/), abra a sua aplicação [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources).

2. No painel de navegação esquerdo, selecione o **blocos de implementação** opção, em seguida, clique em **adicionar ranhura**.
   
    ![Adicionar um novo bloco de implementação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se a aplicação não estiver no **padrão**, **Premium**, ou **Isolated** camada, receberá uma mensagem a indicar as camadas de suporte para ativar a publicação faseada. Neste ponto, tem a opção de selecionar **atualizar** e navegue para o **dimensionamento** separador da sua aplicação antes de continuar.
   > 

3. Na **adicionar uma ranhura** caixa de diálogo, dê o bloco de um nome e selecione se pretende clonar a configuração de aplicações do outro bloco de implementação existente. Clique em **adicionar** para continuar.
   
    ![Origem de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Pode clonar a configuração a partir de qualquer bloco existente. As definições que podem ser Clonadas incluem as definições da aplicação, cadeias de ligação, versões de estrutura de idioma, web sockets, versão HTTP e número de bits de plataforma.

4. Depois do bloco for adicionado, clique em **fechar** para fechar a caixa de diálogo. A nova ranhura agora é mostrada na **blocos de implementação** página. Por predefinição, o **% do tráfego** está definido como 0 para a nova ranhura com todo o tráfego de cliente encaminhado para o bloco de produção.

5. Clique em nova ranhura de implementação para abrir a página de recursos desse bloco.
   
    ![Título do bloco de implementação](./media/web-sites-staged-publishing/StagingTitle.png)

    O bloco de teste tem uma página de gestão tal como qualquer outra aplicação de serviço de aplicações. Pode alterar a configuração da ranhura. O nome do slot é mostrado na parte superior da página para lembrá-lo de que está a ver o bloco de implementação.

6. Clique no URL da aplicação na página de recursos da ranhura. O bloco de implementação tem seu próprio nome de anfitrião e também é uma aplicação em direto. Para limitar o acesso público a ranhura de implementação, consulte [restrições de IP do serviço de aplicações do Azure](app-service-ip-restrictions.md).

A nova ranhura de implementação não tem conteúdo, mesmo que o clone as definições de um bloco de diferente. Por exemplo, pode [publicar esta ranhura com o git](app-service-deploy-local-git.md). Pode implementar para a ranhura a partir de um ramo do repositório diferente ou um repositório diferente. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-swap"></a>O que acontece durante a troca

[Passos da operação de comutação](#swap-operation-steps)
[quais configurações são ativadas?](#which-settings-are-swapped)

### <a name="swap-operation-steps"></a>Passos da operação de troca

Quando troca as ranhuras de dois (normalmente, a partir de um bloco de teste para o bloco de produção), o serviço de aplicações faz o seguinte para se certificar de que a ranhura de destino não sofrer períodos de inatividade:

1. Aplica as seguintes definições de ranhura de destino (por exemplo, o bloco de produção) para todas as instâncias da ranhura de origem: 
    - [Específicos da ranhura](#which-settings-are-swapped) definições de aplicações e as cadeias de ligação, se aplicável.
    - [Implementação contínua](deploy-continuous-deployment.md) definições, se estiver ativada.
    - [Autenticação do serviço de aplicações](overview-authentication-authorization.md) definições, se estiver ativada.
    Qualquer um dos casos acima aciona todas as instâncias na ranhura de origem para reiniciar. Durante [troca com pré-visualização](#Multi-Phase), isto marca o fim da primeira fase, em que a operação de troca está em pausa e pode validar que a ranhura de origem funciona corretamente com as definições de ranhura de destino.

1. Aguarde que todas as instâncias na ranhura de origem para concluir o seu reinício. Se qualquer instância não for reiniciado, a operação de troca reverte todas as alterações para a ranhura de origem e aborta a operação.

1. Se [local cache](overview-local-cache.md) é ativada, acionar a inicialização da local cache, fazendo um HTTP do pedido para a raiz do aplicativo ("/") em cada instância da ranhura de origem e esperar até que cada instância devolve qualquer resposta HTTP. Inicialização da local cache faz com que outro reinício em cada instância.

1. Se [troca automática](#Auto-Swap) está ativado com [aquecimento personalizado](#custom-warm-up), acionador [iniciação de aplicação](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância de origem ranhura. Se uma instância retornar qualquer resposta HTTP, é considerada para ser preparadas.

    Se não `applicationInitialization` é especificada, disparar uma solicitação HTTP para a raiz do aplicativo da ranhura de origem em cada instância. Se uma instância retornar qualquer resposta HTTP, é considerada para ser preparadas.

1. Se todas as instâncias em que a ranhura de origem são preparadas com êxito, troca as ranhuras de dois alternando as regras de encaminhamento para os dois blocos. Após este passo, a ranhura de destino (por exemplo, o bloco de produção) com a aplicação que anteriormente aquecida na ranhura de origem.

1. Agora que a ranhura de origem tiver a aplicação de pré-troca de anteriormente na ranhura de destino, execute a mesma operação ao aplicar a todas as definições e reiniciar as instâncias.

Em qualquer ponto da operação de troca, todo o trabalho de inicialização de aplicações do trocada é feito a ranhura de origem. A ranhura de destino permanece online enquanto a ranhura de origem está a ser preparado e preparadas up, independentemente em que a troca tiver sucesso ou falhar. A troca um bloco de teste com o bloco de produção, certifique-se de que o bloco de produção é sempre a ranhura de destino. Dessa forma, a aplicação de produção não é afetada pela operação de troca.

### <a name="which-settings-are-swapped"></a>As definições que são ativadas?
Quando clona configuração a partir de outro bloco de implementação, a configuração clonada é editável. Além disso, alguns elementos de configuração siga o conteúdo numa troca (não ranhura específica) enquanto outros elementos de configuração permanecem na ranhura de mesmo após uma alternância de (bloco específico). As listas seguintes mostram as definições que alteram quando trocar as ranhuras.

**As definições que são ativadas**:

* Definições gerais - como sockets Web do framework versão, 32/64 bits,
* Definições da aplicação (pode ser configurado para utilizar um bloco de)
* Cadeias de ligação (pode ser configurado para utilizar um bloco de)
* Mapeamentos do processador
* Definições de monitorização e diagnósticos
* Certificados públicos
* Conteúdo de WebJobs
* Ligações híbridas *
* Integração VNet *
* Pontos finais de serviço *
* A CDN do Azure *

Recursos marcados com um * estão planeados devem se transformar em papeizinhos para a ranhura. 

**As definições que não são trocadas**:

* Pontos finais de publicação
* Nomes de domínio personalizados
* Certificados privados e enlaces de SSL
* Definições de dimensionamento
* Agendadores de WebJobs
* Restrições de IP
* Sempre Ativo
* Definições de protocolo (HTTP**S**, versão do TLS, certificados de cliente)
* Definições de registo de diagnóstico
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Para configurar uma cadeia de ligação ou definição de aplicação de adotar um bloco específico (não trocado), navegue para o **Configuration** página, para que a ranhura, adicionar ou editar uma definição, em seguida, selecione o **definição de ranhura de implementação**caixa. Selecionar esta caixa de verificação indica ao serviço de aplicações que a definição não está-swap. 

![Definição de ranhura](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Troca de blocos de dois 
Pode trocar as ranhuras de implementação na sua aplicação **blocos de implementação** página e o **descrição geral** página. Para obter detalhes técnicos sobre a troca de bloco, consulte [o que acontece durante a troca](#what-happens-during-swap)

> [!IMPORTANT]
> Antes de mudar uma aplicação a partir de um bloco de implementação para produção, certifique-se de que a produção é o bloco de destino e que todas as definições na ranhura de origem estão configuradas exatamente como pretende que tê-la em produção.
> 
> 

Para trocar as ranhuras de implementação, siga estes passos:

1. Navegue para a aplicação **blocos de implementação** página e clique em **trocar**.
   
    ![Botão de alternância](./media/web-sites-staged-publishing/SwapButtonBar.png)

    O **trocar** caixa de diálogo mostra as definições nas ranhuras de origem e de destino selecionadas que serão alteradas.

2. Selecione o desejado **origem** e **destino** ranhuras. Normalmente, o destino é o bloco de produção. Além disso, clique nas **alterações de origem** e **alterações de destino** separadores e certifique-se de que as alterações de configuração esperadas. Quando terminar, pode trocar as ranhuras imediatamente clicando **troca**.

    ![Concluir troca](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o bloco de destino seria executado com as novas definições antes da troca aconteça de fato, não clique **troca**, mas seguir as instruções [troca com pré-visualização](#Multi-Phase).

3. Quando tiver terminado, feche a caixa de diálogo clicando **fechar**.

Caso se depare com quaisquer problemas, consulte [trocas de resolução de problemas](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com pré-visualização (múltiplas fase swap)

> [!NOTE]
> Troca com pré-visualização não é suportada nas aplicações web no Linux.

Antes de passar para produção como a ranhura de destino, Valide as execuções de aplicação com as definições de trocada antes da troca acontece. A ranhura de origem é também preparada antes de concluir troca, que também é desejável para aplicativos de missão crítica.

Ao efetuar uma troca com pré-visualização, o serviço de aplicações efetua a mesma [operação de troca](#what-happens-during-swap) , mas coloca em pausa após o primeiro passo. Em seguida, pode verificar o resultado no bloco de teste antes de concluir a troca. 

Se cancelar a troca, o serviço de aplicações volta aplicar os elementos de configuração da ranhura de origem para a ranhura de origem.

Para a troca com pré-visualização, siga estes passos.

1. Siga os passos em [trocar as ranhuras de implementação](#Swap) mas selecione **executar troca com pré-visualização**.

    ![Troca com pré-visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra-lhe como a configuração na ranhura de origem é alterada na fase 1 e como a ranhura de origem e destino alteradas na fase 2.

2. Quando estiver pronto para iniciar a troca, clique em **troca iniciar**.

    Quando tiver concluído a fase 1, receberá uma notificação na caixa de diálogo. Pré-visualizar a troca na ranhura de origem ao navegar para `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando estiver pronto para concluir a troca pendente, selecione **concluir troca** na **ação de troca** e clique em **concluir troca**.

    Para cancelar uma troca pendente, selecione **Cancelar troca** em vez disso e clique em **Cancelar troca**.

4. Quando tiver terminado, feche a caixa de diálogo clicando **fechar**.

Caso se depare com quaisquer problemas, consulte [trocas de resolução de problemas](#troubleshoot-swaps).

Para automatizar uma alternância de múltiplas fase, consulte automatizar com o PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Reverter a troca
Se ocorrerem erros na ranhura de destino (por exemplo, o bloco de produção) após uma alternância de bloco, restaure os blocos para seus Estados de pré-comutação de ao trocar as ranhuras de dois mesmo imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> Troca automática não é suportada nas aplicações web no Linux.

Troca automática simplifica cenários DevOps onde pretende implementar a sua aplicação continuamente com zero a frio e nenhum tempo de inatividade para os clientes do fim da aplicação. Quando troca automática está ativada a partir de um bloco em produção, sempre que enviar suas alterações de código para que a ranhura, serviço de aplicações automaticamente [troca a aplicação para produção](#swap-operation-steps) depois é aquecido na ranhura de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para o bloco de produção, considere a troca automática numa ranhura de destino de não produção de teste pela primeira vez.
   > 

Para configurar a troca automática, siga estes passos:

1. Navegue até à página de recursos da sua aplicação. Selecione **blocos de implementação** >  *\<ranhura de origem pretendido >*  > **configuração**  >  **Definições gerais**.
   
2. Na **troca automática ativada**, selecione **no**, em seguida, selecione a ranhura de destino pretendida na **ranhura de implementação de troca automática**e clique em **guardar** no barra de comandos. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para a ranhura de origem. Acontece de troca automática após um curto período de tempo e a atualização é refletida no URL da ranhura de destino.

Caso se depare com quaisquer problemas, consulte [trocas de resolução de problemas](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Aquecimento personalizado
Ao usar [troca automática](#Auto-Swap), algumas aplicações podem exigir ações personalizadas de aquecimento antes da troca. O `applicationInitialization` elemento de configuração em Web. config permite-lhe especificar a execução de ações de inicialização personalizada. O [operação de troca](#what-happens-during-swap) aguarda que este aquecimento personalizado seja concluída antes de a troca com a ranhura de destino. Aqui está um fragmento de Web. config de exemplo.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações sobre como personalizar o `applicationInitialization` elemento, consulte [falhas de comutação da ranhura de implementação mais comuns e como corrigi-los](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Também pode personalizar o comportamento de aquecimento com um ou mais dos seguintes procedimentos [as definições da aplicação](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para enviar um ping para aquecer seu site. Adicione esta definição de aplicação, especificando um caminho personalizado que começa com uma barra como o valor. Por exemplo, `/statuscheck`. O valor predefinido é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione esta definição de aplicação com uma lista separada por vírgulas de códigos HTTP. Por exemplo: `200,202` . Se o código de estado devolvido não estiver na lista, as operações de aquecimento e troca são paradas. Por predefinição, todos os códigos de resposta são válidos.

Caso se depare com quaisquer problemas, consulte [trocas de resolução de problemas](#troubleshoot-swaps).

## <a name="monitor-swap"></a>Troca de monitor

Se o [operação de troca](#what-happens-during-swap) demora muito tempo a concluir, pode obter informações sobre a operação de troca no [registo de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos da sua aplicação no portal, no painel de navegação esquerdo, selecione **registo de atividades**.

Uma operação de troca é apresentado na consulta de registo como `Swap Web App Slots`. Pode expandi-lo e selecione um dos suboperations ou erros para ver os detalhes.

## <a name="route-traffic"></a>Encaminhar o tráfego

Por predefinição, todos os pedidos de cliente para o URL de produção da aplicação (`http://<app_name>.azurewebsites.net`) são encaminhados para o bloco de produção. Pode encaminhar uma parte do tráfego para o bloco de outro. Esta funcionalidade é útil se precisa de comentários dos utilizadores para uma nova atualização, mas não está pronto para liberá-lo para produção.

### <a name="route-production-traffic-automatically"></a>Encaminhar o tráfego de produção automaticamente

Para encaminhar automaticamente o tráfego de produção, siga estes passos:

1. Navegue até à página de recursos da sua aplicação e selecione **blocos de implementação**.

2. Na **% do tráfego** coluna da ranhura de que pretende encaminhar para, especifique uma percentagem (entre 0 e 100) para representar a quantidade de tráfego total que pretende encaminhar. Clique em **Guardar**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois da definição é guardada, a percentagem especificada de clientes aleatoriamente é encaminhada para a ranhura de não produção. 

Assim que um cliente é automaticamente roteado para um bloco específico, é "fixados" para que a ranhura para o ciclo de vida dessa sessão de cliente. O navegador do cliente, pode ver qual ranhura a sessão está afixada ao observar o `x-ms-routing-name` cookie nos cabeçalhos HTTP. Um pedido que é encaminhado para o bloco de "teste" tem o cookie `x-ms-routing-name=staging`. Um pedido que é encaminhado para o bloco de produção tem o cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Encaminhar o tráfego de produção manualmente

Além do encaminhamento de tráfego automática, o serviço de aplicações podem encaminhar pedidos para um bloco específico. Isto é útil quando pretende que os utilizadores ser capazes de optar ativamente por participar em ou por cancelar o seu aplicativo beta. Para encaminhar o tráfego de produção manualmente, utilize o `x-ms-routing-name` parâmetro de consulta.

Para permitir que os utilizadores a opção de desativar a aplicação de beta, por exemplo, pode colocar esse link na página da web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` Especifica o bloco de produção. Assim que o navegador do cliente acessa o link, não só é redirecionado para o bloco de produção, mas cada solicitação subseqüente tem o `x-ms-routing-name=self` cookie que fixa a sessão para o bloco de produção.

Para permitir que os utilizadores que escolher para a sua aplicação de beta, defina o mesmo parâmetro de consulta para o nome da ranhura de não produção, por exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por predefinição, os blocos de novo recebem uma regra de roteamento de `0%`, conforme apresentado a cinzento. Definindo explicitamente esse valor como `0%` (mostrado em texto preto), os utilizadores podem aceder o bloco de teste manualmente utilizando o `x-ms-routing-name` parâmetro de consulta, mas será não ser encaminhados para a ranhura automaticamente uma vez que a percentagem de encaminhamento está definida como 0. Este é um cenário avançado em que pode "Ocultar" seu bloco de teste do público, permitindo que as equipas internas testar as alterações no bloco de.

<a name="Delete"></a>

## <a name="delete-slot"></a>Eliminar Bloco

Navegue até à página de recursos da sua aplicação. Selecione **blocos de implementação** >  *\<ranhura para eliminar >*  > **descrição geral**. Clique em **eliminar** na barra de comandos.  

![Eliminar um bloco de implementação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que oferece cmdlets para gerir o Azure através do Windows PowerShell, incluindo suporte para o gerenciamento de blocos de implementação no serviço de aplicações do Azure.

Para obter informações sobre como instalar e configurar o Azure PowerShell e sobre a autenticação do Azure PowerShell com a sua subscrição do Azure, consulte [como instalar e configurar o Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-web-app"></a>Criar aplicação Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-slot"></a>Criar a ranhura
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Iniciar a troca com pré-visualização (troca de múltiplas fase) e aplicar a configuração da ranhura de destino para a ranhura de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Cancelar pendentes troca (swap com revisão) e restaurar a configuração da ranhura de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Trocar as ranhuras de implementação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Eventos de troca de monitor na atividade de registo
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-slot"></a>Eliminar Bloco
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatizar com a CLI

Para [CLI do Azure](https://github.com/Azure/azure-cli) comandos para blocos de implementação, consulte [bloco de implementação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Trocas de resolução de problemas

Se ocorrer algum erro durante uma [comutação da ranhura](#what-happens-during-swap), é registado *D:\home\LogFiles\eventlog.xml*, bem como o registo de erros específicos de aplicativo.

Aqui estão alguns erros comuns de comutação:

- Excedeu o tempo limite é uma solicitação HTTP para a raiz do aplicativo. A operação de troca aguarda 90 segundos para cada solicitação HTTP e repete até 5 vezes. Se todas as repetições são o tempo limite, a operação de troca é abortada.

- Inicialização da local cache poderá falhar quando o conteúdo da aplicação excede a quota de disco local especificada para a cache local. Para obter mais informações, consulte [descrição geral de Local cache](overview-local-cache.md).

- Durante [aquecimento personalizado](#custom-warm-up), os pedidos HTTP são efetuados internamente (sem passar pelo URL externo) e pode falhar com o URL de determinadas regras de reescrita na *Web. config*. Por exemplo, as regras para redirecionar os nomes de domínio ou impor HTTPS podem impedir pedidos de aquecimento de acessar o código da aplicação em todos os. Para contornar este problema, modifique as regras de reescrita, adicionando duas condições seguintes:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem o aquecimento personalizado, os pedidos HTTP podem ainda sejam mantidos pelas regras de reescrita de URL. Para contornar este problema, modifique as regras de reescrita, adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Algumas [regras de restrição de IP](app-service-ip-restrictions.md) pode impedir que a operação de troca de enviar solicitações HTTP para a sua aplicação. Intervalos de endereços de IPv4 começados por `10.` e `100.` são internos para a implementação e deve ter permissão para ligar à sua aplicação.

## <a name="next-steps"></a>Passos Seguintes
[Bloquear o acesso a blocos de não produção](app-service-ip-restrictions.md)
