---
title: Monitor de Ligação (Pré-visualização) / Microsoft Docs
description: Aprenda a utilizar o Monitor de Ligação (Pré-visualização) para monitorizar a comunicação da rede num ambiente distribuído.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599330"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorização da conectividade da rede com monitor de ligação (pré-visualização)

O Monitor de Ligação (Pré-visualização) fornece uma monitorização unificada de ligação de ponta a ponta no Observador da Rede Azure. A função Monitor de Ligação (Pré-visualização) suporta implantações híbridas e azure cloud. O Network Watcher fornece ferramentas para monitorizar, diagnosticar e visualizar métricas relacionadas com conectividade para as suas implementações Azure.

Aqui estão alguns casos de utilização para o Monitor de Ligação (Pré-visualização):

- O VM do servidor web frontal comunica com um VM de servidor de base de dados numa aplicação de vários níveis. Você quer verificar a conectividade da rede entre os dois VMs.
- Você quer VMs na região leste dos EUA para ping VMs na região centro dos EUA, e você quer comparar as latenciências da rede transversal.
- Tem vários escritórios no local em Seattle, Washington, e em Ashburn, Virgínia. Os seus sites de escritórios ligam-se ao Office 365 URLs. Para os seus utilizadores do Office 365 URLs, compare as latenciências entre Seattle e Ashburn.
- A sua aplicação híbrida necessita de conectividade com um ponto final de Armazenamento Azure. O seu site no local e a sua aplicação Azure ligam-se ao mesmo ponto final do Armazenamento Azure. Pretende comparar as tardios do local com as tardios da aplicação Azure.
- Você quer verificar a conectividade entre as suas configurações no local e os VMs Azure que acolhem a sua aplicação em nuvem.

Na sua fase de pré-visualização, o Monitor de Ligação combina a melhor de duas funcionalidades: a funcionalidade do Monitor de [Ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) do Observador de Rede e a funcionalidade de Monitor de Conectividade do [Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) Monitor de Desempenho da Rede (NPM).

Aqui estão alguns benefícios do Monitor de Ligação (Pré-visualização):

* Experiência unificada e intuitiva para as necessidades de monitorização do Azure e híbridos
* Monitorização da conectividade transversal e transversal
* Maiores frequências de sondagem e melhor visibilidade no desempenho da rede
* Alerta mais rápido para as suas implementações híbridas
* Suporte para controlos de conectividade baseados em HTTP, TCP e ICMP 
* Suporte de Métricas e Log Analytics para configurações de testes Azure e não-Azure

![Diagrama mostrando como o Monitor de Ligação interage com VMs Azure, anfitriões não-Azure, pontos finais e locais de armazenamento de dados](./media/connection-monitor-2-preview/hero-graphic.png)

Para começar a utilizar o Monitor de Ligação (Pré-visualização) para monitorização, siga estes passos: 

1. Instale agentes de monitorização.
1. Ative o Observador de Rede na sua subscrição.
1. Criar um monitor de ligação.
1. Configurar análise de dados e alertas.
1. Diagnostice problemas na sua rede.

As seguintes secções fornecem detalhes para estes passos.

## <a name="install-monitoring-agents"></a>Instalar agentes de monitorização

O Monitor de Ligação baseia-se em ficheiros executáveis leves para executar verificações de conectividade.  Suporta controlos de conectividade tanto dos ambientes Azure como dos ambientes no local. O ficheiro executável que utiliza depende se o seu VM está hospedado no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais Azure

Para fazer com que o Monitor de Ligação reconheça os seus VMs Azure como fontes de monitorização, instale a extensão virtual da máquina virtual do Agente observador de rede. Esta extensão também é conhecida como a *extensão do Observador*da Rede . As máquinas virtuais Azure requerem a extensão para desencadear a monitorização de ponta a ponta e outras funcionalidades avançadas. 

