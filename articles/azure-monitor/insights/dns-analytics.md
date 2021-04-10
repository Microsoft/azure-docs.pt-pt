---
title: Solução DNS Analytics no Azure Monitor | Microsoft Docs
description: Crie e utilize a solução DNS Analytics no Azure Monitor para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: f01a1204aefbcaabe0ddac254b24bc014c3d5a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654531"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização do DNS Analytics

![Símbolo DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

Este artigo descreve como configurar e usar a solução Azure DNS Analytics no Azure Monitor para recolher informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações.

O DNS Analytics ajuda-o a:

- Identifique clientes que tentem resolver nomes de domínio maliciosos.
- Identifique registos de recursos velhos.
- Identifique nomes de domínio frequentemente questionados e clientes DNS falantes.
- Ver carregar o pedido nos servidores DNS.
- Ver falhas dinâmicas de registo de DNS.

A solução recolhe, analisa e correlaciona registos analíticos e de auditoria do Windows DNS e outros dados relacionados dos seus servidores DNS.

## <a name="connected-sources"></a>Origens ligadas

O quadro que se segue descreve as fontes ligadas suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../agents/agent-windows.md) | Yes | A solução recolhe informações sobre DNS de agentes do Windows. |
| [Agentes do Linux](../vm/quick-collect-linux-computer.md) | No | A solução não recolhe informações de DNS de agentes linux diretos. |
| [Grupo de gestão do System Center Operations Manager](../agents/om-agents.md) | Yes | A solução recolhe informações de DNS de agentes de um grupo de gestão de Gestores de Operações conectado. Não é necessária uma ligação direta do agente gestor de operações ao Azure Monitor. Os dados são reencaminhados do grupo de gestão para o espaço de trabalho Log Analytics. |
| [Conta de armazenamento Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | O armazenamento Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da recolha de dados

A solução recolhe o inventário DNS e dados relacionados com eventos DNS dos servidores DNS onde está instalado um agente Do Registo De Análise. Estes dados são então enviados para o Azure Monitor e apresentados no painel de instrumentos de solução. Os dados relacionados com o inventário, como o número de servidores, zonas e registos de recursos dns, são recolhidos executando os cmdlets DNS PowerShell. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados com o evento são recolhidos perto de tempo real a partir dos [registos analíticos e de auditoria](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) fornecidos por registos dns melhorados e diagnósticos no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Utilize as seguintes informações para configurar a solução:

- Tem de ter um agente [Windows](../agents/agent-windows.md) ou [Operations Manager](../agents/om-agents.md) em cada servidor DNS que pretende monitorizar.
- Pode adicionar a solução DNS Analytics ao seu espaço de trabalho Log Analytics a partir do [Azure Marketplace.](https://aka.ms/dnsanalyticsazuremarketplace) Também pode utilizar o processo descrito nas [soluções Add Azure Monitor da Galeria soluções.](solutions.md)

A solução começa a recolher dados sem a necessidade de uma configuração adicional. No entanto, pode utilizar a seguinte configuração para personalizar a recolha de dados.

### <a name="configure-the-solution"></a>Configure a solução

A partir do espaço de trabalho Log Analytics no portal Azure, selecione o **resumo do Espaço de Trabalho** e, em seguida, clique no azulejo **DNS Analytics.** No painel de instrumentos de solução, clique em **Configuração** para abrir a página de Configuração do DNS Analytics. Existem dois tipos de alterações de configuração que pode fazer:

- **Nomes de domínio listados permitidos**. A solução não processa todas as consultas de procura. Mantém uma lista de sufixos de nome de domínio. As consultas de procura que resolvem os nomes de domínio que correspondem aos sufixos de nome de domínio nesta lista de admissões não são processadas pela solução. O não processamento de nomes de domínios listados ajuda a otimizar os dados enviados para o Azure Monitor. A lista de admissões por defeito inclui nomes de domínio público populares, como www.google.com e www.facebook.com. Pode ver a lista completa por defeito deslocando-se.

  Pode modificar a lista para adicionar qualquer sufixo de nome de domínio que pretenda visualizar insights de procura. Também pode remover qualquer sufixo de nome de domínio que não queira visualizar insights de procura.

- **Limiar de cliente talkativo**. Os clientes DNS que excedam o limiar para o número de pedidos de procura são destacados na lâmina dos **Clientes DNS.** O limiar de incumprimento é de 1.000. Pode editar o limiar.

    ![Nomes de domínio listados permitidos](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se estiver a utilizar o Agente de Monitorização da Microsoft para ligar ao seu espaço de trabalho Log Analytics, está instalado o seguinte pacote de gestão:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Se o seu grupo de gestão de Gestor de Operações estiver ligado ao seu espaço de trabalho Log Analytics, os seguintes pacotes de gestão são instalados no Gestor de Operações quando adicionar esta solução. Não é necessária a configuração ou manutenção destes pacotes de gestão:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Configuração de análise DO Microsoft System Center DNS Analytics (Microsoft.IntelligencePack.Dns.Configuration)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../agents/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Utilize a solução DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


O azulejo DNS inclui o número de servidores DNS onde os dados estão a ser recolhidos. Inclui também o número de pedidos feitos pelos clientes para resolver domínios maliciosos nas últimas 24 horas. Quando clica no azulejo, o painel de instrumentos de solução abre-se.

![Azulejo DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O painel de instrumentos de solução mostra informações resumidas para as várias funcionalidades da solução. Inclui também ligações à visão detalhada para análise e diagnóstico forense. Por predefinição, os dados são apresentados nos últimos sete dias. Pode alterar a data e o intervalo de tempo utilizando o **controlo de seleção da data,** como mostra a seguinte imagem:

![Controlo de seleção de tempo](./media/dns-analytics/dns-time.png)

O painel de instrumentos de solução mostra as seguintes lâminas:

**Segurança DNS**. Relata os clientes DNS que estão a tentar comunicar com domínios maliciosos. Ao utilizar feeds de inteligência de ameaça da Microsoft, o DNS Analytics pode detetar iPs de clientes que estão a tentar aceder a domínios maliciosos. Em muitos casos, os dispositivos infetados com malware "marcam" para o centro de "comando e controlo" do domínio malicioso, resolvendo o nome do domínio do malware.

![Lâmina de segurança DNS](./media/dns-analytics/dns-security-blade.png)

Quando clica num IP do cliente na lista, o Log Search abre e mostra os detalhes de pesquisa da respetiva consulta. No exemplo seguinte, o DNS Analytics detetou que a comunicação foi feita com um [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Registar resultados de pesquisa mostrando ircbot](./media/dns-analytics/ircbot.png)

A informação ajuda-o a identificar:

- Ip cliente que iniciou a comunicação.
- Nome de domínio que resolve o IP malicioso.
- Endereços IP que o nome de domínio resolve.
- Endereço IP malicioso.
- Gravidade da questão.
- Razão para bloquear o IP malicioso.
- Hora de deteção.

**Domínios Consultados**. Fornece os nomes de domínio mais frequentes que são consultados pelos clientes DNS no seu ambiente. Pode ver a lista de todos os nomes de domínio consultados. Também pode aprofundar os detalhes do pedido de procura de um nome de domínio específico na Pesquisa de Registo.

![Lâmina consultada de domínios](./media/dns-analytics/domains-queried-blade.png)

**Clientes DNS**. Reporta os clientes *que infringem o limiar* do número de consultas no período de tempo escolhido. Pode ver a lista de todos os clientes DNS e os detalhes das consultas por eles feitas na Log Search.

![Lâmina de clientes DNS](./media/dns-analytics/dns-clients-blade.png)

**Inscrições dinâmicas de DNS.** Relatórios de falhas de registo de nome. Todas as falhas de registo dos [registos de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de endereço (Tipo A e AAAA) são destacadas juntamente com os IPs do cliente que e fizeram os pedidos de registo. Em seguida, pode utilizar estas informações para encontrar a causa principal da falha de registo seguindo estes passos:

1. Encontre a zona que é autoritária para o nome que o cliente está a tentar atualizar.

1. Utilize a solução para verificar as informações de inventário dessa zona.

1. Verifique se a atualização dinâmica da zona está ativada.

1. Verifique se a zona está configurada para uma atualização dinâmica segura ou não.

    ![Lâmina dinâmica de registos DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Pedidos de registo de nomes**. O azulejo superior mostra uma linha de tendência de pedidos de atualização dinâmica de DNS bem sucedidos e falhados. O azulejo inferior lista os 10 principais clientes que estão a enviar pedidos de atualização DNS falhados para os servidores DNS, classificados pelo número de falhas.

![Lâmina de pedidos de registo de nome](./media/dns-analytics/name-reg-req-blade.png)

**Amostra de consultas de análise DDI**. Contém uma lista das consultas de pesquisa mais comuns que recolhem dados de análise bruta diretamente.


![Consultas de exemplo](./media/dns-analytics/queries.png)

Pode utilizar estas consultas como ponto de partida para criar as suas próprias consultas para relatórios personalizados. As consultas ligam-se à página DNS Analytics Log Search onde os resultados são apresentados:

- **Lista de servidores DNS**. Mostra uma lista de todos os servidores DNS com o seu FQDN associado, nome de domínio, nome da floresta e IPs do servidor.
- **Lista de Zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome da zona associada, estado de atualização dinâmica, servidores de nomes e estado de assinatura de DNSSEC.
- **Registos de Recursos Não Reutilizados.** Mostra uma lista de todos os registos de recursos não reutilizados/velhos. Esta lista contém o nome de registo de recursos, o tipo de registo de recursos, o servidor DNS associado, o tempo de geração do registo e o nome da zona. Pode utilizar esta lista para identificar os registos de recursos DNS que já não estão a ser utilizados. Com base nesta informação, pode então remover essas entradas dos servidores DNS.
- **Consulta de serviços DNS .** Mostra informações para que possa ter uma perspetiva da carga DNS nos seus servidores DNS. Esta informação pode ajudá-lo a planear a capacidade dos servidores. Pode ir ao separador **Métricas** para alterar a vista para uma visualização gráfica. Esta visualização ajuda-o a compreender como a carga DNS é distribuída pelos seus servidores DNS. Mostra as tendências da taxa de consulta de DNS para cada servidor.

    ![Servidores DNS consultam resultados de pesquisa de registo](./media/dns-analytics/dns-servers-query-load.png)

- **Carga de consulta de zonas DNS**. Mostra as estatísticas de consulta de zona de DNS por segundo de todas as zonas dos servidores DNS que estão a ser geridas pela solução. Clique no separador **Métricas** para alterar a vista de registos detalhados para uma visualização gráfica dos resultados.
- **Eventos de configuração**. Mostra todos os eventos de alteração de configuração do DNS e mensagens associadas. Em seguida, pode filtrar estes eventos com base no tempo do evento, ID de evento, servidor DNS ou categoria de tarefa. Os dados podem ajudá-lo a auditar alterações feitas a servidores DNS específicos em momentos específicos.
- **Registo analítico DNS**. Mostra todos os eventos analíticos em todos os servidores DNS geridos pela solução. Em seguida, pode filtrar estes eventos com base no tempo do evento, ID de evento, servidor DNS, IP do cliente que fez a consulta de procuração e categoria de tarefa tipo consulta. Os eventos analíticos do servidor DNS permitem o rastreio de atividade no servidor DNS. Um evento analítico é registado sempre que o servidor envia ou recebe informações DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Faça uma pesquisa utilizando o DNS Analytics Log Search

Na página 'Procurar registo', pode criar uma consulta. Pode filtrar os resultados da sua pesquisa utilizando controlos de faceta. Também pode criar consultas avançadas para transformar, filtrar e reportar os seus resultados. Comece por utilizar as seguintes consultas:

1. Na **caixa de consulta de pesquisa,** escreva `DnsEvents` para ver todos os eventos DNS gerados pelos servidores DNS geridos pela solução. Os resultados listam os dados de registo de todos os eventos relacionados com consultas de procura, registos dinâmicos e alterações de configuração.

    ![DnsEvents log search](./media/dns-analytics/log-search-dnsevents.png)  

    a. Para visualizar os dados de registo de consultas de procuração, selecione **LookUpQuery** como o filtro **subtipo** do controlo faceta à esquerda. É apresentada uma tabela que lista todos os eventos de consulta de procura para o período de tempo selecionado.

    b. Para visualizar os dados de registo para registos dinâmicos, selecione **DynamicRegistration** como o filtro **subtipo** do controlo faceta à esquerda. É apresentada uma tabela que lista todos os eventos dinâmicos de registo para o período de tempo selecionado.

    c. Para visualizar os dados de registo para alterações de configuração, **selecione ConfigurationChange** como o filtro **subtipo** do controlo faceta à esquerda. É apresentada uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado.

1. Na **caixa de consulta de pesquisa,** `DnsInventory` escreva para ver todos os dados relacionados com o inventário DNS para os servidores DNS geridos pela solução. Os resultados listam os dados de registo dos servidores DNS, zonas DNS e registos de recursos.

    ![Pesquisa de registo dnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Resolução de problemas

Etapas comuns de resolução de problemas:

1. Dados de pesquisas de DNS em falta - Para resolver problemas deste problema, tente redefinir o config ou basta carregar a página de configuração uma vez no portal. Para repor, basta alterar uma definição para outro valor, em seguida, alterá-la de volta para o valor original, e salvar o config.

## <a name="suggestions"></a>Sugestões

Para fornecer feedback, visite a [página Do Utilizador Do Log Analytics](https://aka.ms/dnsanalyticsuservoice) para publicar ideias para funcionalidades DNS Analytics para trabalhar. 

## <a name="next-steps"></a>Passos seguintes

[Registos de consulta](../logs/log-query-overview.md) para visualizar registos de registos DNS detalhados.
