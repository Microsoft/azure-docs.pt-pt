---
title: Monitor de conexão (pré-visualização) / Microsoft Docs
description: Saiba como utilizar o Connection Monitor (Preview) para monitorizar a comunicação da rede num ambiente distribuído.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 80934dca73d7f8a205c62a49c418828cab1820e7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447826"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorização da conectividade da rede com monitor de ligação (pré-visualização)

O Monitor de Ligação (Preview) fornece uma monitorização unificada de ligação de ponta a ponta no Observador de Rede Azure. A funcionalidade Connection Monitor (Preview) suporta implementações híbridas e azure em nuvem. O Network Watcher fornece ferramentas para monitorizar, diagnosticar e visualizar métricas relacionadas com a conectividade para as suas implementações Azure.

Aqui estão alguns casos de utilização para Monitor de Ligação (Pré-visualização):

- O VM do seu servidor web frontal comunica com um VM de servidor de base de dados numa aplicação de vários níveis. Pretende verificar a conectividade da rede entre os dois VMs.
- Você quer VMs na região leste dos EUA para ping VMs na região central dos EUA, e você quer comparar latências de rede cross-region.
- Tem vários escritórios no local em Seattle, Washington, e em Ashburn, Virgínia. Os seus sites de escritório ligam-se aos URLs Microsoft 365. Para os seus utilizadores de URLs Microsoft 365, compare as latências entre Seattle e Ashburn.
- A sua aplicação híbrida necessita de conectividade para um ponto final de armazenamento Azure. O seu site no local e a sua aplicação Azure ligam-se ao mesmo ponto final de armazenamento Azure. Você quer comparar as latências do local com as latências da aplicação Azure.
- Pretende verificar a conectividade entre as configurações no local e os VMs Azure que acolhem a sua aplicação em nuvem.

