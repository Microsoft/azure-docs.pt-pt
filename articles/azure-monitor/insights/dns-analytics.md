---
title: Solução de análise de DNS no Azure Monitor | Documentos da Microsoft
description: Configurar e utilizar a solução de análise de DNS no Azure Monitor para recolher informações acerca da infraestrutura DNS em segurança, desempenho e operações.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 6e3c456b3ee92f30b3b152a39faf3f1fe483013f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876607"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recolher informações sobre a infraestrutura DNS com a solução de análise de DNS pré-visualização

![Símbolo de análise de DNS](./media/dns-analytics/dns-analytics-symbol.png)

Este artigo descreve como configurar e utilizar a solução de análise de DNS do Azure no Azure Monitor para recolher informações acerca da infraestrutura DNS em segurança, desempenho e operações.

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
| [Grupo de gestão do System Center Operations Manager](../platform/om-agents.md) | Sim | A solução recolhe informações de DNS de agentes num grupo de gestão do Operations Manager ligado. Não é necessária uma conexão direta do agente do Operations Manager para o Azure Monitor. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é utilizado pela solução. |

### <a name="data-collection-details"></a>Detalhes de recolha de dados

A solução recolhe dados de relacionados a eventos DNS de inventário DNS e dos servidores DNS onde está instalado um agente de Log Analytics. Estes dados, em seguida, são carregados para o Azure Monitor e apresentados no dashboard da solução. Dados relacionados com o inventário, como o número de servidores DNS, zonas e registos de recursos, são recolhidos ao executar os cmdlets do PowerShell de DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são recolhidos quase em tempo real do [analíticas e registos de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecida pelo aprimorados registo e diagnósticos DNS no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Utilize as seguintes informações para configurar a solução:

- Tem de ter uma [Windows](../platform/agent-windows.md) ou [Operations Manager](../platform/om-agents.md) agente em cada servidor DNS que pretende monitorizar.
- Pode adicionar a solução de análise de DNS para a área de trabalho do Log Analytics do [do Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Também pode utilizar o processo descrito em [soluções de adicionar o Azure Monitor da Galeria de soluções](solutions.md).

A solução iniciar a recolha de dados sem a necessidade de configuração adicional. No entanto, pode utilizar a seguinte configuração para personalizar a recolha de dados.

### <a name="configure-the-solution"></a>Configurar a solução

No dashboard de solução, clique em **configuração** para abrir a página de configuração de análise de DNS. Existem dois tipos de alterações de configuração que pode fazer:

- **Nomes de domínio na lista de permissões**. A solução não processa todas as consultas de pesquisa. Ele mantém uma lista de permissões de sufixos de nome de domínio. As consultas de pesquisa que resolver os nomes de domínio que correspondem aos sufixos de nome de domínio nesta lista de permissões não são processadas pela solução. Não processar nomes de domínio na lista de permissões ajuda a otimizar os dados enviados para o Azure Monitor. A lista de permissões predefinida inclui nomes de domínio públicos conhecidos, como www.google.com e www.facebook.com. Pode ver a lista completa ao deslocar.

 É possível modificar a lista para adicionar qualquer sufixo de nome de domínio que pretende ver informações de pesquisa para. Também pode remover qualquer sufixo de nome de domínio que não pretende ver informações de pesquisa para.

- **Do cliente conversador**. Os clientes DNS que excedam o limiar para o número de pedidos de pesquisa está realçado na **clientes DNS** painel. O limiar predefinido é 1000. Pode editar o limiar.

    ![Nomes de domínio na lista de permissões](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se estiver a utilizar o Microsoft Monitoring Agent para ligar à sua área de trabalho do Log Analytics, está instalado o pacote de gestão seguintes:

- Pacote de informações de Recoletor de dados de DNS da Microsoft (Microsoft.IntelligencePacks.Dns)

Se o grupo de gestão do Operations Manager estiver ligado à sua área de trabalho do Log Analytics, os seguintes pacotes de gestão são instalados no Operations Manager ao adicionar esta solução. Não existe nenhuma configuração necessária ou a manutenção destes pacotes de gestão:

- Pacote de informações de Recoletor de dados de DNS da Microsoft (Microsoft.IntelligencePacks.Dns)
- Configuração de análise DNS do Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Utilizar a solução de análise de DNS

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


O mosaico DNS inclui o número de servidores DNS, onde os dados estão a ser recolhidos. Também inclui o número de pedidos efetuados por clientes para resolver domínios maliciosos nas últimas 24 horas. Quando clica no mosaico, abre o dashboard da solução.

![Mosaico de análise de DNS](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O dashboard de solução mostra as informações resumidas para as diversas funcionalidades da solução. Ele também inclui ligações para a vista detalhada para análise forense e de diagnóstico. Por predefinição, os dados são apresentados nos últimos sete dias. Pode alterar o intervalo de data e hora, utilizando o **controlo de seleção de data / hora**, conforme mostrado na imagem seguinte:

![Controlo de seleção de tempo](./media/dns-analytics/dns-time.png)

O dashboard de solução mostra os seguintes painéis:

**Segurança DNS**. Relatórios de clientes DNS que estão a tentar comunicar com domínios maliciosos. Ao utilizar os feeds de inteligência de ameaças do Microsoft, análise de DNS pode detetar cliente IPs que está a tentar aceder aos domínios maliciosos. Em muitos casos, dispositivos infetados com software maligno "aumentar horizontalmente" para o Centro de "comando e controlo" do domínio malicioso por resolver o nome de domínio de software maligno.

![Painel de segurança de DNS](./media/dns-analytics/dns-security-blade.png)

Quando clica num IP de cliente na lista, pesquisa de registos é aberto e mostra os detalhes de pesquisa da respetiva consulta. No exemplo seguinte, a análise de DNS detetou que a comunicação foi efetuada com uma [Win32/ircbot](https://www.microsoft.com/wdsi/threats/threat-search?query=Win32/IRCbot):

![Resultados de pesquisa de registo mostrando Win32/ircbot](./media/dns-analytics/ircbot.png)

As informações de ajudam-o a identificar o:

- Cliente IP que iniciou a comunicação.
- Nome de domínio que é resolvido para o IP malicioso.
- Endereços IP que o nome de domínio resolve.
- Endereço IP malicioso.
- Gravidade do problema.
- Motivo para blacklisting o IP malicioso.
- Hora da deteção.

**Domínios consultados**. Fornece os nomes de domínio mais frequentes que está sendo consultados pelos clientes do DNS no seu ambiente. Pode ver a lista de todos os nomes de domínio consultados. Também pode desagregar os detalhes de pedido de pesquisa de um nome de domínio específico na pesquisa de registos.

![Painel de Queried de domínios](./media/dns-analytics/domains-queried-blade.png)

**Os clientes DNS**. Relatórios de clientes *que viola o limiar* para o número de consultas no período de tempo escolhido. Pode ver a lista de todos os clientes DNS e os detalhes das consultas feitas pelo-los em pesquisa de registos.

![Painel clientes DNS](./media/dns-analytics/dns-clients-blade.png)

**Registos DNS dinâmico**. Falhas de registo de nome de relatórios. Todas as falhas de registo para o endereço [registos de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (tipo a e AAAA) são realçados juntamente com o IPs que efetuou o registo de pedidos de cliente. Em seguida, pode utilizar estas informações para encontrar a causa da falha de registo ao seguir estes passos:

1. Encontre a zona que é autoritativa para o nome que o cliente está a tentar atualizar.

1. Utilize a solução para verificar as informações de inventário dessa zona.

1. Certifique-se de que a atualização dinâmica para a zona está ativada.

1. Verifique se a zona está configurada para a atualização dinâmica segura ou não.

    ![Painel de registos do DNS dinâmico](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Dê um nome de pedidos de registo**. O mosaico superior mostra uma trendline de pedidos de atualização dinâmica de DNS com êxito ou falhados. O mosaico inferior apresenta uma lista de principais 10 clientes que estão a enviar solicitações de atualização de DNS falhou para os servidores DNS, classificados pelo número de falhas.

![Painel de pedidos de registo de nome](./media/dns-analytics/name-reg-req-blade.png)

**Exemplo de consultas de análise DDI**. Contém uma lista das consultas de pesquisa mais comuns que obtêm dados de análise brutos diretamente.


![Amostras de consultas](./media/dns-analytics/queries.png)

Pode utilizar estas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. A ligação de consultas para a página de pesquisa de registos de análise de DNS em que os resultados são apresentados:

- **Lista de servidores DNS**. Mostra uma lista de todos os servidores DNS com seus associados FQDN, nome de domínio, nome da floresta e servidor IPs.
- **Lista de zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome da zona associados, o estado de atualização dinâmica, a servidores de nomes e o estado de assinatura DNSSEC.
- **Registos de recursos não utilizados**. Mostra uma lista de todos os registros de recursos não utilizados/obsoleta. Esta lista contém o nome do registo de recursos, o tipo de registo de recursos, o servidor DNS associado, geração de registos e o nome da zona. Pode utilizar esta lista para identificar os registos de recursos DNS já não estão em utilização. Com base nessas informações, pode remover, em seguida, essas entradas dos servidores DNS.
- **Carregamento de consulta de servidores DNS**. Mostra informações para que pode obter uma perspetiva da carga DNS nos seus servidores DNS. Estas informações podem ajudar a planear a capacidade para os servidores. Pode ir para o **métricas** separador para alterar o modo de exibição para uma visualização gráfica. Esta vista ajuda a compreender a forma como a carga DNS é distribuída em todos os servidores DNS. Consulta DNS é mostra tendências da taxa de cada servidor.

    ![Resultados de pesquisa de registo de consulta de servidores DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Carregamento de consulta de zonas DNS**. Mostra as estatísticas de zona consulta por segundo de DNS de todas as zonas nos servidores DNS a ser geridos pela solução. Clique nas **métricas** separador para alterar a vista de registos detalhados para uma visualização gráfica dos resultados.
- **Eventos de configuração**. Mostra todos os eventos de alteração de configuração de DNS e mensagens associadas a eles. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, ou categoria de tarefa. Os dados podem ajudá-lo a auditar as alterações feitas aos servidores DNS específicas em horários específicos.
- **Registo analítico de DNS**. Mostra todos os eventos analíticos em todos os servidores DNS geridos pela solução. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, IP de cliente que efetuou a consulta de pesquisa e a categoria de tarefa do tipo de consulta. Eventos analíticos de servidor DNS ativar a atividade de controle no servidor DNS. Um evento de análise é registado sempre que o servidor envia ou recebe informações de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Procure, utilizando a pesquisa de registos de análise de DNS

Na página de pesquisa de registos, pode criar uma consulta. Pode filtrar os resultados da pesquisa através de controlos de faceta. Também pode criar consultas avançadas para transformação, o filtro e o relatório em seus resultados. Iniciar com as seguintes consultas:

1. Na **caixa de consulta de pesquisa**, tipo `DnsEvents` para ver todos os eventos DNS gerados pelos servidores DNS geridos pela solução. Os resultados listam os dados de registo para todos os eventos relacionados com consultas de pesquisa, registos dinâmicos e alterações de configuração.

    ![Pesquisa de registos de DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Para ver os dados de registo para consultas de pesquisa, selecione **LookUpQuery** como o **subtipo** filtro do controle de faceta à esquerda. É apresentada uma tabela que lista todos os eventos de consulta de pesquisa para o período de tempo selecionado.

    b. Para ver os dados de registo para registos dinâmicos, selecione **DynamicRegistration** como o **subtipo** filtro do controle de faceta à esquerda. É apresentada uma tabela que lista todos os eventos de registo dinâmico para o período de tempo selecionado.

    c. Para ver os dados de registo de alterações de configuração, selecione **ConfigurationChange** como o **subtipo** filtro do controle de faceta à esquerda. É apresentada uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado.

1. Na **caixa de consulta de pesquisa**, tipo `DnsInventory` para ver todos os DNS relacionadas com o inventário dados para os servidores DNS geridos pela solução. Os resultados listam os dados de registo para servidores DNS, zonas DNS e registos de recursos.

    ![Pesquisa de registos de DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentários

Existem duas formas, pode fornecer comentários:

- **UserVoice**. Publicar ideias para funcionalidades de análise de DNS trabalhar em. Visite o [página do UserVoice do Log Analytics](https://aka.ms/dnsanalyticsuservoice).
- **Junte-se a nossa coorte**. Estamos sempre que novos clientes, Junte-se a nossa coortes para obter acesso antecipado aos novos recursos e ajudar-na melhorar a análise de DNS. Se estiver interessado em aderir ao nosso coortes, preencha [este inquérito Rápido](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Passos Seguintes

[Consultar os registos](../log-query/log-query-overview.md) para ver os registos DNS detalhados.
