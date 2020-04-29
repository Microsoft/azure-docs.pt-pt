---
title: Monitor, diagnóstico e resolução de problemas Azure Storage [ Microsoft Docs
description: Utilize funcionalidades como análise de armazenamento, registo saqueado do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 0bbffacc0a8c47950b8637e826d1d5db9fbdb234
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605074"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição geral
Diagnosticar e resolver problemas numa aplicação distribuída alojada num ambiente de nuvem pode ser mais complexo do que em ambientes tradicionais. As aplicações podem ser implantadas numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel, ou em alguma combinação destes ambientes. Normalmente, o tráfego de rede da sua aplicação pode atravessar redes públicas e privadas e a sua aplicação pode usar várias tecnologias de armazenamento, tais como Mesas de Armazenamento Microsoft Azure, Blobs, Filas ou Ficheiros, além de outras lojas de dados, como bases de dados relacionais e documentais.

Para gerir estas aplicações com sucesso, deve monitorizá-las proactivamente e compreender como diagnosticar e resolver todos os aspetos das mesmas e das suas tecnologias dependentes. Como utilizador dos serviços de Armazenamento Azure, deve monitorizar continuamente os serviços de Armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas de comportamento (como tempos de resposta mais lentos do que o habitual), e utilizar o registo para recolher dados mais detalhados e analisar um problema em profundidade. As informações de diagnóstico obtidas tanto da monitorização como do registo irão ajudá-lo a determinar a causa principal do problema que a sua aplicação encontrou. Depois pode resolver o problema e determinar as medidas adequadas que pode tomar para o remediar. O Azure Storage é um serviço Azure central, e constitui uma parte importante da maioria das soluções que os clientes implementam para a infraestrutura Azure. O Azure Storage inclui capacidades para simplificar problemas de monitorização, diagnóstico e armazenamento de problemas nas suas aplicações baseadas na nuvem.

> [!NOTE]
> Os Ficheiros Azure não suportam a exploração madeireira neste momento.
>

Para obter um guia prático para resolução de problemas de ponta a ponta nas aplicações de Armazenamento Azure, consulte a resolução de problemas de ponta a ponta utilizando métricas de [armazenamento e registo sinuosos, AzCopy e Analisador de Mensagens.](../storage-e2e-troubleshooting.md)

* [Introdução]
  * [Como este guia é organizado]
* [Monitorização do seu serviço de armazenamento]
  * [Monitorização da saúde do serviço]
  * [Capacidade de monitorização]
  * [Monitorização da disponibilidade]
  * [Desempenho de monitorização]
* [Diagnóstico de problemas de armazenamento]
  * [Problemas de saúde do serviço]
  * [Problemas de desempenho]
  * [Diagnóstico de erros]
  * [Problemas do emulador de armazenamento]
  * [Ferramentas de exploração madeireira de armazenamento]
  * [Utilização de ferramentas de registo de rede]
* [Rastreio de ponta a ponta]
  * [Correlacionar dados de registo]
  * [ID de pedido de cliente]
  * [ID de pedido do servidor]
  * [Carimbos de Data/Hora]
* [Orientação de resolução de problemas]
  * [As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]
  * [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
  * [As métricas apresentam uma AverageServerLatency alta]
  * [Ocorrem atrasos inesperados na entrega de mensagens numa fila]
  * [As métricas apresentam um aumento do PercentThrottlingError]
  * [As métricas apresentam um aumento do PercentTimeoutError]
  * [As métricas apresentam um aumento do PercentNetworkError]
  * [O cliente está a receber mensagens de HTTP 403 (Proibido)]
  * [O cliente está a receber mensagens de HTTP 404 (Não encontrado)]
  * [O cliente está a receber mensagens de HTTP 409 (Conflito)]
  * [As métricas mostram que as entradas de registo low PercentSuccess ou analytics têm operações com o estado de transação do ClientOtherErrors]
  * [As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]
  * [O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]
  * [Encontra problemas na instalação do SDK Azure para .NET]
  * [Você tem um problema diferente com um serviço de armazenamento]
  * [Resolução de Problemas VHDs em máquinas virtuais do Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Resolução de Problemas VHDs em máquinas virtuais Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Problemas de resolução de problemas de Ficheiros Azure com Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Problemas de resolução de problemas de Ficheiros Azure com Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Apêndices]
  * [Apêndice 1: Usar o Violinista para capturar o tráfego HTTP e HTTPS]
  * [Apêndice 2: Usar o Wireshark para capturar o tráfego da rede]
  * [Apêndice 3: Usar o Microsoft Message Analyzer para capturar o tráfego da rede]
  * [Apêndice 4: Utilizar o Excel para visualizar métricas e registar dados]
  * [Apêndice 5: Monitorização com Insights de Aplicação para DevOps Azure]

## <a name="introduction"></a><a name="introduction"></a>Introdução
Este guia mostra-lhe como usar funcionalidades como o Azure Storage Analytics, a exploração madeireira do lado do cliente na Biblioteca de Clientes de Armazenamento Azure e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure.

![][1]

Este guia destina-se a ser lido principalmente por desenvolvedores de serviços online que utilizam os Serviços de Armazenamento Azure e os IT Pros responsáveis pela gestão desses serviços online. Os objetivos deste guia são:

* Para ajudá-lo a manter a saúde e desempenho das suas contas de Armazenamento Azure.
* Para lhe fornecer os processos e ferramentas necessários para ajudá-lo a decidir se um problema ou problema numa aplicação está relacionado com o Armazenamento Azure.
* Para lhe fornecer orientações atonais para resolver problemas relacionados com o Armazenamento Azure.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Como este guia é organizado
A secção "[Monitorizar o seu serviço]de armazenamento " descreve como monitorizar a saúde e o desempenho dos seus serviços de armazenamento Azure usando métricas de análise de armazenamento azure (Métricas de armazenamento).

A secção "[Problemas]de armazenamento de diagnóstico " descreve como diagnosticar problemas usando o registo de armazenamento de armazenamento azure (armazenamento madeirio). Também descreve como permitir a exploração madeireira do lado do cliente utilizando as instalações de uma das bibliotecas de clientes, como a Biblioteca de Clientes de Armazenamento para .NET ou o Azure SDK para Java.

A secção " Rastreio de[ponta a ponta]" descreve como pode correlacionar a informação contida em vários ficheiros de registo e dados de métricas.

A secção " Orientação de resolução de[problemas]" fornece orientação de resolução de problemas para alguns dos problemas comuns relacionados com o armazenamento que você pode encontrar.

Os "[Apêndices]" incluem informações sobre a utilização de outras ferramentas, como wireshark e Netmon para analisar dados de pacotes de rede, Fiddler para analisar mensagens HTTP/HTTPS e Microsoft Message Analyzer para correlacionar dados de registo.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Monitorização do seu serviço de armazenamento
Se estiver familiarizado com a monitorização de desempenho do Windows, pode pensar que as Métricas de Armazenamento são um equivalente de Armazenamento Azure dos balcões do Windows Performance Monitor. Nas Métricas de Armazenamento, encontrará um conjunto abrangente de métricas (contadores na terminologia do Windows Performance Monitor) como disponibilidade de serviço, número total de pedidos de serviço ou percentagem de pedidos de serviço bem-sucedidos. Para obter uma lista completa das métricas disponíveis, consulte [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx). Pode especificar se pretende que o serviço de armazenamento recolha e agrega métricas a cada hora ou a cada minuto. Para obter mais informações sobre como ativar métricas e monitorizar as suas contas de armazenamento, consulte [a Habilitação](https://go.microsoft.com/fwlink/?LinkId=510865)das métricas de armazenamento e os dados das métricas de visualização .

Pode escolher quais as métricas horárias que pretende apresentar no [portal Azure](https://portal.azure.com) e configurar regras que notificam os administradores por e-mail sempre que uma métrica horária exceda um determinado limiar. Para mais informações, consulte [Receber Notificações](/azure/monitoring-and-diagnostics/monitoring-overview-alerts)de Alerta .

Recomendamos que reveja o [Monitor Azure para Armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (pré-visualização). É uma característica do Azure Monitor que oferece uma monitorização abrangente das suas contas de Armazenamento Azure, fornecendo uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de Armazenamento Azure. Não requer que ative ou configure nada, e pode imediatamente visualizar estas métricas a partir dos gráficos interativos pré-definidos e outras visualizações incluídas.

O serviço de armazenamento recolhe métricas utilizando um esforço melhor, mas pode não registar todas as operações de armazenamento.

No portal Azure, pode ver métricas como disponibilidade, pedidos totais e números médios de latência para uma conta de armazenamento. Foi também criada uma regra de notificação para alertar um administrador se a disponibilidade descer abaixo de um determinado nível. Ao visualizar estes dados, uma área possível para investigação é a percentagem de sucesso do serviço de mesa abaixo de 100% (para mais informações, ver a secção "[Métricas mostram que percentSuccess baixo ou entradas de registo de análise têm operações com o estado de transação de ClientesOtherErrors]").

Deve monitorizar continuamente as suas aplicações Azure para garantir que estão saudáveis e a atuar como esperado:

* Estabelecer algumas métricas de base para aplicação que lhe permitirão comparar os dados atuais e identificar quaisquer alterações significativas no comportamento do armazenamento do Azure e da sua aplicação. Os valores das suas métricas de base serão, em muitos casos, específicos da aplicação e deverá supor-se quando estiver a testar a sua aplicação.
* Registar métricas minúsculas e usá-las para monitorizar ativamente erros e anomalias inesperadas, tais como picos nas contagens de erros ou taxas de pedido.
* Registar métricas horárias e usá-las para monitorizar valores médios como contagens médias de erros e taxas de pedido.
* Investigar potenciais problemas usando ferramentas de diagnóstico, como discutido mais tarde na secção "[Diagnosticar problemas]de armazenamento ."

Os gráficos na imagem seguinte ilustram como a média que ocorre para métricas horárias pode esconder picos de atividade. As métricas horárias parecem mostrar uma taxa constante de pedidos, enquanto as métricas minúsculas revelam as flutuações que estão realmente a ocorrer.

![][3]

O resto desta secção descreve quais as métricas que deve monitorizar e porquê.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Monitorização da saúde do serviço
Pode utilizar o [portal Azure](https://portal.azure.com) para ver a saúde do serviço de armazenamento (e outros serviços Azure) em todas as regiões do Azure em todo o mundo. A monitorização permite-lhe ver imediatamente se um problema fora do seu controlo está a afetar o serviço de Armazenamento na região que utiliza para a sua aplicação.

O [portal Azure](https://portal.azure.com) também pode fornecer notificações de incidentes que afetam os vários serviços Azure.
Nota: Esta informação estava previamente disponível, juntamente com dados históricos, no [Painel de Assistência Azure](https://status.azure.com).

Enquanto o [portal Azure](https://portal.azure.com) recolhe informações de saúde de dentro dos centros de dados Azure (monitorização interna), também poderia considerar a adoção de uma abordagem externa para gerar transações sintéticas que acedem periodicamente à sua aplicação web hospedada no Azure a partir de vários locais. Os serviços oferecidos pela [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) e pela Application Insights para a Azure DevOps são exemplos desta abordagem. Para obter mais informações sobre os Insights de Aplicação para Azure DevOps, consulte o apêndice "[Apêndice 5: Monitorização com Insights de Aplicação para Azure DevOps](#appendix-5)."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Capacidade de monitorização
Armazenamento As métricas apenas armazenam métricas de capacidade para o serviço de blob porque as bolhas normalmente representam a maior proporção de dados armazenados (no momento da escrita, não é possível usar Métricas de Armazenamento para monitorizar a capacidade das suas mesas e filas). Pode encontrar estes dados na tabela **$MetricsCapacityBlob** se tiver ativado a monitorização do serviço Blob. As Métricas de Armazenamento registam estes dados uma vez por dia, e pode utilizar o valor do **RowKey** para determinar se a linha contém uma entidade que se relaciona com os dados dos utilizadores **(dados**de valor) ou dados de análise **(análise**de valor). Cada entidade armazenada contém informações sobre a quantidade de armazenamento utilizada **(Capacidade** medida em bytes) e o número atual de contentores **(ContainerCount)** e blobs **(ObjectCount)** em uso na conta de armazenamento. Para obter mais informações sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob,** consulte Storage [Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Deve monitorizar estes valores para um aviso precoce de que está a aproximar-se dos limites de capacidade da sua conta de armazenamento. No portal Azure, pode adicionar regras de alerta para notificá-lo se o uso agregado de armazenamento exceder ou ficar abaixo dos limiares que especifica.
>
>

Para ajudar a estimar o tamanho de vários objetos de armazenamento, como bolhas, consulte o post de blog [Understanding Azure Storage Billing – Width, Transactions e Capacity](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Monitorização da disponibilidade
Deve monitorizar a disponibilidade dos serviços de armazenamento na sua conta de armazenamento, monitorizando o valor da coluna **Disponibilidade** nas tabelas de métricas de hora ou minutos - **$MetricsHourPrimaryTransactionsBlob,** **$MetricsHourPrimaryTransactionsTable,** **$MetricsHourPrimaryTransactionsQueue,** **$MetricsMinutePrimaryTransactionsBlob,** **$MetricsMinutePrimaryTransactionsTable,** **$MetricsMinutePrimaryTransactionsQueue,** **$MetricsCapacityBlob.** A coluna **Disponibilidade** contém um valor percentual que indica a disponibilidade do serviço ou da operação API representada pela linha (a **RowKey** mostra se a linha contém métricas para o serviço como um todo ou para uma operação específica da API).

Qualquer valor inferior a 100% indica que alguns pedidos de armazenamento estão a falhar. Pode ver porque estão a falhar examinando as outras colunas nos dados das métricas que mostram o número de pedidos com diferentes tipos de erros, tais como **ServerTimeoutError**. Deve esperar ver a **Disponibilidade** cair temporariamente abaixo de 100% por razões como intervalos transitórios do servidor enquanto o serviço move divisórias para um pedido de melhor equilíbrio de carga; a lógica de retry na sua aplicação cliente deve lidar com tais condições intermitentes. O artigo [Armazenamento Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que as Métricas de Armazenamento incluem no seu cálculo de **Disponibilidade.**

No [portal Azure,](https://portal.azure.com)pode adicionar regras de alerta para notificá-lo se **a Disponibilidade** para um serviço ficar abaixo de um limiar que especifica.

A secção " Orientação de resolução de[problemas]" deste guia descreve alguns problemas comuns de serviço de armazenamento relacionados com a disponibilidade.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Desempenho de monitorização
Para monitorizar o desempenho dos serviços de armazenamento, pode utilizar as seguintes métricas a partir das tabelas de métricas de hora e minutos.

* Os valores nas colunas **AverageE2ELatency** e **AverageServerLatency** mostram o tempo médio que o serviço de armazenamento ou o tipo de operação API estão a levar para processar pedidos. **AverageE2ELatency** é uma medida de latência de ponta a ponta que inclui o tempo de leitura do pedido e enviar a resposta para além do tempo de processo (portanto, inclui latência da rede assim que o pedido chegar ao serviço de armazenamento); **AverageServerLatency** é uma medida do tempo de processamento e, portanto, exclui qualquer latência da rede relacionada com a comunicação com o cliente. Consulte a secção "[Métricas mostram alta médiaE2ELatency e baixa médiaServerLatency]" mais tarde neste guia para uma discussão sobre por que pode haver uma diferença significativa entre estes dois valores.
* Os valores nas colunas **TotalIngress** e **TotalEgress** mostram a quantidade total de dados, em bytes, entrando e saindo do seu serviço de armazenamento ou através de um tipo específico de operação API.
* Os valores na coluna **TotalRequests** mostram o número total de pedidos que o serviço de armazenamento da operação API está a receber. **TotalRequests** é o número total de pedidos que o serviço de armazenamento recebe.

Normalmente, irá monitorizar para alterações inesperadas em qualquer um destes valores como um indicador de que tem um problema que requer investigação.

No [portal Azure,](https://portal.azure.com)pode adicionar regras de alerta para notificá-lo se alguma das métricas de desempenho deste serviço ficar abaixo ou exceder um limiar que especifica.

A secção "[Troubleshooting guidance]" deste guia descreve alguns problemas comuns de serviço de armazenamento relacionados com o desempenho.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnóstico de problemas de armazenamento
Há várias maneiras de tomar conhecimento de um problema ou problema na sua aplicação, incluindo:

* Uma falha grave que faz com que a aplicação se despenhe ou deixe de funcionar.
* Alterações significativas dos valores de base nas métricas que está a monitorizar como descrito na secção anterior "[Monitorizando o seu serviço]de armazenamento ."
* Relatos de utilizadores da sua aplicação de que alguma operação em particular não foi concluída como esperado ou que alguma funcionalidade não está a funcionar.
* Erros gerados dentro da sua aplicação que aparecem em ficheiros de registo ou através de algum outro método de notificação.

Tipicamente, as questões relacionadas com os serviços de armazenamento azure enquadram-se numa das quatro grandes categorias:

* A sua aplicação tem um problema de desempenho, relatado pelos seus utilizadores, ou revelado por alterações nas métricas de desempenho.
* Existe um problema com a infraestrutura de armazenamento Azure em uma ou mais regiões.
* A sua aplicação encontra um erro, relatado pelos seus utilizadores, ou revelado por um aumento numa das métricas de contagem de erros que monitoriza.
* Durante o desenvolvimento e teste, pode estar a utilizar o emulador de armazenamento local; pode encontrar algumas questões relacionadas especificamente com o uso do emulador de armazenamento.

As seguintes secções descrevem os passos que deve seguir para diagnosticar e resolver problemas em cada uma destas quatro categorias. A secção " Orientação de[resolução de problemas]" mais tarde neste guia fornece mais detalhes para algumas questões comuns que você pode encontrar.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problemas de saúde do serviço
Os problemas de saúde do serviço estão normalmente fora do seu controlo. O [portal Azure](https://portal.azure.com) fornece informações sobre quaisquer problemas em curso com os serviços Azure, incluindo serviços de armazenamento. Se optou pelo Armazenamento Geo-Redundante de Leitura-Acesso quando criou a sua conta de armazenamento, então se os seus dados ficarem indisponíveis no local principal, a sua aplicação pode mudar temporariamente para a cópia apenas de leitura no local secundário. Para ler a partir do secundário, a sua aplicação deve ser capaz de alternar entre a utilização dos locais de armazenamento primário e secundário e poder trabalhar num modo de funcionalidade reduzido com dados apenas de leitura. As bibliotecas do Cliente de Armazenamento Azure permitem definir uma política de retry que pode ler a partir do armazenamento secundário no caso de uma leitura do armazenamento primário falhar. A sua aplicação também precisa de estar ciente de que os dados no local secundário são eventualmente consistentes. Para mais informações, consulte o blog post [Azure Storage Redundancy Options e read Access Geo Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problemas de desempenho
O desempenho de uma aplicação pode ser subjetivo, sobretudo na perspetiva do utilizador. Por conseguinte, é importante ter métricas de linha base disponíveis para o ajudar a identificar onde poderá existir um problema de desempenho. Muitos fatores podem afetar o desempenho de um serviço de armazenamento Azure do ponto de vista da aplicação do cliente. Estes fatores podem funcionar no serviço de armazenamento, no cliente, ou na infraestrutura de rede; por conseguinte, é importante dispor de uma estratégia para identificar a origem da questão do desempenho.

Depois de ter identificado a localização provável da causa do problema de desempenho a partir das métricas, pode então utilizar os ficheiros de registo para encontrar informações detalhadas para diagnosticar e resolver ainda mais o problema.

A secção " Orientação de[resolução de problemas]" mais tarde neste guia fornece mais informações sobre algumas questões comuns relacionadas com o desempenho que você pode encontrar.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnóstico de erros
Os utilizadores da sua aplicação podem notificá-lo dos erros reportados pela aplicação do cliente. As Métricas de Armazenamento também registam contagens de diferentes tipos de erros dos seus serviços de armazenamento, tais como **NetworkError,** **ClientTimeoutError**, ou **AuthorizationError**. Enquanto as Métricas de Armazenamento registam apenas contagens de diferentes tipos de erros, pode obter mais detalhes sobre pedidos individuais examinando registos do lado do servidor, do lado do cliente e da rede. Normalmente, o código de estado HTTP devolvido pelo serviço de armazenamento dará uma indicação do motivo pelo qual o pedido falhou.

> [!NOTE]
> Lembre-se que deve esperar ver alguns erros intermitentes: por exemplo, erros devido a condições de rede transitórias ou erros de aplicação.
>
>

Os recursos seguintes são úteis para compreender os códigos de estado e erro relacionados com o armazenamento:

* [Códigos comuns de erro da API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Códigos de Erro do Serviço de Blobs](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Códigos de erro do serviço de fila](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Códigos de erro do serviço de mesa](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Códigos de erro de serviço de ficheiros](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problemas do emulador de armazenamento
O Azure SDK inclui um emulador de armazenamento que pode executar numa estação de trabalho de desenvolvimento. Este emulador simula a maior parte do comportamento dos serviços de armazenamento Azure e é útil durante o desenvolvimento e teste, permitindo-lhe executar aplicações que utilizam serviços de armazenamento Azure sem a necessidade de uma subscrição Azure e uma conta de armazenamento Azure.

A secção " Orientação de Resolução de[Problemas]" deste guia descreve algumas questões comuns encontradas usando o emulador de armazenamento.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Ferramentas de exploração madeireira de armazenamento
Armazenamento A Logging fornece registo de pedidos de armazenamento do lado do servidor na sua conta de armazenamento Azure. Para obter mais informações sobre como ativar o registo do lado do servidor e aceder aos dados de registo, consulte [a ativação](https://go.microsoft.com/fwlink/?LinkId=510867)do registo de armazenamento e o acesso aos dados de registo .

A Biblioteca do Cliente de Armazenamento para .NET permite-lhe recolher dados de registo do lado do cliente relacionados com as operações de armazenamento realizadas pela sua aplicação. Para obter mais informações, veja [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868) (Registo do Lado do Cliente com a Biblioteca de Clientes de Armazenamento .NET).

> [!NOTE]
> Em algumas circunstâncias (como falhas de autorização SAS), um utilizador pode reportar um erro para o qual não encontra dados de pedido nos registos de Armazenamento do lado do servidor. Pode utilizar as capacidades de registo da Biblioteca de Clientes de Armazenamento para investigar se a causa do problema está no cliente ou utilizar ferramentas de monitorização de rede para investigar a rede.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Utilização de ferramentas de registo de rede
Pode capturar o tráfego entre o cliente e o servidor para fornecer informações detalhadas sobre os dados que o cliente e o servidor estão a trocar e as condições de rede subjacentes. Ferramentas úteis de registo de rede incluem:

* [Fiddler](https://www.telerik.com/fiddler) é um proxy de depuração web gratuito que lhe permite examinar os cabeçalhos e os dados de carga útil de MENSAGENs de pedido e resposta HTTP e HTTPS. Para mais informações, consulte [o Apêndice 1: Utilizar o Violinista para capturar o tráfego HTTP e HTTPS](#appendix-1).
* [O Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) e o [Wireshark](https://www.wireshark.org/) são analisadores de protocolos de rede gratuitos que lhe permitem visualizar informações detalhadas sobre pacotes para uma vasta gama de protocolos de rede. Para obter mais informações sobre wireshark, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego](#appendix-2)da rede ".
* O Microsoft Message Analyzer é uma ferramenta da Microsoft que substitui o Netmon e que além de capturar dados de pacotes de rede, ajuda-o a visualizar e analisar os dados de registo capturados a partir de outras ferramentas. Para mais informações, consulte " Apêndice 3: Utilizar o[Microsoft Message Analyzer para capturar o tráfego](#appendix-3)da rede ".
* Se pretender realizar um teste de conectividade básico para verificar se a sua máquina cliente pode ligar-se ao serviço de armazenamento Azure através da rede, não pode fazê-lo utilizando a ferramenta de **ping** padrão no cliente. No entanto, pode utilizar a ferramenta [ **tcping** ](https://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de registo do Armazenamento De registo e da Biblioteca de Clientes de Armazenamento serão suficientes para diagnosticar um problema, mas em alguns cenários, poderá necessitar da informação mais detalhada que estas ferramentas de registo de rede podem fornecer. Por exemplo, usar o Fiddler para visualizar mensagens HTTP e HTTPS permite-lhe visualizar dados de cabeçalho e carga útil enviados de e para os serviços de armazenamento, o que lhe permitiria examinar como uma aplicação de cliente retenta operações de armazenamento. Os analisadores de protocolos, como o Wireshark, operam ao nível do pacote, permitindo-lhe visualizar dados de TCP, o que lhe permitiria resolver problemas com pacotes perdidos e problemas de conectividade. O Analisador de Mensagens pode funcionar tanto nas camadas HTTP como nas camadas TCP.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Rastreio de ponta a ponta
Rastreio de ponta a ponta usando uma variedade de ficheiros de registo é uma técnica útil para investigar potenciais problemas. Pode utilizar as informações de data/hora a partir dos seus dados de métricas como indicação de onde começar a procurar nos ficheiros de registo para obter informações detalhadas que o ajudarão a resolver o problema.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Correlacionar dados de registo
Ao visualizar registos de aplicações de clientes, vestígios de rede e registo de armazenamento do lado do servidor, é fundamental poder correlacionar os pedidos através dos diferentes ficheiros de registo. Os ficheiros de registo incluem uma série de diferentes campos que são úteis como identificadores de correlação. O ID de pedido do cliente é o campo mais útil para ser usado para correlacionar as entradas nos diferentes registos. No entanto, por vezes, pode ser útil usar o ID de pedido do servidor ou selos de tempo. As seguintes secções fornecem mais detalhes sobre estas opções.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID de pedido de cliente
A Biblioteca do Cliente de Armazenamento gera automaticamente um ID de pedido único do cliente para cada pedido.

* No registo do cliente que a Biblioteca do Cliente de Armazenamento cria, o ID de pedido do cliente aparece no campo de ID do Pedido de **Cliente** de cada entrada de registo relacionada com o pedido.
* Num traço de rede, como um capturado pela Fiddler, o ID de pedido do cliente é visível em mensagens de pedido como o valor de cabeçalho http de **x-ms-cliente-pedido-id** HTTP.
* No registo de registo de registo de armazenamento do lado do servidor, o ID de pedido do cliente aparece na coluna de ID de pedido do Cliente.

> [!NOTE]
> É possível que vários pedidos partilhem o mesmo ID de pedido de cliente porque o cliente pode atribuir este valor (embora a Biblioteca do Cliente de Armazenamento atribua automaticamente um novo valor). Quando o cliente se retenta, todas as tentativas partilham a mesma identificação de pedido de cliente. No caso de um lote enviado do cliente, o lote tem uma identificação de pedido de cliente único.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID de pedido do servidor
O serviço de armazenamento gera automaticamente iDs de pedido de servidor.

* No registo de registo de registo de registo de armazenamento do lado do servidor, o ID de pedido do servidor aparece na coluna de cabeçalho do **Id de Pedido.**
* Num traço de rede, como um capturado pelo Fiddler, o ID de pedido do servidor aparece em mensagens de resposta como o valor do cabeçalho http do **x-ms-request-id.**
* No registo do lado do cliente que a Biblioteca do Cliente de Armazenamento cria, o ID de pedido do servidor aparece na coluna 'Texto de **Operação'** para a entrada de registo mostrando detalhes da resposta do servidor.

> [!NOTE]
> O serviço de armazenamento atribui sempre um ID de pedido de servidor único a cada pedido que recebe, pelo que cada tentativa de retry do cliente e cada operação incluída num lote tem um ID de pedido de servidor único.
>
>

Se a Biblioteca do Cliente de Armazenamento lançar uma **Exceção** de Armazenamento no cliente, a propriedade **RequestInformation** contém um objeto **RequestResult** que inclui uma propriedade **ServiceRequestID.** Também pode aceder a um objeto **RequestResult** a partir de uma instância **OperationContext.**

A amostra de código abaixo demonstra como definir um valor personalizado **do ClienteRequestId** anexando um objeto **OperationContext** o pedido ao serviço de armazenamento. Também mostra como recuperar o valor **ServerRequestId** a partir da mensagem de resposta.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a><a name="timestamps"></a>Carimbos de Data/Hora
Também pode usar selos temporais para localizar entradas de registo relacionadas, mas tenha cuidado com qualquer desvio de relógio entre o cliente e o servidor que possa existir. Procure mais ou menos 15 minutos para combinar as entradas do lado do servidor com base na marca de tempo no cliente. Lembre-se que os metadados blob para as bolhas que contêm métricas indicam o intervalo de tempo para as métricas armazenadas na bolha. Este intervalo de tempo é útil se tiver muitas manchas métricas durante o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Orientação de resolução de problemas
Esta secção irá ajudá-lo no diagnóstico e resolução de problemas de algumas das questões comuns que a sua aplicação pode encontrar ao utilizar os serviços de armazenamento Azure. Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Árvore de decisão de resolução de problemas**

---
O seu problema diz respeito ao desempenho de um dos serviços de armazenamento?

* [As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]
* [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
* [As métricas apresentam uma AverageServerLatency alta]
* [Ocorrem atrasos inesperados na entrega de mensagens numa fila]

---
O seu problema diz respeito à disponibilidade de um dos serviços de armazenamento?

* [As métricas apresentam um aumento do PercentThrottlingError]
* [As métricas apresentam um aumento do PercentTimeoutError]
* [As métricas apresentam um aumento do PercentNetworkError]

---
 A sua aplicação cliente está a receber uma resposta HTTP 4XX (como 404) de um serviço de armazenamento?

* [O cliente está a receber mensagens de HTTP 403 (Proibido)]
* [O cliente está a receber mensagens de HTTP 404 (Não encontrado)]
* [O cliente está a receber mensagens de HTTP 409 (Conflito)]

---
[As métricas mostram que as entradas de registo low PercentSuccess ou analytics têm operações com o estado de transação do ClientOtherErrors]

---
[As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]

---
[Você está experimentando reboots inesperados de Máquinas Virtuais que têm um grande número de VHDs anexados]

---
[O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]

---
[Encontra problemas na instalação do SDK Azure para .NET]

---
[Você tem um problema diferente com um serviço de armazenamento]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa
A ilustração abaixo da ferramenta de monitorização do [portal Azure](https://portal.azure.com) mostra um exemplo em que a **MédiaE2ELatency** é significativamente superior à **Média ServerLatency**.

![][4]

O serviço de armazenamento apenas calcula a métrica **AverageE2ELatency** para pedidos bem sucedidos e, ao contrário da **AverageServerLatency,** inclui o tempo que o cliente demora a enviar os dados e receber reconhecimento do serviço de armazenamento. Portanto, uma diferença entre **AverageE2ELatency** e **AverageServerLatency** pode ser devido à aplicação do cliente ser lenta para responder, ou devido às condições na rede.

> [!NOTE]
> Também pode **visualizar e2ELatency** e **ServerLatency** para operações individuais de armazenamento nos dados de registo de registo de armazenamento.
>
>

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente
As possíveis razões para o cliente responder lentamente incluem ter um número limitado de ligações ou fios disponíveis, ou ser baixo em recursos como CPU, memória ou largura de banda da rede. Poderá ser capaz de resolver o problema modificando o código do cliente para ser mais eficiente (por exemplo, utilizando chamadas assíncronas para o serviço de armazenamento), ou utilizando uma Máquina Virtual maior (com mais núcleos e mais memória).

Para os serviços de mesa e fila, o algoritmo Nagle também pode causar **alta médiaE2ELatency** em comparação com **averageServerLatency**: para mais informações, consulte o algoritmo pós [Nagle não é amigável para pequenos pedidos](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Pode desativar o algoritmo Nagle em código utilizando a classe **ServicePointManager** no **espaço de** System.Net nome. Deve fazê-lo antes de fazer chamadas para a mesa ou serviços de fila na sua aplicação, uma vez que isso não afeta ligações que já estão abertas. O exemplo que se segue provém do método **Application_Start** no papel de trabalhador.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Deverá verificar os registos do lado do cliente para ver quantos pedidos o seu pedido de cliente está a enviar, e verificar se existem estrangulamentos gerais de desempenho relacionados com .NET no seu cliente, tais como CPU, .NET recolha de lixo, utilização da rede ou memória. Como ponto de partida para a resolução de problemas com aplicações de clientes .NET, consulte [Debugging, Tracing e Profiling](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência da rede
Tipicamente, a alta latência de ponta a ponta causada pela rede deve-se a condições transitórias. Pode investigar problemas de rede transitórios e persistentes, tais como pacotes abandonados, utilizando ferramentas como o Wireshark ou o Microsoft Message Analyzer.

Para obter mais informações sobre o uso da Wireshark para resolver problemas de rede, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego]da rede ."

Para obter mais informações sobre a utilização do Microsoft Message Analyzer para resolver problemas de rede, consulte " Apêndice 3: Utilizar o[Microsoft Message Analyzer para capturar o tráfego]da rede ."

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada
Neste cenário, a causa mais provável é um atraso nos pedidos de armazenamento que chegam ao serviço de armazenamento. Deve investigar porque é que os pedidos do cliente não estão a chegar ao serviço de blob.

Uma das razões possíveis para o cliente atrasar os pedidos é que há um número limitado de ligações ou fios disponíveis.

Verifique também se o cliente está a realizar várias tentativas e investigue a razão se for. Para determinar se o cliente está a realizar múltiplas tentativas, pode:

* Examine os registos de Análise de Armazenamento. Se várias tentativas estiverem a acontecer, verá várias operações com o mesmo ID de pedido de cliente, mas com diferentes iDs de pedido de servidor.
* Examine os registos do cliente. A exploração madeireira verbosa indicará que ocorreu uma nova tentativa.
* Desinveja o seu código e verifique as propriedades do objeto **OperationContext** associado supérficante ao pedido. Se a operação tiver sido novamente tentada, a propriedade **RequestResults** incluirá múltiplas iDs de pedido de servidor únicos. Também pode verificar os tempos de início e fim para cada pedido. Para mais informações, consulte a amostra de código na secção [Id de pedido]do servidor .

Se não houver problemas no cliente, deve investigar potenciais problemas de rede, como a perda de pacotes. Pode utilizar ferramentas como wireshark ou Microsoft Message Analyzer para investigar problemas de rede.

Para obter mais informações sobre o uso da Wireshark para resolver problemas de rede, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego]da rede ."

Para obter mais informações sobre a utilização do Microsoft Message Analyzer para resolver problemas de rede, consulte " Apêndice 3: Utilizar o[Microsoft Message Analyzer para capturar o tráfego]da rede ."

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>As métricas apresentam uma AverageServerLatency alta
No caso de **uma elevada média de** venda de serviços para pedidos de descarregamento de blob, deve utilizar os registos de registo de armazenamento para ver se existem pedidos repetidos para a mesma bolha (ou conjunto de bolhas). Para pedidos de upload de blob, você deve investigar o tamanho do bloco que o cliente está usando (por exemplo, blocos com menos de 64 K de tamanho pode resultar em despesas aéreas a menos que as leituras também estejam em pedaços inferiores a 64 K), e se vários clientes estão carregando blocos para a mesma bolha em paralelo. Deve também verificar as métricas por minuto para picos no número de pedidos que resultam em exceder os objetivos de escalabilidade por segundo: ver também "[Métricas mostram um aumento no PercentTimeoutError]."

Se estiver a ver uma **alta média de vendas para** pedidos de descarregamento de blob quando há pedidos repetidos a mesma bolha ou conjunto de bolhas, então deve considerar o corte destas bolhas usando o Azure Cache ou a Rede de Entrega de Conteúdos Azure (CDN). Para pedidos de upload, pode melhorar a entrada utilizando um tamanho de bloco maior. Para consultas a tabelas, também é possível implementar o caching do lado do cliente em clientes que realizam as mesmas operações de consulta e onde os dados não mudam frequentemente.

Valores de **Alta Média ServerLatency** também podem ser um sintoma de tabelas ou consultas mal concebidas que resultam em operações de digitalização ou que seguem o padrão de apêndice/preparação. Para mais informações, consulte "[As métricas mostram um aumento no PercentThrottlingError]".

> [!NOTE]
> Pode encontrar aqui uma lista completa de verificação de desempenho da lista de verificação: [Desempenho do Armazenamento do Microsoft Azure e lista](storage-performance-checklist.md)de verificação de escalabilidade .
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Ocorrem atrasos inesperados na entrega de mensagens numa fila
Se estiver a passar por um atraso entre o momento em que uma aplicação adiciona uma mensagem a uma fila e o tempo que fica disponível para ler a partir da fila, então deve tomar os seguintes passos para diagnosticar o problema:

* Verifique se a aplicação está a adicionar com sucesso as mensagens à fila. Verifique se a aplicação não está a tentar novamente o método **AddMessage** várias vezes antes de ter sucesso. Os registos da Biblioteca de Clientes de Armazenamento mostrarão quaisquer repetições repetidas de operações de armazenamento.
* Verifique se não há nenhum desvio de relógio entre a função do trabalhador que adiciona a mensagem à fila e o papel do trabalhador que lê a mensagem da fila que faz parecer que há um atraso no processamento.
* Verifique se o papel do trabalhador que lê as mensagens da fila está a falhar. Se um cliente de fila ligar para o método **GetMessage** mas não responder com um reconhecimento, a mensagem permanecerá invisível na fila até que o período de **invisibilidade Timeout** expire. Neste ponto, a mensagem volta a estar disponível para processamento.
* Verifique se o comprimento da fila está a crescer ao longo do tempo. Isto pode ocorrer se não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros trabalhadores estão colocando na fila. Verifique também as métricas para ver se os pedidos de eliminação estão a falhar e a contagem de fila em mensagens, o que pode indicar tentativas repetidas falhadas de apagar a mensagem.
* Examine os registos de registo de armazenamento para quaisquer operações de fila que tenham valores **de E2ELatency** e **ServerLatency** superiores ao esperado durante um período de tempo mais longo do que o habitual.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>As métricas apresentam um aumento do PercentThrottlingError
Erros de estrangulamento ocorrem quando excede os alvos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento acelera para garantir que nenhum cliente ou inquilino possa utilizar o serviço em detrimento de outros. Para obter mais informações, consulte [a escalabilidade e os objetivos](scalability-targets-standard-account.md) de desempenho para as contas de armazenamento padrão para obter detalhes sobre os alvos de escalabilidade para contas de armazenamento e metas de desempenho para divisórias dentro das contas de armazenamento.

Se a métrica **PercentThrottlingError** mostrar um aumento na percentagem de pedidos que estão a falhar com um erro de estrangulamento, precisa de investigar um de dois cenários:

* [Aumento transitório no PercentThrottlingError]
* [Aumento permanente do erro do Erro PercentThrottling Error]

Um aumento no **PercentThrottlingError** ocorre frequentemente ao mesmo tempo que um aumento no número de pedidos de armazenamento, ou quando está inicialmente a carregar a testar a sua aplicação. Isto também pode manifestar-se no cliente como "503 Server Busy" ou "500 Operation Timeout" HTTP mensagens de estado de http das operações de armazenamento.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório no PercentThrottlingError
Se estiver a ver picos no valor do **PercentThrottlingError** que coincidem com períodos de alta atividade para a aplicação, implementa uma estratégia exponencial (não linear) de back-off para repetições no seu cliente. Os retentores de back-off reduzem a carga imediata na divisória e ajudam a sua aplicação a suavizar os picos no tráfego. Para obter mais informações sobre como implementar políticas de retry utilizando a Biblioteca de Clientes de Armazenamento, consulte o [espaço de nome Microsoft.Azure.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Também pode ver picos no valor do **PercentThrottlingError** que não coincidem com períodos de alta atividade para a aplicação: a causa mais provável aqui é o serviço de armazenamento movendo divisórias para melhorar o equilíbrio de carga.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente do erro do Erro PercentThrottling Error
Se estiver a ver um valor consistentemente elevado para o **PercentThrottlingError** na sequência de um aumento permanente dos volumes de transações, ou quando está a realizar os seus testes de carga iniciais na sua aplicação, então precisa de avaliar como a sua aplicação está a usar divisórias de armazenamento e se está a aproximar-se dos alvos de escalabilidade para uma conta de armazenamento. Por exemplo, se estiver a ver erros de estrangulamento numa fila (que conta como uma única divisória), então deve considerar usar filas adicionais para espalhar as transações em várias divisórias. Se estiver a ver erros de estrangulamento numa mesa, tem de considerar a utilização de um esquema de partilha diferente para difundir as suas transações em várias divisórias, utilizando uma gama mais alargada de valores-chave de divisórias. Uma das causas comuns deste problema é o anti-padrão de preparação/apêndice onde seleciona a data como chave de partição e, em seguida, todos os dados de um determinado dia são escritos a uma divisória: sob carga, isto pode resultar num estrangulamento de escrita. Ou considere um design de divisória diferente ou avalie se o uso do armazenamento de bolhas pode ser uma solução melhor. Verifique também se a estrangulamento está a ocorrer em resultado de picos no seu tráfego e investigue formas de suavizar o seu padrão de pedidos.

Se distribuir as suas transações por várias divisórias, ainda deve estar ciente dos limites de escalabilidade definidos para a conta de armazenamento. Por exemplo, se usou dez filas cada uma processando o máximo de 2.000 mensagens 1KB por segundo, estará no limite global de 20.000 mensagens por segundo para a conta de armazenamento. Se precisa processar mais de 20.000 entidades por segundo, deve considerar a utilização de várias contas de armazenamento. Deve também ter em conta que a dimensão dos seus pedidos e entidades tem impacto no momento em que o serviço de armazenamento acelera os seus clientes: se tiver pedidos e entidades maiores, poderá ser estrangulado mais cedo.

O design de consulta ineficiente também pode fazer com que você atinja os limites de escalabilidade para divisórias de mesa. Por exemplo, uma consulta com um filtro que seleciona apenas um por cento das entidades numa partição mas que digitaliza todas as entidades numa partição terá de aceder a cada entidade. Todas as entidades lidas contarão para o número total de transações nessa divisória; portanto, pode facilmente atingir os alvos de escalabilidade.

> [!NOTE]
> Os seus testes de desempenho devem revelar quaisquer projetos de consulta ineficientes na sua aplicação.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>As métricas apresentam um aumento do PercentTimeoutError
As suas métricas mostram um aumento no **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um elevado volume de mensagens de estado "500 OperaTimeout" HTTP das operações de armazenamento.

> [!NOTE]
> Pode ver erros de timeout temporariamente à medida que o serviço de armazenamento equilibra os pedidos, movendo uma partição para um novo servidor.
>
>

A métrica **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousTimeTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimetimeerror**e **SASServerTimeoutError**.

Os intervalos do servidor são causados por um erro no servidor. Os prazos de cliente acontecem porque uma operação no servidor excedeu o prazo especificado pelo cliente; por exemplo, um cliente que utilize a Biblioteca do Cliente de Armazenamento pode definir um prazo para uma operação utilizando a propriedade **ServerTimeout** da classe **QueueRequestOptions.**

Os intervalos do servidor indicam um problema com o serviço de armazenamento que requer uma investigação mais aprofundada. Pode usar métricas para ver se está a atingir os limites de escalabilidade do serviço e identificar quaisquer picos de tráfego que possam estar a causar este problema. Se o problema for intermitente, pode ser devido à atividade de equilíbrio de carga no serviço. Se o problema for persistente e não for causado pela sua aplicação atingir os limites de escalabilidade do serviço, deverá levantar uma questão de suporte. Para os timeouts do cliente, deve decidir se o prazo definido para um valor adequado no cliente e alterar o valor de tempo definido no cliente ou investigar como pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizando as consultas de mesa ou reduzindo o tamanho das suas mensagens.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>As métricas apresentam um aumento do PercentNetworkError
As suas métricas mostram um aumento no **PercentNetworkError** para um dos seus serviços de armazenamento. A métrica **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError**, e **SASNetworkError**. Estes ocorrem quando o serviço de armazenamento deteta um erro de rede quando o cliente faz um pedido de armazenamento.

A causa mais comum deste erro é um cliente desligar antes que um prazo expire no serviço de armazenamento. Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento. Também pode utilizar wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. Estas ferramentas são descritas nos [apêndices.]

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>O cliente está a receber mensagens de HTTP 403 (Proibido)
Se a aplicação cliente estiver a gerar erros HTTP 403 (Proibido), uma das causas prováveis é o cliente estar a utilizar uma Assinatura de Acesso Partilhado (SAS) expirada quando envia um pedido de armazenamento (embora outras causas possíveis incluam distorção do relógio, chaves inválidas e cabeçalhos vazios). Se uma chave de SAS expirada for a causa, não verá entradas nos dados do Registo de Armazenamento do lado do servidor. A tabela seguinte mostra uma amostra do registo do lado do cliente gerado pela Biblioteca do Cliente de Armazenamento que ilustra este problema que ocorre:

| Origem | Verbosidade | Verbosidade | ID de pedido de cliente | Texto de operação |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Informações |3 |85d077ab-... |Início da operação com localização Primary por modo de localização PrimaryOnly. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Iniciando pedido sincronizado para<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |À espera de resposta. |
| Microsoft.Azure.Storage |Aviso |2 |85d077ab -... |Exceção lançada enquanto aguarda resposta: O servidor remoto devolveu um erro: (403) Proibido. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Resposta recebida. Código de estado = 403, ID de pedido = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Aviso |2 |85d077ab -... |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (403) Proibido.. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Verificando se a operação deve ser novamente tentada. Contagem de retry = 0, CÓDIGO de estado HTTP = 403, Exceção = O servidor remoto devolveu um erro: (403) Proibido.. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |A próxima localização foi definida para Primary, com base no modo de localização. |
| Microsoft.Azure.Storage |Erro |1 |85d077ab -... |A política de retry não permitiu uma nova tentativa. Falha com o servidor remoto devolveu um erro: (403) Proibido. |

Neste cenário, deve investigar por que razão o token SAS está expirando antes que o cliente envie o token para o servidor:

* Normalmente, não deve definir uma hora de início quando cria uma SAS para um cliente utilizar imediatamente. Se existirem pequenas diferenças horárias entre o anfitrião que gera a SAS com a hora atual e o serviço de armazenamento, é possível que o serviço de armazenamento receba uma SAS que ainda não seja válida.
* Não defina um tempo de expiração demasiado curto numa SAS. Novamente, pequenas diferenças horárias entre o anfitrião que gera a SAS e o serviço de armazenamento podem levar uma SAS a expirar aparentemente mais cedo do que o previsto.
* O parâmetro da versão na tecla SAS (por exemplo **sv=2015-04-05**) corresponde à versão da Biblioteca de Clientes de Armazenamento que está a utilizar? Recomendamos que utilize sempre a versão mais recente da Biblioteca do Cliente de [Armazenamento.](https://www.nuget.org/packages/WindowsAzure.Storage/)
* Se voltar a gerar as chaves de acesso ao armazenamento, quaisquer tokens de SAS existentes podem ser invalidados. Este problema pode ocorrer se gerar tokens de SAS com um tempo de expiração longo para aplicações cliente na cache.

Se estiver a utilizar a Biblioteca de Cliente de Armazenamento para gerar tokens de SAS, é fácil criar um token válido. No entanto, se estiver a utilizar a API REST de armazenamento e a construir as fichas SAS à mão, consulte o [Acesso Delegante com uma Assinatura de Acesso Partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>O cliente está a receber mensagens de HTTP 404 (Não encontrado)
Se a aplicação cliente receber uma mensagem de HTTP 404 (Não encontrado) do servidor, isso implica que o objeto que o cliente estava a tentar utilizar (por exemplo, um blob, contentor, entidade, tabela ou fila) não existe no serviço de armazenamento. Existem vários motivos possíveis para tal, como:

* [O cliente ou outro processo eliminou anteriormente o objeto]
* [Um problema de autorização da Assinatura de Acesso Partilhado (SAS)]
* [O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]
* [Falha de rede]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>O cliente ou outro processo eliminou anteriormente o objeto
Nos cenários em que o cliente está a tentar ler, atualizar ou eliminar dados num serviço de armazenamento é geralmente fácil identificar nos registos do lado do servidor uma operação anterior que apagou o objeto em questão do serviço de armazenamento. Frequentemente, os dados de registo mostram que outro utilizador ou processo apagou o objeto. No registo de registo de registo de armazenamento do lado do servidor, as colunas de chave de objetos de funcionamento e solicitadas mostram quando um cliente apagou um objeto.

No cenário em que um cliente está a tentar inserir um objeto, pode não ser imediatamente óbvio por que isso resulta numa resposta HTTP 404 (Não encontrada) dado que o cliente está a criar um novo objeto. No entanto, se o cliente está a criar uma bolha, deve ser capaz de encontrar o recipiente blob, se o cliente estiver a criar uma mensagem deve ser capaz de encontrar uma fila, e se o cliente estiver adicionando uma linha deve ser capaz de encontrar a mesa.

Pode utilizar o registo do lado do cliente da Biblioteca do Cliente de Armazenamento para obter uma compreensão mais detalhada de quando o cliente envia pedidos específicos para o serviço de armazenamento.

O seguinte registo do lado do cliente gerado pela biblioteca do Cliente de Armazenamento ilustra o problema quando o cliente não consegue encontrar o recipiente para a bolha que está a criar. Este registo inclui detalhes das seguintes operações de armazenamento:

| ID do pedido | Operação |
| --- | --- |
| 07b26a5d-... |**EliminarSeExiste** método para eliminar o recipiente de bolhas. Note que esta operação inclui um pedido **HEAD** para verificar a existência do recipiente. |
| e2d06d78... |**CreateIfNotExiste** método para criar o recipiente blob. Note que esta operação inclui um pedido **HEAD** que verifica a existência do recipiente. O **HEAD** devolve uma mensagem 404, mas continua. |
| de8b1c3c-... |**Método UploadFromStream** para criar a bolha. O pedido **do PUT** falha com uma mensagem 404 |

Entradas de registo:

| ID do pedido | Texto da Operação |
| --- | --- |
| 07b26a5d-... |Iniciando um pedido sincronizado `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`para . |
| 07b26a5d-... |StringtoSign = HEAD.................. x-ms-cliente-request-id:07b26a5d-.... x-ms-date:Tue, 03 jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |À espera de resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 200, Id de Pedido = eeead849-... Conteúdo-MD5 = , &quot;ETag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| 07b26a5d-... |A descarregar o corpo de resposta. |
| 07b26a5d-... |Operação concluída com sucesso. |
| 07b26a5d-... |Iniciando um pedido sincronizado `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`para . |
| 07b26a5d-... |StringtoSign = DELETE.................. x-ms-cliente-request-id:07b26a5d-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |À espera de resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 202, ID de pedido = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| 07b26a5d-... |A descarregar o corpo de resposta. |
| 07b26a5d-... |Operação concluída com sucesso. |
| e2d06d78-... |Iniciando um pedido assíncrono para `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`.</td> |
| e2d06d78-... |StringtoSign = HEAD.................. x-ms-cliente-request-id:e2d06d78-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |À espera de resposta. |
| de8b1c3c-... |Iniciando um pedido sincronizado `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`para . |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==........ x-ms-blob-type:BlockBlob.x-ms-cliente-request-id:de8b1c3c-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Preparando-se para escrever dados de pedidos. |
| e2d06d78-... |Exceção lançada enquanto aguarda resposta: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| e2d06d78-... |Resposta recebida. Código de estado = 404, ID de pedido = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| e2d06d78-... |A descarregar o corpo de resposta. |
| e2d06d78-... |Operação concluída com sucesso. |
| e2d06d78-... |Iniciando um pedido assíncrono para `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-cliente-request-id:e2d06d78-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |À espera de resposta. |
| de8b1c3c-... |Escrever dados de pedidos. |
| de8b1c3c-... |À espera de resposta. |
| e2d06d78-... |Exceção lançada enquanto aguarda resposta: O servidor remoto devolveu um erro: (409) Conflito.. |
| e2d06d78-... |Resposta recebida. Código de estado = 409, ID de pedido = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Descarregar o corpo de resposta a erros. |
| de8b1c3c-... |Exceção lançada enquanto aguarda resposta: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| de8b1c3c-... |Resposta recebida. Código de estado = 404, ID de pedido = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| de8b1c3c-... |A política de retry não permitiu uma nova tentativa. Falha com o servidor remoto devolveu um erro: (404) Não Encontrado.. |
| e2d06d78-... |A política de retry não permitiu uma nova tentativa. Falha com o servidor remoto devolveu um erro: (409) Conflito.. |

Neste exemplo, o registo mostra que o cliente está a interceder pedidos do método **CreateIfNotExists** (solicitar ID e2d06d78...) com os pedidos do método **UploadFromStream** (de8b1c3c-...). Esta interdição acontece porque a aplicação do cliente está invocando estes métodos assincronicamente. Modifique o código assíncrono no cliente para garantir que cria o recipiente antes de tentar enviar quaisquer dados para uma bolha nesse recipiente. Idealmente, deve criar todos os seus recipientes com antecedência.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Um problema de autorização da Assinatura de Acesso Partilhado (SAS)
Se a aplicação do cliente tentar utilizar uma chave SAS que não inclua as permissões necessárias para a operação, o serviço de armazenamento devolve uma mensagem HTTP 404 (Não encontrada) ao cliente. Ao mesmo tempo, verá também um valor não nulo para o **SASAuthorizationError** nas métricas.

A tabela que se segue mostra uma mensagem de registo do lado do servidor da amostra do ficheiro de registo de registo de armazenamento:

| Nome | Valor |
| --- | --- |
| Hora de início do pedido | 2014-05-30t06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Estado do pedido     | Erro de Autorização sas        |
| Código de estado de HTTP   | 404                            |
| Tipo de autenticação| Sas                          |
| Tipo de serviço       | Blobs                         |
| URL do Pedido         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&&;sig=XXXXX api-version=2014-02-14 |
| Pedir cabeçalho de ID  | a1f348d5-8032-4912-93ef-b393e52a3b |
| ID de pedido de cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigue por que razão o seu pedido de cliente está a tentar realizar uma operação para a qual não lhe foram concedidas permissões.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>O código JavaScript do lado do cliente não tem permissão para aceder ao objeto
Se estiver a utilizar um cliente JavaScript e o serviço de armazenamento estiver a devolver mensagens HTTP 404, verifique os seguintes erros JavaScript no navegador:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Pode utilizar as Ferramentas de Desenvolvimento F12 no Internet Explorer para rastrear as mensagens trocadas entre o navegador e o serviço de armazenamento quando estiver a resolver problemas com o lado do cliente, o JavaScript.
>
>

Estes erros ocorrem porque o navegador implementa a mesma restrição de segurança da política de [origem](https://www.w3.org/Security/wiki/Same_Origin_Policy) que impede uma página web de chamar uma API num domínio diferente do domínio de onde a página provém.

Para contornar o problema do JavaScript, pode configurar a Cross Origin Resource Sharing (CORS) para o serviço de armazenamento a que o cliente está a aceder. Para mais informações, consulte suporte para serviços de armazenamento de [recursos de origem cruzada (CORS) para serviços de armazenamento azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).

A seguinte amostra de código mostra como configurar o seu serviço de blob para permitir que o JavaScript funcionano domínio Contoso aceda a uma bolha no seu serviço de armazenamento blob:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a><a name="network-failure"></a>Falha na rede
Em algumas circunstâncias, os pacotes de rede perdidos podem levar ao serviço de armazenamento devolvendo mensagens HTTP 404 ao cliente. Por exemplo, quando a sua aplicação cliente está a apagar uma entidade do serviço de mesa, vê o cliente lançar uma exceção de armazenamento reportando uma mensagem de estado "HTTP 404 (Não Encontrada)" do serviço de mesa. Ao investigar a tabela no serviço de armazenamento de mesa, vê que o serviço apagou a entidade conforme solicitado.

Os detalhes de exceção no cliente incluem o ID de pedido (7e84f12d...) atribuído pelo serviço de mesa para o pedido: você pode usar esta informação para localizar os detalhes do pedido nos registos de armazenamento do lado do servidor, pesquisando na coluna de **cabeçalho de pedido** no ficheiro de registo. Também pode utilizar as métricas para identificar quando ocorrerem falhas como esta e, em seguida, pesquisar os ficheiros de registo com base no momento em que as métricas registaram este erro. Esta entrada de registo mostra que a eliminação falhou com uma mensagem de estado "HTTP (404) Client Other Error". A mesma entrada de registo inclui também o ID de pedido gerado pelo cliente na coluna de **pedido-cliente** (813ea74f...).

O registo do lado do servidor também inclui outra entrada com o mesmo valor de **cliente-pedido-id** (813ea74f...) para uma operação de eliminação bem sucedida para a mesma entidade, e do mesmo cliente. Esta operação de eliminação bem sucedida ocorreu muito pouco antes do pedido de eliminação falhado.

A causa mais provável deste cenário é que o cliente enviou um pedido de eliminação da entidade para o serviço de mesa, que conseguiu, mas não recebeu um reconhecimento do servidor (talvez devido a um problema temporário de rede). O cliente voltou então a tentar automaticamente a operação (utilizando o mesmo **cliente-pedido-id),** e este retry falhou porque a entidade já tinha sido apagada.

Se este problema ocorrer com frequência, deve investigar por que razão o cliente não está a receber agradecimentos do serviço de mesa. Se o problema for intermitente, deverá prender o erro "HTTP (404) Não Encontrado" e instá-lo no cliente, mas permitir que o cliente continue.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>O cliente está a receber mensagens de HTTP 409 (Conflito)
A tabela seguinte mostra um extrato do registo do lado do servidor para duas operações do cliente: **DeleteIfExists** seguido imediatamente pela **CreateIfNotExists** usando o mesmo nome de recipiente blob. Cada operação do cliente resulta em dois pedidos enviados para o servidor, primeiro um pedido **getContainerProperties** para verificar se o recipiente existe, seguido do pedido **de DeleteContainer** ou **CreateContainer.**

| Carimbo de data/hora | Operação | Resultado | Nome do contentor | ID de pedido de cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |Eliminar Recipiente |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

O código na aplicação do cliente elimina e recria imediatamente um recipiente blob com o mesmo nome: o método **CreateIfNotExists** (Id de pedido de cliente bc881924-...) acaba por falhar com o erro HTTP 409 (Conflito). Quando um cliente elimina blobs, contentores, tabelas ou filas, tem de passar um curto período até o nome ficar novamente disponível.

A aplicação cliente deve utilizar nomes de contentor exclusivos sempre que criar novos contentores se o padrão de eliminação/recriação for comum.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>As métricas mostram que as entradas de registo low PercentSuccess ou analytics têm operações com o estado de transação do ClientOtherErrors
A métrica **PercentSuccess** captura a percentagem de operações que foram bem sucedidas com base no seu Código de Estado HTTP. As operações com códigos de estado de 2XX contam como bem sucedidas, enquanto as operações com códigos de estado nas gamas 3XX, 4XX e 5XX são contabilizadas como infrutíferas e baixam o valor métrico **PercentSuccess.** Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com um estado de transação de **ClientesOtherErrors**.

É importante notar que estas operações foram concluídas com sucesso e, portanto, não afetam outras métricas, como a disponibilidade. Alguns exemplos de operações que executam com sucesso, mas que podem resultar em códigos de estado HTTP mal sucedidos incluem:

* **ResourceNotFound** (Não Encontrado 404), por exemplo, de um pedido GET para uma bolha que não existe.
* **Recursos JáExistis** (Conflito 409), por exemplo, a partir de uma operação **CreateIfNotExist** onde o recurso já existe.
* **CondicionadoNotMet** (Não Modificado 304), por exemplo, a partir de uma operação condicional, como quando um cliente envia um valor **ETag** e um cabeçalho HTTP **If-None-Match** para solicitar uma imagem apenas se tiver sido atualizado desde a última operação.

Pode encontrar uma lista de códigos de erro comuns da API REST que os serviços de armazenamento devolvem na página Códigos de [Erro DaPI Common REST](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento
Se vir mudanças repentinas e inesperadas no uso da capacidade na sua conta de armazenamento, pode investigar as razões, analisando primeiro as suas métricas de disponibilidade; por exemplo, um aumento do número de pedidos de eliminação falhados pode levar a um aumento da quantidade de armazenamento blob que está a usar como operações de limpeza específicas da aplicação que você poderia esperar estar libertando espaço pode não estar funcionando como esperado (por exemplo, porque as fichas SAS usadas para libertar o espaço expiraram).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste
Normalmente utiliza o emulador de armazenamento durante o desenvolvimento e testa-se para evitar a exigência de uma conta de armazenamento Azure. As questões comuns que podem ocorrer quando se está a usar o emulador de armazenamento são:

* [A funcionalidade "X" não está a funcionar no emulador de armazenamento]
* [Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento]
* [Executar o emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>A funcionalidade "X" não está a funcionar no emulador de armazenamento
O emulador de armazenamento não suporta todas as funcionalidades dos serviços de armazenamento Azure, como o serviço de ficheiros. Para mais informações, consulte [Utilize o Emulador](storage-use-emulator.md)de Armazenamento Azure para Desenvolvimento e Teste .

Para as características que o emulador de armazenamento não suporta, utilize o serviço de armazenamento Azure na nuvem.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento
Está a testar a sua aplicação que utiliza a Biblioteca do Cliente de Armazenamento contra o emulador de armazenamento local e chamadas de métodos como **CreateIfNotExists** falham com a mensagem de erro "O valor para um dos cabeçalhos HTTP não está no formato correto." Isto indica que a versão do emulador de armazenamento que está a utilizar não suporta a versão da biblioteca de clientes de armazenamento que está a utilizar. A Biblioteca do Cliente de Armazenamento adiciona a **versão x-ms-cabeçalho** a todos os pedidos que faz. Se o emulador de armazenamento não reconhecer o valor no cabeçalho da **versão x-ms,** rejeita o pedido.

Pode utilizar os registos do Cliente da Biblioteca de Armazenamento para ver o valor do cabeçalho da **versão x-ms** que está a enviar. Também pode ver o valor do cabeçalho da **versão x-ms** se utilizar o Fiddler para rastrear os pedidos da sua aplicação de cliente.

Este cenário ocorre normalmente se instalar e utilizar a versão mais recente da Biblioteca do Cliente de Armazenamento sem atualizar o emulador de armazenamento. Deve instalar a versão mais recente do emulador de armazenamento ou utilizar o armazenamento em nuvem em vez do emulador para desenvolvimento e teste.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Executar o emulador de armazenamento requer privilégios administrativos
É solicitado para credenciais de administrador quando executa o emulador de armazenamento. Isto só ocorre quando está a inicializar o emulador de armazenamento pela primeira vez. Depois de ter rubricado o emulador de armazenamento, não precisa de privilégios administrativos para o voltar a executar.

Para mais informações, consulte [Utilize o Emulador](storage-use-emulator.md)de Armazenamento Azure para Desenvolvimento e Teste . Também pode inicializar o emulador de armazenamento no Estúdio Visual, que também exigirá privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Encontra problemas na instalação do SDK Azure para .NET
Quando tenta instalar o SDK, falha a tentativa de instalar o emulador de armazenamento na sua máquina local. O registo de instalação contém uma das seguintes mensagens:

* CAQuietExec: Erro: Incapaz de aceder à instância SQL
* CAQuietExec: Erro: Incapaz de criar base de dados

A causa é um problema com a instalação localDB existente. Por padrão, o emulador de armazenamento utiliza o LocalDB para persistir os dados quando simula os serviços de armazenamento Azure. Pode redefinir a sua instância LocalDB executando os seguintes comandos numa janela de comando-pronta antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O comando **de eliminação** remove quaisquer ficheiros de base de dados antigos de instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Você tem um problema diferente com um serviço de armazenamento
Se as secções anteriores de resolução de problemas não incluirem o problema que está a ter com um serviço de armazenamento, deverá adotar a seguinte abordagem para diagnosticar e resolver problemas.

* Verifique as suas métricas para ver se há alguma alteração do seu comportamento esperado na linha de base. A partir das métricas, poderá ser capaz de determinar se o problema é transitório ou permanente e quais as operações de armazenamento que o problema está a afetar.
* Pode utilizar as informações das métricas para o ajudar a pesquisar os dados de registo do lado do servidor para obter informações mais detalhadas sobre quaisquer erros que ocorram. Esta informação pode ajudá-lo a resolver problemas e resolver o problema.
* Se as informações nos registos do servidor não forem suficientes para resolver o problema com sucesso, pode utilizar os registos do lado do cliente da Biblioteca de Clientes de Armazenamento para investigar o comportamento da sua aplicação cliente, e ferramentas como O Fiddler, Wireshark e Microsoft Message Analyzer para investigar a sua rede.

Para obter mais informações sobre a utilização do Violinista, consulte "[Apêndice 1: Utilizar o Violinista para capturar o tráfego HTTP e HTTPS]."

Para obter mais informações sobre a utilização do Wireshark, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego]da rede ."

Para obter mais informações sobre a utilização do Microsoft Message Analyzer, consulte " Apêndice 3: Utilizar o[Microsoft Message Analyzer para capturar]o tráfego da rede ."

## <a name="appendices"></a><a name="appendices"></a>Apêndices
Os apêndices descrevem várias ferramentas que pode achar úteis quando está a diagnosticar e resolver problemas com o Armazenamento Azure (e outros serviços). Estas ferramentas não fazem parte do Armazenamento Azure e algumas são produtos de terceiros. Como tal, as ferramentas discutidas nestes apêndices não estão abrangidas por qualquer acordo de suporte que possa ter com o Microsoft Azure ou o Azure Storage, pelo que, como parte do seu processo de avaliação, deve examinar as opções de licenciamento e suporte disponíveis junto dos fornecedores destas ferramentas.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Apêndice 1: Usar o Violinista para capturar o tráfego HTTP e HTTPS
[O Fiddler](https://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre a aplicação do seu cliente e o serviço de armazenamento Azure que está a utilizar.

> [!NOTE]
> O violinista pode descodificar o tráfego HTTPS; deve ler atentamente a documentação do Fiddler para entender como o faz e compreender as implicações de segurança.
>
>

Este apêndice fornece uma breve passagem de como configurar o Fiddler para capturar o tráfego entre a máquina local onde instalou o Fiddler e os serviços de armazenamento Azure.

Depois de ter lançado o Fiddler, começará a capturar o tráfego HTTP e HTTPS na sua máquina local. Seguem-se alguns comandos úteis para controlar o Violinista:

* Pare e comece a capturar o tráfego. No menu principal, vá ao **File** e clique em **Capture Traffic** para alternar a captura.
* Guarde os dados de tráfego capturados. No menu principal, vá a **File,** clique em **Guardar**, e depois clique em **Todas as Sessões**: isto permite-lhe guardar o tráfego num ficheiro Dearquivo de Sessão. Pode recarregar um Arquivo de Sessão mais tarde para análise, ou enviá-lo se solicitado para o suporte da Microsoft.

Para limitar a quantidade de tráfego que o Fiddler captura, pode utilizar filtros que configura no separador **Filtros.** A imagem seguinte mostra um filtro que captura apenas o tráfego enviado para o ponto final de armazenamento **contosoemaildist.table.core.windows.net:**

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Apêndice 2: Usar o Wireshark para capturar o tráfego da rede
[O Wireshark](https://www.wireshark.org/) é um analisador de protocolo de rede que lhe permite visualizar informações detalhadas sobre pacotes para uma ampla gama de protocolos de rede.

O procedimento seguinte mostra como capturar informações detalhadas sobre o tráfego da máquina local onde instalou o Wireshark no serviço de mesa na sua conta de armazenamento Azure.

1. Lance o Wireshark na sua máquina local.
2. Na secção **Iniciar,** selecione a interface de rede local ou as interfaces que estão ligadas à internet.
3. Clique em **opções de captura**.
4. Adicione um filtro à caixa de texto **do Filtro de Captura.** Por exemplo, **o anfitrião contosoemaildist.table.core.windows.net** configurará o Wireshark para capturar apenas pacotes enviados para ou a partir do ponto final do serviço de mesa na conta de armazenamento **contoso-e-mail..** Confira a [lista completa de Filtros de Captura](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Clique em **Iniciar**. A Wireshark irá agora capturar todos os pacotes enviados de ou para o ponto final do serviço de mesa, uma vez que utiliza a aplicação do seu cliente na sua máquina local.
6. Quando terminar, no menu principal clique em **Capturar** **e,** em seguida, parar .
7. Para guardar os dados capturados num Ficheiro de Captura de Wireshark, no menu principal clique em **File** e, em seguida, **Guardar**.

O WireShark irá destacar quaisquer erros que existam na janela da **lista de pacotes.** Também pode utilizar a janela **Expert Info** (clique em **Analisar,** depois **Expert Info**) para ver um resumo de erros e avisos.

![][7]

Também pode optar por visualizar os dados do TCP à medida que a camada de aplicação os vê clicando corretamente nos dados do TCP e selecionando **o Fluxo de TCP**. Isto é útil se capturar a sua lixeira sem filtro de captura. Para mais informações, consulte [A seguir fluxos de TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Para mais informações sobre a utilização do Wireshark, consulte o Guia de [Utilizadores de Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Apêndice 3: Usar o Microsoft Message Analyzer para capturar o tráfego da rede
Pode utilizar o Microsoft Message Analyzer para capturar o tráfego HTTP e HTTPS de forma semelhante ao Fiddler e capturar o tráfego da rede de forma semelhante à Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configure uma sessão de rastreio web utilizando o Microsoft Message Analyzer
Para configurar uma sessão de rastreio web para o tráfego HTTP e HTTPS utilizando o Microsoft Message Analyzer, execute a aplicação Do Microsoft Message Analyzer e, em seguida, no menu **'Ficheiro',** clique em **Capturar/Rastrear**. Na lista de cenários de vestígios disponíveis, selecione **Web Proxy**. Em seguida, no painel de configuração do **cenário de traço,** na caixa de texto **HostnameFilter,** adicione os nomes dos seus pontos finais de armazenamento (pode procurar estes nomes no [portal Azure).](https://portal.azure.com) Por exemplo, se o nome da sua conta de armazenamento Azure for **contosodata,** deverá adicionar o seguinte à caixa de texto **HostnameFilter:**

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Um personagem espacial separa os nomes de anfitriões.
>
>

Quando estiver pronto para começar a recolher dados de vestígios, clique no botão **Iniciar com o botão.**

Para obter mais informações sobre o rastreio web **proxy** do Microsoft Message Analyzer, consulte [microsoft-PEF-WebProxy Provider](https://technet.microsoft.com/library/jj674814.aspx).

O traço de **proxy web** incorporado no Microsoft Message Analyzer baseia-se no Fiddler; pode capturar o tráfego HTTPS do lado do cliente e exibir mensagens HTTPS não encriptadas. O rastreio **do Web Proxy** funciona configurando um proxy local para todo o tráfego HTTP e HTTPS que lhe dá acesso a mensagens não encriptadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de rede usando o Microsoft Message Analyzer
Além de utilizar o traço web **proxy** do Microsoft Message Analyzer para capturar detalhes do tráfego HTTP/HTTPs entre a aplicação do cliente e o serviço de armazenamento, também pode usar o traço de camada de **link local** incorporado para capturar informações de pacotes de rede. Isto permite-lhe capturar dados semelhantes aos que pode capturar com o Wireshark e diagnosticar problemas de rede como pacotes abandonados.

A imagem que se segue mostra um exemplo de traço de camada de **ligação local** com algumas mensagens **informativas** na coluna **DiagnosisTypes.** Clicar num ícone na coluna **DiagnosticsTypes** mostra os detalhes da mensagem. Neste exemplo, o servidor retransmitiu a mensagem #305 porque não recebeu um reconhecimento do cliente:

![][9]

Quando criar a sessão de rastreio no Microsoft Message Analyzer, pode especificar filtros para reduzir a quantidade de ruído no rastreio. Na página **Capturar / Rastrear** onde define o rastreio, clique no link **Configure** ao lado do **Microsoft-Windows-NDIS-PacketCapture**. A seguinte imagem mostra uma configuração que filtra o tráfego de TCP para os endereços IP de três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreio da camada de link local do Microsoft Message Analyzer, consulte [microsoft-PEF-NDIS-PacketCapture Provider](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Apêndice 4: Utilizar o Excel para visualizar métricas e registar dados
Muitas ferramentas permitem-lhe descarregar os dados das Métricas de Armazenamento do armazenamento da mesa Azure num formato delimitado que facilita a colocação dos dados no Excel para visualização e análise. Armazenamento Dados de registo de armazenamento do armazenamento de blob Azure já estão num formato delimitado que pode carregar no Excel. No entanto, terá de adicionar títulos de coluna apropriados baseados nas informações do [Formato](https://msdn.microsoft.com/library/azure/hh343259.aspx) de Registo de Armazenamento Analytics e da Tabela de [Métricas de Armazenamento Analytics Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar os seus dados de Registo de Armazenamento para Excel depois de os transferir do armazenamento de blob:

* No menu **Dados,** clique **em Texto**.
* Navegue no ficheiro de registo que pretende visualizar e clique em **Importar**.
* No passo 1 do Assistente de Importação de **Texto,** selecione **Delimited**.

No passo 1 do Assistente de Importação de **Texto,** selecione **Semicolon** como o único delimitador e escolha a dupla cotação como **qualificação**para texto . Em seguida, clique em **Terminar** e escolha onde colocar os dados no seu livro de trabalho.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Apêndice 5: Monitorização com Insights de Aplicação para DevOps Azure
Também pode utilizar a funcionalidade Application Insights para O Azure DevOps como parte do seu desempenho e monitorização de disponibilidade. Esta ferramenta pode:

* Certifique-se de que o seu serviço web está disponível e reativo. Quer a sua aplicação seja um web site ou uma aplicação de dispositivoque utilize um serviço web, pode testar o seu URL a cada poucos minutos de locais de todo o mundo, e informá-lo se há algum problema.
* Diagnostice rapidamente quaisquer problemas de desempenho ou exceções no seu serviço web. Descubra se cpU ou outros recursos estão sendo esticados, obtenha vestígios de pilhas de exceções e facilmente procure através de vestígios de registo. Se o desempenho da aplicação descer abaixo dos limites aceitáveis, a Microsoft pode enviar-lhe um e-mail. Pode monitorizar os serviços web .NET e Java.

Pode encontrar mais informações no [What is Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre analítica no Armazenamento Azure, consulte estes recursos:

* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Análise de armazenamento](storage-analytics.md)
* [Métricas de análise de armazenamento](storage-analytics-metrics.md)
* [Esquema de mesa de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Registos de análise de armazenamento](storage-analytics-logging.md)
* [Formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introdução]: #introduction
[Como este guia é organizado]: #how-this-guide-is-organized

[Monitorização do seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorização da saúde do serviço]: #monitoring-service-health
[Capacidade de monitorização]: #monitoring-capacity
[Monitorização da disponibilidade]: #monitoring-availability
[Desempenho de monitorização]: #monitoring-performance

[Diagnóstico de problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de saúde do serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnóstico de erros]: #diagnosing-errors
[Problemas do emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de exploração madeireira de armazenamento]: #storage-logging-tools
[Utilização de ferramentas de registo de rede]: #using-network-logging-tools

[Rastreio de ponta a ponta]: #end-to-end-tracing
[Correlacionar dados de registo]: #correlating-log-data
[ID de pedido de cliente]: #client-request-id
[ID de pedido do servidor]: #server-request-id
[Carimbos de Data/Hora]: #timestamps

[Orientação de resolução de problemas]: #troubleshooting-guidance
[As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Ocorrem atrasos inesperados na entrega de mensagens numa fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[As métricas apresentam um aumento do PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento transitório no PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente do erro do Erro PercentThrottling Error]: #permanent-increase-in-PercentThrottlingError
[As métricas apresentam um aumento do PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[As métricas apresentam um aumento do PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está a receber mensagens de HTTP 403 (Proibido)]: #the-client-is-receiving-403-messages
[O cliente está a receber mensagens de HTTP 404 (Não encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo eliminou anteriormente o objeto]: #client-previously-deleted-the-object
[Um problema de autorização da Assinatura de Acesso Partilhado (SAS)]: #SAS-authorization-issue
[O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (Conflito)]: #the-client-is-receiving-409-messages

[As métricas mostram que as entradas de registo low PercentSuccess ou analytics têm operações com o estado de transação do ClientOtherErrors]: #metrics-show-low-percent-success
[As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[A funcionalidade "X" não está a funcionar no emulador de armazenamento]: #feature-X-is-not-working
[Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[Executar o emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Encontra problemas na instalação do SDK Azure para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Você tem um problema diferente com um serviço de armazenamento]: #you-have-a-different-issue-with-a-storage-service

[Apêndices]: #appendices
[Apêndice 1: Usar o Violinista para capturar o tráfego HTTP e HTTPS]: #appendix-1
[Apêndice 2: Usar o Wireshark para capturar o tráfego da rede]: #appendix-2
[Apêndice 3: Usar o Microsoft Message Analyzer para capturar o tráfego da rede]: #appendix-3
[Apêndice 4: Utilizar o Excel para visualizar métricas e registar dados]: #appendix-4
[Apêndice 5: Monitorização com Insights de Aplicação para DevOps Azure]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