Na sua fase de pré-visualização, o Connection Monitor combina o melhor de duas funcionalidades: a funcionalidade Monitor de [Ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) do Observador de Rede e o Monitor de Conectividade de Serviço (NPM) Monitor de [Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity), [Monitorização ExpressRoute](https://docs.microsoft.com/azure/expressroute/how-to-npm)e [Monitorização de Desempenho.](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-performance-monitor)

Aqui estão alguns benefícios do Monitor de Ligação (Visualização):

* Experiência unificada e intuitiva para necessidades de monitorização Azure e híbridas
* Monitorização da conectividade transversal e transversal do espaço de trabalho
* Frequências de sondagem mais elevadas e melhor visibilidade no desempenho da rede
* Alerta mais rápido para as suas implementações híbridas
* Suporte para verificações de conectividade baseadas em HTTP, TCP e ICMP 
* Métricas e suporte de Log Analytics tanto para configurações de teste Azure como não-Azure

![Diagrama mostrando como o Monitor de Ligação interage com VMs Azure, anfitriões não-Azure, pontos finais e locais de armazenamento de dados](./media/connection-monitor-2-preview/hero-graphic.png)

Para começar a utilizar o Monitor de Ligação (Pré-visualização) para monitorização, siga estes passos: 

1. Instale agentes de monitorização.
1. Ativar o Observador de Rede na sua subscrição.
1. Crie um monitor de ligação.
1. Configurar análise de dados e alertas.
1. Diagnostice problemas na sua rede.

As seguintes secções fornecem detalhes para estes passos.

## <a name="install-monitoring-agents"></a>Instalar agentes de monitorização

O Monitor de Ligação baseia-se em ficheiros leves executáveis para executar verificações de conectividade.  Suporta controlos de conectividade tanto dos ambientes Azure como dos ambientes no local. O ficheiro executável que utiliza depende se o seu VM está hospedado no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais Azure

Para que o Monitor de Ligação reconheça os seus VMs Azure como fontes de monitorização, instale a extensão da máquina virtual do Network Watcher. Esta extensão também é conhecida como a *extensão do Observador de Rede*. As máquinas virtuais Azure requerem a extensão para desencadear a monitorização de ponta a ponta e outras funcionalidades avançadas. 

Pode instalar a extensão do Observador de Rede quando [criar um VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Também pode instalar, configurar e resolver problemas separadamente a extensão do Observador de Rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

As regras para um grupo de segurança de rede (NSG) ou firewall podem bloquear a comunicação entre a fonte e o destino. O Monitor de Ligação deteta este problema e mostra-o como uma mensagem de diagnóstico na topologia. Para ativar a monitorização da ligação, certifique-se de que as regras de NSG e firewall permitem pacotes sobre TCP ou ICMP entre a fonte e o destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para máquinas no local

Para que o Monitor de Ligação reconheça as suas máquinas no local como fontes de monitorização, instale o agente Log Analytics nas máquinas. Em seguida, ative a solução Monitor de Desempenho da Rede. Estes agentes estão ligados aos espaços de trabalho do Log Analytics, por isso é necessário configurar o ID do espaço de trabalho e a chave primária antes que os agentes possam começar a monitorizar.

Para instalar o agente Log Analytics para máquinas Windows, consulte [a extensão da máquina virtual do Azure Monitor para windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se o caminho incluir firewalls ou aparelhos virtuais de rede (NVAs), certifique-se de que o destino está acessível.

## <a name="enable-network-watcher-on-your-subscription"></a>Ativar o Observador de Rede na sua subscrição

Todas as subscrições que tenham uma rede virtual estão ativadas com o Network Watcher. Quando cria uma rede virtual na sua subscrição, o Network Watcher é automaticamente ativado na região e subscrição da rede virtual. Esta ativação automática não afeta os seus recursos nem incorre numa cobrança. Certifique-se de que o Network Watcher não está explicitamente desativado na sua subscrição. 

Para obter mais informações, consulte [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Criar um monitor de ligação 

O Monitor de Ligação monitoriza a comunicação a intervalos regulares. Informa-o das mudanças na capacidade de acesso e latência. Também pode verificar a topologia da rede atual e histórica entre agentes de origem e pontos finais de destino.

As fontes podem ser VMs Azure ou máquinas no local que tenham um agente de monitorização instalado. Os pontos finais de destino podem ser Microsoft 365 URLs, Dynamics 365 URLs, URLs personalizados, IDs de recursos VM Azure, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="access-connection-monitor-preview"></a>Monitor de ligação de acesso (pré-visualização)

1. Na página inicial do portal Azure, vá ao **Network Watcher**.
1. À esquerda, na secção **de Monitorização,** selecione **Connection Monitor (Preview)**.
1. Vê todos os monitores de ligação que foram criados no Connection Monitor (Preview). Para ver os monitores de ligação que foram criados na experiência clássica do Connection Monitor, aceda ao separador **Monitor de Ligação.**
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot mostrando monitores de conexão que foram criados no Monitor de Ligação (Preview)" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Criar um monitor de ligação

Nos monitores de ligação que cria no Connection Monitor (Preview), pode adicionar tanto as máquinas no local como os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Ligação (Pré-visualização) inclui as seguintes entidades:

* **Recurso de monitor** de ligação - Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final** – Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem VMs Azure, agentes no local, URLs e IPs.
* **Configuração de teste** - Uma configuração específica do protocolo para um teste. Com base no protocolo que escolheu, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste** – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

 ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

Pode criar uma pré-visualização do monitor de ligação utilizando [o portal Azure](connection-monitor-preview-create-using-portal.md) ou [ARMClient](connection-monitor-preview-create-using-arm-client.md)

Todas as fontes, destinos e configurações de teste que adiciona a um grupo de teste são discriminadas a testes individuais. Aqui está um exemplo de como as fontes e destinos são divididos:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configurações de teste: 2 (Config 1, Config 2)
* Total de testes criados: 12

| Número do teste | Origem | Destino | Configuração do teste |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | N | D | Config 2 |
| 7 | N | E | Config 1 |
| 8 | N | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm os seguintes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analisar dados de monitorização e definir alertas

Depois de criar um monitor de ligação, as fontes verificam a conectividade com os destinos com base na configuração do teste.

### <a name="checks-in-a-test"></a>Verifica num teste

Com base no protocolo que escolheu na configuração do teste, o Connection Monitor (Preview) executa uma série de verificações para o par de destino de origem. Os cheques são executados de acordo com a frequência de teste que escolheu.

Se utilizar HTTP, o serviço calcula o número de respostas HTTP que devolveram um código de resposta válido. Podem ser definidos códigos de resposta válidos utilizando o PowerShell e o CLI. O resultado determina a percentagem de verificações falhadas. Para calcular o RTT, o serviço mede o tempo entre uma chamada HTTP e a resposta.

Se utilizar O TCP ou o ICMP, o serviço calcula a percentagem de perda de pacote para determinar a percentagem de verificações falhadas. Para calcular o RTT, o serviço mede o tempo necessário para receber o reconhecimento (ACK) para os pacotes que foram enviados. Se ativar os dados de rastreamento para os seus testes de rede, pode ver perda de hop-by-hop e latência para a sua rede no local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados que os cheques devolvem, os testes podem ter os seguintes estados:

* **Passe** – Os valores reais para a percentagem de verificações falhadas e o RTT estão dentro dos limiares especificados.
* **Falha** – Os valores reais para a percentagem de verificações falhadas ou rtt excederam os limiares especificados. Se não for especificado um limiar, um teste atinge o estado de Falha quando a percentagem de controlos falhados é de 100.
* **Aviso** – 
     * Se o limiar for especificado e o Monitor de Ligação (Preview) observar os controlos falhados por cento mais de 80% do limiar, o teste é marcado como aviso.
     * Na ausência de limiares especificados, o Monitor de Ligação (Pré-visualização) atribui automaticamente um limiar. Quando esse limiar for ultrapassado, o estado do teste muda para Aviso.Para o tempo de ida e volta nos testes TCP ou ICMP, o limiar é de 750msec. No que diz a falta de cheques por cento, o limiar é de 10%. 
* **Indeterminado**   – Sem dados no Log Analytics Workspace.Verifique as métricas. 
* **Não correr**   – Deficiente por desativar o grupo de teste  

### <a name="data-collection-analysis-and-alerts"></a>Recolha, análise e alertas de dados

Os dados que o Monitor de Ligação (Preview) recolhe são armazenados no espaço de trabalho Do Log Analytics. Você configura este espaço de trabalho quando criou o monitor de ligação. 

Os dados de monitorização também estão disponíveis em Métricas do Monitor Azure. Pode utilizar o Log Analytics para manter os seus dados de monitorização durante o tempo que desejar. O Azure Monitor armazena métricas por apenas 30 dias por defeito. 

Pode [definir alertas baseados em métricas nos dados.](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)

#### <a name="monitoring-dashboards"></a>Painéis de monitorização

Nos painéis de monitorização, vê uma lista dos monitores de ligação a que pode aceder para as suas assinaturas, regiões, selos de tempo, fontes e tipos de destino.

Quando vai ao Monitor de Ligação (Pré-visualização) do Observador de Redes, pode visualizar dados por:

* **Monitor de conexão** – Lista de todos os monitores de ligação criados para as suas subscrições, regiões, selos de tempo, fontes e tipos de destino. Esta vista é o padrão.
* **Grupos de teste** – Lista de todos os grupos de teste criados para as suas subscrições, regiões, selos de tempo, fontes e tipos de destino. Estes grupos de teste não são filtrados por monitores de ligação.
* **Teste** – Lista de todos os testes que funcionam para as suas assinaturas, regiões, selos temporais, fontes e tipos de destino. Estes testes não são filtrados por monitores de ligação ou grupos de teste.

Na imagem a seguir, as três vistas de dados são indicadas pela seta 1.

No painel de instrumentos, pode expandir cada monitor de ligação para ver os seus grupos de teste. Depois pode expandir cada grupo de teste para ver os testes que nele funcionam. 

Pode filtrar uma lista com base em:

* **Filtros de nível superior** – Lista de pesquisa por texto, tipo de entidade (Monitor de ligação, grupo de teste ou teste) de timetamp. O âmbito inclui assinaturas, regiões, fontes e tipos de destino. Ver a caixa 1 na imagem a seguir.
* **Filtros estatais** – Filtrar pelo estado do monitor de ligação, grupo de teste ou teste. Ver caixa 2 na imagem a seguir.
* **Filtro baseado em alerta** - Filtrar por alertas disparados sobre o recurso do monitor de ligação. Ver a caixa 3 na imagem a seguir.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Screenshot mostrando como filtrar vistas de monitores de ligação, grupos de teste e testes no Monitor de Ligação (Preview)" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Por exemplo, para ver todos os testes no Monitor de Ligação (Pré-visualização) onde o IP de origem é 10.192.64.56:
1. Alterar a vista para **Testar**.
1. No campo de pesquisa, tipo *10.192.64.56*
1. No **Âmbito do** filtro de nível superior, selecione **Fontes**.

Para mostrar apenas testes falhados no Monitor de Ligação (Pré-visualização) onde o IP de origem é 10.192.64.56:
1. Alterar a vista para **Testar**.
1. Para o filtro estatal, selecione **Fail**.
1. No campo de pesquisa, tipo *10.192.64.56*
1. No **Âmbito do** filtro de nível superior, selecione **Fontes**.

Para mostrar apenas testes falhados no Monitor de Ligação (Pré-visualização) onde o destino é outlook.office365.com:
1. Alterar a vista para **o Teste**.
1. Para o filtro estatal, selecione **Fail**.
1. No campo de pesquisa, *introduza outlook.office365.com*
1. No **Âmbito do** filtro de nível superior, selecione **Destinos**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Screenshot mostrando uma vista que é filtrada para mostrar apenas testes falhados para o destino Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Para saber a razão da falha de um monitor de ligação ou grupo de teste ou teste, clique na coluna chamada reason.  Isto indica qual o limiar (verificações falhadas % ou RTT) e mensagens de diagnóstico relacionadas
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Screenshot mostrando razão de falha para um monitor de ligação, teste ou grupo de teste" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Para visualizar as tendências do RTT e a percentagem de verificações falhadas de um monitor de ligação:
1. Selecione o monitor de ligação que pretende investigar.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Screenshot mostrando métricas para um monitor de ligação, exibido por grupo de teste" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Verá as seguintes secções  
    1. Essenciais - Propriedades específicas do recurso do monitor de conexão selecionado 
    1. Resumo - 
        1. Tendências agregadas para RTT e percentagem de verificações falhadas para todos os testes no monitor de ligação. Pode definir uma hora específica para ver os detalhes.
        1. Top 5 entre grupos de teste, fontes e destinos com base no RTT ou percentagem de verificações falhadas. 
    1. Separadores para grupos de teste , Fontes, Destinos e Configurações de Teste- Lista grupos de teste, fontes ou destinos no Monitor de Ligação. Os testes de verificação falharam, agregaram o RTT e os controlos falharam % dos valores.  Também pode voltar no tempo para ver os dados. 
    1. Problemas - Problemas de nível de lúpulo para cada teste no Monitor de Ligação. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Screenshot mostrando métricas para um monitor de ligação, exibido pela parte do grupo de teste 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. É possível
    * Clique em Ver todos os testes - para ver todos os testes no Monitor de Ligação
    * Clique em Ver todos os grupos de teste, configurações de teste, fontes e destinos - para ver detalhes específicos de cada um. 
    * Escolha um grupo de teste, configuração de teste, origem ou destino - para ver todos os testes na entidade.

1. A partir da vista de todos os testes, você pode:
    * Selecione testes e clique em comparação.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Screenshot mostrando comparação de 2 testes" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Utilize cluster para expandir recursos compostos como vnet, subnets aos seus recursos infantis
    * Ver topologia para quaisquer testes clicando em topologia.

Para visualizar as tendências do RTT e a percentagem de controlos falhados para um grupo de ensaio:
1. Selecione o grupo de teste que pretende investigar. 
1. Você verá semelhante ao monitor de ligação - essencial, resumo, tabela para grupos de teste, fontes, destinos e configurações de teste. Navegue-os como faria para um monitor de ligação

Para visualizar as tendências do RTT e a percentagem de verificações falhadas para um teste:
1. Selecione o teste que pretende investigar. Você verá a topologia da rede e o fim para terminar os gráficos de tendência para verificações falhou % e tempo de viagem de ida e volta. Para ver os problemas identificados, na topologia, selecione qualquer salto no caminho. (Estes lúpulo são recursos Azure.) Esta funcionalidade não está atualmente disponível para redes no local

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Screenshot mostrando vista topologia de um teste" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Consultas de registo em Log Analytics

Utilize o Log Analytics para criar visualizações personalizadas dos seus dados de monitorização. Todos os dados que a UI exibe são do Log Analytics. Pode analisar interativamente dados no repositório. Correlacionar os dados do Agent Health ou outras soluções que se baseiem no Log Analytics. Exporte os dados para Excel ou Power BI, ou crie um link partilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Nos monitores de ligação que foram criados antes da experiência do Monitor de Ligação (Pré-visualização), as quatro métricas estão disponíveis: % Sondas Falhadas, MédiasRoundtripMs, ChecksFailedPercent (Preview) e RoundTripTimeMs (Preview). Nos monitores de ligação que foram criados na experiência Connection Monitor (Preview), os dados só estão disponíveis para as métricas que estão marcadas com *(Preview)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Screenshot mostrando métricas no Monitor de Ligação (Pré-visualização)" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Quando utilizar métricas, desenhe o tipo de recurso como Microsoft.Network/networkWatchers/connectionMonitors

| Métrica | Nome a apresentar | Unidade | Tipo de agregação | Description | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Sondas Falhadas | Percentagem | Média | A percentagem de sondas de monitorização de conectividade falhou. | Sem dimensões |
| Médias Desvelundos | Avg. Tempo de ida e volta (ms) | Milissegundos | Média | Rede média RTT para sondas de monitorização de conectividade enviadas entre a fonte e o destino. |             Sem dimensões |
| ChecksFailedPercent (Pré-visualização) | % Verificações falhadas (pré-visualização) | Percentagem | Média | Percentagem de cheques falhados para um teste. | ConnectionMonitorResourceId <br>FonteAddress <br>Nome fonte <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinoAddress <br>Nome de destino <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |
| RoundTripTimeMs (Pré-visualização) | Tempo de ida e volta (ms) (Pré-visualização) | Milissegundos | Média | RTT para verificações enviadas entre a fonte e o destino. Este valor não é mediado. | ConnectionMonitorResourceId <br>FonteAddress <br>Nome fonte <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinoAddress <br>Nome de destino <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Alertas métricos para o Monitor de Ligação

Pode criar alertas métricos em monitores de ligação utilizando os métodos abaixo 

1. Do Monitor de Ligação (Preview), durante a criação do Monitor de [Ligação utilizando o portal Azure](connection-monitor-preview-create-using-portal.md#) 
1. Do Monitor de Ligação (Pré-visualização), utilizando "Alertas de Configuração" no painel de instrumentos 
1. Do Monitor Azure - Para criar um alerta no Monitor Azure: 
    1. Escolha o recurso do monitor de ligação que criou no Monitor de Ligação (Preview).
    1. Certifique-se de que **a Métrica** aparece como tipo de sinal para o monitor de ligação.
    1. Em **Condição de Adicionar** , para o Nome do **Sinal** , selecione **ChecksFailedPercent (Preview)** ou **RoundTripTimeMs (Preview)**.
    1. Para **o tipo de sinal,** escolha **métricas.** Por exemplo, selecione **ChecksFailedPercent (Preview)**.
    1. Todas as dimensões da métrica estão listadas. Escolha o nome de dimensão e valor da dimensão. Por exemplo, selecione **o Endereço de Origem** e, em seguida, introduza o endereço IP de qualquer fonte no seu monitor de ligação.
    1. Na **Lógica de Alerta,** preencha os seguintes detalhes:
        * **Tipo de condição** : **Estática**.
        * **Condição** e **Limiar**.
        * **Agregação Granularidade e Frequência de Avaliação** : Monitor de Ligação (Pré-visualização) atualiza os dados a cada minuto.
    1. Em **Ações,** escolha o seu grupo de ação.
    1. Forneça detalhes de alerta.
    1. Crie a regra de alerta.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Screenshot mostrando a área de regras Criar no Monitor Azure. O endereço de origem e o nome do ponto final de Origem estão destacados" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas na sua rede

O Monitor de Ligação (Pré-visualização) ajuda-o a diagnosticar problemas no monitor de ligação e na sua rede. Os problemas na sua rede híbrida são detetados pelos agentes do Log Analytics que instalou anteriormente. Os problemas em Azure são detetados pela extensão do Observador de Rede. 

Pode ver problemas na rede Azure na topologia da rede.

Para as redes cujas fontes são VMs no local, podem ser detetadas as seguintes questões:

* Pedido cronometrado.
* Ponto final não resolvido pelo DNS – temporário ou persistente. URL inválido.
* Não foram encontrados anfitriões.
* Fonte incapaz de ligar ao destino. Alvo não alcançável através do ICMP.
* Questões relacionadas com certificados: 
    * Certificado de cliente necessário para autenticar o agente. 
    * A lista de deslocalização de certificados não está acessível. 
    * O nome do anfitrião do ponto final não corresponde ao sujeito do certificado ou nome alternativo do sujeito. 
    * O certificado de raiz está em falta na loja local de certificação de confiança do computador da fonte. 
    * O certificado SSL é caducado, inválido, revogado ou incompatível.

Para as redes cujas fontes são Azure VMs, podem ser detetadas as seguintes questões:

* Problemas com o agente:
    * Agente parado.
    * Resolução falhada do DNS.
    * Nenhuma aplicação ou ouvinte escuta na porta de destino.
    * A tomada não pôde ser aberta.
* Questões estatais VM: 
    * A iniciar
    * A parar
    * Parada
    * A desalocar
    * Desalocada
    * Reinicialização
    * Não atribuído
* Falta entrada na mesa ARP.
* O tráfego foi bloqueado devido a problemas de firewall locais ou regras da NSG.
* Problemas de gateway de rede virtual: 
    * Rotas desaparecidas.
    * O túnel entre dois portais está desligado ou desaparecido.
    * O segundo portal não foi encontrado perto do túnel.
    * Nenhuma informação foi encontrada.
* Faltava a rota no Microsoft Edge.
* O trânsito parou por causa das vias do sistema ou da UDR.
* BGP não está ativado na ligação do gateway.
* A sonda DIP está no equilibrador de carga.

## <a name="next-steps"></a>Passos Seguintes
    
   * [Saiba como criar o Monitor de Ligação (Pré-visualização) utilizando o portal Azure](connection-monitor-preview-create-using-portal.md)  
   * [Saiba como criar o Monitor de Ligação (Pré-visualização) utilizando o ARMClient](connection-monitor-preview-create-using-arm-client.md)  
