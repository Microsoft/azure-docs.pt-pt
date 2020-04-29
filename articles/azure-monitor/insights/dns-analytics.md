---
title: Solução DNS Analytics no Monitor Azure [ Monitor] Microsoft Docs
description: Configurar e utilizar a solução DNS Analytics no Monitor Azure para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657338"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização DNS Analytics

![Símbolo dNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

Este artigo descreve como configurar e usar a solução Azure DNS Analytics no Azure Monitor para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.

O DNS Analytics ajuda-o a:

- Identifique clientes que tentem resolver nomes de domínio maliciosos.
- Identifique os registos de recursos.
- Identifique frequentemente nomes de domínio e clientes dNS falantes.
- Ver carga de pedido nos servidores DNS.
- Ver falhas dinâmicas de registo dNS.

A solução recolhe, analisa e correlaciona registos analíticos e de auditoria do Windows DNS e outros dados relacionados dos seus servidores DNS.

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as fontes conectadas que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../platform/agent-windows.md) | Sim | A solução recolhe informações dNS de agentes do Windows. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações dNS de agentes diretos do Linux. |
| [Grupo de gestão do System Center Operations Manager](../platform/om-agents.md) | Sim | A solução recolhe informações do DNS de agentes de um grupo de gestão de Gestão de Operações conectados. Não é necessária uma ligação direta do agente do Gestor de Operações ao Monitor Azure. Os dados são encaminhados do grupo de gestão para o espaço de trabalho log Analytics. |
| [Conta de armazenamento azure](../platform/collect-azure-metrics-logs.md) | Não | O armazenamento azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da recolha de dados

