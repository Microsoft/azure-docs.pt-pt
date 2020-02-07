---
title: Monitor de Ligação (Pré-visualização) / Microsoft Docs
description: Saiba como utilizar o Monitor de Ligação (Pré-visualização) para monitorizar a comunicação da rede num ambiente distribuído
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049265"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorização unificada da conectividade com o Monitor de Ligação (Pré-visualização)

O Monitor de Ligação (Pré-visualização) fornece capacidades unificadas de monitorização de ligação de ponta a ponta no Vigilante da Rede Azure para implementações híbridas e azure cloud. O Azure Network Watcher fornece ferramentas para monitorizar, diagnosticar e visualizar métricas relacionadas com conectividade para as suas implementações Azure.

Casos de utilização chave:

- Tem um VM de servidor web frontal que comunica com um VM de servidor de base de dados numa aplicação de vários níveis. Você quer verificar a conectividade da rede entre os dois VMs.
- Você quer VMs na região leste dos EUA para ping VMs na região centro dos EUA e comparar as lláctências da rede de regiões transversais
- Tem vários escritórios no local em cidades como Seattle. ligação ao Office 365 URLs. Pretende comparar as latências sentidas pelos utilizadores que usam os URLs do Office 365 de Seattle e Ashburn.
- Tem uma aplicação híbrida configurada que precisa de conectividade com um Ponto Final de Armazenamento Azure. Pretende comparar as tardios entre um local no local e a aplicação Azure, ambas ligadas ao mesmo Ponto final de Armazenamento Azure.
- Você deseja verificar a conectividade dos VMs Azure que hospedam a sua aplicação em nuvem para as suas configurações no local.

Nesta fase de pré-visualização, a solução reúne o melhor de duas capacidades-chave - Monitor de [Ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) do Observador de Rede e Monitor de Conectividade de [Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)(NPM).

Destaques:

* Experiência unificada e intuitiva para as necessidades de monitorização do Azure e híbridos
* Região transversal, monitorização da conectividade do espaço de trabalho
* Maiores frequências de sondagem e melhor visibilidade no desempenho da rede
* Alerta mais rápido para as suas implementações híbridas
* Controlos de conectividade baseados em HTTP, TCP e ICMP
* Suporte de Métricas e Log Analytics para configurações de testes Azure e não-Azure

![Monitor de Ligação](./media/connection-monitor-2-preview/hero-graphic.png)

Siga os passos abaixo mencionados para começar a monitorizar utilizando o Monitor de Ligação (Pré-visualização)

## <a name="step-1-install-monitoring-agents"></a>Passo 1: Instalar agentes de monitorização

O Monitor de Ligação baseia-se em executáveis leves para executar verificações de conectividade.  Apoiamos controlos de conectividade tanto em ambientes Azure como no local. O executável específico a ser utilizado depende se o seu VM está hospedado em Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais Azure

Para que o Monitor de Ligação reconheça os seus VMs Azure como fonte de monitorização, é necessário instalar a extensão virtual do Agente observador de rede (também conhecida como extensão do Observador de Rede) nos mesmos. A extensão do Agente observador de rede é um requisito para desencadear a monitorização final e outras funcionalidades avançadas em máquinas virtuais Azure. Pode [criar um VM e instalar a extensão do Observador de Rede](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[nele](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm).  Também pode instalar, configurar e resolver problemas a extensão do Observador de Rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) separadamente.

Se as regras de NSG ou firewall estiverem a bloquear a comunicação entre fonte e destino, o Connection Monitor detetará o problema e mostrará-o como uma mensagem de diagnóstico na topologia. Para permitir a monitorização da ligação, certifique-se de que as regras de NSG e firewall permitem pacotes sobre TCP ou ICMP entre fonte e destino.

### <a name="agents-for-on-premise-machines"></a>Agentes para máquinas no local

Para que o Monitor de Ligação reconheça as suas máquinas no local como fontes de monitorização, é necessário instalar o agente Log Analytics nas máquinas e ativar a solução de Monitorização do Desempenho da Rede. Estes agentes estão ligados aos espaços de trabalho do Log Analytics e precisam de identificação do espaço de trabalho e chave primária configurada antes de poderem começar a monitorizar.

