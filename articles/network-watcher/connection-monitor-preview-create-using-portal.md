---
title: Create Connection Monitor Preview - Portal Azure
titleSuffix: Azure Network Watcher
description: Saiba como criar o Monitor de Ligação (Pré-visualização) utilizando o portal Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363023"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Criar um Monitor de Ligação (Pré-visualização) utilizando o portal Azure

Saiba como criar o Connection Monitor (Preview) para monitorizar a comunicação entre os seus recursos utilizando o portal Azure. Suporta implantações de nuvem híbrida e Azure.

## <a name="before-you-begin"></a>Before you begin 

Nos monitores de ligação que cria no Connection Monitor (Preview), pode adicionar tanto as máquinas no local como os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Ligação (Pré-visualização) inclui as seguintes entidades:


* **Recurso de monitor** de ligação - Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final** – Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem 
    * VMs do Azure
    * Azure VNETs
    * Sub-redes Azure
    * Agentes no local
    * Subesí redes no local
    * Rede personalizada no local composta por várias sub-redes
    * URLs e IPs 
* **Configuração de teste** - Uma configuração específica do protocolo para um teste. Com base no protocolo que escolheu, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste** – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

    ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Passos para criar

Para criar um Monitor de Ligação (Pré-visualização) utilizando o portal Azure, siga os passos abaixo:

