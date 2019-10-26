---
title: Análise de DNS solução no Azure Monitor | Microsoft Docs
description: Configure e use a solução Análise de DNS no Azure Monitor para coletar informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2018
ms.openlocfilehash: a4123ed4633cbb8195639766e7b23e3ea9f14e71
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899096"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Reúna informações sobre sua infraestrutura de DNS com a solução de visualização Análise de DNS

![Símbolo de Análise de DNS](./media/dns-analytics/dns-analytics-symbol.png)

Este artigo descreve como configurar e usar a solução de Análise de DNS do Azure no Azure Monitor para coletar informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.

Análise de DNS ajuda a:

- Identificar clientes que tentam resolver nomes de domínio mal-intencionados.
- Identificar registros de recursos obsoletos.
- Identifique nomes de domínio consultados com frequência e clientes DNS comunicativos.
- Exibir a carga de solicitação em servidores DNS.
- Exibir falhas de registro de DNS dinâmico.

A solução coleta, analisa e correlaciona os logs analíticos e de auditoria do DNS do Windows e outros dados relacionados dos servidores DNS.

## <a name="connected-sources"></a>Origens ligadas

A tabela a seguir descreve as fontes conectadas que têm suporte nesta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../platform/agent-windows.md) | Sim | A solução coleta informações de DNS de agentes do Windows. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | A solução não coleta informações de DNS de agentes diretos do Linux. |
| [System Center Operations Manager grupo de gerenciamento](../platform/om-agents.md) | Sim | A solução coleta informações de DNS de agentes em um grupo de gerenciamento Operations Manager conectado. Não é necessária uma conexão direta do agente de Operations Manager para Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho Log Analytics. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos DNS e de inventário de DNS dos servidores DNS onde um agente de Log Analytics está instalado. Esses dados são então carregados para Azure Monitor e exibidos no painel da solução. Os dados relacionados ao inventário, como o número de servidores DNS, zonas e registros de recursos, são coletados pela execução dos cmdlets do PowerShell do DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs analíticos e de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos pelo log e diagnóstico DNS aprimorados no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Use as informações a seguir para configurar a solução:

- Você deve ter um agente do [Windows](../platform/agent-windows.md) ou [Operations Manager](../platform/om-agents.md) em cada servidor DNS que você deseja monitorar.
- Você pode adicionar a solução de Análise de DNS ao seu espaço de trabalho do Log Analytics do [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Você também pode usar o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](solutions.md).

A solução começa a coletar dados sem a necessidade de configuração adicional. No entanto, você pode usar a configuração a seguir para personalizar a coleta de dados.

### <a name="configure-the-solution"></a>Configurar a solução

No painel da solução, clique em **configuração** para abrir a página configuração do análise de DNS. Há dois tipos de alterações de configuração que você pode fazer:

- **Nomes de domínio na lista**de permissões. A solução não processa todas as consultas de pesquisa. Ele mantém uma lista branca de sufixos de nome de domínio. As consultas de pesquisa que são resolvidas para os nomes de domínio que correspondem a sufixos de nome de domínio nesta lista de permissões não são processadas pela solução. O não processamento de nomes de domínio na lista de permissões ajuda a otimizar os dados enviados para Azure Monitor. A lista de permissões padrão inclui nomes de domínio públicos populares, como www.google.com e www.facebook.com. Você pode exibir a lista padrão completa rolando.

  Você pode modificar a lista para adicionar qualquer sufixo de nome de domínio para o qual você deseja exibir informações de pesquisa. Você também pode remover qualquer sufixo de nome de domínio para o qual não deseja exibir informações de pesquisa.