Para instalar o agente Log Analytics para máquinas Windows, siga as instruções mencionadas [neste link](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Certifique-se de que o destino é acessível se houver firewalls ou aparelhos de rede virtuais (NVA) no caminho.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Passo 2: Ativar o Observador de Rede na sua subscrição

Todas as subscrições com um VNET estão ativadas com o Observador de Rede. Quando criar uma rede virtual na sua subscrição, o Network Watcher será ativado automaticamente na região e subscrição da Rede Virtual. Não há impacto nos seus recursos ou encargos associados para ativar automaticamente o Observador da Rede. Certifique-se de que o Observador de Rede não está explicitamente desativado na sua subscrição. Para mais informações, consulte [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Passo 3: Criar monitor de ligação 

_O Connection Monitor_ monitoriza a comunicação a intervalos regulares e informa-o das alterações de alcance, latência e topoologia da rede entre os agentes de origem e os pontos finais do destino. As fontes podem ser VMs Azure ou máquinas no local que tenham um agente de monitorização instalado. Os Pontos Finais de Destino podem ser Office 365 URLs, Dynamics 365 URLs, URLs Personalizados, IDs de recursos Azure VM, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="accessing-connection-monitor-preview"></a>Monitor de ligação de acesso (Pré-visualização)

1. A partir da página inicial do portal Azure, visite o Network Watcher
2. Clique no separador "Monitor de Ligação (Pré-visualização)" na secção de monitorização no painel esquerdo do Observador da Rede.
3. Pode ver todos os Monitores de Ligação criados utilizando a experiência do Monitor de Ligação (Pré-visualização). Todos os Monitores de Ligação criados utilizando a experiência clássica do separador Monitor de Ligação serão visíveis no separador Monitor de Ligação.

    ![Criar um Monitor de Ligação](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Criação de um Monitor de Ligação

Os Monitores de Ligação criados utilizando o Monitor de Ligação (Pré-visualização) fornecem a capacidade de adicionar tanto no local como os VMs Azure como fontes e monitorizar a conectividade com os pontos finais, que podem abranger o Azure ou qualquer outro URL/IP.

Seguem-se as entidades de um Monitor de Ligação:

* Recurso de Monitor de Ligação – Recurso Azure específico da região. Todas as entidades mencionadas abaixo são propriedades de um recurso do Connection Monitor.
* Pontos finais – Todas as fontes e destinos que participam em controlos de conectividade são chamados como pontos finais. Exemplos de ponto final – VMs Azure, agentes no local, URLs, IPs
* Configuração do teste – Cada configuração do teste é específica do protocolo. Com base no protocolo escolhido, pode definir porta, limiares, frequência de teste e outros parâmetros
* Grupo de teste – Cada grupo de teste contém pontos finais de origem, pontos finais de destino e configurações de teste. Cada Monitor de Ligação pode conter mais de um grupo de teste
* Teste – Combinação de um ponto final de origem, ponto final de destino e configuração de teste fazem um teste. O teste é o nível mais baixo em que os dados de monitorização (controlos falhados % e RTT) estão disponíveis

 ![Criar um Monitor de Ligação](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Do portal

Para criar um Monitor de Ligação, siga os passos abaixo mencionados:

1. No painel do Monitor de Ligação (Pré-visualização), clique em "Criar" a partir do canto superior esquerdo.
2. No separador Basic, introduza informações para o seu monitor de ligação
   1. Nome do monitor de ligação – Nome do monitor de ligação. As regras padrão de nomeação dos recursos Azure aplicam-se aqui.
   2. Subscrição – Escolha uma subscrição para o seu Monitor de Ligação.
   3. Região – Escolha uma região para o seu recurso Desconexão Monitor. Só é possível selecionar os VMs de origem que são criados nesta região.
   4. Configuração do espaço de trabalho - Pode utilizar o espaço de trabalho predefinido criado pelo Connection Monitor para armazenar os seus dados de monitorização clicando na caixa de verificação predefinida. Para escolher um espaço de trabalho personalizado, desmarque esta caixa. Escolha a subscrição e região para selecionar o espaço de trabalho, que irá conter os seus dados de monitorização.
   5. Clique em "Next: Test Groups" para adicionar grupos de teste

      ![Criar um Monitor de Ligação](./media/connection-monitor-2-preview/create-cm-basics.png)

3. No separador grupos de teste, clique em "+ Test Group" para adicionar um Grupo de Teste. Utilize _a criação_ de grupos de teste no Monitor de Ligação para adicionar grupos de teste. Clique em "Rever + criar" para rever o seu Monitor de Ligação.

   ![Criar um Monitor de Ligação](./media/connection-monitor-2-preview/create-tg.png)

4. No separador "Rever + criar", reveja as informações básicas e os grupos de teste antes de criar o Monitor de Ligação. Para editar o Monitor de Ligação a partir da vista "Review + criar":
   1. Para editar os detalhes básicos, utilize o ícone do lápis conforme especificado pela casa 1 na imagem 2
   2. Para editar os grupos de teste individuais, clique no grupo de teste que pretende editar para abrir o grupo de teste no modo de edição.
   3. O Custo/mês Atual indicou o custo durante a pré-visualização. Não há atualmente nenhuma carga para usar o Monitor de Ligação, por isso esta coluna mostrará zero. O custo/mês real indicou o preço que será cobrado após a Disponibilidade Geral. Note que as taxas de ingestão de análise de registo serão aplicadas mesmo durante a pré-visualização.

5. No separador "Rever + criar", clique no botão "criar" para criar o Monitor de Ligação.

   ![Criar um Monitor de Ligação](./media/connection-monitor-2-preview/review-create-cm.png)

6.  O Monitor de Ligação (Pré-visualização) criará o recurso do Monitor de Ligação em segundo plano.

#### <a name="from-armclient"></a>Da Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Criação de grupos de teste no Connection Monitor

Cada grupo de teste no Connection Monitor inclui fontes e destino que são testados em parâmetros de rede de verificações falhadas e RTT sobre configurações de teste.

#### <a name="from-portal"></a>Do portal

Para criar um Grupo de Teste num Monitor de Ligação, especifice o valor para os campos abaixo mencionados:

1. Grupo de teste de desativação – A verificação deste campo irá desativar a monitorização de todas as fontes e destinos especificados no grupo de teste. Verá esta opção desmarcada por defeito.
2. Nome – Nome do seu grupo de teste
3. Fontes – Pode especificar as máquinas Azure VMs e On-Premise como fontes se os agentes estiverem instalados nas mesmas. Consulte o Passo 1 para instalar o agente específico da sua fonte.
   1. Clique no separador "Agentes Azure" para selecionar agentes Azure. Só verão os VMs listados que estão ligados à região que especificou no momento da criação do Monitor de Ligação. Os VMs são por padrão agrupados na subscrição a que pertencem e os grupos estão em colapso. Pode reduzir do nível de Subscrições para outros níveis na hierarquia:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Também pode alterar o valor do campo "Group by" para iniciar a árvore a partir de qualquer outro nível. Por exemplo: Group by – VNET mostrará os VMs com agentes na hierarquia VNETs -\&gt; Subredes -\&gt; VMs com agentes.

      ![Adicionar Fontes](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Clique no separador "Agentes Não - Azure" para selecionar agentes no local. Por defeito, verá agentes agrupados em espaços de trabalho numa região. Apenas serão listados os espaços de trabalho que tenham a solução de Monitor de Desempenho da Rede configurada. Adicione a solução de Monitor de Desempenho da Rede ao seu espaço de trabalho a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Por predefinição, verá a região selecionada no separador Informação Básica na vista Create Connection Monitor. Pode mudar a região e escolher agentes de espaços de trabalho da região recém-seleccionada. Também pode alterar o valor do campo "Group by" para agrupar-se nas subredes.

      ![Fontes não-azuras](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Clique em "Rever " para rever os agentes Azure e Não-Azure selecionados.

      ![Fontes de revisão](./media/connection-monitor-2-preview/review-sources.png)

   4. Clique em "Done" quando terminar a seleção das fontes.

4. Destinos – Pode monitorizar a conectividade com Os VMs Azure ou qualquer ponto final (IP público, URL, FQDN) especificando-os como destinos. Num único grupo de teste, pode adicionar VMs Azure, UrLs O365, URLs D365 ou pontos finais personalizados.

   1. Clique no separador "VMs Azure" para selecionar VMs Azure como destinos. Por padrão, verá os VMs Azure agrupados na hierarquia de subscrição na mesma região que foi selecionada no separador Informação Básica na vista Create Connection Monitor. Pode mudar a região e escolher VMs Azure da região recém-seleccionada. Você pode reduzir do nível de Subscrições para outros níveis na hierarquia, como agentes Azure.

      ![Adicionar Destinos](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Adicionar Destinos 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Clique no separador "Endpoints" para selecionar VMs Azure como destinos. A lista de pontos finais será povoada com URLs de teste O365 e D365, agrupados pelo nome.  Também pode escolher um ponto final criado noutros grupos de teste no mesmo Monitor de Ligação. Para adicionar um novo ponto final, clique em "+ Endpoint" a partir do canto superior direito do ecrã e forneça URL/IP/FQDN de ponto final

      ![Adicionar pontos finais](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Clique em "Review" para rever os agentes Azure e Non-Azure selecionados.
   4. Clique em "Done" quando terminar a seleção das fontes.

5. Configuração do teste – Pode associar qualquer número de configurações de teste num determinado grupo de teste. O Portal restringe-o a uma configuração de teste por grupo de teste, mas use o Armclient para adicionar mais.
   1. Nome – Nome para a configuração do teste
   2. Protocolo – Pode escolher entre TCP, ICMP ou HTTP. Para alterar HTTP para HTTPS, selecione HTTP como protocolo e 443 como porta
   3. Criar configuração de teste de rede- Só verá esta caixa de verificação se selecionar HTTP no campo Protocol. Ative este campo para criar outra configuração de teste utilizando as mesmas fontes e destinos especificados nos passos 3 e 4 sobre o protocolo TCP/ICMP. A configuração de teste recém-criada chama-se "\&lt;nome especificado em 5.a\&gt;\_redeTestConfig"
   4. Desativar traceroute – Este campo será aplicável para grupos de teste com TCP ou ICMP como protocolo.  Verifique este campo para impedir que as fontes descubram topologia e tempo de ida e volta hop-by-hop.
   5. Porta de Destino – Pode personalizar este campo para colocar num porto de destino à sua escolha.
   6. Frequência de Teste – Este campo decide com que frequência as fontes irão pingdestinos destinos no protocolo e porta acima especificado. Pode escolher entre 30 segundos, 1 minuto, 5 minutos, 15 minutos e 30 minutos. As fontes vão testar a conectividade com destinos com base no valor que escolher.  Por exemplo, se selecionar 30 segundos, as fontes verificarão a conectividade para o destino pelo menos uma vez em 30 segundos.
   7. Limiares de Saúde – Pode fixar limiares nos parâmetros da rede mencionados abaixo
      1. Controlos Falhados em % - A percentagem de controlos falhou quando as fontes verificam a conectividade com o destino sobre os critérios acima especificados. No caso do protocolo TCP/ICMP, os controlos falhados em % podem ser equiparados à perda de pacotes %. Para o protocolo HTTP, este campo representa o número de pedidos de http que não tiveram uma resposta.
      2. RTT em milissegundos – Tempo de ida e volta em milissegundos quando as fontes se ligam ao destino através da configuração de teste acima especificada.

      ![Adicionar TG](./media/connection-monitor-2-preview/add-test-config.png)

Todas as fontes e destinos adicionados a um grupo de teste com a configuração do teste especificada são discriminados em testes individuais. Por exemplo:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configuração do teste: 2 (Config 1, Config 2)
* Testes Criados: Total = 12

| **Número de teste** | **Origem** | **Destino** | **Teste nome config** |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>Limites de escala

* Max # dos Monitores de Ligação por subscrição por região: 100
* Máx # dos grupos de teste por Monitor de Ligação - 20
* Fontes Max + destinos por Monitor de Ligação – 100
* Max # de configurações de teste por Monitor de Ligação – 20 via Armclient. 2 via Portal.

## <a name="step-4--data-analysis-and-alerts"></a>Passo 4: Análise de dados e alertas

Uma vez criado um Monitor de Ligação, as fontes verificam a conectividade com destinos com base na configuração do teste especificada.

### <a name="checks-in-a-test"></a>Verificação num teste

Com base no protocolo selecionado por um utilizador na configuração do teste, o Monitor de Ligação (Pré-visualização) executa uma série de verificações para o par de destino fonte sobre a frequência de teste escolhida.

Se http for selecionado, o serviço calcula o número de respostas HTTP que devolveram um código de resposta para determinar que os controlos falharam %.  Para calcular o RTT, medimos o tempo devida para receber a resposta de uma chamada HTTP.

Se o TCP ou o ICMP forem selecionados, o serviço calcula o pacote % para determinar que as verificações falharam %. Para calcular o RTT, medimos o tempo de adoção para receber o ACK para os pacotes enviados. Se tiver ativado os dados da traceroute para os seus testes de rede, pode ver a perda de hop-by-hop e a latência para a sua rede no local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados devolvidos por verificação num teste, cada teste pode então ter os seguintes estados:

* Passe = Quando os valores reais dos controlos falharam % e RTT estão dentro dos limiares especificados
* Fail = Quando os valores reais para controlos falharam % ou limites de cruzamento RTT especificados. Se não for especificado um limiar, então um teste é marcado falha quando os controlos falharam % = 100%
* Aviso – Quando os critérios de verificação falharam % não são especificados. Neste caso, o Monitor de Ligação (Pré-visualização) utiliza um critério de conjunto automático como limiar e quando esse limiar é violado o estado do teste é definido para "Aviso"

### <a name="data-collection-analysis-and-alerts"></a>Recolha de dados, análise e alertas

Todos os dados recolhidos pelo Connection Monitor (Preview) estão armazenados no espaço de trabalho do Log Analytics configurado no momento da criação do Monitor de Ligação. Os dados de monitorização também estão disponíveis nas Métricas do Monitor Do Azure. Pode utilizar o Log Analytics para manter os seus dados de monitorização durante o tempo que quiser, mas o Azure Monitor armazena métricas por padrão durante 30 dias **.** Em seguida, pode [definir alertas baseados em métricas nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Monitorização de dashboards na solução De controlo de ligação

Verá uma lista do seu Monitor de Ligação a que tem acesso para uma determinada seleção de subscrições, regiões, carimbo de tempo, fonte e tipos de destino.

Quando navegar para o Monitor de Ligação (Pré-visualização) do serviço De Vigilância da Rede, pode optar por **Ver Por:**

* Monitor de Ligação (padrão) – Lista de todos os Monitores de Ligação criados para subscrições, regiões, carimbo de tempo, origem e tipos de destino
* Grupos de Teste – Lista de todos os Grupos de Teste criados para assinaturas, regiões, carimbo de tempo, origem e tipos de destino. Estes grupos de teste não são filtrados no Monitor de Ligação
* Testes – Lista de todos os testes em execução para assinaturas, regiões, timestamp, fonte e tipos de destino escolhidos. Estes testes não são filtrados no Monitor de Ligação ou nos Grupos de Teste.

Pode expandir cada Monitor de Ligação para os Grupos de Teste e cada Grupo de Teste para os vários testes individuais que estão a ser realizados no painel de instrumentos. Marcado como [1] na imagem abaixo.

Pode filtrar esta lista com base em:

* Filtros de alto nível - Assinaturas, regiões, fonte de carimbo de tempo e tipos de destino. Marcado como [2] na imagem abaixo.
* Filtros estatais - Filtro de segundo nível em estado de monitor de ligação/ grupo de teste/ Teste. Marcado como [3] na imagem abaixo.
* Campo de pesquisa - Escolha "Tudo" para fazer uma pesquisa genérica. Para pesquisar em qualquer entidade específica, use o dropdown para reduzir os resultados da pesquisa. Marcado como [4] na imagem abaixo.

![Testes de filtro](./media/connection-monitor-2-preview/cm-view.png)

Por exemplo:

1. Para analisar todos os testes em todos os monitores de ligação (Pré-visualização) onde a fonte IP = 10.192.64.56:
   1. Alterar a visão por "Testes"
   2. Pesquisa Arquivada = 10.192.64,56
   3. Utilize o dropdown ao lado do valor para selecionar "Fontes"
2. Para filtrar apenas os testes falhados em todos os monitores de ligação (Pré-visualização) onde a fonte IP = 10.192.64.56
   1. Alterar a visão por "Testes"
   2. Selecione "Fail" a partir de filtros estatais.
   3. Campo de Pesquisa = 10.192.64.56
   4. Utilize o dropdown ao lado do valor para selecionar "Fontes"
3. Para filtrar apenas os testes falhados em todos os monitores de ligação (Pré-visualização) onde o destino é outlook.office365.com
   1. Alterar a visão por "Testes"
   2. Selecione "Fail" a partir de filtros estatais.
   3. Campo de Pesquisa = outlook.office365.com
   4. Utilize o dropdown ao lado do valor para selecionar "Destinos"

   ![Testes falhados](./media/connection-monitor-2-preview/tests-view.png)

Para ver as tendências dos controlos falharam % e a RTT para:

1. Monitor de Ligação
   1. Clique no Monitor de Ligação que pretende investigar em detalhe
   2. Por padrão, verá os dados de monitorização por "Grupos de Teste"

      ![Ver Métricas por](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Escolha o Grupo de Teste que pretende investigar em detalhe

      ![Métricas por TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Verá que os 5 melhores testes falhados em controlos falharam % ou rtt msecs para o grupo de teste que escolheu no passo anterior. Para cada teste, verá falhas nas tendências dos controlos , e RTT msec
   5. Selecione um teste da lista acima ou escolha outro teste para investigar em detalhe.
   6. Para o intervalo de tempo selecionado, para verificações falhadas %, verá limiar e valores reais. Para rtt msec, você verá limiar, avg, min e valores máximos.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Alterar o intervalo de tempo para ver mais dados
  8. Pode alterar a vista no passo b e optar por ver por fontes, destinos ou configurações de teste. Em seguida, escolha uma fonte com base em testes falhados e investigue os 5 testes falhados.  Por exemplo: Escolha a vista por: Fontes e Destinos para investigar todos os testes que executam entre essa combinação no Monitor de Ligação selecionado.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Grupo de Teste
   1. Clique no Grupo de Teste que pretende investigar em detalhe
   2. Por padrão, irá visualizar os dados de monitorização por "Source + Destination + Test Configuration (Test) "

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Escolha o Teste que pretende investigar em detalhe
   4. Para o intervalo de tempo selecionado, para verificações falhadas %, verá limiar e valores reais. Para rtt msec, você verá limiar, avg, min e valores máximos. Também verá alertas disparados específicos para o teste que selecionou.
   5. Alterar o intervalo de tempo para ver mais dados
   6. Pode alterar a vista no passo b e optar por ver por fontes, destinos ou configurações de teste. Em seguida, escolha uma entidade para investigar os 5 testes falhados do top 5.  Por exemplo: Escolha a vista por: Fontes e Destinos para investigar todos os testes que executam entre essa combinação no Monitor de Ligação selecionado.

3. Teste
   1. Clique na Fonte + Destino + Configuração de Teste que pretende investigar em detalhe
   2. Para o intervalo de tempo selecionado, para verificações falhadas %, verá limiar e valores reais. Para rtt msec, você verá limiar, avg, min e valores máximos. Também verá alertas disparados específicos para o teste que selecionou.

      ![Teste1](./media/connection-monitor-2-preview/test-drill.png)

   3. Também pode clicar em "Topologia" para ver a topologia da rede a qualquer momento.

      ![Teste2](./media/connection-monitor-2-preview/test-topo.png)

   4. Pode clicar em qualquer sonorde de ligação para a rede Azure para ver os problemas identificados pelo Connection Monitor. Esta capacidade não está disponível para redes no local no momento.

       ![Teste 3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Consultas de log no Azure Monitor Log Analytics

Utilize o Log Analytics para criar vistas personalizadas dos seus dados de monitorização. Todos os dados apresentados na UI são povoados a partir de Log Analytics. Pode realizar análises interativas de dados no repositório e correlacionar dados de diferentes fontes, como a saúde do agente e outras aplicações baseadas em Log Analytics. Também pode exportar os dados para Excel, Power BI ou um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Para o Monitor de Ligação criado antes da experiência do Monitor de Ligação (Pré-visualização), todas as 4 métricas estariam disponíveis. Para monitores de ligação criados através da experiência Do Monitor de Ligação (Pré-visualização), os dados estarão disponíveis apenas para as Métricas marcadas com "(Pré-visualização)".

Tipo de Recursos - Microsoft.Network/NetworkWatchers/connectionMonitors

| Métrica | Nome a apresentar de métrica | Unidade | Tipo de Agregação | Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % sondas falhadas | Percentagem | Média | % das sondas de monitorização da conectividade falharam | Nenhuma dimensão |
| AverageRoundtripMs | Média de tempo de ida e volta (MS) | Milissegundos | Média | Tempo de ida e volta médio da rede (MS) para investigações de monitoramento de conectividade enviadas entre a origem e o destino |             Nenhuma dimensão |
| ChecksFailedPercent (Pré-visualização) | % de verificações falhadas (pré-visualização) | Percentagem | Média | % dos controlos falharam para um teste | * ConnectionMonitorResourceId <br> * Endereço de origem <br> * Nome de origem <br> * SourceResourceId <br> * Tipo de fonte <br> * Protocolo <br> * Endereço de destino <br> * Nome de destino <br> * DestinationResourceid <br> * DestinoTipo <br> * DestinationPort <br> * TestGroupName <br> * Nome de Configuração de Teste <br> * Região |
| RoundTripTimeMs (Pré-visualização) | Tempo de ida e volta (ms) (Pré-visualização) | Milissegundos | Média | Tempo de ida e volta (MS) para verificações enviadas entre fonte e destino. Este valor não é mediado | * ConnectionMonitorResourceId <br> * Endereço de origem <br> * Nome de origem <br> * SourceResourceId <br> * Tipo de fonte <br> * Protocolo <br> * Endereço de destino <br> * Nome de destino <br> * DestinationResourceid <br> * DestinoTipo <br> * DestinationPort <br> * TestGroupName <br> * Nome de Configuração de Teste <br> * Região |

 ![Monitorizar Métricas](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Alertas métricos no Monitor Azure

Para criar um alerta:

1. Escolha o seu recurso Desconexão Monitor criado através do Monitor de Ligação (Pré-visualização)
2. Certifique-se de que "Métrica" aparece como tipo de sinal para o recurso selecionado no passo anterior
3. Em Condição de Adição, escolha o nome do sinal como ChecksFailedPercent (Pré-visualização) ou RoundTripTimeMs (Pré-visualização) e Tipo de Sinal como Métricas. Por exemplo: Escolha chequesFailedPercent (pré-visualização)
4. Todas as dimensões aplicáveis de acordo com as métricas serão listadas.  Escolha o nome de dimensão e o valor de dimensão. Por exemplo: Escolha o Endereço fonte e forneça endereço IP de qualquer fonte envolvida no recurso do Monitor de Ligação escolhido na Etapa 1
5. Na Lógica de Alerta, escolha:
   1. Tipo de condição - Estática
   2. Condição e Limiar
   3. Agregação Granularity and Frequency of Evaluation – Connection Monitor (Preview) atualiza os dados a cada 1 minuto.
6.  Em ações, escolha o seu grupo de ação
7. Fornecer detalhes de alerta
8. Criar regra de alerta

   ![Alertas](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Passo 5: Diagnosticar problemas na sua rede

O Monitor de Ligação irá ajudá-lo a diagnosticar problemas correspondentes ao recurso do Monitor de Ligação e na sua rede. Os problemas na sua rede híbrida serão detetados pelos agentes Log Analytics instalados no Passo 1 e os problemas no Azure serão detetados pela Extensão do Observador da Rede.  Os problemas na rede híbrida serão visíveis na página de Diagnósticos e os problemas na Rede Azure serão visíveis na topologia da rede.

Para redes com VMs no local como fontes, detetamos:

* Pedido cronometrado
* Ponto final não resolvido pelo DNS – temporário ou persistente. URL inválido.
* Não há anfitriões encontrados.
* Fonte incapaz de ligar ao destino. Alvo não alcançável através do ICMP.
* Emissão relacionada com certificado - Certificado de cliente necessário para autenticar agente, Lista de Relocalização de Certificados não acessível, nome de anfitrião do ponto final não corresponde ao nome sujeito ou sujeito do certificado, certificado de raiz em falta na loja local de certificação de computador esquelético da fonte, certificado SSL caducado/inválido/revogado, incompatível

Para redes com VMs Azure são fontes, detetamos:

* Problemas de Agente – Agente parado, Resolução DNS falhada, Nenhuma aplicação/ouvinte ouvindo na porta de destino, Socket não pôde ser aberta
* Questões estatais VM – começar, parar, parar, fazer negócios, negociar, relançar, não ser atribuídos
* Entrada de mesa ARP em falta
* Tráfego bloqueado devido a problemas de firewall locais, regras da NSG
* VNET Gateway – Rotas em falta, Túnel entre duas portas é desligado ou desaparecido ou segundo portal não encontrado por túnel, nenhuma informação de observação encontrada
* Rota desaparecida em MS Edge.
* Tráfego parado por causa de rotas do sistema ou UDR
* BGP não habilitado na ligação de gateway
* Sonda DIP para baixo no Balancer de Carga