1. Na página inicial do portal Azure, vá ao **Network Watcher**.
1. À esquerda, na secção **de Monitorização,** selecione **Connection Monitor (Preview)**.
1. Vê todos os monitores de ligação que foram criados no Connection Monitor (Preview). Para ver os monitores de ligação que foram criados na experiência clássica do Connection Monitor, aceda ao separador **Monitor de Ligação.**

    ![Screenshot mostrando monitores de conexão que foram criados no Monitor de Ligação (Preview)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. No painel **de instrumentos do Monitor de Ligação (Pré-visualização),** no canto superior esquerdo, selecione **Criar**.

   ![Screenshot mostrando o separador Básico no Monitor de Ligação](./media/connection-monitor-2-preview/create-cm-basics.png)

1. No **separador Básicos,** introduza informações para o seu monitor de ligação:
   * **Nome do monitor de ligação** – Adicione o nome do seu monitor de ligação. Use as regras padrão de nomeação para recursos Azure.
   * **Subscrição** – Escolha uma subscrição para o seu monitor de ligação.
   * **Região** – Escolha uma região para o seu monitor de ligação. Pode selecionar apenas os VM de origem que são criados nesta região.
   * **Configuração do espaço de trabalho** - O seu espaço de trabalho contém os seus dados de monitorização. Você pode usar um espaço de trabalho personalizado ou o espaço de trabalho padrão. 
       * Para utilizar o espaço de trabalho predefinido, selecione a caixa de verificação. 
       * Para escolher um espaço de trabalho personalizado, limpe a caixa de verificação. Em seguida, escolha a subscrição e região para o seu espaço de trabalho personalizado. 
1. Na parte inferior da aba, selecione **Seguinte: Grupos de teste**.

   ![Screenshot mostrando o separador Grupo de Teste criar no Monitor de Ligação](./media/connection-monitor-2-preview/create-tg.png)

1. Adicione fontes, destinos e configurações de teste nos seus grupos de teste. Para configurar os seus grupos de teste, consulte [criar grupos de teste no Monitor de Ligação](#create-test-groups-in-a-connection-monitor). 
1. Na parte inferior do separador, selecione **Seguinte: Criar alertas**.

   ![Screenshot mostrando o painel onde cria alertas](./media/connection-monitor-2-preview/create-alert.png)

1. Para criar alertas, consulte [Criar Alertas no Monitor de Ligação](#create-alerts-in-connection-monitor)
1. Na parte inferior do separador, selecione **Seguinte: Revisão + criar**.

  ![Screenshot do Monitor de Ligação, mostrando o separador Review + create](./media/connection-monitor-2-preview/review-create-cm.png)

1. No **separador 'Rever +' criar,** rever as informações básicas e os grupos de teste antes de criar o monitor de ligação. Se precisar de editar o monitor de ligação:
   * Para editar detalhes básicos, selecione o ícone do lápis.
   * Para editar um grupo de teste, selecione-o.

   > [!NOTE] 
   > O **separador Review + create** mostra o custo por mês durante a fase de pré-visualização do Monitor de Ligação. Atualmente, a coluna **CUSTO CORRENTE** não apresenta qualquer custo. Quando o Monitor de Ligação estiver geralmente disponível, esta coluna apresentará uma carga mensal. 
   > 
   > Mesmo na fase de pré-visualização do Monitor de Ligação, aplicam-se os custos de ingestão do Log Analytics.

1. Quando estiver pronto para criar o monitor de ligação, na parte inferior do **separador 'Revisão + criar',** selecione **Criar**.

O Monitor de Ligação (Pré-visualização) cria o recurso do monitor de ligação em segundo plano.

## <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste num monitor de ligação

Cada grupo de teste num monitor de ligação inclui fontes e destinos que são testados em parâmetros de rede. São testados para a percentagem de verificações que falham e o RTT sobre as configurações de teste.

A partir do portal Azure, para criar um grupo de teste num monitor de ligação, especifique valores para os seguintes campos:

* **Desativar o Grupo de Teste** – Pode selecionar este campo para desativar a monitorização de todas as fontes e destinos que o grupo de teste especifica. Esta seleção é apurada por defeito.
* **Nome** - Nomeie o seu grupo de teste.
* **Fontes** – Pode especificar tanto os VMs Azure como as máquinas no local como fontes se os agentes forem instalados neles. Para instalar um agente para a sua fonte, consulte [os agentes de monitorização da Instalação](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Para escolher agentes Azure, selecione o **separador pontos finais Azure.** Aqui vê apenas VMs que estão ligados à região que especificou quando criou o monitor de ligação. Por padrão, os VMs são agrupados na subscrição a que pertencem. Estes grupos estão em colapso. 
   
       Pode aprofundar do nível de Subscrição para outros níveis na hierarquia:

      **Assinatura**  >  Grupos de **recursos**  >  **VNETs**  >  **Sub-redes**  >  **VMs com agentes**

      Também pode alterar o valor do **grupo por** campo para iniciar a árvore a partir de qualquer outro nível. Por exemplo, se agrupar por rede virtual, vê os VMs que têm agentes na hierarquia **VNETs**  >  **Subnets**  >  **VMs com agentes**.
       Selecionar um VNET ,subnet ou um único VM define o ID de recurso correspondente como ponto final. Por predefinição, todos os VMs na VNET ou sub-rede selecionada com extensão do Observador de Rede participam na monitorização. Para reduzir o âmbito, selecione sub-redes/agentes específicos ou altere o valor da propriedade de âmbito. 

      ![Screenshot do Monitor de Ligação, mostrando o painel Add Sources e o separador Azure Agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Para escolher agentes no local, selecione o **separador Agentes Não-Azure.** Por defeito, os agentes são agrupados em espaços de trabalho por região. Todos estes espaços de trabalho têm a solução Network Performance Monitor configurada. 
   
       Se precisar de adicionar monitor de desempenho de rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [soluções de monitorização no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Na visão **'Criar Monitor de Ligação',** no separador **Básicos,** a região predefinida é selecionada. Se mudar a região, pode escolher agentes de espaços de trabalho na nova região.  Pode selecionar um ou mais agentes ou sub-redes. Na vista da sub-rede, pode selecionar IPs específicos para monitorização. A adição de várias sub-redes criará uma rede personalizada no local, nomeadamente "OnPremises_Network_1". Também pode alterar o valor do **Grupo por** campo para grupo por sub-redes.

      ![Screenshot do Monitor de Ligação, mostrando o painel Add Sources e o separador Agentes Não Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * Também pode escolher pontos finais recentemente utilizados, utilizando o **separador Endpoint Recente** 
   
   * Quando terminar de configurar as fontes, selecione **'Fazer'.** Ainda pode editar propriedades básicas como o nome do ponto final clicando no ponto final na vista Do Grupo de Teste criar. 

* **Destinos** – Pode monitorizar a conectividade com VMs Azure, máquinas no local ou qualquer ponto final (um IP público, URL ou FQDN) especificando-os como destinos. Num único grupo de teste, pode adicionar VMs Azure, máquina no local, URLs Office 365, UrLs Dynamics 365 e pontos finais personalizados.

    * Para escolher VMs Azure como destinos, selecione o **separador Azure endpoints.** Por padrão, os VMs Azure são agrupados numa hierarquia de subscrição que está na mesma região que selecionou na visão **Do Monitor de Ligação Create,** no separador **Básicos.** Pode alterar a região e escolher VMs Azure da região recém-seleccionada. Em seguida, pode descer do nível de Subscrição para outros níveis na hierarquia, tal como os pontos finais de Azure de origem.
     Pode selecionar VNETs, sub-redes ou VMs individuais, semelhantes aos pontos finais Azure de origem. Selecionar um VNET ,subnet ou um único VM define o ID de recurso correspondente como ponto final. Por predefinição, todos os VMs na VNET ou sub-rede selecionada com extensão do Observador de Rede participam na monitorização. Para reduzir o âmbito, selecione sub-redes/agentes específicos ou altere o valor da propriedade de âmbito. 

       ![Screenshot do painel Add Destinations, mostrando o separador Azure VMs](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Screenshot do painel Add Destinations, mostrando o nível de subscrição](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Para escolher agentes não Azure como destinos, selecione o separador pontos finais Non-Azure. Por defeito, os agentes são agrupados em espaços de trabalho por região. Todos estes espaços de trabalho têm a solução Network Performance Monitor configurada. Se precisar de adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no Azure Marketplace. Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte soluções de monitorização no Azure Monitor. Na visão 'Criar Monitor de Ligação', no separador Básicos, a região predefinida é selecionada. Se mudar a região, pode escolher agentes de espaços de trabalho na nova região. Pode selecionar um ou mais agentes ou sub-redes. Na vista da sub-rede, pode selecionar IPs específicos para monitorização. A adição de várias sub-redes criará uma rede personalizada no local, nomeadamente "OnPremises_Network_1".  
    
     ![Screenshot do painel Add non Azure Destinations](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Para escolher pontos finais públicos como destinos, selecione o separador **Endereços Externos.** A lista de pontos finais inclui URLs de teste office 365 e URLs de teste Dynamics 365, agrupados pelo nome. Além destes pontos finais, pode escolher um ponto final que foi criado em outros grupos de teste no mesmo monitor de ligação. 
    
        Para adicionar um novo ponto final, no canto superior direito, selecione **+ Pontos de final**. Em seguida, forneça um nome de ponto final e URL, IP ou FQDN.

       ![Screenshot mostrando onde adicionar pontos finais públicos como destinos no Connection Monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Para escolher pontos finais recentemente utilizados, aceda ao **separador Pontos finais**   recentes.
    * Quando terminar de escolher destinos, selecione **'Fazer'.** Ainda pode editar propriedades básicas como o nome do ponto final clicando no ponto final na vista Do Grupo de Teste criar. 

* **Configurações de teste** – Pode associar uma ou mais configurações de teste num grupo de teste. Crie uma nova configuração de teste utilizando o separador "Nova configuração" ou utilize uma configuração de teste utilizada noutros grupos de teste no mesmo Monitor de Ligação através do separador "Escolha a existência".

    * **Nome** – Nomeie a configuração do teste.
    * **Protocolo** – Escolha TCP, ICMP ou HTTP. Para alterar HTTP para HTTPS, selecione **HTTP** como o protocolo e selecione **443** como porta.
        * **Criar configuração de teste de rede** – Esta caixa de verificação só aparece se selecionar **HTTP** no campo **Protocolo.** Selecione esta caixa para criar outra configuração de teste que utilize as mesmas fontes e destinos especificados em outros lugares na sua configuração. A configuração de teste recém-criada é nomeada `<the name of your test configuration>_networkTestConfig` .
        * **Desativar o traceroute** – Este campo aplica-se quando o protocolo é TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram topologia e hop-by-hop RTT.
    * **Porto de destino** – Pode personalizar este campo com uma porta de destino à sua escolha.
        * Ouvir na porta - Este campo aplica-se quando o protocolo é TCP. Selecione esta caixa para abrir a porta TCP escolhida se ainda não estiver aberta. 
    * **Frequência de Teste** – Utilize este campo para escolher a frequência com que as fontes irão pingar destinos no protocolo e na porta que especificou. Pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. Selecione o costume para introduzir a frequência da sua escolha entre 30 segundos a 30 minutos. As fontes testarão a conectividade com destinos com base no valor que escolher.  Por exemplo, se selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez num período de 30 segundos.
    * **Limiar de Sucesso** – Pode definir limiares nos seguintes parâmetros de rede:
       * **Verificações falhadas** – Desa esta cumpra a percentagem de verificações que podem falhar quando as fontes verificam a conectividade com os destinos utilizando os critérios especificados. No que diz o protocolo TCP ou ICMP, a percentagem de controlos falhados pode ser equiparada à percentagem de perda de pacotes. Para o protocolo HTTP, este campo representa a percentagem de pedidos HTTP que não receberam resposta.
       * **Tempo de ida e volta** – Desaconte o RTT em milissegundos para saber quanto tempo as fontes podem demorar a ligar-se ao destino através da configuração do teste.
    
       ![Screenshot mostrando onde configurar uma configuração de teste no Monitor de Ligação](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Criar alertas no monitor de ligação

Pode configurar alertas em testes que estão a falhar com base nos limiares definidos nas configurações de teste.   

A partir do portal Azure, para criar alertas num monitor de ligação, especifique valores para os seguintes campos: 

- Criar alerta - Pode selecionar este campo para criar um alerta métrico no Azure Monitor. A seleção deste facto permitirá a edição dos outros campos. Os encargos adicionais para alerta serão aplicáveis com base nos [preços dos alertas](https://azure.microsoft.com/pricing/details/monitor/) 

- Âmbito -> Recurso e Âmbito -> Hierarquia - Este é preenchido com base nos valores especificados no separador Básicos 

- Condição -> O alerta é criado na métrica "Resultado de Teste(pré-visualização)". Quando o resultado do teste do monitor de ligação estiver falhado, a regra de alerta será disparada. 

- Grupo de Ação - Pode optar por inserir o seu e-mail diretamente ou pode optar por criar alertas através de grupos de ação. Se optar por inserir o seu e-mail diretamente, é criado um grupo de ação com o nome NPM Email ActionGroup e o ID de e-mail é adicionado a esse grupo de ação. Se optar por utilizar grupos de ação, terá de selecionar um grupo de ação previamente criado. Pode aprender a criar um grupo de ação aqui. Uma vez criado o alerta com sucesso, pode utilizar o link 'Gerir alertas' para gerir os seus alertas. 

- Nome da regra do alerta - Nome do monitor de ligação 

- Permitir a regra sobre a criação - Isto permitirá a regra de alerta com base na condição. Desative isto se quiser criar a regra, mas não a ative. 

    ![Screenshot mostrando a criação de alerta no painel do Monitor de Ligação](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm os seguintes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 2 através do portal Azure

## <a name="next-steps"></a>Passos seguintes

* Saiba [como analisar dados de monitorização e definir alertas](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas na sua rede](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
