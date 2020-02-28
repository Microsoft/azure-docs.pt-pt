---
title: Solução DNS Analytics no Monitor Azure [ Monitor] Microsoft Docs
description: Configurar e utilizar a solução DNS Analytics no Monitor Azure para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657338"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recolher informações sobre a infraestrutura DNS com a solução de análise de DNS pré-visualização

![Símbolo de análise de DNS](./media/dns-analytics/dns-analytics-symbol.png)

Este artigo descreve como configurar e usar a solução Azure DNS Analytics no Azure Monitor para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.

Análise de DNS ajuda-o a:

- Identificar os clientes que tentam resolver os nomes de domínio malicioso.
- Identifica registos de recursos obsoletos.
- Identifica os nomes de domínio consultados com frequência e conversador clientes DNS.
- Carga de pedidos de vista nos servidores DNS.
- Ver falhas de registo DNS dinâmicas.

A solução recolhe, analisa e correlaciona a análise de DNS de Windows e registos de auditoria e outros dados relacionados de seus servidores DNS.

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../platform/agent-windows.md) | Sim | A solução recolhe informações de DNS de agentes do Windows. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de DNS de agentes diretos do Linux. |
| [Grupo de gestão de gestão de gestão de gestão de operações do Centro de Sistema](../platform/om-agents.md) | Sim | A solução recolhe informações de DNS de agentes num grupo de gestão do Operations Manager ligado. Não é necessária uma ligação direta do agente do Gestor de Operações ao Monitor Azure. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é utilizado pela solução. |

### <a name="data-collection-details"></a>Detalhes de recolha de dados

