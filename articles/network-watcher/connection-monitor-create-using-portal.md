---
title: Criar Monitor de Conexão - Portal Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreve como criar um monitor no Monitor de Ligação utilizando o portal Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: bd13712d137ec5a1fdfa6dec8e6f6d1e0a7432cb
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833172"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Criar um monitor no Monitor de Ligação utilizando o portal Azure

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Também não poderá adicionar novos monitores de ligação no Connection Monitor (clássico). Pode continuar a utilizar os testes e monitores de ligação criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migrar do Connection Monitor (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo Monitor de Ligação no Observador de Rede Azure antes de 29 de fevereiro de 2024.

Saiba como utilizar o Connection Monitor para monitorizar a comunicação entre os seus recursos. Este artigo descreve como criar um monitor utilizando o portal Azure. O Connection Monitor suporta implementações de nuvem híbrida e Azure.


## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de ligação que cria utilizando o Connection Monitor, pode adicionar tanto as máquinas no local como os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem ser em Azure ou em qualquer outro URL ou IP.

Aqui estão algumas definições para começar:

* **Recurso do monitor de ligação**. Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final**. Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem:
    * VMs Azure.
    * Redes virtuais Azure.
    * Sub-redes Azure.
    * Agentes no local.
    * Sub-redes no local.
    * No local, redes personalizadas que incluem várias sub-redes.
    * URLs e IPs.
* **Configuração do teste**. Uma configuração específica do protocolo para um teste. Com base no protocolo que escolher, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste**. O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Test** (Testar). A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagrama que mostra um monitor de ligação e define a relação entre grupos de teste e testes.":::


## <a name="create-a-connection-monitor"></a>Criar um monitor de ligação

Para criar um monitor no Monitor de Ligação utilizando o portal Azure:

1. Na página inicial do portal Azure, vá ao **Network Watcher**.
1. No painel esquerdo, na secção **de monitorização,** selecione **Monitor de ligação**.

   Verá todos os monitores de ligação que foram criados no Connection Monitor. Para ver os monitores de ligação que foram criados no clássico Monitor de Ligação, aceda ao **separador Monitor de Ligação.**

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot que mostra monitores de conexão criados no Connection Monitor.":::
   
    
1. No painel **'Monitor de** Ligação', no canto superior esquerdo, selecione **Criar**.

   

1. No **separador Básicos,** introduza informações para o seu monitor de ligação: 
   * **Nome do monitor de ligação**: Introduza um nome para o seu monitor de ligação. Use as regras padrão de nomeação para recursos Azure.
   * **Subscrição**: Selecione uma subscrição para o seu monitor de ligação.
   * **Região**: Selecione uma região para o seu monitor de ligação. Pode selecionar apenas os VM de origem que são criados nesta região.
   * **Configuração do espaço de trabalho**: Escolha um espaço de trabalho personalizado ou o espaço de trabalho predefinido. O seu espaço de trabalho contém os seus dados de monitorização.
       * Para utilizar o espaço de trabalho predefinido, selecione a caixa de verificação. 
       * Para escolher um espaço de trabalho personalizado, limpe a caixa de verificação. Em seguida, selecione a subscrição e a região para o seu espaço de trabalho personalizado. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Screenshot que mostra o separador Básico no Monitor de Ligação.":::
   
1. Na parte inferior da aba, selecione **Seguinte: Grupos de teste**.

1. Adicione fontes, destinos e configurações de teste nos seus grupos de teste. Para saber mais sobre a configuração dos seus grupos de teste, consulte [Criar grupos de teste no Monitor de Ligação](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Screenshot que mostra o separador grupos de teste no Monitor de Ligação.":::

1. Na parte inferior do separador, selecione **Seguinte: Criar alertas**. Para saber sobre a criação de alertas, consulte [Criar alertas no Monitor de Ligação.](#create-alerts-in-connection-monitor)

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Screenshot que mostra o separador de alerta Criar.":::

1. Na parte inferior do separador, selecione **Seguinte: Revisão + criar**.

1. No **separador 'Rever +' criar,** rever as informações básicas e os grupos de teste antes de criar o monitor de ligação. Se precisar de editar o monitor de ligação, pode fazê-lo voltando aos respetivos separadores. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Screenshot que mostra o separador Review + criar no Monitor de Ligação.":::
   > [!NOTE] 
   > O **separador Review + create** mostra o custo por mês durante a fase do Monitor de Ligação. Atualmente, a coluna **Custo Corrente/Mês** não apresenta qualquer custo. Quando o Monitor de Ligação estiver geralmente disponível, esta coluna apresentará uma carga mensal. 
   > 
   > Mesmo durante a fase do Monitor de Ligação, aplicam-se os custos de ingestão do Log Analytics.

1. Quando estiver pronto para criar o monitor de ligação, na parte inferior do **separador 'Revisão + criar',** selecione **Criar**.

O Monitor de Ligação cria o recurso do monitor de ligação em segundo plano.

## <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste num monitor de ligação

Cada grupo de teste num monitor de ligação inclui fontes e destinos que são testados em parâmetros de rede. São testados para a percentagem de verificações que falham e o RTT sobre as configurações de teste.

No portal Azure, para criar um grupo de teste num monitor de ligação, especifique valores para os seguintes campos:

* **Grupo de teste desativação**: Pode selecionar esta caixa de verificação para desativar a monitorização de todas as fontes e destinos que o grupo de teste especifica. Esta seleção é apurada por defeito.
* **Nome:** Nomeie o seu grupo de teste.
* **Fontes**: Pode especificar tanto os VMs Azure como as máquinas no local como fontes se os agentes forem instalados neles. Para saber mais sobre a instalação de um agente para a sua fonte, consulte [os agentes de monitorização da Instalação](./connection-monitor-overview.md#install-monitoring-agents).
   * Para escolher agentes Azure, selecione o **separador pontos finais Azure.** Aqui vê apenas VMs que estão ligados à região que especificou quando criou o monitor de ligação. Por padrão, os VMs são agrupados na subscrição a que pertencem. Estes grupos estão em colapso. 
   
       Pode aprofundar do nível de **Subscrição** para outros níveis na hierarquia:

      **Assinatura**  >  **Grupo de recursos**  >  **VNET**  >  **Sub-rede**  >  **VMs com agentes**

      Também pode alterar o **Grupo por** seletor para iniciar a árvore a partir de qualquer outro nível. Por exemplo, se agrupar por rede virtual, vê os VMs que têm agentes na hierarquia **VNET**  >  **Subnet**  >  **VMs com agentes**.

       Quando seleciona um VNET, sub-rede ou VM único, o ID de recurso correspondente é definido como ponto final. Por predefinição, todos os VMs na VNET ou sub-rede selecionada que tenham a extensão do Observador da Rede Azure participam na monitorização. Para reduzir o âmbito, selecione sub-redes ou agentes específicos ou altere o valor da propriedade de âmbito. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Screenshot que mostra o painel Add Sources e o separador pontos finais Azure no Monitor de Ligação.":::

   * Para escolher agentes no local, selecione o **separador pontos finais Non-Azure.** Por defeito, os agentes são agrupados em espaços de trabalho por região. Todos estes espaços de trabalho têm o Monitor de Desempenho de Rede configurado. 
   
       Se precisar de adicionar monitor de desempenho de rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [soluções de monitorização no Azure Monitor](../azure-monitor/insights/solutions.md). 
   
       No **Monitor de Ligação Create**, no separador **Básicos,** a região predefinida é selecionada. Se mudar a região, pode escolher agentes de espaços de trabalho na nova região. Pode selecionar um ou mais agentes ou sub-redes. Na vista **Subnet,** pode selecionar IPs específicos para monitorização. Se adicionar várias sub-redes, será criada uma rede personalizada no local chamada **OnPremises_Network_1.** Também pode alterar o **Grupo por** selecionador para grupo por agentes.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Screenshot que mostra o painel Add Sources e o separador pontos finais não-Azure no Monitor de Ligação.":::

   * Para escolher pontos finais recentemente utilizados, pode utilizar o **separador ponto final recente** 
   
   * Quando terminar de configurar as fontes, selecione **'Fazer'** na parte inferior do separador. Ainda pode editar propriedades básicas como o nome do ponto final selecionando o ponto final na vista **Do Grupo de Teste criar.** 

* **Destinos**: Pode monitorizar a conectividade a um Azure VM, uma máquina no local ou qualquer ponto final (um IP público, URL ou FQDN) especificando-o como um destino. Num único grupo de teste, pode adicionar VMs Azure, máquinas no local, URLs Office 365, UrLs Dynamics 365 e pontos finais personalizados.

    * Para escolher VMs Azure como destinos, selecione o **separador Azure endpoints.** Por padrão, os VMs Azure são agrupados numa hierarquia de subscrição que está na região que selecionou no **Monitor de Ligação Create** no **separador Básicos.** Pode mudar a região e escolher VMs Azure da nova região. Em seguida, pode perfurar do nível de **Subscrição** para outros níveis na hierarquia, assim como pode quando definir os pontos finais Azure fonte.

      Pode selecionar VNETs, sub-redes ou VMs individuais, como pode quando definir os pontos finais Azure de origem. Quando seleciona um VNET, sub-rede ou VM único, o ID de recurso correspondente é definido como ponto final. Por predefinição, todos os VMs na VNET ou sub-rede selecionada que tenham a extensão do Observador de Rede participam na monitorização. Para reduzir o âmbito, selecione sub-redes ou agentes específicos ou altere o valor da propriedade de âmbito. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<Screenshot que mostra o painel Add Destinations e o separador Azure endpoints.>":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<Screenshot que mostra o painel de Destinos adicionais ao nível de subscrição.>":::
       
    
    * Para escolher agentes não-Azure como destinos, selecione o **separador pontos finais Non-Azure.** Por defeito, os agentes são agrupados em espaços de trabalho por região. Todos estes espaços de trabalho têm o Monitor de Desempenho da Rede configurado. 
    
      Se precisar de adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no Azure Marketplace. Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [soluções de monitorização no Azure Monitor](../azure-monitor/insights/solutions.md). 

      No **Monitor de Ligação Create**, no separador **Básicos,** a   região predefinida é selecionada. Se mudar a região, pode escolher agentes de espaços de trabalho na nova região. Pode selecionar um ou mais agentes ou sub-redes. Na vista **Subnet,** pode selecionar IPs específicos para monitorização. Se adicionar várias sub-redes, será criada uma rede personalizada no local chamada **OnPremises_Network_1.**  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Screenshot que mostra o painel Add Destinations e o separador pontos finais não-Azure.":::
    
    * Para escolher pontos finais públicos como destinos, selecione o separador **Endereços Externos.** A lista de pontos finais inclui URLs de teste office 365 e URLs de teste Dynamics 365, agrupados pelo nome. Também pode escolher pontos finais que foram criados em outros grupos de teste no mesmo monitor de ligação. 
    
        Para adicionar um ponto final, no canto superior direito, selecione **Add Endpoint**. Em seguida, forneça um nome de ponto final e URL, IP ou FQDN.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Screenshot que mostra onde adicionar pontos finais públicos como destinos no Connection Monitor.":::

    * Para escolher os pontos finais recentemente utilizados, aceda ao **separador ponto final recente.**  
    * Quando terminar de escolher destinos, selecione **'Fazer'.** Ainda pode editar propriedades básicas como o nome do ponto final selecionando o ponto final na vista **Do Grupo de Teste criar.** 

* **Configurações de teste**: Pode adicionar uma ou mais configurações de teste a um grupo de teste. Crie uma nova configuração de teste utilizando o **separador de configuração Novo.** Ou adicione uma configuração de teste de outro grupo de teste no mesmo Monitor de Ligação do **separador Escolha.**

    * **Nome da configuração do teste**: Nomeie a configuração do teste.
    * **Protocolo**: Selecione **TCP,** **ICMP,** ou **HTTP**. Para alterar HTTP para HTTPS, selecione **HTTP** como o protocolo e, em seguida, selecione **443** como porta.
        * **Criar configuração de teste TCP**: Esta caixa de verificação só aparece se selecionar **HTTP** na lista **de protocolos.** Selecione esta caixa de verificação para criar outra configuração de teste que utilize as mesmas fontes e destinos especificados em outros lugares na sua configuração. A nova configuração do teste chama-se **\<name of test configuration> _networkTestConfig**.
        * **Desativar o rasto**: Esta caixa de verificação aplica-se quando o protocolo é TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram topologia e hop-by-hop RTT.
    * **Porto de destino**: Você pode fornecer um porto de destino à sua escolha.
        * **Ouvir na porta**: Esta caixa de verificação aplica-se quando o protocolo é TCP. Selecione esta caixa de verificação para abrir a porta TCP escolhida se ainda não estiver aberta. 
    * **Frequência de teste**: Nesta lista, especifique com que frequência as fontes irão pingar destinos no protocolo e na porta que especificou. Pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. Selecione **o costume** para introduzir outra frequência que está entre 30 segundos e 30 minutos. As fontes testarão a conectividade com destinos com base no valor que escolher. Por exemplo, se selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em cada período de 30 segundos.
    * **Limiar de sucesso**: Pode definir limiares nos seguintes parâmetros de rede:
       * **Verificações falhadas**: Desa esta medida de verificação que pode falhar quando as fontes verificam a conectividade com os destinos utilizando os critérios especificados. Para o protocolo TCP ou ICMP, a percentagem de controlos falhados pode ser equiparada à percentagem de perda de pacotes. Para o protocolo HTTP, este valor representa a percentagem de pedidos HTTP que não receberam resposta.
       * **Tempo de viagem de ida** e volta : Desaconte o RTT, em milissegundos, para saber quanto tempo as fontes podem demorar a ligar-se ao destino através da configuração do teste.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Screenshot que mostra onde configurar uma configuração de teste no Monitor de Ligação.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Criar alertas no Monitor de Ligação

Pode configurar alertas em testes que estão a falhar com base nos limiares definidos nas configurações de teste.

No portal Azure, para criar alertas para um monitor de ligação, especifique valores para estes campos: 

- **Criar alerta**: Pode selecionar esta caixa de verificação para criar um alerta métrico no Azure Monitor. Quando selecionar esta caixa de verificação, os outros campos serão ativados para edição. Serão aplicáveis taxas adicionais para o alerta, com base no [preço dos alertas.](https://azure.microsoft.com/pricing/details/monitor/) 

- **Âmbito de aplicação**  >  **Recurso**  >  **Hierarquia**: Estes valores são preenchidos automaticamente, com base nos valores especificados no separador **Básicos.**

- **Nome da condição**: O alerta é criado na `Test Result(preview)` métrica. Quando o resultado do teste do monitor de ligação for um resultado falhado, a regra de alerta dispara. 

- **Nome do grupo de** ação : Pode introduzir o seu e-mail diretamente ou pode criar alertas através de grupos de ação. Se introduzir o seu e-mail diretamente, será criado um grupo de ação com o nome **NPM Email ActionGroup.** O ID de e-mail é adicionado a esse grupo de ação. Se optar por utilizar grupos de ação, tem de selecionar um grupo de ação previamente criado. Para aprender a criar um grupo de ação, consulte [Criar grupos de ação no portal Azure.](../azure-monitor/platform/action-groups.md) Após a criação do alerta, pode [gerir os seus alertas](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Nome da regra de alerta**: O nome do monitor de ligação.

- **Ativar a regra após a criação**: Selecione esta caixa de verificação para ativar a regra de alerta com base na condição. Desative esta caixa de verificação se pretender criar a regra sem a ativar. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Screenshot que mostra o separador de alerta Criar no Monitor de Ligação.":::

## <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm estes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 2 através do portal Azure

## <a name="next-steps"></a>Passos seguintes

* Saiba [como analisar os dados de monitorização e definir alertas.](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas na sua rede.](./connection-monitor-overview.md#diagnose-issues-in-your-network)