Pode instalar a extensão Do Observador de Rede quando [criar um VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Também pode instalar, configurar e resolver problemas separadamente a extensão do Observador de Rede para [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

As regras para um grupo de segurança de rede (NSG) ou firewall podem bloquear a comunicação entre a fonte e o destino. O Monitor de Ligação deteta este problema e mostra-o como uma mensagem de diagnóstico na topologia. Para permitir a monitorização da ligação, certifique-se de que as regras de NSG e firewall permitem pacotes sobre TCP ou ICMP entre a fonte e o destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para máquinas no local

Para fazer com que o Monitor de Ligação reconheça as suas máquinas no local como fontes de monitorização, instale o agente Log Analytics nas máquinas. Em seguida, ative a solução de Monitor de Desempenho da Rede. Estes agentes estão ligados aos espaços de trabalho do Log Analytics, pelo que é necessário configurar o ID do espaço de trabalho e a chave primária antes que os agentes possam começar a monitorizar.

Para instalar o agente Log Analytics para máquinas Windows, consulte a extensão virtual da [máquina do Monitor Azure para windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se o caminho incluir firewalls ou aparelhos virtuais de rede (NVAs), certifique-se de que o destino está acessível.

## <a name="enable-network-watcher-on-your-subscription"></a>Ativar o Observador de Rede na sua subscrição

Todas as subscrições que possuam uma rede virtual estão ativadas com o Network Watcher. Quando cria uma rede virtual na sua subscrição, o Network Watcher é ativado automaticamente na região e subscrição da rede virtual. Esta habilitação automática não afeta os seus recursos nem incorre numa carga. Certifique-se de que o Observador de Rede não está explicitamente desativado na sua subscrição. 

Para mais informações, consulte [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Criar um monitor de ligação 

O Monitor de Ligação monitoriza a comunicação a intervalos regulares. Informa-o das mudanças de alcance e latência. Também pode verificar a topologia da rede atual e histórica entre agentes de origem e pontos finais de destino.

As fontes podem ser VMs Azure ou máquinas no local que tenham um agente de monitorização instalado. Os pontos finais de destino podem ser Office 365 URLs, Dynamics 365 URLs, URLs personalizados, IDs de recursos Azure VM, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="access-connection-monitor-preview"></a>Monitor de ligação de acesso (Pré-visualização)

1. Na página inicial do portal Azure, vá ao **Network Watcher.**
1. À esquerda, na secção **de monitorização,** selecione Monitor de **Ligação (Pré-visualização)** .
1. Você vê todos os monitores de ligação que foram criados no Monitor de Ligação (Pré-visualização). Para ver os monitores de ligação que foram criados na experiência clássica do Monitor de Ligação, vá ao separador Monitor de **Ligação.**

    ![Screenshot mostrando monitores de ligação que foram criados no Monitor de Ligação (Pré-visualização)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Criar um monitor de ligação

Nos monitores de ligação que cria no Monitor de Ligação (Pré-visualização), pode adicionar as máquinas no local e os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Ligação (Pré-visualização) inclui as seguintes entidades:

* Recurso de **monitor de ligação** – Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final** – Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem VMs Azure, agentes no local, URLs e IPs.
* **Configuração do teste** – Uma configuração específica do protocolo para um teste. Com base no protocolo que escolheu, pode definir a porta, os limiares, a frequência de teste e outros parâmetros.
* **Grupo** de teste – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular ao qual os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de controlos que falharam e o tempo de ida e volta (RTT).

 ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Criar um monitor de ligação a partir do portal Azure

Para criar um monitor de ligação a partir do portal Azure, siga estes passos:

1. No painel do Monitor de **Ligação (Pré-visualização),** no canto superior esquerdo, selecione **Criar**.
1. No separador **Basics,** introduza informações para o seu monitor de ligação:
   * Nome do monitor de **ligação** – Adicione o nome do seu monitor de ligação. Use as regras padrão de nomeação para os recursos Azure.
   * **Subscrição** – Escolha uma subscrição para o seu monitor de ligação.
   * **Região** – Escolha uma região para o seu monitor de ligação. Só pode selecionar os VMs de origem que são criados nesta região.
   * **Configuração do espaço** de trabalho - O seu espaço de trabalho contém os seus dados de monitorização. Pode utilizar um espaço de trabalho personalizado ou o espaço de trabalho padrão. 
       * Para utilizar o espaço de trabalho predefinido, selecione a caixa de verificação. 
       * Para escolher um espaço de trabalho personalizado, limpe a caixa de verificação. Em seguida, escolha a subscrição e região para o seu espaço de trabalho personalizado. 
1. Na parte inferior do separador, selecione **Seguinte: Grupos**de teste .

   ![Screenshot mostrando o separador Basics no Monitor de Ligação](./media/connection-monitor-2-preview/create-cm-basics.png)

1. No separador **grupos de teste,** selecione **+ Grupo**de teste . Para configurar os seus grupos de teste, consulte Criar grupos de [teste no Monitor de Ligação](#create-test-groups-in-a-connection-monitor). 
1. Na parte inferior do separador, selecione **Seguinte: Rever + criar** para rever o monitor de ligação.

   ![Screenshot mostrando o separador de grupos de teste e o painel onde adiciona detalhes do grupo de teste](./media/connection-monitor-2-preview/create-tg.png)

1. No **separador Review + criar,** reveja as informações básicas e os grupos de teste antes de criar o monitor de ligação. Se precisar editar o monitor de ligação:
   * Para editar detalhes básicos, selecione o ícone do lápis.
   * Para editar um grupo de teste, selecione-o.

   > [!NOTE] 
   > O separador **Review + criar** mostra o custo por mês durante a fase de pré-visualização do Monitor de Ligação. Atualmente, a coluna **CURRENT COST** não mostra qualquer custo. Quando o Monitor de Ligação estiver geralmente disponível, esta coluna apresentará uma carga mensal. 
   > 
   > Mesmo na fase de pré-visualização do Monitor de Ligação, aplicam-se os encargos de ingestão de Log Analytics.

1. Quando estiver pronto para criar o monitor de ligação, na parte inferior do **separador Review + crie** o separador, selecione **Criar**.

   ![Screenshot do Monitor de Ligação, mostrando o review + criar separador](./media/connection-monitor-2-preview/review-create-cm.png)

O Monitor de Ligação (Pré-visualização) cria o recurso do monitor de ligação em segundo plano.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Criar um monitor de ligação utilizando o ARMClient

Utilize o seguinte código para criar um monitor de ligação utilizando o ARMClient.

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

address: '\&lt;FQDN of your on-premises agent'

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

Aqui está o comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste num monitor de ligação

Cada grupo de teste num monitor de ligação inclui fontes e destinos que são testados em parâmetros de rede. São testados para a percentagem de cheques que falham e o RTT sobre as configurações de teste.

A partir do portal Azure, para criar um grupo de teste num monitor de ligação, especifice valores para os seguintes campos:

* **Desativar o Grupo** de Teste – Pode selecionar este campo para desativar a monitorização de todas as fontes e destinos que o grupo de teste especifica. Esta seleção é desmarcada por defeito.
* **Nome** – Nome do seu grupo de teste.
* **Fontes** – Pode especificar tanto os VMs Azure como as máquinas no local como fontes se os agentes estiverem instalados nas mesmas. Para instalar um agente para a sua fonte, consulte Instalar agentes de [monitorização](#install-monitoring-agents).
   * Para escolher os agentes Azure, selecione o separador **Agentes Azure.** Aqui só vemos VMs que estão ligados à região que especificou quando criou o monitor de ligação. Por predefinição, os VMs são agrupados na subscrição a que pertencem. Estes grupos estão em colapso. 
   
       Pode reduzir do nível de Subscrição para outros níveis na hierarquia:

      **Grupos de** recursos de > de assinatura > **VNETs** > **Subnets** > **VMs com agentes**

      Também pode alterar o valor do **Grupo por** campo para iniciar a árvore a partir de qualquer outro nível. Por exemplo, se você agrupa por rede virtual, você vê os VMs que têm agentes na hierarquia **VNETs** > **Subnets** > **VMs com agentes**.

      ![Screenshot do Monitor de Ligação, mostrando o painel Adicionar Fontes e o separador Agentes Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Para escolher agentes no local, selecione o separador **Agentes Não-Azure.** Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos estes espaços de trabalho têm a solução De desempenho da rede configurada. 
   
       Se precisar de adicionar o Monitor de Desempenho da Rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar o Monitor de Desempenho da Rede, consulte [soluções de monitorização no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Na vista **Create Connection Monitor,** no separador **Basics,** a região predefinida é selecionada. Se mudar a região, pode escolher agentes de espaços de trabalho na nova região. Também pode alterar o valor do **Grupo por** campo para grupo por subredes.

      ![Screenshot do Monitor de Ligação, mostrando o painel Adicionar Fontes e o separador agentes não-azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Para rever os agentes Azure e não-Azure que selecionou, vá ao separador **Review.**

      ![Screenshot do Monitor de Ligação, mostrando o painel Adicionar Fontes e o separador Rever](./media/connection-monitor-2-preview/review-sources.png)

   * Quando terminar de configurar fontes, na parte inferior do painel **Adicionar Fontes,** selecione **Done**.

* **Destinos** – Pode monitorizar a conectividade com Os VMs Azure ou qualquer ponto final (um IP público, URL ou FQDN) especificando-os como destinos. Num único grupo de testes, pode adicionar VMs Azure, Office 365 URLs, Dynamics 365 URLs e pontos finais personalizados.

    * Para escolher Os VMs Azure como destinos, selecione o separador **VMs Azure.** Por padrão, os VMs Azure estão agrupados numa hierarquia de subscrição que está na mesma região que selecionou na vista **Create Connection Monitor,** no separador **Basics.** Pode mudar a região e escolher VMs Azure da região recém-seleccionada. Depois pode reduzir do nível de subscrição para outros níveis na hierarquia, como o nível dos Agentes Azure.

       ![Screenshot do painel Add Destinations, mostrando o separador VMs Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Screenshot do painel Add Destinations, mostrando o nível de subscrição](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Para escolher pontos finais como destinos, selecione o separador **Pontos Finais.** A lista de pontos finais inclui URLs de teste do Office 365 e URLs de teste Dynamics 365, agrupados pelo nome. Além destes pontos finais, pode escolher um ponto final que foi criado noutros grupos de teste no mesmo monitor de ligação. 
    
        Para adicionar um novo ponto final, no canto superior direito, **selecione + Pontos Finais**. Em seguida, forneça um nome final e URL, IP ou FQDN.

       ![Screenshot mostrando onde adicionar pontos finais como destinos no Connection Monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Para rever os VMs e pontos finais azure que escolheu, selecione o separador **Rever.**
    * Quando terminar de escolher destinos, selecione **Done**.

* **Configurações de teste** – Pode associar configurações de teste num grupo de teste. O portal Azure permite apenas uma configuração de teste por grupo de teste, mas pode utilizar o ARMClient para adicionar mais.

    * **Nome** – Nome da configuração do teste.
    * **Protocolo** – Escolha TCP, ICMP ou HTTP. Para alterar HTTP para HTTPS, selecione **HTTP** como protocolo e selecione **443** como porta.
        * **Criar configuração de teste** de rede – Esta caixa de verificação só aparece se selecionar **HTTP** no campo **Protocol.** Selecione esta caixa para criar outra configuração de teste que utilize as mesmas fontes e destinos que especificou noutros locais da sua configuração. A configuração de teste recém-criada chama-se `<the name of your test configuration>_networkTestConfig`.
        * **Desativar a via de rastreio** – Este campo aplica-se a grupos de teste cujo protocolo seja TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram topologia e RTT hop-by-hop.
    * **Porta** de destino – Pode personalizar este campo com um porto de destino à sua escolha.
    * **Frequência** de Teste – Utilize este campo para escolher a frequência com que as fontes irão pingdestinos no protocolo e na porta que especificou. Pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. As fontes vão testar a conectividade com destinos com base no valor que escolher.  Por exemplo, se selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez num período de 30 segundos.
    * **Limiar de Sucesso** – Pode fixar limiares nos seguintes parâmetros de rede:
       * **Verificações falhadas** – Deteto a percentagem de controlos que podem falhar quando as fontes verificarem a conectividade com os destinos utilizando os critérios que especificou. No caso do protocolo TCP ou do ICMP, a percentagem de controlos falhados pode ser equiparada à percentagem de perdas de pacotes. Para o protocolo HTTP, este campo representa a percentagem de pedidos http que não receberam resposta.
       * **Tempo de ida e volta** – Desloque o RTT em milissegundos para quanto tempo as fontes podem demorar a ligar-se ao destino através da configuração do teste.
    
       ![Screenshot mostrando onde configurar uma configuração de teste no Monitor de Ligação](./media/connection-monitor-2-preview/add-test-config.png)

Todas as fontes, destinos e configurações de teste que adiciona a um grupo de teste são divididos em testes individuais. Aqui está um exemplo de como fontes e destinos são divididos:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configurações de teste: 2 (Config 1, Config 2)
* Total de testes criados: 12

| Número de teste | Origem | Destino | Configuração do teste |
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

Os monitores de ligação têm os seguintes limites de escala:

* Monitores máximos de ligação por subscrição por região: 100
* Grupos máximos de ensaio por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de ensaio por monitor de ligação: 
    * 20 via ARMClient
    * 2 através do portal Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analisar dados de monitorização e alertas definidos

Depois de criar um monitor de ligação, as fontes verificam a conectividade com destinos com base na configuração do seu teste.

### <a name="checks-in-a-test"></a>Verificação num teste

Com base no protocolo que escolheu na configuração do teste, o Connection Monitor (Preview) executa uma série de verificações para o par de destino-fonte. As verificações são de acordo com a frequência de teste que escolheu.

Se utilizar http, o serviço calcula o número de respostas HTTP que devolveram um código de resposta. O resultado determina a percentagem de controlos falhados. Para calcular o RTT, o serviço mede o tempo entre uma chamada HTTP e a resposta.

Se utilizar tCP ou ICMP, o serviço calcula a percentagem de perda de pacote para determinar a percentagem de controlos falhados. Para calcular o RTT, o serviço mede o tempo de receção do reconhecimento (ACK) para os pacotes que foram enviados. Se tiver ativado os dados da traceroute para os seus testes de rede, pode ver a perda de hop-by-hop e a latência para a sua rede no local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados que os controlos regressam, os testes podem ter os seguintes estados:

* **Passe** – Os valores reais para a percentagem de controlos falhados e RTT estão dentro dos limiares especificados.
* **Falha** – Os valores reais para a percentagem de controlos falhados ou RTT ultrapassaram os limiares especificados. Se não for especificado um limiar, então um teste atinge o estado de Falha quando a percentagem de controlos falhados é de 100.
* **Aviso** – Não foram especificados critérios para a percentagem de controlos falhados. Na ausência de critérios especificados, o Monitor de Ligação (Pré-visualização) atribui automaticamente um limiar. Quando esse limiar é ultrapassado, o estado do teste muda para aviso.

### <a name="data-collection-analysis-and-alerts"></a>Recolha, análise e alertas de dados

Os dados que o Monitor de Ligação (Pré-Visualização) recolhe estão armazenados no espaço de trabalho do Log Analytics. Cria este espaço de trabalho quando criou o monitor de ligação. 

Os dados de monitorização também estão disponíveis nas Métricas do Monitor Do Azure. Pode utilizar o Log Analytics para manter os seus dados de monitorização durante o tempo que quiser. O Azure Monitor armazena métricas por apenas 30 dias por defeito. 

Pode [definir alertas métricos nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Painéis de monitorização

Nos dashboards de monitorização, você vê uma lista dos monitores de ligação a que pode aceder para as suas subscrições, regiões, selos temporais, fontes e tipos de destino.

Quando for ao Monitor de Ligação (Pré-visualização) do Observador de Rede, pode visualizar dados por:

* Monitor de **ligação** – Lista de todos os monitores de ligação criados para as suas subscrições, regiões, selos temporais, fontes e tipos de destino. Esta vista é o padrão.
* **Grupos** de teste – Lista de todos os grupos de teste criados para as suas subscrições, regiões, selos temporais, fontes e tipos de destino. Estes grupos de teste não são filtrados por monitores de ligação.
* **Teste** – Lista de todos os testes que funcionam para as suas subscrições, regiões, selos temporais, fontes e tipos de destino. Estes testes não são filtrados por monitores de ligação ou grupos de teste.

Na imagem seguinte, as três visualizações de dados são indicadas pela seta 1.

No painel de instrumentos, pode expandir cada monitor de ligação para ver os seus grupos de teste. Depois pode expandir cada grupo de testes para ver os testes que nele correm. 

Pode filtrar uma lista com base em:

* **Filtros de alto nível** – Escolha subscrições, regiões, fontes de carimbo seleção e tipos de destino. Consulte a caixa 2 na seguinte imagem.
* **Filtros estatais** – Filtrar pelo estado do monitor de ligação, grupo de teste ou teste. Consulte a seta 3 na seguinte imagem.
* **Filtros personalizados** – **Escolha todos** para fazer uma pesquisa genérica. Para pesquisar por uma entidade específica, selecione a partir da lista de lançamentos. Consulte a seta 4 na seguinte imagem.

![Screenshot mostrando como filtrar vistas de monitores de ligação, grupos de teste e testes no Monitor de Ligação (Pré-visualização)](./media/connection-monitor-2-preview/cm-view.png)

Por exemplo, para analisar todos os testes no Monitor de Ligação (Pré-visualização) onde o IP de origem é 10.192.64.56:
1. Altere a vista para **Teste**.
1. No campo de pesquisa, tipo *10.192.64.56*
1. Na lista de lançamentos, selecione **Sources**.

Para mostrar apenas testes falhados no Monitor de Ligação (Pré-visualização) onde o IP de origem é 10.192.64.56:
1. Altere a vista para **Teste**.
1. Para o filtro estatal, selecione **Fail**.
1. No campo de pesquisa, tipo *10.192.64.56*
1. Na lista de lançamentos, selecione **Sources**.

Para mostrar apenas testes falhados no Monitor de Ligação (Pré-visualização) onde o destino é outlook.office365.com:
1. Alterar a vista para **Testar**.
1. Para o filtro estatal, selecione **Fail**.
1. No campo de pesquisa, *insira outlook.office365.com*
1. Na lista de lançamentos, selecione **Destinos**.

   ![Screenshot mostrando uma vista que é filtrada para mostrar apenas testes falhados para o destino Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Para ver as tendências da RTT e a percentagem de controlos falhados para um monitor de ligação:
1. Selecione o monitor de ligação que pretende investigar. Por padrão, os dados de monitorização são organizados por grupo de teste.

   ![Screenshot mostrando métricas para um monitor de ligação, exibido por grupo de teste](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Escolha o grupo de teste que pretende investigar.

   ![Screenshot mostrando onde selecionar um grupo de teste](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Vê os cinco melhores testes falhados do seu grupo de testes, com base no RTT ou na percentagem de controlos falhados. Para cada teste, você vê o RTT e linhas de tendência para a percentagem de verificações falhadas.
1. Selecione um teste da lista ou escolha outro teste para investigar. Para o seu intervalo de tempo e a percentagem de cheques falhados, vê limiar e valores reais. Para a RTT, vê-se os valores para limiar, médio, mínimo e máximo.

   ![Screenshot mostrando os resultados de um teste para RTT e percentagem de controlos falhados](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Mude o intervalo de tempo para ver mais dados.
1. Altere a vista para ver fontes, destinos ou configurações de teste. 
1. Escolha uma fonte baseada em testes falhados e investigue os cinco testes falhados. Por exemplo, escolha **Visualização por** **fontes** e **visualização de** > Por > **Destinos** para investigar os testes relevantes no monitor de ligação.

   ![Screenshot mostrando métricas de desempenho para os cinco melhores testes falhados](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Para ver as tendências da RTT e a percentagem de controlos falhados para um grupo de teste:

1. Selecione o grupo de teste que pretende investigar. 

    Por predefinição, os dados de monitorização são organizados por fontes, destinos e configurações de teste (testes). Mais tarde, pode alterar a vista de grupos de teste para fontes, destinos ou configurações de teste. Em seguida, escolha uma entidade para investigar os cinco testes falhados. Por exemplo, altere a vista para fontes e destinos para investigar os testes relevantes no monitor de ligação selecionado.
1. Escolha o teste que pretende investigar.

   ![Screenshot mostrando onde selecionar um teste](./media/connection-monitor-2-preview/tg-drill.png)

    Para o seu intervalo de tempo e para a sua percentagem de cheques falhados, você vê valores-limiar e valores reais. Para a RTT, vê-se valores para limiar, média, mínima e máxima. Também vê alertas disparados para o teste que selecionou.
1. Mude o intervalo de tempo para ver mais dados.

Para ver as tendências da RTT e a percentagem de controlos falhados para um teste:
1. Selecione a configuração de origem, destino e teste que pretende investigar.

    Para o seu intervalo de tempo e para a percentagem de controlos falhados, vê valores-limiar e valores reais. Para a RTT, vê-se valores para limiar, média, mínima e máxima. Também vê alertas disparados para o teste que selecionou.

   ![Screenshot mostrando métricas para um teste](./media/connection-monitor-2-preview/test-drill.png)

1. Para ver a topologia da rede, selecione **Topologia.**

   ![Screenshot mostrando o separador Topology da rede](./media/connection-monitor-2-preview/test-topo.png)

1. Para ver os problemas identificados, na topologia, selecione qualquer salto no caminho. (Estes lúpulos são recursos Azure.) Esta funcionalidade não está atualmente disponível para redes no local.

   ![Screenshot mostrando um link de lúpulo selecionado no separador Topology](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Consultas de log em Log Analytics

Utilize o Log Analytics para criar vistas personalizadas dos seus dados de monitorização. Todos os dados que a UI exibe são do Log Analytics. Pode analisar interativamente os dados no repositório. Correlacionar os dados do Agente Health ou de outras soluções baseadas no Log Analytics. Exportar os dados para Excel ou Power BI, ou criar um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Nos monitores de ligação criados antes da experiência do Monitor de Ligação (Pré-visualização), todas as quatro métricas estão disponíveis: % sondas falhadas, AverageRoundtripMs, ChecksFailedPercent (Pré-visualização) e RoundTripTimeMs (Pré-visualização). Nos monitores de ligação criados na experiência Do Monitor de Ligação (Pré-visualização), os dados só estão disponíveis para as métricas que estão marcadas com *(Pré-visualização)* .

![Screenshot mostrando métricas no Monitor de Ligação (Pré-visualização)](./media/connection-monitor-2-preview/monitor-metrics.png)

Quando utilizar métricas, detete o tipo de recurso como Microsoft.Network/NetworkWatchers/connectionMonitors

| Métrica | Nome a apresentar | Unidade | Tipo de agregação | Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % sondas falhadas | Percentagem | Média | A percentagem de sondas de monitorização da conectividade falhou. | Sem dimensões |
| AverageRoundtripMs | Avg. Tempo de ida e volta (ms) | Milissegundos | Média | Rede rtt média para sondas de monitorização de conectividade enviadas entre fonte e destino. |             Sem dimensões |
| ChecksFailedPercent (Pré-visualização) | % de verificações falhadas (pré-visualização) | Percentagem | Média | Percentagem de cheques falhados para um teste. | ConnectionMonitorResourceid <br>Endereço de origem <br>Nome de origem <br>SourceResourceId <br>sourceType <br>Protocolo <br>DestinationAddress <br>Nome de destino <br>DestinationResourceid <br>DestinoTipo <br>DestinationPort <br>TestGroupName <br>Nome de Configuração de Teste <br>Região |
| RoundTripTimeMs (Pré-visualização) | Tempo de ida e volta (ms) (Pré-visualização) | Milissegundos | Média | RTT para verificações enviadas entre fonte e destino. Este valor não é mediado. | ConnectionMonitorResourceid <br>Endereço de origem <br>Nome de origem <br>SourceResourceId <br>sourceType <br>Protocolo <br>DestinationAddress <br>Nome de destino <br>DestinationResourceid <br>DestinoTipo <br>DestinationPort <br>TestGroupName <br>Nome de Configuração de Teste <br>Região |

#### <a name="metric-alerts-in-azure-monitor"></a>Alertas métricos no Monitor Azure

Para criar um alerta no Monitor Azure:

1. Escolha o recurso do monitor de ligação que criou no Monitor de Ligação (Pré-visualização).
1. Certifique-se de que a **Métrica** aparece como tipo de sinal para o monitor de ligação.
1. Na **condição de adição**, para o nome do **sinal,** selecione **ChecksFailedPercent (Pré-visualização)** ou **RoundTripTimeMs (Pré-visualização)** .
1. Para **o tipo de sinal,** escolha **Métricas**. Por exemplo, selecione **ChecksFailedPercent (Pré-visualização)** .
1. Todas as dimensões da métrica estão listadas. Escolha o nome de dimensão e o valor de dimensão. Por exemplo, selecione **O Endereço fonte** e introduza o endereço IP de qualquer fonte no seu monitor de ligação.
1. Na Lógica de **Alerta,** preencha os seguintes detalhes:
   * **Tipo de condição**: **Estática**.
   * **Condição** e **Limiar**.
   * **Agregação Granularity and Frequency of Evaluation**: Connection Monitor (Preview) atualiza os dados a cada minuto.
1. Em **Ações,** escolha o seu grupo de ação.
1. Forneça detalhes de alerta.
1. Crie a regra de alerta.

   ![Screenshot mostrando a área de regra Criar no Monitor Azure; "Endereço de origem" e "nome de ponto final de origem" são destacados](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas na sua rede

O Monitor de Ligação (Pré-visualização) ajuda a diagnosticar problemas no seu monitor de ligação e na sua rede. Os problemas na sua rede híbrida são detetados pelos agentes Log Analytics que instalou anteriormente. Os problemas em Azure são detetados pela extensão do Observador da Rede. 

Pode ver problemas na rede Azure na topologia da rede.

Para redes cujas fontes estejam no local, podem ser detetadas as seguintes questões:

* Pedido cronometrado.
* Ponto final não resolvido pelo DNS – temporário ou persistente. URL inválido.
* Não há anfitriões encontrados.
* Fonte incapaz de ligar ao destino. Alvo não alcançável através do ICMP.
* Questões relacionadas com certificados: 
    * Certificado de cliente necessário para autenticar agente. 
    * A lista de deslocalização de certificados não é acessível. 
    * O nome do anfitrião do ponto final não corresponde ao sujeito do certificado ou ao nome alternativo do sujeito. 
    * Falta um certificado de raiz na loja local de certificação fidedigna de computador esquecê-lo. 
    * O certificado SSL é expirado, inválido, revogado ou incompatível.

Para redes cujas fontes são VMs Azure, podem ser detetados os seguintes problemas:

* Problemas com o agente:
    * O agente parou.
    * Resolução DNS falhada.
    * Nenhuma aplicação ou ouvinte ou ouvido na porta de destino.
    * A tomada não pôde ser aberta.
* Questões estatais VM: 
    * A iniciar
    * A parar
    * Parada
    * A desalocar
    * Desalocada
    * Reiniciar
    * Não atribuído
* Falta a entrada na mesa da ARP.
* O trânsito foi bloqueado devido a problemas de firewall locais ou regras da NSG.
* Problemas de gateway de rede virtual: 
    * Rotas desaparecidas.
    * O túnel entre dois portões está desligado ou desaparecido.
    * A segunda porta de entrada não foi encontrada perto do túnel.
    * Nenhuma informação foi encontrada.
* A rota desapareceu no Microsoft Edge.
* O trânsito parou por causa das rotas do sistema ou da UDR.
* O BGP não está ativado na ligação de gateway.
* A sonda DIP está no equilíbrio de carga.