A solução recolhe dados de relacionados a eventos DNS de inventário DNS e dos servidores DNS onde está instalado um agente de Log Analytics. Estes dados são então enviados para o Monitor Azure e apresentados no painel de solução. Dados relacionados com o inventário, como o número de servidores DNS, zonas e registos de recursos, são recolhidos ao executar os cmdlets do PowerShell de DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados com o evento são recolhidos em quase tempo real a partir dos [registos analíticos e de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos por registos e diagnósticos dNS melhorados no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Utilize as seguintes informações para configurar a solução:

- Deve ter um agente [Do Windows](../platform/agent-windows.md) ou Do Gestor de [Operações](../platform/om-agents.md) em cada servidor DNS que pretenda monitorizar.
- Pode adicionar a solução DNS Analytics ao seu espaço de trabalho Log Analytics a partir do [Mercado Azure](https://aka.ms/dnsanalyticsazuremarketplace). Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](solutions.md).

A solução iniciar a recolha de dados sem a necessidade de configuração adicional. No entanto, pode utilizar a seguinte configuração para personalizar a recolha de dados.

### <a name="configure-the-solution"></a>Configurar a solução

No painel de solução, clique em **Configuração** para abrir a página de Configuração DNS Analytics. Existem dois tipos de alterações de configuração que pode fazer:

- **Nomes de domínio listados em branco.** A solução não processa todas as consultas de pesquisa. Ele mantém uma lista de permissões de sufixos de nome de domínio. As consultas de pesquisa que resolver os nomes de domínio que correspondem aos sufixos de nome de domínio nesta lista de permissões não são processadas pela solução. Não processar nomes de domínio seletos brancos ajuda a otimizar os dados enviados ao Monitor Azure. A lista de permissões predefinida inclui nomes de domínio públicos conhecidos, como www.google.com e www.facebook.com. Pode ver a lista completa ao deslocar.

  É possível modificar a lista para adicionar qualquer sufixo de nome de domínio que pretende ver informações de pesquisa para. Também pode remover qualquer sufixo de nome de domínio que não pretende ver informações de pesquisa para.

- Limiar de **cliente falante.** Os clientes DNS que excedam o limiar para o número de pedidos de procura são destacados na lâmina **dNS Clientes.** O limiar predefinido é 1000. Pode editar o limiar.

    ![Nomes de domínio na lista de permissões](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se estiver a utilizar o Microsoft Monitoring Agent para ligar à sua área de trabalho do Log Analytics, está instalado o pacote de gestão seguintes:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Se o grupo de gestão do Operations Manager estiver ligado à sua área de trabalho do Log Analytics, os seguintes pacotes de gestão são instalados no Operations Manager ao adicionar esta solução. Não existe nenhuma configuração necessária ou a manutenção destes pacotes de gestão:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Configuração de análise DNS do Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Utilizar a solução de análise de DNS

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


O azulejo DNS inclui o número de servidores DNS onde os dados estão a ser recolhidos. Também inclui o número de pedidos efetuados por clientes para resolver domínios maliciosos nas últimas 24 horas. Quando clica no mosaico, abre o dashboard da solução.

![Mosaico de análise de DNS](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O dashboard de solução mostra as informações resumidas para as diversas funcionalidades da solução. Ele também inclui ligações para a vista detalhada para análise forense e de diagnóstico. Por predefinição, os dados são apresentados nos últimos sete dias. Pode alterar a data e o intervalo de horas utilizando o controlo de seleção de **datas,** como mostra a seguinte imagem:

![Controlo de seleção de tempo](./media/dns-analytics/dns-time.png)

O dashboard de solução mostra os seguintes painéis:

**Segurança DNS**. Relatórios de clientes DNS que estão a tentar comunicar com domínios maliciosos. Ao utilizar os feeds de inteligência de ameaças do Microsoft, análise de DNS pode detetar cliente IPs que está a tentar aceder aos domínios maliciosos. Em muitos casos, dispositivos infetados com software maligno "aumentar horizontalmente" para o Centro de "comando e controlo" do domínio malicioso por resolver o nome de domínio de software maligno.

![Painel de segurança de DNS](./media/dns-analytics/dns-security-blade.png)

Quando clica num IP de cliente na lista, pesquisa de registos é aberto e mostra os detalhes de pesquisa da respetiva consulta. No exemplo seguinte, o DNS Analytics detetou que a comunicação foi feita com um [IRCbot:](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)

![Resultados de pesquisa de registo mostrando Win32/ircbot](./media/dns-analytics/ircbot.png)

As informações de ajudam-o a identificar o:

- Cliente IP que iniciou a comunicação.
- Nome de domínio que é resolvido para o IP malicioso.
- Endereços IP que o nome de domínio resolve.
- Endereço IP malicioso.
- Gravidade do problema.
- Motivo para blacklisting o IP malicioso.
- Hora da deteção.

**Domínios Queried.** Fornece os nomes de domínio mais frequentes que está sendo consultados pelos clientes do DNS no seu ambiente. Pode ver a lista de todos os nomes de domínio consultados. Também pode desagregar os detalhes de pedido de pesquisa de um nome de domínio específico na pesquisa de registos.

![Painel de Queried de domínios](./media/dns-analytics/domains-queried-blade.png)

**Clientes DNS**. Informa os clientes *que violam o limiar* do número de consultas no período de tempo escolhido. Pode ver a lista de todos os clientes DNS e os detalhes das consultas feitas pelo-los em pesquisa de registos.

![Painel clientes DNS](./media/dns-analytics/dns-clients-blade.png)

**Registos DNS Dinâmicos**. Falhas de registo de nome de relatórios. Todas as falhas de registo de [registos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de recursos de endereço (Tipo A e AAAA) são destacadas juntamente com os IPs do cliente que efizeram os pedidos de registo. Em seguida, pode utilizar estas informações para encontrar a causa da falha de registo ao seguir estes passos:

1. Encontre a zona que é autoritativa para o nome que o cliente está a tentar atualizar.

1. Utilize a solução para verificar as informações de inventário dessa zona.

1. Certifique-se de que a atualização dinâmica para a zona está ativada.

1. Verifique se a zona está configurada para a atualização dinâmica segura ou não.

    ![Painel de registos do DNS dinâmico](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Pedidos de registo de nome**. O mosaico superior mostra uma trendline de pedidos de atualização dinâmica de DNS com êxito ou falhados. O mosaico inferior apresenta uma lista de principais 10 clientes que estão a enviar solicitações de atualização de DNS falhou para os servidores DNS, classificados pelo número de falhas.

![Painel de pedidos de registo de nome](./media/dns-analytics/name-reg-req-blade.png)

**Amostra DDI Analytics Consultas**. Contém uma lista das consultas de pesquisa mais comuns que obtêm dados de análise brutos diretamente.


![Amostras de consultas](./media/dns-analytics/queries.png)

Pode utilizar estas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. A ligação de consultas para a página de pesquisa de registos de análise de DNS em que os resultados são apresentados:

- **Lista de Servidores DNS**. Mostra uma lista de todos os servidores DNS com seus associados FQDN, nome de domínio, nome da floresta e servidor IPs.
- **Lista de Zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome da zona associados, o estado de atualização dinâmica, a servidores de nomes e o estado de assinatura DNSSEC.
- **Registos de Recursos Não Utilizados.** Mostra uma lista de todos os registros de recursos não utilizados/obsoleta. Esta lista contém o nome do registo de recursos, o tipo de registo de recursos, o servidor DNS associado, geração de registos e o nome da zona. Pode utilizar esta lista para identificar os registos de recursos DNS já não estão em utilização. Com base nessas informações, pode remover, em seguida, essas entradas dos servidores DNS.
- Carga de consulta de **servidores DNS**. Mostra informações para que pode obter uma perspetiva da carga DNS nos seus servidores DNS. Estas informações podem ajudar a planear a capacidade para os servidores. Pode ir ao separador **Métricas** para alterar a vista para uma visualização gráfica. Esta vista ajuda a compreender a forma como a carga DNS é distribuída em todos os servidores DNS. Consulta DNS é mostra tendências da taxa de cada servidor.

    ![Resultados de pesquisa de registo de consulta de servidores DNS](./media/dns-analytics/dns-servers-query-load.png)

- Carga de consulta de **zonas DNS**. Mostra as estatísticas de zona consulta por segundo de DNS de todas as zonas nos servidores DNS a ser geridos pela solução. Clique no separador **Métricas** para alterar a visualização gráfica dos resultados.
- **Eventos**de Configuração . Mostra todos os eventos de alteração de configuração de DNS e mensagens associadas a eles. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, ou categoria de tarefa. Os dados podem ajudá-lo a auditar as alterações feitas aos servidores DNS específicas em horários específicos.
- **Registo Analítico DNS**. Mostra todos os eventos analíticos em todos os servidores DNS geridos pela solução. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, IP de cliente que efetuou a consulta de pesquisa e a categoria de tarefa do tipo de consulta. Eventos analíticos de servidor DNS ativar a atividade de controle no servidor DNS. Um evento de análise é registado sempre que o servidor envia ou recebe informações de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Procure, utilizando a pesquisa de registos de análise de DNS

Na página de pesquisa de registos, pode criar uma consulta. Pode filtrar os resultados da pesquisa através de controlos de faceta. Também pode criar consultas avançadas para transformação, o filtro e o relatório em seus resultados. Iniciar com as seguintes consultas:

1. Na caixa de consulta de **pesquisa,** o tipo `DnsEvents` para visualizar todos os eventos DNS gerados pelos servidores DNS geridos pela solução. Os resultados listam os dados de registo para todos os eventos relacionados com consultas de pesquisa, registos dinâmicos e alterações de configuração.

    ![Pesquisa de registos de DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Para visualizar os dados de registo para consultas de procura, selecione **LookUpQuery** como o filtro **Subtipo** do controlo de facetas à esquerda. É apresentada uma tabela que lista todos os eventos de consulta de pesquisa para o período de tempo selecionado.

    b. Para visualizar os dados de registo sinuoso para registos dinâmicos, selecione **DynamicRegistration** como filtro **subtipo** a partir do controlo de facetas à esquerda. É apresentada uma tabela que lista todos os eventos de registo dinâmico para o período de tempo selecionado.

    c. Para visualizar os dados de registo para alterações de configuração, **selecione ConfigurationChange** como o filtro **Subtipo** do controlo de faceta à esquerda. É apresentada uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado.

1. Na **caixa**de consulta de pesquisa , o type `DnsInventory` para visualizar todos os dados relacionados com o inventário DNS para os servidores DNS geridos pela solução. Os resultados listam os dados de registo para servidores DNS, zonas DNS e registos de recursos.

    ![Pesquisa de registos de DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Resolução de problemas

Passos comuns de resolução de problemas:

1. Dados de procuração DNS em falta - Para resolver este problema, tente reajustar o config ou apenas carregar a página de configuração uma vez no portal. Para reajustar, basta alterar uma definição para outro valor, em seguida, troque-a de volta para o valor original, e guarde o config.

## <a name="feedback"></a>Comentários

Para fornecer feedback, visite a [página Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) para publicar ideias para as funcionalidades do DNS Analytics para trabalhar. 

## <a name="next-steps"></a>Passos seguintes

[Registos](../log-query/log-query-overview.md) de consultas para visualizar registos de registos dNS detalhados.