- **Limite do cliente comunicativos**. Os clientes DNS que excedem o limite para o número de solicitações de pesquisa são realçados na folha **clientes DNS** . O limite padrão é 1.000. Você pode editar o limite.

    ![Nomes de domínio na lista de permissões](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se você estiver usando o Microsoft Monitoring Agent para se conectar ao seu espaço de trabalho do Log Analytics, o seguinte pacote de gerenciamento será instalado:

- Pacote de inteligência do coletor de dados de DNS da Microsoft (Microsoft. IntelligencePacks. DNS)

Se o grupo de gerenciamento do Operations Manager estiver conectado ao seu espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager quando você adicionar essa solução. Não há nenhuma configuração ou manutenção necessária para esses pacotes de gerenciamento:

- Pacote de inteligência do coletor de dados de DNS da Microsoft (Microsoft. IntelligencePacks. DNS)
- Microsoft System Center Advisor Análise de DNS configuração (Microsoft. IntelligencePack. DNS. Configuration)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Usar a solução Análise de DNS

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


O bloco DNS inclui o número de servidores DNS onde os dados estão sendo coletados. Ele também inclui o número de solicitações feitas por clientes para resolver domínios mal-intencionados nas últimas 24 horas. Quando você clica no bloco, o painel da solução é aberto.

![Bloco Análise de DNS](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O painel da solução mostra informações resumidas para os vários recursos da solução. Ele também inclui links para a exibição detalhada para análise forense e diagnóstico. Por padrão, os dados são mostrados nos últimos sete dias. Você pode alterar o intervalo de data e hora usando o **controle de seleção de data e hora**, conforme mostrado na imagem a seguir:

![Controle de seleção de tempo](./media/dns-analytics/dns-time.png)

O painel da solução mostra as seguintes folhas:

**Segurança do DNS**. Relata os clientes DNS que estão tentando se comunicar com domínios mal-intencionados. Usando feeds de inteligência contra ameaças da Microsoft, Análise de DNS pode detectar IPs de cliente que estão tentando acessar domínios mal-intencionados. Em muitos casos, os dispositivos infectados por malware "discam" para o centro de "comando e controle" do domínio mal-intencionado resolvendo o nome de domínio do malware.

![Folha de segurança do DNS](./media/dns-analytics/dns-security-blade.png)

Quando você clica em um IP do cliente na lista, a pesquisa de logs é aberta e mostra os detalhes da pesquisa da respectiva consulta. No exemplo a seguir, Análise de DNS detectou que a comunicação foi feita com um [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot):

![Resultados da pesquisa de log mostrando IRCBot](./media/dns-analytics/ircbot.png)

As informações ajudam a identificar o:

- IP do cliente que iniciou a comunicação.
- Nome de domínio que resolve para o IP mal-intencionado.
- Endereços IP aos quais o nome de domínio é resolvido.
- Endereço IP mal-intencionado.
- Gravidade do problema.
- Motivo da lista negra do IP mal-intencionado.
- Tempo de detecção.

**Domínios consultados**. Fornece os nomes de domínio mais frequentes que estão sendo consultados pelos clientes DNS em seu ambiente. Você pode exibir a lista de todos os nomes de domínio consultados. Você também pode fazer uma busca detalhada nos detalhes da solicitação de pesquisa de um nome de domínio específico na pesquisa de logs.

![Folha de consulta de domínios](./media/dns-analytics/domains-queried-blade.png)

**Clientes DNS**. Relata os clientes que estão *violando o limite* do número de consultas no período de tempo escolhido. Você pode exibir a lista de todos os clientes DNS e os detalhes das consultas feitas por eles na pesquisa de logs.

![Folha clientes DNS](./media/dns-analytics/dns-clients-blade.png)

**Registros DNS dinâmicos**. Relata falhas de registro de nome. Todas as falhas de registro para os [registros de recurso](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de endereço (tipo A e aaaa) são realçadas junto com os IPs de cliente que fizeram as solicitações de registro. Você pode usar essas informações para encontrar a causa raiz da falha de registro seguindo estas etapas:

1. Localize a zona que é autoritativa para o nome que o cliente está tentando atualizar.

1. Use a solução para verificar as informações de inventário dessa zona.

1. Verifique se a atualização dinâmica da zona está habilitada.

1. Verifique se a zona está configurada para atualização dinâmica segura ou não.

    ![Folha de registros de DNS dinâmico](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Solicitações de registro de nome**. O bloco superior mostra uma tendência de solicitações de atualização dinâmica de DNS com êxito e com falha. O bloco inferior lista os 10 principais clientes que estão enviando solicitações de atualização de DNS com falha para os servidores DNS, classificados pelo número de falhas.

![Folha solicitações de registro de nome](./media/dns-analytics/name-reg-req-blade.png)

**Exemplos de consultas de análise de DDI**. Contém uma lista das consultas de pesquisa mais comuns que buscam dados de análise brutos diretamente.


![Exemplos de consultas](./media/dns-analytics/queries.png)

Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. As consultas são vinculadas à página de pesquisa de log do Análise de DNS, onde os resultados são exibidos:

- **Lista de servidores DNS**. Mostra uma lista de todos os servidores DNS com seu FQDN, nome de domínio, nome de floresta e IPs de servidor associados.
- **Lista de zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome de zona associado, status de atualização dinâmica, servidores de nomes e status de assinatura de DNSSEC.
- **Registros de recursos não utilizados**. Mostra uma lista de todos os registros de recursos não utilizados/obsoletos. Essa lista contém o nome do registro de recurso, o tipo de registro de recurso, o servidor DNS associado, o tempo de geração de registro e o nome da zona. Você pode usar essa lista para identificar os registros de recursos de DNS que não estão mais em uso. Com base nessas informações, você pode remover essas entradas dos servidores DNS.
- **Carregamento de consulta de servidores DNS**. Mostra informações para que você possa obter uma perspectiva da carga DNS nos servidores DNS. Essas informações podem ajudá-lo a planejar a capacidade dos servidores. Você pode ir para a guia **métricas** para alterar a exibição para uma visualização gráfica. Essa exibição ajuda a entender como a carga DNS é distribuída entre os servidores DNS. Ele mostra as tendências de taxa de consulta DNS para cada servidor.

    ![Resultados da pesquisa de log de consultas de servidores DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Zonas DNS carregar a consulta**. Mostra as estatísticas de consulta por segundo de zona DNS de todas as zonas nos servidores DNS que estão sendo gerenciados pela solução. Clique na guia **métricas** para alterar a exibição de registros detalhados para uma visualização gráfica dos resultados.
- **Eventos de configuração**. Mostra todos os eventos de alteração de configuração DNS e as mensagens associadas. Em seguida, você pode filtrar esses eventos com base na hora do evento, na ID do evento, no servidor DNS ou na categoria da tarefa. Os dados podem ajudá-lo a auditar as alterações feitas em servidores DNS específicos em momentos específicos.
- **Log analítico de DNS**. Mostra todos os eventos analíticos em todos os servidores DNS gerenciados pela solução. Em seguida, você pode filtrar esses eventos com base na hora do evento, na ID do evento, no servidor DNS, no IP do cliente que fez a consulta de pesquisa e na categoria de tarefa tipo de consulta. Os eventos analíticos do servidor DNS habilitam o rastreamento de atividades no servidor DNS. Um evento analítico é registrado toda vez que o servidor envia ou recebe informações de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Pesquisar usando Análise de DNS pesquisa de logs

Na página pesquisa de logs, você pode criar uma consulta. Você pode filtrar os resultados da pesquisa usando controles de faceta. Você também pode criar consultas avançadas para transformar, filtrar e relatar seus resultados. Comece usando as seguintes consultas:

1. Na **caixa de consulta de pesquisa**, digite `DnsEvents` para exibir todos os eventos DNS gerados pelos servidores DNS gerenciados pela solução. Os resultados listam os dados de log para todos os eventos relacionados a consultas de pesquisa, registros dinâmicos e alterações de configuração.

    ![Pesquisa de logs do DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Para exibir os dados de log para consultas de pesquisa, selecione **LookUpQuery** como o filtro de **subtipo** do controle de faceta à esquerda. Uma tabela que lista todos os eventos de consulta de pesquisa para o período de tempo selecionado é exibida.

    b. Para exibir os dados de log para registros dinâmicos, selecione **DynamicRegistration** como o filtro de **subtipo** do controle de faceta à esquerda. Uma tabela que lista todos os eventos de registro dinâmico para o período de tempo selecionado é exibida.

    c. Para exibir os dados de log para alterações de configuração, selecione **ConfigurationChange** como o filtro de **subtipo** do controle de faceta à esquerda. Uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado é exibida.

1. Na **caixa Pesquisar consulta**, digite `DnsInventory` para exibir todos os dados relacionados ao inventário de DNS para os servidores DNS gerenciados pela solução. Os resultados listam os dados de log para servidores DNS, zonas DNS e registros de recursos.

    ![Pesquisa de logs do DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Resolução de problemas

Etapas comuns de solução de problemas:

1. Dados de pesquisas de DNS ausentes-para solucionar esse problema, tente redefinir a configuração ou apenas carregar a página de configuração uma vez no Portal. Para redefinir, basta alterar uma configuração para outro valor e, em seguida, alterá-la de volta para o valor original e salvar a configuração.

## <a name="feedback"></a>Comentários

Para fornecer comentários, visite a [página log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) para postar ideias para análise de DNS recursos para trabalhar. 

## <a name="next-steps"></a>Passos seguintes

[Logs de consulta](../log-query/log-query-overview.md) para exibir registros de log DNS detalhados.