A solução recolhe o inventário DNS e os dados relacionados com o evento DNS dos servidores DNS onde um agente Log Analytics está instalado. Estes dados são então enviados para o Monitor Azure e apresentados no painel de solução. Os dados relacionados com o inventário, como o número de servidores, zonas e registos de recursos dNS, são recolhidos executando os cmdlets DNS PowerShell. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados com o evento são recolhidos em quase tempo real a partir dos [registos analíticos e de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos por registos e diagnósticos dNS melhorados no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Utilize as seguintes informações para configurar a solução:

- Deve ter um agente [Do Windows](../platform/agent-windows.md) ou Do Gestor de [Operações](../platform/om-agents.md) em cada servidor DNS que pretenda monitorizar.
- Pode adicionar a solução DNS Analytics ao seu espaço de trabalho Log Analytics a partir do [Mercado Azure](https://aka.ms/dnsanalyticsazuremarketplace). Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](solutions.md).

A solução começa a recolher dados sem necessidade de mais configurações. No entanto, pode utilizar a seguinte configuração para personalizar a recolha de dados.

### <a name="configure-the-solution"></a>Configure a solução

No painel de solução, clique em **Configuração** para abrir a página de Configuração DNS Analytics. Existem dois tipos de alterações de configuração que pode fazer:

- **Nomes de domínio listados em branco.** A solução não processa todas as consultas de procuração. Mantém uma lista branca de sufixos de nome de domínio. As consultas de procura que resolvem os nomes de domínio que correspondem aos sufixos de nome de domínio nesta lista branca não são processadas pela solução. Não processar nomes de domínio seletos brancos ajuda a otimizar os dados enviados ao Monitor Azure. A lista branca padrão inclui nomes de domínio público popular, como www.google.com e www.facebook.com. Pode visualizar a lista de incumprimento completa através do scrolling.

  Pode modificar a lista para adicionar qualquer sufixo de nome de domínio que deseja ver insights de procura. Também pode remover qualquer sufixo de nome de domínio que não queira ver insights de procura.

- Limiar de **cliente falante.** Os clientes DNS que excedam o limiar para o número de pedidos de procura são destacados na lâmina **dNS Clientes.** O limiar de incumprimento é de 1.000. Pode editar o limiar.

    ![Nomes de domínio whitelist](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se estiver a utilizar o Agente de Monitorização da Microsoft para se ligar ao seu espaço de trabalho log Analytics, está instalado o seguinte pacote de gestão:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Se o seu grupo de gestão de Gestão de Operações estiver ligado ao seu espaço de trabalho Log Analytics, os seguintes pacotes de gestão são instalados no Gestor de Operações quando adicionar esta solução. Não é necessária configuração ou manutenção destes pacotes de gestão:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Configuração dNS Analytics (Microsoft.IntelligencePack.Dns.Configuração)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Utilize a solução DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


O azulejo DNS inclui o número de servidores DNS onde os dados estão a ser recolhidos. Inclui também o número de pedidos feitos pelos clientes para resolver domínios maliciosos nas últimas 24 horas. Quando clica no azulejo, o painel de instrumentos de solução abre-se.

![Azulejo Sinalítico DNS](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O painel de solução mostra informação resumida para as várias características da solução. Inclui também ligações à visão detalhada para análise forense e diagnóstico. Por padrão, os dados são mostrados nos últimos sete dias. Pode alterar a data e o intervalo de horas utilizando o controlo de seleção de **datas,** como mostra a seguinte imagem:

![Controlo de seleção de tempo](./media/dns-analytics/dns-time.png)

O painel de instrumentos de solução mostra as seguintes lâminas:

**Segurança DNS**. Reporta os clientes DoNs que estão a tentar comunicar com domínios maliciosos. Ao utilizar feeds de inteligência de ameaça da Microsoft, o DNS Analytics pode detetar iPs de clientes que estão a tentar aceder a domínios maliciosos. Em muitos casos, os dispositivos infetados por malware "marcam" para o centro de "comando e controlo" do domínio malicioso, resolvendo o nome de domínio do malware.

![Lâmina de segurança DNS](./media/dns-analytics/dns-security-blade.png)

Quando clica num IP de cliente na lista, o Log Search abre e mostra os detalhes de pesquisa da respetiva consulta. No exemplo seguinte, o DNS Analytics detetou que a comunicação foi feita com um [IRCbot:](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)

![Resultados de pesquisa de log mostrando ircbot](./media/dns-analytics/ircbot.png)

A informação ajuda-o a identificar o:

- Cliente IP que iniciou a comunicação.
- Nome de domínio que resolve o IP malicioso.
- Endereços IP que o nome de domínio resolve.
- Endereço IP malicioso.
- Gravidade da questão.
- Razão para colocar na lista negra o IP malicioso.
- Hora de deteção.

**Domínios Queried.** Fornece os nomes de domínio mais frequentes que estão a ser consultados pelos clientes DNS no seu ambiente. Pode ver a lista de todos os nomes de domínio consultados. Também pode aprofundar os detalhes do pedido de pesquisa de um nome de domínio específico em Pesquisa de Registo.

![Lâmina de domínios queried](./media/dns-analytics/domains-queried-blade.png)

**Clientes DNS**. Informa os clientes *que violam o limiar* do número de consultas no período de tempo escolhido. Pode ver a lista de todos os clientes do DNS e os detalhes das consultas feitas por eles em Pesquisa de Registo.

![Lâmina de clientes DNS](./media/dns-analytics/dns-clients-blade.png)

**Registos DNS Dinâmicos**. Relatórios nome de falhas no registo. Todas as falhas de registo de [registos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de recursos de endereço (Tipo A e AAAA) são destacadas juntamente com os IPs do cliente que efizeram os pedidos de registo. Em seguida, pode utilizar estas informações para encontrar a causa principal da falha de registo seguindo estes passos:

1. Encontre a zona que é autoritária para o nome que o cliente está a tentar atualizar.

1. Utilize a solução para verificar as informações de inventário dessa zona.

1. Verifique se a atualização dinâmica da zona está ativada.

1. Verifique se a zona está configurada para uma atualização dinâmica segura ou não.

    ![Lâmina dinâmica de registos DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Pedidos de registo de nome**. O azulejo superior mostra uma linha de tendência de pedidos de atualização dinâmica DNS bem sucedidos e falhados. O azulejo inferior lista os 10 principais clientes que estão a enviar pedidos de atualização DNS falhados para os servidores DNS, classificados pelo número de falhas.

![Nome registra lâmina de pedidos](./media/dns-analytics/name-reg-req-blade.png)

**Amostra DDI Analytics Consultas**. Contém uma lista das consultas de pesquisa mais comuns que rebuscam dados de análise crua diretamente.


![Consultas de exemplo](./media/dns-analytics/queries.png)

Você pode usar estas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. As consultas ligam-se à página de Pesquisa de Registos DNS Analytics onde os resultados são apresentados:

- **Lista de Servidores DNS**. Mostra uma lista de todos os servidores DNS com o seu FQDN associado, nome de domínio, nome da floresta e IPs do servidor.
- **Lista de Zonas DNS**. Mostra uma lista de todas as zonas dNS com o nome de zona associado, estado de atualização dinâmica, servidores de nome e estado de assinatura dNSSEC.
- **Registos de Recursos Não Utilizados.** Mostra uma lista de todos os registos de recursos não utilizados/velhos. Esta lista contém o nome de registo de recursos, o tipo de registo de recursos, o servidor DNS associado, o tempo de geração de registos e o nome da zona. Pode utilizar esta lista para identificar os registos de recursos DNS que já não estão a ser utilizados. Com base nesta informação, pode então remover essas entradas dos servidores DNS.
- Carga de consulta de **servidores DNS**. Mostra informações para que possa obter uma perspetiva da carga DNS nos seus servidores DNS. Estas informações podem ajudá-lo a planear a capacidade dos servidores. Pode ir ao separador **Métricas** para alterar a vista para uma visualização gráfica. Esta vista ajuda-o a entender como a carga DNS é distribuída pelos seus servidores DNS. Mostra as tendências da taxa de consulta dNS para cada servidor.

    ![Resultados de pesquisa de registo de servidores DNS](./media/dns-analytics/dns-servers-query-load.png)

- Carga de consulta de **zonas DNS**. Mostra as estatísticas dNS de consulta de zona por segundo de todas as zonas nos servidores DNS que estão a ser geridas pela solução. Clique no separador **Métricas** para alterar a visualização gráfica dos resultados.
- **Eventos**de Configuração . Mostra todos os eventos de mudança de configuração DNS e mensagens associadas. Em seguida, pode filtrar estes eventos com base no tempo do evento, ID do evento, servidor DNS ou categoria de tarefa. Os dados podem ajudá-lo a auditar alterações feitas em servidores DNS específicos em momentos específicos.
- **Registo Analítico DNS**. Mostra todos os eventos analíticos em todos os servidores DNS geridos pela solução. Em seguida, pode filtrar estes eventos com base no tempo do evento, ID do evento, servidor DNS, IP do cliente que fez a consulta de procura e categoria de tarefa do tipo consulta. Os eventos analíticos do servidor DNS permitem o rastreio de atividade no servidor DNS. Um evento analítico é registado cada vez que o servidor envia ou recebe informações de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Pesquisar utilizando pesquisa de registo sintetizá-lo

Na página de Pesquisa de Registos, pode criar uma consulta. Pode filtrar os resultados da sua pesquisa utilizando controlos de faceta. Também pode criar consultas avançadas para transformar, filtrar e reportar os seus resultados. Comece por utilizar as seguintes consultas:

1. Na **caixa**de consulta `DnsEvents` de pesquisa , escreva para visualizar todos os eventos DNS gerados pelos servidores DNS geridos pela solução. Os resultados listam os dados de registo de todos os eventos relacionados com consultas de procuração, registos dinâmicos e alterações de configuração.

    ![Pesquisa de log DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Para visualizar os dados de registo para consultas de procura, selecione **LookUpQuery** como o filtro **Subtipo** do controlo de facetas à esquerda. É apresentada uma tabela que lista todos os eventos de consulta de procura para o período de tempo selecionado.

    b. Para visualizar os dados de registo sinuoso para registos dinâmicos, selecione **DynamicRegistration** como filtro **subtipo** a partir do controlo de facetas à esquerda. É apresentada uma tabela que lista todos os eventos dinâmicos de registo para o período de tempo selecionado.

    c. Para visualizar os dados de registo para alterações de configuração, **selecione ConfigurationChange** como o filtro **Subtipo** do controlo de faceta à esquerda. É apresentada uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado.

1. Na **caixa**de consulta `DnsInventory` de pesquisa , escreva para visualizar todos os dados relacionados com o inventário DNS para os servidores DNS geridos pela solução. Os resultados listam os dados de registo para servidores DNS, zonas DNS e registos de recursos.

    ![Pesquisa de registo DnsInventário](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Resolução de problemas

Passos comuns de resolução de problemas:

1. Dados de procuração DNS em falta - Para resolver este problema, tente reajustar o config ou apenas carregar a página de configuração uma vez no portal. Para reajustar, basta alterar uma definição para outro valor, em seguida, troque-a de volta para o valor original, e guarde o config.

## <a name="feedback"></a>Comentários

Para fornecer feedback, visite a [página Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) para publicar ideias para as funcionalidades do DNS Analytics para trabalhar. 

## <a name="next-steps"></a>Passos seguintes

[Registos](../log-query/log-query-overview.md) de consultas para visualizar registos de registos dNS detalhados.
