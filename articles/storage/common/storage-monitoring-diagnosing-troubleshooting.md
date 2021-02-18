---
title: Monitor, diagnóstico e resolução de problemas Azure Storage | Microsoft Docs
description: Use funcionalidades como análise de armazenamento, registo de registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f68452bed69e4e174f126d259e8df6d2283e6cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585214"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição Geral
Diagnosticar e resolver problemas numa aplicação distribuída hospedada num ambiente em nuvem pode ser mais complexo do que em ambientes tradicionais. As aplicações podem ser implantadas numa infraestrutura PaaS ou IaaS, nas instalações, num dispositivo móvel ou em alguma combinação destes ambientes. Normalmente, o tráfego de rede da sua aplicação pode atravessar redes públicas e privadas e a sua aplicação pode utilizar várias tecnologias de armazenamento, tais como Tabelas de Armazenamento do Microsoft Azure, Blobs, Filas ou Ficheiros, além de outras lojas de dados, como bases de dados relacionais e documentos.

Para gerir estas aplicações com sucesso, deve monitorizá-las proativamente e entender como diagnosticar e resolver problemas de todos os aspetos das mesmos e das suas tecnologias dependentes. Como utilizador dos serviços de Armazenamento Azure, deve monitorizar continuamente os serviços de Armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas de comportamento (por exemplo, mais lentas do que os tempos de resposta habituais), e utilizar o registo para recolher dados mais detalhados e analisar um problema em profundidade. As informações de diagnóstico que obtém tanto da monitorização como do registo registado irão ajudá-lo a determinar a causa principal do problema que a sua aplicação encontrou. Em seguida, pode resolver o problema e determinar os passos adequados que pode tomar para remediar o problema. O Azure Storage é um serviço Azure core, e constitui uma parte importante da maioria das soluções que os clientes implementam para a infraestrutura Azure. O Azure Storage inclui capacidades para simplificar problemas de monitorização, diagnóstico e resolução de problemas nas suas aplicações baseadas na nuvem.

* [Introdução]
  * [Como este guia é organizado]
* [Monitorização do seu serviço de armazenamento]
  * [Monitorização da saúde do serviço]
  * [Capacidade de monitorização]
  * [Monitorizar a disponibilidade]
  * [Monitorizar o desempenho]
* [Diagnóstico de problemas de armazenamento]
  * [Problemas de saúde de serviço]
  * [Problemas de desempenho]
  * [Erros de diagnóstico]
  * [Problemas de emulador de armazenamento]
  * [Ferramentas de registo de armazenamento]
  * [Utilização de ferramentas de registo de rede]
* [Rastreio de ponta a ponta]
  * [Correlacionar dados de registo]
  * [ID de pedido de cliente]
  * [ID de pedido de servidor]
  * [Carimbos de Data/Hora]
* [Orientação para a resolução de problemas]
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
  * [As métricas mostram que as entradas de registo de percentsuccess ou de registo de análise têm operações com o estado de transação dos ClientOtherErrors]
  * [As métricas da capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]
  * [O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]
  * [Está a ter problemas em instalar o Azure SDK para .NET]
  * [Você tem um problema diferente com um serviço de armazenamento]
  * [VHDs de resolução de problemas em máquinas virtuais do Windows](../../virtual-machines/troubleshooting/index.yml)   
  * [VHDs de resolução de problemas em máquinas virtuais Linux](../../virtual-machines/troubleshooting/index.yml)
  * [Problemas de resolução de problemas com ficheiros Azure com o Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Problemas na resolução de problemas dos Ficheiros Azure com o Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Apêndices]
  * [Apêndice 1: Utilização do Violinista para capturar tráfego HTTP e HTTPS]
  * [Apêndice 2: Utilização de arame para capturar tráfego de rede]
  * [Apêndice 4: Usar o Excel para visualizar métricas e registar dados]
  * [Apêndice 5: Monitorização com insights de aplicações para devOps Azure]

## <a name="introduction"></a><a name="introduction"></a>Introdução
Este guia mostra-lhe como utilizar funcionalidades como a Azure Storage Analytics, o registo do lado do cliente na Biblioteca do Cliente de Armazenamento Azure e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure.

![Diagrama que mostra o fluxo de informação entre aplicações de clientes e serviços de armazenamento Azure.][1]

Este guia destina-se a ser lido principalmente por desenvolvedores de serviços online que utilizam os Serviços de Armazenamento Azure e OS PROFISSIONAis de TI responsáveis pela gestão desses serviços online. Os objetivos deste guia são:

* Para ajudá-lo a manter a saúde e o desempenho das suas contas de Armazenamento Azure.
* Para lhe fornecer os processos e ferramentas necessários para ajudá-lo a decidir se um problema ou problema numa aplicação está relacionado com o Azure Storage.
* Para lhe fornecer orientações acccionáveis para resolver problemas relacionados com o Armazenamento Azure.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Como este guia é organizado
A secção "[Monitorizar o seu serviço de armazenamento]" descreve como monitorizar a saúde e o desempenho dos seus serviços de armazenamento Azure utilizando métricas de análise de armazenamento Azure (Métricas de Armazenamento).

A secção "[Diagnosticar problemas de armazenamento]" descreve como diagnosticar problemas usando Azure Storage Analytics Logging (Registo de Armazenamento). Também descreve como permitir a exploração madeireira do lado do cliente utilizando as instalações de uma das bibliotecas do cliente, como a Biblioteca do Cliente de Armazenamento para .NET ou o Azure SDK para Java.

A secção "[Rastreio de ponta a ponta]" descreve como pode correlacionar as informações contidas em vários ficheiros de registo e dados de métricas.

A secção "[Orientação de resolução de]problemas " fornece orientação de resolução de problemas para algumas das questões comuns relacionadas com o armazenamento que você pode encontrar.

Os "[Apêndices]" incluem informações sobre a utilização de outras ferramentas, tais como Wireshark e Netmon para analisar dados de pacotes de rede, e Fiddler para analisar mensagens HTTP/HTTPS.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Monitorização do seu serviço de armazenamento
Se estiver familiarizado com a monitorização de desempenho do Windows, pode pensar nas Métricas de Armazenamento como sendo um equivalente a Azure Storage dos contadores Windows Performance Monitor. Nas Métricas de Armazenamento, encontrará um conjunto abrangente de métricas (contadores na terminologia do Windows Performance Monitor) como a disponibilidade do serviço, o número total de pedidos de serviço ou a percentagem de pedidos de serviço bem sucedidos. Para obter uma lista completa das métricas disponíveis, consulte [o Esquema de Tabela de Métricas de Armazenamento Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Pode especificar se deseja que o serviço de armazenamento recolha e agrega as métricas a cada hora ou a cada minuto. Para obter mais informações sobre como ativar métricas e monitorizar as suas contas de armazenamento, consulte [Ativar as métricas de armazenamento e visualizar dados de métricas](../blobs/monitor-blob-storage.md).

Pode escolher quais as métricas horárias que pretende apresentar no [portal Azure](https://portal.azure.com) e configurar regras que notificam os administradores por e-mail sempre que uma métrica de hora em hora excede um determinado limiar. Para mais informações, consulte [receber notificações de alerta.](../../azure-monitor/alerts/alerts-overview.md)

Recomendamos que reveja [o Monitor Azure para armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (pré-visualização). É uma funcionalidade do Azure Monitor que oferece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de armazenamento Azure. Não requer que você ative ou configuure nada, e você pode imediatamente ver estas métricas a partir dos gráficos interativos pré-definidos e outras visualizações incluídas.

O serviço de armazenamento recolhe métricas usando um melhor esforço, mas pode não registar todas as operaçãos de armazenamento.

No portal Azure, pode ver métricas como disponibilidade, pedidos totais e números médios de latência para uma conta de armazenamento. Foi também criada uma regra de notificação para alertar um administrador se a disponibilidade cair abaixo de um determinado nível. Ao visualizar estes dados, uma área possível para investigação é a percentagem de sucesso do serviço de mesa abaixo de 100% (para mais informações, consulte a secção "[Métricas mostram baixas percentsuccess ou entradas de registo de análise têm operações com o estado de transação de ClientOtherErrors]").

Deve monitorizar continuamente as suas aplicações Azure para garantir que estão saudáveis e a funcionar como esperado por:

* Estabelecer algumas métricas de base para aplicação que lhe permitirão comparar os dados atuais e identificar quaisquer alterações significativas no comportamento do armazenamento Azure e na sua aplicação. Os valores das suas métricas de base serão, em muitos casos, específicos da aplicação e deverá estabelecer-os quando estiver a testar a sua aplicação.
* Registar métricas minúsculas e usá-las para monitorizar ativamente para erros e anomalias inesperados, tais como picos em contagens de erros ou taxas de pedido.
* Registar métricas horárias e usá-las para monitorizar valores médios, como contagens médias de erros e taxas de pedido.
* Investigar potenciais problemas usando ferramentas de diagnóstico como discutido mais tarde na secção "[Diagnosticar questões de armazenamento]."

Os gráficos na imagem a seguir ilustram como a média que ocorre para métricas de hora a hora pode esconder picos na atividade. As métricas horárias parecem mostrar uma taxa constante de pedidos, enquanto as métricas minúsculas revelam as flutuações que estão realmente acontecendo.

![Gráficos que mostram como a média que ocorre para métricas de hora a hora pode esconder picos na atividade.][3]

O restante desta secção descreve quais as métricas que deve monitorizar e porquê.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Monitorização da saúde do serviço
Você pode usar o [portal Azure](https://portal.azure.com) para ver a saúde do serviço de Armazenamento (e outros serviços Azure) em todas as regiões do Azure em todo o mundo. A monitorização permite-lhe ver imediatamente se um problema fora do seu controlo está a afetar o serviço de Armazenamento na região que utiliza para a sua aplicação.

O [portal Azure](https://portal.azure.com) também pode fornecer notificações de incidentes que afetam os vários serviços Azure.
Nota: Esta informação estava anteriormente disponível, juntamente com dados históricos, no [Painel de Instrumentos de Serviço Azure](https://status.azure.com).
Para obter mais informações sobre insights de aplicações para Azure DevOps, consulte o apêndice "[Apêndice 5: Monitorização com Insights de Aplicações para Azure DevOps](#appendix-5)."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Capacidade de monitorização
As métricas de armazenamento apenas armazenam métricas de capacidade para o serviço blob porque as bolhas normalmente representam a maior proporção de dados armazenados (no momento da escrita, não é possível utilizar Métricas de Armazenamento para monitorizar a capacidade das suas mesas e filas). Pode encontrar estes dados na tabela **$MetricsCapacityBlob** se tiver ativado a monitorização do serviço Blob. As Métricas de Armazenamento registam estes dados uma vez por dia, e pode utilizar o valor do **RowKey** para determinar se a linha contém uma entidade que se relaciona com os dados do utilizador **(dados** de valor) ou dados de análise **(análise** de valor). Cada entidade armazenada contém informações sobre a quantidade de armazenamento utilizada **(Capacidade** medida em bytes) e o número atual de contentores **(Contentores)** e bolhas **(ObjectCount)** em uso na conta de armazenamento. Para obter mais informações sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob,** consulte [o Esquema de Tabela de Métricas de Armazenamento Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Deve monitorizar estes valores para um aviso precoce de que está a aproximar-se dos limites de capacidade da sua conta de armazenamento. No portal Azure, pode adicionar regras de alerta para o notificar se o uso agregado de armazenamento exceder ou ficar abaixo dos limiares especificados.
>
>

Para ajudar a estimar o tamanho de vários objetos de armazenamento, tais como bolhas, consulte o post do blog [Understanding Azure Storage Billing – Bandwidth, Transactions e Capacity](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Monitorizar a disponibilidade
Deverá monitorizar a disponibilidade dos serviços de armazenamento na sua conta de armazenamento, monitorizando o valor na coluna **Disponibilidade** nas tabelas de métricas horárias ou minúsculas — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable,** **$MetricsHourPrimaryTransactionsQueue,** **$MetricsMinutePrimaryTransactionsBlob,** **$MetricsMinutePrimaryTransactionsTable,** **$MetricsMinutePrimaryTransactionsQueue,** **$MetricsCapacityBlob**. A coluna **Disponibilidade** contém um valor percentual que indica a disponibilidade do serviço ou da operação API representada pela linha (o **RowKey** mostra se a linha contém métricas para o serviço como um todo ou para uma operação API específica).

Qualquer valor inferior a 100% indica que alguns pedidos de armazenamento estão a falhar. Pode ver por que estão falhando examinando as outras colunas nos dados das métricas que mostram o número de pedidos com diferentes tipos de erro, tais como **ServerTimeoutError**. Deverá esperar que a **Disponibilidade** caia temporariamente abaixo dos 100% por razões como intervalos transitórios do servidor, enquanto o serviço move divisórias para um melhor pedido de equilíbrio de carga; a lógica de recandidatura na sua aplicação ao cliente deve lidar com tais condições intermitentes. O artigo [Armazenamento Analytics Operações e Mensagens de Estado](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) lista os tipos de transação que as Métricas de Armazenamento incluem no seu cálculo de **Disponibilidade.**

No [portal Azure,](https://portal.azure.com)pode adicionar regras de alerta para o notificar se **a Disponibilidade** de um serviço ficar abaixo de um limiar que especifique.

A secção "[Orientação de Resolução de Problemas]" deste guia descreve algumas questões comuns do serviço de armazenamento relacionadas com a disponibilidade.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Monitorizar o desempenho
Para monitorizar o desempenho dos serviços de armazenamento, pode utilizar as seguintes métricas a partir das tabelas de métricas de hora e minuto.

* Os valores nas colunas **AverageEE2ELatency** e **AverageServerLatency** mostram o tempo médio que o serviço de armazenamento ou o tipo de operação API estão a levar para processar pedidos. **A Média E2ELatency** é uma medida de latência de ponta a ponta que inclui o tempo de leitura do pedido e enviar a resposta para além do tempo necessário para processar o pedido (portanto, inclui a latência da rede uma vez que o pedido chega ao serviço de armazenamento); **A AverageServerLatency** é uma medida apenas do tempo de processamento e, portanto, exclui qualquer latência da rede relacionada com a comunicação com o cliente. Consulte a secção "[Metrics show high AverageE2ELatency and low AverageServerLatency]" mais tarde neste guia para uma discussão sobre por que pode haver uma diferença significativa entre estes dois valores.
* Os valores nas **colunas TotalIngress** e **TotalEgress** mostram a quantidade total de dados, em bytes, entrando e saindo do seu serviço de armazenamento ou através de um tipo específico de operação API.
* Os valores na coluna **TotalRequests** mostram o número total de pedidos que o serviço de armazenamento da operação API está a receber. **TotalRequests** é o número total de pedidos que o serviço de armazenamento recebe.

Normalmente, irá monitorizar alterações inesperadas em qualquer um destes valores como um indicador de que tem um problema que requer investigação.

No [portal Azure,](https://portal.azure.com)pode adicionar regras de alerta para o notificar se alguma das métricas de desempenho deste serviço ficar abaixo ou exceder um limiar que especifique.

A secção "[Orientação de Resolução de Problemas]" deste guia descreve algumas questões comuns de serviço de armazenamento relacionadas com o desempenho.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnóstico de problemas de armazenamento
Há várias formas de tomar conhecimento de um problema ou problema na sua aplicação, incluindo:

* Uma grande falha que faz com que a aplicação se despenhe ou deixe de funcionar.
* Alterações significativas dos valores de base nas métricas que está a monitorizar, conforme descrito na secção anterior "[Monitorização]do seu serviço de armazenamento ."
* Relatos de utilizadores da sua aplicação que alguma operação em particular não concluiu como esperado ou que alguma funcionalidade não está a funcionar.
* Erros gerados dentro da sua aplicação que aparecem em ficheiros de registo ou através de algum outro método de notificação.

Normalmente, as questões relacionadas com os serviços de armazenamento Azure enquadram-se numa das quatro grandes categorias:

* A sua aplicação tem um problema de desempenho, relatado pelos seus utilizadores, ou revelado por alterações nas métricas de desempenho.
* Existe um problema com a infraestrutura de armazenamento Azure numa ou mais regiões.
* A sua aplicação encontra-se a encontrar um erro, relatado pelos seus utilizadores, ou revelado por um aumento de uma das métricas de contagem de erros que monitoriza.
* Durante o desenvolvimento e teste, poderá utilizar o emulador de armazenamento local; pode encontrar alguns problemas que se relacionam especificamente com o uso do emulador de armazenamento.

As secções seguintes descrevem os passos que deve seguir para diagnosticar e resolver problemas em cada uma destas quatro categorias. A secção "[Orientação de resolução de problemas]" mais tarde neste guia fornece mais detalhes para algumas questões comuns que você pode encontrar.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problemas de saúde de serviço
Os problemas de saúde do serviço estão normalmente fora do seu controlo. O [portal Azure](https://portal.azure.com) fornece informações sobre quaisquer problemas em curso com os serviços Azure, incluindo serviços de armazenamento. Se optou por Read-Access Geo-Redundant Armazenamento quando criou a sua conta de armazenamento, então se os seus dados não se tornarem disponíveis na localização primária, a sua aplicação pode mudar temporariamente para a cópia apenas de leitura no local secundário. Para ler a partir do secundário, a sua aplicação deve ser capaz de alternar entre usar os locais de armazenamento primário e secundário e ser capaz de trabalhar num modo de funcionalidade reduzido com apenas dados de leitura. As bibliotecas do Cliente de Armazenamento Azure permitem definir uma política de reação que pode ler a partir do armazenamento secundário no caso de uma leitura do armazenamento primário falhar. A sua aplicação também precisa de estar ciente de que os dados na localização secundária são eventualmente consistentes. Para obter mais informações, consulte o blog post [Azure Storage Despedimento Opções e Leia Acesso Geo Redundante Armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problemas de desempenho
O desempenho de uma aplicação pode ser subjetivo, sobretudo na perspetiva do utilizador. Por conseguinte, é importante ter métricas de linha base disponíveis para o ajudar a identificar onde poderá existir um problema de desempenho. Muitos fatores podem afetar o desempenho de um serviço de armazenamento Azure do ponto de vista da aplicação do cliente. Estes fatores podem funcionar no serviço de armazenamento, no cliente ou na infraestrutura de rede; por conseguinte, é importante ter uma estratégia para identificar a origem da questão do desempenho.

Depois de ter identificado a localização provável da causa do problema de desempenho a partir das métricas, pode então utilizar os ficheiros de registo para encontrar informações detalhadas para diagnosticar e resolver o problema ainda mais.

A secção "[Orientação de resolução de problemas]" mais tarde neste guia fornece mais informações sobre algumas questões comuns relacionadas com o desempenho que você pode encontrar.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Erros de diagnóstico
Os utilizadores da sua aplicação podem notificá-lo de erros reportados pela aplicação do cliente. As Métricas de Armazenamento também registam contagens de diferentes tipos de erros dos seus serviços de armazenamento, tais como **NetworkError,** **ClientTimeoutError,** ou **AuthorizationError**. Embora as Métricas de Armazenamento apenas regissugem as contagens de diferentes tipos de erros, pode obter mais detalhes sobre pedidos individuais examinando registos do lado do servidor, do lado do cliente e da rede. Normalmente, o código de estado HTTP devolvido pelo serviço de armazenamento dará uma indicação do porquê do pedido ter falhado.

> [!NOTE]
> Lembre-se que deve esperar ver alguns erros intermitentes: por exemplo, erros devido a condições de rede transitórias ou erros de aplicação.
>
>

Os recursos seguintes são úteis para compreender os códigos de estado e erro relacionados com o armazenamento:

* [Códigos de erro comuns da API REST](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Códigos de erro de serviço blob](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Códigos de erro de serviço de fila](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Códigos de erro de serviço de tabela](/rest/api/storageservices/Table-Service-Error-Codes)
* [Códigos de erro do serviço de ficheiros](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento
O Azure SDK inclui um emulador de armazenamento que você pode executar em uma estação de trabalho de desenvolvimento. Este emulador simula a maior parte do comportamento dos serviços de armazenamento Azure e é útil durante o desenvolvimento e teste, permitindo-lhe executar aplicações que utilizem serviços de armazenamento Azure sem a necessidade de uma subscrição Azure e uma conta de armazenamento Azure.

A secção "[Orientação de Resolução de Problemas]" deste guia descreve algumas questões comuns encontradas usando o emulador de armazenamento.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Ferramentas de registo de armazenamento
O Registo de Armazenamento fornece o registo de pedidos de armazenamento do lado do servidor na sua conta de armazenamento Azure. Para obter mais informações sobre como ativar o registo do lado do servidor e aceder aos dados de registo, consulte Ativar o [registo de armazenamento e aceder aos dados do registo](./storage-analytics-logging.md).

A Biblioteca do Cliente de Armazenamento para .NET permite-lhe recolher dados de registo do lado do cliente que se relacionam com as operações de armazenamento realizadas pela sua aplicação. Para obter mais informações, veja [Client-side Logging with the .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library) (Registo do Lado do Cliente com a Biblioteca de Clientes de Armazenamento .NET).

> [!NOTE]
> Em algumas circunstâncias (tais como falhas de autorização SAS), um utilizador pode reportar um erro para o qual não pode encontrar dados de pedido nos registos de armazenamento do lado do servidor. Pode utilizar as capacidades de registo da Biblioteca do Cliente de Armazenamento para investigar se a causa do problema está no cliente ou utilizar ferramentas de monitorização de rede para investigar a rede.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Utilização de ferramentas de registo de rede
Pode capturar o tráfego entre o cliente e o servidor para fornecer informações detalhadas sobre os dados que o cliente e o servidor estão a trocar e as condições de rede subjacentes. As ferramentas úteis de registo de rede incluem:

* [O Fiddler](https://www.telerik.com/fiddler) é um proxy de depurador de web gratuito que lhe permite examinar os cabeçalhos e dados de carga útil de mensagens de pedido e resposta HTTPS e HTTPS. Para obter mais informações, consulte [o Apêndice 1: Utilizar o violinista para capturar o tráfego HTTP e HTTPS](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://download.cnet.com/s/network-monitor/) e [Wireshark](https://www.wireshark.org/) são analisadores de protocolo de rede gratuitos que lhe permitem visualizar informações detalhadas de pacotes para uma ampla gama de protocolos de rede. Para obter mais informações sobre o Wireshark, consulte "[Apêndice 2: Utilização de Arame para capturar o tráfego da rede](#appendix-2)".
* Se pretender realizar um teste básico de conectividade para verificar se a sua máquina cliente pode ligar-se ao serviço de armazenamento Azure através da rede, não poderá fazê-lo utilizando a ferramenta de **ping** padrão do cliente. No entanto, pode utilizar a ferramenta [ **tcping**](https://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de registo de registo de armazenamento e a Biblioteca do Cliente de Armazenamento serão suficientes para diagnosticar um problema, mas em alguns cenários, poderá necessitar da informação mais detalhada que estas ferramentas de registo de rede podem fornecer. Por exemplo, a utilização de mensagens HTTP e HTTPS permite-lhe visualizar dados de cabeçalho e carga útil enviados de e para os serviços de armazenamento, o que lhe permitiria examinar como uma aplicação de clientes retrigem operações de armazenamento. Os analisadores de protocolos, como o Wireshark, operam ao nível do pacote, permitindo-lhe visualizar dados de TCP, o que lhe permitiria resolver os problemas perdidos e problemas de conectividade. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Rastreio de ponta a ponta
O rastreio de ponta a ponta usando uma variedade de ficheiros de registo é uma técnica útil para investigar potenciais problemas. Pode utilizar as informações de data/hora a partir dos dados das suas métricas como uma indicação de onde começar a procurar nos ficheiros de registo para obter informações detalhadas que o ajudarão a resolver o problema.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Correlacionar dados de registo
Ao visualizar registos de aplicações de clientes, vestígios de rede e registo de armazenamento do lado do servidor, é fundamental que seja possível correlacionar pedidos nos diferentes ficheiros de registo. Os ficheiros de registo incluem uma série de campos diferentes que são úteis como identificadores de correlação. O ID de pedido do cliente é o campo mais útil para se utilizar para correlacionar entradas nos diferentes registos. No entanto, por vezes, pode ser útil usar o ID do pedido do servidor ou os timetamps. As seguintes secções fornecem mais detalhes sobre estas opções.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID de pedido de cliente
A Biblioteca do Cliente de Armazenamento gera automaticamente um ID de pedido único do cliente para cada pedido.

* No registo do lado do cliente que a Biblioteca do Cliente de Armazenamento cria, o ID de pedido do cliente aparece no campo **ID** do Pedido de Cliente de cada entrada de registo relativa ao pedido.
* Num traço de rede como o capturado pelo Fiddler, o ID de pedido do cliente é visível nas mensagens de pedido como o valor do cabeçalho HTTP **x-ms-cliente-pedido..**
* No registo de registo de armazenamento do lado do servidor, o ID de pedido do cliente aparece na coluna de ID do pedido do Cliente.

> [!NOTE]
> É possível que vários pedidos partilhem o mesmo ID de pedido do cliente porque o cliente pode atribuir este valor (embora a Biblioteca do Cliente de Armazenamento atribua automaticamente um novo valor). Quando o cliente re-tenta, todas as tentativas partilham a mesma identificação de pedido de cliente. No caso de um lote enviado do cliente, o lote tem uma única identificação de pedido de cliente.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID de pedido de servidor
O serviço de armazenamento gera automaticamente iDs de pedido de servidor.

* No registo de registo de registo de armazenamento do lado do servidor, o ID do pedido do servidor aparece na coluna **do cabeçalho do ID do pedido.**
* Num traço de rede como um capturado pelo Fiddler, o ID de pedido do servidor aparece em mensagens de resposta como o valor do cabeçalho HTTP **x-ms-pedido.**
* No registo do lado do cliente que a Biblioteca do Cliente de Armazenamento cria, o ID de pedido do servidor aparece na coluna **'Texto de Operação'** para a entrada de registo mostrando detalhes da resposta do servidor.

> [!NOTE]
> O serviço de armazenamento atribui sempre um ID de pedido de servidor único a cada pedido que recebe, pelo que cada tentativa de tentativa de repetição do cliente e cada operação incluída num lote tem um ID de pedido de servidor único.
>
>

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

A amostra de código abaixo demonstra como usar um ID de pedido de cliente personalizado. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Se a Biblioteca do Cliente de Armazenamento lançar uma **StorageException** no cliente, a propriedade **RequestInformation** contém um objeto **RequestResult** que inclui uma propriedade **ServiceRequestID.** Também pode aceder a um objeto **RequestResult a** partir de uma instância **OperaçãoContexto.**

A amostra de código abaixo demonstra como definir um valor personalizado **do ClientRequestId** anexando um objeto **OperationContext** o pedido ao serviço de armazenamento. Também mostra como recuperar o valor **do ServerRequestId** a partir da mensagem de resposta.

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

---

### <a name="timestamps"></a><a name="timestamps"></a>Carimbos de Data/Hora
Também pode utilizar os relógios para localizar entradas de registos relacionados, mas tenha cuidado com qualquer distorção do relógio entre o cliente e o servidor que possa existir. Procure mais ou menos 15 minutos para combinar as entradas do lado do servidor com base no tempotamp no cliente. Lembre-se que os metadados blob para as métricas que contêm métricas indicam o intervalo de tempo para as métricas armazenadas na bolha. Este intervalo de tempo é útil se tiver muitas métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Orientação para a resolução de problemas
Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que a sua aplicação pode encontrar ao utilizar os serviços de armazenamento Azure. Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Árvore de decisão de resolução de problemas**

---
A sua questão diz respeito ao desempenho de um dos serviços de armazenamento?

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
 A sua aplicação de cliente está a receber uma resposta HTTP 4XX (tal como 404) de um serviço de armazenamento?

* [O cliente está a receber mensagens de HTTP 403 (Proibido)]
* [O cliente está a receber mensagens de HTTP 404 (Não encontrado)]
* [O cliente está a receber mensagens de HTTP 409 (Conflito)]

---
[As métricas mostram que as entradas de registo de percentsuccess ou de registo de análise têm operações com o estado de transação dos ClientOtherErrors]

---
[As métricas da capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]

---
[Você está experimentando reboots inesperados de máquinas virtuais que têm um grande número de VHDs anexados]

---
[O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]

---
[Está a ter problemas em instalar o Azure SDK para .NET]

---
[Você tem um problema diferente com um serviço de armazenamento]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa
A ilustração abaixo da ferramenta de monitorização do [portal Azure](https://portal.azure.com) mostra um exemplo em que a **média de E2ELatency** é significativamente superior à **MédiaServerLatency**.

![Ilustração do portal Azure que mostra um exemplo onde a Média E2ELatency é significativamente maior do que a AverageServerLatency.][4]

O serviço de armazenamento apenas calcula a métrica **AverageE2ELatency** para pedidos bem sucedidos e, ao contrário da **AverageServerLatency,** inclui o tempo que o cliente demora a enviar os dados e receber o reconhecimento do serviço de armazenamento. Portanto, uma diferença entre **AverageE2ELatency** e **AverageServerLatency** pode ser devido à lentidão da aplicação do cliente para responder, ou devido às condições da rede.

> [!NOTE]
> Também pode ver **E2ELatency** e **ServerLatency** para operações individuais de armazenamento nos dados de registo de registo de registo de armazenamento.
>
>

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente
As possíveis razões para o cliente responder lentamente incluem ter um número limitado de ligações ou fios disponíveis, ou estar com poucos recursos como CPU, memória ou largura de banda de rede. Pode ser capaz de resolver o problema modificando o código do cliente para ser mais eficiente (por exemplo, utilizando chamadas assíncronos para o serviço de armazenamento), ou utilizando uma Máquina Virtual maior (com mais núcleos e mais memória).

Para os serviços de mesa e fila, o algoritmo Nagle também pode causar uma alta **média de E2ELatency** em comparação com **a AverageServerLatency**: para mais informações, consulte o algoritmo do post [Nagle não é amigável para pedidos pequenos.](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests) Pode desativar o algoritmo Nagle em código utilizando a classe **ServicePointManager** no espaço de nome **System.Net.** Deverá fazê-lo antes de efetivar quaisquer chamadas para a mesa ou serviços de fila na sua aplicação, uma vez que isso não afeta as ligações que já estão abertas. O exemplo a seguir vem do **método Application_Start** numa função de trabalhador.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Deverá verificar os registos do lado do cliente para ver quantos pedidos o seu pedido de cliente está a apresentar e verificar se existem estrangulamentos de desempenho relacionados com a NET no seu cliente, tais como CPU, recolha de lixo .NET, utilização da rede ou memória. Como ponto de partida para a resolução de aplicações de clientes .NET, consulte [Debugging, Tracing e Profiling](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência da rede
Tipicamente, a latência de ponta a ponta causada pela rede deve-se a condições transitórias. Pode investigar problemas de rede transitórios e persistentes, tais como pacotes abandonados utilizando ferramentas como a Wireshark.

Para obter mais informações sobre a utilização da Wireshark para resolver problemas de rede, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego da rede]."

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada
Neste cenário, a causa mais provável é um atraso nos pedidos de armazenamento que chegam ao serviço de armazenamento. Você deve investigar por que os pedidos do cliente não estão passando para o serviço blob.

Uma razão possível para o cliente atrasar os pedidos de envio é que há um número limitado de ligações ou fios disponíveis.

Verifique também se o cliente está a realizar várias retretes, e investigue a razão se é. Para determinar se o cliente está a realizar várias retrógios, pode:

* Examine os registos de Storage Analytics. Se várias retrações estiverem acontecendo, você verá várias operações com o mesmo ID de pedido do cliente, mas com diferentes IDs de pedido de servidor.
* Examine os registos dos clientes. A registo verboso indicará que ocorreu uma nova tripagem.
* Depurar o seu código e verificar as propriedades do objeto **OperationContext** associado ao pedido. Se a operação tiver sido novamente experimentada, a propriedade **RequestResults** incluirá vários IDs exclusivos de pedido de servidor. Também pode verificar os horários de início e fim de cada pedido. Para obter mais informações, consulte a amostra de código na secção [ID de pedido do servidor].

Se não houver problemas no cliente, deverá investigar potenciais problemas de rede, tais como a perda de pacotes. Pode utilizar ferramentas como a Wireshark para investigar problemas de rede.

Para obter mais informações sobre a utilização da Wireshark para resolver problemas de rede, consulte "[Apêndice 2: Usar o Wireshark para capturar o tráfego da rede]."

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>As métricas apresentam uma AverageServerLatency alta
No caso de high **AverageServerLatency** para pedidos de descarregamento de bolhas, deve utilizar os registos de registo de armazenamento para ver se há pedidos repetidos para a mesma bolha (ou conjunto de bolhas). Para pedidos de upload de blob, você deve investigar o tamanho do bloco que o cliente está usando (por exemplo, blocos de tamanho inferior a 64 K podem resultar em sobrecargas, a menos que as leituras também estejam em pedaços inferiores a 64 K), e se vários clientes estiverem a carregar blocos para a mesma bolha em paralelo. Deve também verificar as métricas por minuto de picos no número de pedidos que resultam em exceder os objetivos de escala por segundo: também ver "[Metrics show a increase in PercentTimeoutError]."

Se estiver a ver uma **Alta AverageServerLatency** para pedidos de descarregamento de bolhas quando há pedidos repetidos, a mesma bolha ou conjunto de bolhas, então deve considerar a cache destas bolhas usando a Cache Azure ou a Rede de Entrega de Conteúdos Azure (CDN). Para pedidos de upload, pode melhorar a produção utilizando um tamanho de bloco maior. Para consultas às tabelas, também é possível implementar caching do lado do cliente em clientes que realizam as mesmas operações de consulta e onde os dados não mudam frequentemente.

Valores **de Alta MédiaServerLatency** também podem ser um sintoma de tabelas ou consultas mal desenhadas que resultam em operações de digitalização ou que seguem o apêndice/pré-padrão. Para obter mais informações, consulte "[Metrics show a increase in PercentThrottlingError]".

> [!NOTE]
> Pode encontrar aqui uma lista completa de verificação de desempenho: [Microsoft Azure Storage Performance e Escalaability Checklist](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Ocorrem atrasos inesperados na entrega de mensagens numa fila
Se estiver a sentir um atraso entre o momento em que uma aplicação adiciona uma mensagem a uma fila e o tempo que fica disponível para ler a partir da fila, então deve tomar as seguintes medidas para diagnosticar o problema:

* Verifique se a aplicação está a adicionar com sucesso as mensagens à fila. Verifique se a aplicação não está a voltar a tentar o método **AddMessage várias vezes** antes de ser bem sucedida. Os registos da Biblioteca do Cliente de Armazenamento mostrarão quaisquer retrações repetidas de operações de armazenamento.
* Verifique se não há um desvio de relógio entre a função do trabalhador que adiciona a mensagem à fila e a função do trabalhador que lê a mensagem da fila que faz com que pareça que há um atraso no processamento.
* Verifique se o papel do trabalhador que lê as mensagens da fila está a falhar. Se um cliente de fila ligar para o método **GetMessage** mas não responder com um reconhecimento, a mensagem permanecerá invisível na fila até que o período **de invisibilidadeTimeout** expire. Neste ponto, a mensagem torna-se disponível para processamento novamente.
* Verifique se o comprimento da fila está a crescer ao longo do tempo. Isto pode ocorrer se não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros trabalhadores estão colocando na fila. Verifique também as métricas para ver se os pedidos de eliminação estão a falhar e a contagem deques em mensagens, o que pode indicar tentativas falhadas repetidas de apagar a mensagem.
* Examine os registos de registo de armazenamento para quaisquer operações de fila que tenham valores **de E2ELatency** e **ServerLatency** mais elevados do que o habitual.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>As métricas apresentam um aumento do PercentThrottlingError
Os erros de estrangulamento ocorrem quando excede os objetivos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento acelera para garantir que nenhum cliente ou inquilino possa utilizar o serviço em detrimento de outros. Para obter mais informações, consulte [os objetivos de escalabilidade e desempenho das contas de armazenamento padrão](scalability-targets-standard-account.md) para detalhes sobre os alvos de escalabilidade das contas de armazenamento e os objetivos de desempenho das divisórias dentro das contas de armazenamento.

Se a métrica **PercentThrottlingError** mostrar um aumento na percentagem de pedidos que estão a falhar com um erro de estrangulamento, é necessário investigar um de dois cenários:

* [Aumento transitório no PercentThrottlingError]
* [Aumento permanente do erro do PercentThrottlingError]

Um aumento no **PercentThrottlingError** ocorre frequentemente ao mesmo tempo que um aumento do número de pedidos de armazenamento, ou quando está inicialmente a carregar a sua aplicação. Isto também pode manifestar-se no cliente como "503 Server Busy" ou "500 Operation Timeout" mensagens de estado HTTP das operações de armazenamento.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório no PercentThrottlingError
Se estiver a ver picos no valor de **PercentThrottlingError** que coincidem com períodos de alta atividade para a aplicação, implementa uma estratégia de back-off exponencial (não linear) para retrações no seu cliente. As retrações de retrocesso reduzem a carga imediata na partição e ajudam a sua aplicação a suavizar os picos de tráfego. Para obter mais informações sobre como implementar políticas de retagem utilizando a Biblioteca do Cliente de Armazenamento, consulte o espaço de [nomes Microsoft.Azure.Storage.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Pode também ver picos no valor de **PercentThrottlingError** que não coincidem com períodos de alta atividade para a aplicação: a causa mais provável aqui são as divisórias móveis do serviço de armazenamento para melhorar o equilíbrio de carga.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente do erro do PercentThrottlingError
Se estiver a ver um valor consistentemente elevado para **o PercentThrottlingError** na sequência de um aumento permanente dos seus volumes de transação, ou quando estiver a realizar os seus testes iniciais de carga na sua aplicação, então tem de avaliar como a sua aplicação está a utilizar divisórias de armazenamento e se está a aproximar-se dos alvos de escalabilidade de uma conta de armazenamento. Por exemplo, se estiver a ver erros de estrangulamento numa fila (que conta como uma única partição), então deve considerar usar filas adicionais para espalhar as transações através de várias divisórias. Se estiver a ver erros de estrangulamento numa tabela, tem de considerar a utilização de um esquema de partição diferente para difundir as suas transações através de várias divisórias utilizando uma gama mais alargada de valores-chave de partição. Uma das causas comuns deste problema é o anti-padrão pré-end/apêndice onde seleciona a data como chave de partição e, em seguida, todos os dados de um dia específico são escritos para uma partição: sob carga, isto pode resultar num estrangulamento de escrita. Considere um design de partição diferente ou avalie se o armazenamento de bolhas pode ser uma solução melhor. Verifique também se o estrangulamento está a ocorrer como resultado de picos no seu tráfego e investigue formas de suavizar o seu padrão de pedidos.

Se distribuir as suas transações por várias divisórias, ainda deve estar ciente dos limites de escalabilidade definidos para a conta de armazenamento. Por exemplo, se utilizar dez filas cada, processando o máximo de 2.000 mensagens 1KB por segundo, estará no limite total de 20.000 mensagens por segundo para a conta de armazenamento. Se precisar de processar mais de 20.000 entidades por segundo, deve considerar a utilização de várias contas de armazenamento. Deve também ter em conta que a dimensão dos seus pedidos e entidades tem impacto quando o serviço de armazenamento acelera os seus clientes: se tiver pedidos e entidades maiores, poderá ser estrangulado mais cedo.

O design de consulta ineficiente também pode fazer com que você atinja os limites de escalabilidade para divisórias de mesa. Por exemplo, uma consulta com um filtro que apenas seleciona um por cento das entidades numa partição mas que digitaliza todas as entidades numa partição terá de aceder a cada entidade. Todas as entidades lidas contarão para o número total de transações nessa partição; portanto, pode facilmente alcançar os alvos de escalabilidade.

> [!NOTE]
> O seu teste de desempenho deve revelar quaisquer designs de consulta ineficientes na sua aplicação.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>As métricas apresentam um aumento do PercentTimeoutError
As suas métricas mostram um aumento no **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um elevado volume de mensagens de estado HTTP "500 Operação Timeout" das operações de armazenamento.

> [!NOTE]
> Pode ver erros de tempo limite temporariamente à medida que a carga de serviço de armazenamento equilibra os pedidos, movendo uma divisória para um novo servidor.
>
>

A métrica **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, e **SASServerTimeoutError**.

Os intervalos do servidor são causados por um erro no servidor. Os intervalos de tempo do cliente acontecem porque uma operação no servidor excedeu o prazo especificado pelo cliente; por exemplo, um cliente que utilize a Biblioteca do Cliente de Armazenamento pode definir um tempo limite para uma operação utilizando a propriedade **ServerTimeout** da classe **QueueRequestOptions.**

Os intervalos de tempo do servidor indicam um problema com o serviço de armazenamento que requer uma investigação mais aprofundada. Você pode usar métricas para ver se você está atingindo os limites de escalabilidade para o serviço e para identificar quaisquer picos de tráfego que possam estar causando este problema. Se o problema for intermitente, pode ser devido a uma atividade de equilíbrio de carga no serviço. Se o problema for persistente e não for causado pela sua aplicação atingindo os limites de escalabilidade do serviço, deverá levantar um problema de suporte. Para intervalos de tempo do cliente, deve decidir se o tempo limite é definido para um valor apropriado no cliente e alterar o valor de tempo limite definido no cliente ou investigar como pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizando as suas consultas de mesa ou reduzindo o tamanho das suas mensagens.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>As métricas apresentam um aumento do PercentNetworkError
As suas métricas mostram um aumento no **PercentNetworkError** para um dos seus serviços de armazenamento. A métrica **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError** e **SASNetworkError**. Estes ocorrem quando o serviço de armazenamento deteta um erro de rede quando o cliente faz um pedido de armazenamento.

A causa mais comum deste erro é desligar um cliente antes que um intervalo expire no serviço de armazenamento. Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento. Também pode usar a Wireshark, ou Tcping para investigar problemas de conectividade de rede do cliente. Estas ferramentas são [descritas nos apêndices.]

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>O cliente está a receber mensagens de HTTP 403 (Proibido)
Se a aplicação cliente estiver a gerar erros HTTP 403 (Proibido), uma das causas prováveis é o cliente estar a utilizar uma Assinatura de Acesso Partilhado (SAS) expirada quando envia um pedido de armazenamento (embora outras causas possíveis incluam distorção do relógio, chaves inválidas e cabeçalhos vazios). Se uma chave de SAS expirada for a causa, não verá entradas nos dados do Registo de Armazenamento do lado do servidor. A tabela a seguir mostra uma amostra do registo do lado do cliente gerado pela Biblioteca do Cliente de Armazenamento que ilustra este problema que ocorre:

| Origem | Verbosidade | Verbosidade | ID de pedido de cliente | Texto de operação |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Informações |3 |85d077ab-... |Iniciar a operação com a localização Principal por modo de localização PrimaryOnly. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Início de pedido sincronizado para <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |À espera de resposta. |
| Microsoft.Azure.Storage |Aviso |2 |85d077ab -... |Exceção lançada enquanto aguardava resposta: O servidor remoto devolveu um erro: (403) Proibido. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Resposta recebida. Código de estado = 403, ID pedido = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Conteúdo-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Aviso |2 |85d077ab -... |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (403) Proibido.. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |Verificando se a operação deve ser novamente experimentada. Contagem de novo = 0, código de estado HTTP = 403, Exceção = O servidor remoto devolveu um erro: (403) Proibido.. |
| Microsoft.Azure.Storage |Informações |3 |85d077ab -... |A próxima localização foi definida para a Primária, com base no modo de localização. |
| Microsoft.Azure.Storage |Erro |1 |85d077ab -... |A política de retíria não permitiu uma nova mente. Falha com O servidor remoto devolveu um erro: (403) Proibido. |

Neste cenário, deverá investigar por que razão o token SAS expira antes de o cliente enviar o sinal para o servidor:

* Normalmente, não deve definir uma hora de início quando cria uma SAS para um cliente utilizar imediatamente. Se existirem pequenas diferenças horárias entre o anfitrião que gera a SAS com a hora atual e o serviço de armazenamento, é possível que o serviço de armazenamento receba uma SAS que ainda não seja válida.
* Não defina um tempo de expiração demasiado curto numa SAS. Novamente, pequenas diferenças horárias entre o anfitrião que gera a SAS e o serviço de armazenamento podem levar uma SAS a expirar aparentemente mais cedo do que o previsto.
* O parâmetro da versão na tecla SAS (por **exemplo, sv=2015-04-05)** corresponde à versão da Biblioteca do Cliente de Armazenamento que está a utilizar? Recomendamos que utilize sempre a versão mais recente da Biblioteca do [Cliente de Armazenamento.](https://www.nuget.org/packages/WindowsAzure.Storage/)
* Se voltar a gerar as chaves de acesso ao armazenamento, quaisquer tokens de SAS existentes podem ser invalidados. Este problema pode ocorrer se gerar tokens de SAS com um tempo de expiração longo para aplicações cliente na cache.

Se estiver a utilizar a Biblioteca de Cliente de Armazenamento para gerar tokens de SAS, é fácil criar um token válido. No entanto, se estiver a utilizar a API de armazenamento REST e a construir os tokens SAS à mão, consulte [Delegating Access with a Shared Access Signature](/rest/api/storageservices/delegate-access-with-shared-access-signature).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>O cliente está a receber mensagens de HTTP 404 (Não encontrado)
Se a aplicação cliente receber uma mensagem de HTTP 404 (Não encontrado) do servidor, isso implica que o objeto que o cliente estava a tentar utilizar (por exemplo, um blob, contentor, entidade, tabela ou fila) não existe no serviço de armazenamento. Existem vários motivos possíveis para tal, como:

* [O cliente ou outro processo eliminou anteriormente o objeto]
* [Um problema de autorização da Assinatura de Acesso Partilhado (SAS)]
* [O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]
* [Falha de rede]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>O cliente ou outro processo anteriormente eliminado o objeto
Em cenários em que o cliente está a tentar ler, atualizar ou apagar dados num serviço de armazenamento, é geralmente fácil identificar nos registos do lado do servidor uma operação anterior que apagou o objeto em questão do serviço de armazenamento. Frequentemente, os dados de registo mostram que outro utilizador ou processo apagou o objeto. No registo de registo de armazenamento do lado do servidor, as colunas de chave de objetos de funcionamento e objetos solicitados mostram quando um cliente apagou um objeto.

No cenário em que um cliente está a tentar inserir um objeto, pode não ser imediatamente óbvio por que isso resulta numa resposta HTTP 404 (Não encontrada) dado que o cliente está a criar um novo objeto. No entanto, se o cliente estiver a criar uma bolha, deve ser capaz de encontrar o recipiente blob, se o cliente estiver a criar uma mensagem deve ser capaz de encontrar uma fila, e se o cliente está adicionando uma linha deve ser capaz de encontrar a mesa.

Pode utilizar o registo do lado do cliente da Biblioteca do Cliente de Armazenamento para obter uma compreensão mais detalhada de quando o cliente envia pedidos específicos para o serviço de armazenamento.

O seguinte registo do lado do cliente gerado pela biblioteca do Cliente de Armazenamento ilustra o problema quando o cliente não consegue encontrar o recipiente para a bolha que está a criar. Este registo inclui detalhes das seguintes operações de armazenamento:

| ID do Pedido | Operação |
| --- | --- |
| 07b26a5d-... |**EliminarIfExists** método para eliminar o recipiente blob. Note que esta operação inclui um pedido **de CABEÇA** para verificar a existência do recipiente. |
| e2d06d78... |Crie o método **CreateIfNotExists** para criar o recipiente blob. Note que esta operação inclui um pedido **de HEAD** que verifica a existência do recipiente. O **HEAD** devolve uma mensagem 404, mas continua. |
| de8b1c3c-... |**Carregar MétodoFromStream** para criar a bolha. O pedido **PUT** falha com uma mensagem 404 |

Entradas de registo:

| ID do Pedido | Texto de operação |
| --- | --- |
| 07b26a5d-... |Iniciando pedido sincronizado para `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = CABEÇA.................. x-ms-cliente-pedido-id:07b26a5d-.... x-ms-date:Tue, 03 jun 2014 10:33:11 GMT.x-ms-versão:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |À espera de resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 200, ID do pedido = eead849-... Conteúdo-MD5 = ETag =    &quot; 0x8D14D2DC63D059B &quot; . |
| 07b26a5d-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| 07b26a5d-... |A descarregar o corpo de resposta. |
| 07b26a5d-... |A operação foi concluída com sucesso. |
| 07b26a5d-... |Iniciando pedido sincronizado para `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = DELETE............... x-ms-cliente-pedido-id:07b26a5d-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-versão:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |À espera de resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 202, ID pedido = 6ab2a4cf-..., Conteúdo-MD5 = , ETag = . |
| 07b26a5d-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| 07b26a5d-... |A descarregar o corpo de resposta. |
| 07b26a5d-... |A operação foi concluída com sucesso. |
| e2d06d78-... |Iniciando pedido assíncronos a `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = CABEÇA.................. x-ms-cliente-pedido-id:e2d06d78-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-versão:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |À espera de resposta. |
| de8b1c3c-... |Iniciando pedido sincronizado para `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ=........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-versão:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Preparando-se para escrever dados de pedido. |
| e2d06d78-... |Exceção lançada enquanto aguardava resposta: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| e2d06d78-... |Resposta recebida. Código de estado = 404, ID pedido = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Os cabeçalhos de resposta foram processados com sucesso, prosseguindo com o resto da operação. |
| e2d06d78-... |A descarregar o corpo de resposta. |
| e2d06d78-... |A operação foi concluída com sucesso. |
| e2d06d78-... |Iniciando pedido assíncronos a `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = PUT... 0......x-ms-cliente-pedido-id:e2d06d78-.... x-ms-date:Tue, 03 jun 2014 10:33:12 GMT.x-ms-versão:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |À espera de resposta. |
| de8b1c3c-... |Escrever dados de pedido. |
| de8b1c3c-... |À espera de resposta. |
| e2d06d78-... |Exceção lançada enquanto aguardava resposta: O servidor remoto devolveu um erro: (409) Conflito.. |
| e2d06d78-... |Resposta recebida. Código de estado = 409, ID pedido = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Descarregar o corpo de resposta a erros. |
| de8b1c3c-... |Exceção lançada enquanto aguardava resposta: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| de8b1c3c-... |Resposta recebida. Código de estado = 404, ID pedido = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (404) Não Encontrado.. |
| de8b1c3c-... |A política de retíria não permitiu uma nova mente. Falha com o servidor remoto devolveu um erro: (404) Não Encontrado.. |
| e2d06d78-... |A política de retíria não permitiu uma nova mente. Falha com o servidor remoto devolveu um erro: (409) Conflito.. |

Neste exemplo, o registo mostra que o cliente está a intercalar pedidos a partir do método **CreateIfNotExists** (solicitar ID e2d06d78...) com os pedidos do método **UploadFromStream** (de8b1c3c-...). Esta interleaving acontece porque a aplicação do cliente está invocando estes métodos assíncronos. Modifique o código assíncrona no cliente para garantir que cria o recipiente antes de tentar enviar quaisquer dados para uma bolha nesse recipiente. Idealmente, deve criar todos os seus recipientes com antecedência.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso partilhado (SAS)
Se a aplicação do cliente tentar utilizar uma chave SAS que não inclua as permissões necessárias para a operação, o serviço de armazenamento devolve ao cliente uma mensagem HTTP 404 (Não encontrada). Ao mesmo tempo, também verá um valor não nulo para **SASAuthorizationError** nas métricas.

A tabela a seguir mostra uma mensagem de registo do lado do servidor da amostra a partir do ficheiro de registo de registo de registo de armazenamento:

| Name | Valor |
| --- | --- |
| Pedir hora de início | 2014-05-30T06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Estado do pedido     | SASAuthorizationError        |
| Código de estado de HTTP   | 404                            |
| Tipo de autenticação| Rio Sas                          |
| Tipo de serviço       | Blobs                         |
| URL do Pedido         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX &; api-version=2014-02-14 |
| Cabeçalho de ID do pedido  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID de pedido de cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigue por que razão o seu pedido de cliente está a tentar realizar uma operação para a qual não foi concedida permissão.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>O código JavaScript do lado do cliente não tem permissão para aceder ao objeto
Se estiver a utilizar um cliente JavaScript e o serviço de armazenamento estiver a devolver mensagens HTTP 404, verifique se existem os seguintes erros javaScript no navegador:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Pode utilizar as Ferramentas de Desenvolvimento F12 no Internet Explorer para rastrear as mensagens trocadas entre o navegador e o serviço de armazenamento quando está a resolver problemas de JavaScript do lado do cliente.
>
>

Estes erros ocorrem porque o navegador web implementa a mesma restrição de segurança da política de [origem](https://www.w3.org/Security/wiki/Same_Origin_Policy) que impede uma página web de chamar uma API em um domínio diferente do domínio de que a página vem.

Para contornar a questão javaScript, pode configurar a Partilha de Recursos de Origem Cruzada (CORS) para o serviço de armazenamento a que o cliente está a aceder. Para obter mais informações, consulte [o suporte para partilha de recursos de origem cruzada (CORS) para serviços de armazenamento Azure.](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)

A amostra de código que se segue mostra como configurar o seu serviço blob para permitir que o JavaScript em execução no domínio Contoso aceda a uma bolha no seu serviço de armazenamento de bolhas:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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

---

#### <a name="network-failure"></a><a name="network-failure"></a>Falha de rede
Em algumas circunstâncias, os pacotes de rede perdidos podem levar ao serviço de armazenamento devolvendo HTTP 404 mensagens ao cliente. Por exemplo, quando a sua aplicação ao cliente está a excluir uma entidade do serviço de mesa, vê o cliente lançar uma exceção de armazenamento reportando uma mensagem de estado "HTTP 404 (Não Encontrado)" do serviço de mesa. Quando investiga a tabela no serviço de armazenamento de mesa, vê que o serviço eliminou a entidade conforme solicitado.

Os detalhes da exceção no cliente incluem o ID do pedido (7e84f12d...) atribuído pelo serviço de tabela para o pedido: pode utilizar estas informações para localizar os dados do pedido nos registos de armazenamento do lado do servidor, procurando na coluna de **cabeçalho de pedido** no ficheiro de registo. Também pode utilizar as métricas para identificar quando falhas como esta ocorrem e, em seguida, pesquisar os ficheiros de registo com base no tempo que as métricas registaram este erro. Esta entrada de registo mostra que a eliminação falhou com uma mensagem de estado "HTTP (404) Client Other Error". A mesma entrada de registo inclui também o ID de pedido gerado pelo cliente na coluna **de id de pedido de cliente** (813ea74f...).

O registo do lado do servidor também inclui outra entrada com o mesmo valor **de id de pedido de cliente** (813ea74f...) para uma operação de eliminação bem sucedida para a mesma entidade, e do mesmo cliente. Esta operação de eliminação bem sucedida ocorreu pouco antes do pedido de eliminação falhado.

A causa mais provável deste cenário é que o cliente enviou um pedido de eliminação da entidade para o serviço de mesa, que conseguiu, mas não recebeu um reconhecimento do servidor (talvez devido a um problema temporário de rede). Em seguida, o cliente voltou automaticamente a tentar a operação (utilizando o mesmo **id de pedido de cliente),** e esta nova tentativa falhou porque a entidade já tinha sido eliminada.

Se este problema ocorrer com frequência, deverá investigar por que razão o cliente não está a receber agradecimentos do serviço de mesa. Se o problema for intermitente, deve prender o erro "HTTP (404) Não Encontrado" e registar no cliente, mas permitir que o cliente continue.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>O cliente está a receber mensagens HTTP 409 (Conflito)
A tabela a seguir mostra um extrato do registo do lado do servidor para duas operações do cliente: **DeleteIfExists** seguido imediatamente por **CreateIfNotExists** usando o mesmo nome de recipiente blob. Cada operação do cliente resulta em dois pedidos enviados para o servidor, primeiro um pedido **getContainerProperties** para verificar se o recipiente existe, seguido do pedido **DeleteContainer** ou **CreateContainer.**

| CarimboDeDataEHora | Operação | Resultado | Nome do contentor | ID de pedido de cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CriarContainer |409 |mmcont |bc881924-... |

O código na aplicação do cliente elimina e recria imediatamente um recipiente blob com o mesmo nome: o método **CreateIfNotExists** (Pedido de Cliente ID bc881924-...) acaba por falhar com o erro HTTP 409 (Conflito). Quando um cliente elimina blobs, contentores, tabelas ou filas, tem de passar um curto período até o nome ficar novamente disponível.

A aplicação cliente deve utilizar nomes de contentor exclusivos sempre que criar novos contentores se o padrão de eliminação/recriação for comum.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>As métricas mostram que as entradas de registo de percentsuccess ou de registo de análise têm operações com o estado de transação dos ClientOtherErrors
A métrica **PercentSuccess** captura a percentagem de operações que foram bem sucedidas com base no seu Código de Estado HTTP. As operações com códigos de estado de 2XX contam como bem-sucedidas, enquanto as operações com códigos de estado nas gamas 3XX, 4XX e 5XX são contabilizadas como infrutíferas e diminuem o valor métrico **percentSuccess.** Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com um estado de transação de **ClientOtherErrors**.

É importante notar que estas operações foram concluídas com sucesso e, portanto, não afetam outras métricas, como a disponibilidade. Alguns exemplos de operações que executam com sucesso mas que podem resultar em códigos de estado HTTP infrutíferos incluem:

* **ResourceNotFound** (Não Encontrado 404), por exemplo, de um pedido GET a uma bolha que não existe.
* **ResourceAlreadyExists** (Conflict 409), por exemplo, a partir de uma operação **CreateIfNotExist** onde o recurso já existe.
* **ConditionNotMet** (Não Modificado 304), por exemplo, a partir de uma operação condicional, como quando um cliente envia um valor **ETag** e um cabeçalho HTTP **If-None-Match** para solicitar uma imagem apenas se tiver sido atualizado desde a última operação.

Pode encontrar uma lista de códigos de erro comuns da API REST que os serviços de armazenamento devolvem na página [Códigos de Erro da API Common REST](/rest/api/storageservices/Common-REST-API-Error-Codes).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>As métricas da capacidade mostram um aumento inesperado no uso da capacidade de armazenamento
Se vir mudanças repentinas e inesperadas no uso da capacidade na sua conta de armazenamento, pode investigar as razões analisando primeiro as suas métricas de disponibilidade; por exemplo, um aumento do número de pedidos de eliminação falhados pode levar a um aumento da quantidade de armazenamento de bolhas que está a usar como operações de limpeza específicas da aplicação que poderia esperar não estar a funcionar como esperado (por exemplo, porque os tokens SAS utilizados para libertar o espaço expiraram).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste
Normalmente, utiliza-se o emulador de armazenamento durante o desenvolvimento e teste para evitar a exigência de uma conta de armazenamento Azure. As questões comuns que podem ocorrer quando se está a usar o emulador de armazenamento são:

* [A funcionalidade "X" não está a funcionar no emulador de armazenamento]
* [Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento]
* [Executar o emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>A funcionalidade "X" não está a funcionar no emulador de armazenamento
O emulador de armazenamento não suporta todas as funcionalidades dos serviços de armazenamento Azure, como o serviço de ficheiros. Para obter mais informações, consulte [o Emulador de Armazenamento Azure para desenvolvimento e teste.](storage-use-emulator.md)

Para as funcionalidades que o emulador de armazenamento não suporta, utilize o serviço de armazenamento Azure na nuvem.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento
Está a testar a sua aplicação que utiliza a Biblioteca do Cliente de Armazenamento contra o emulador de armazenamento local e chamadas de métodos como o **CreateIfNotExists** falham com a mensagem de erro "O valor de um dos cabeçalhos HTTP não está no formato correto." Isto indica que a versão do emulador de armazenamento que está a utilizar não suporta a versão da biblioteca do cliente de armazenamento que está a utilizar. A Biblioteca do Cliente de Armazenamento adiciona a **versão x-ms-cabeçalho** a todos os pedidos que faz. Se o emulador de armazenamento não reconhecer o valor no cabeçalho da **versão x-ms,** rejeita o pedido.

Pode utilizar os registos do Cliente da Biblioteca de Armazenamento para ver o valor do cabeçalho da **versão x-ms** que está a enviar. Também pode ver o valor do cabeçalho da **versão x-ms** se utilizar o Fiddler para rastrear os pedidos da sua aplicação do cliente.

Este cenário ocorre normalmente se instalar e utilizar a versão mais recente da Biblioteca do Cliente de Armazenamento sem atualizar o emulador de armazenamento. Deve instalar a versão mais recente do emulador de armazenamento ou utilizar o armazenamento em nuvem em vez do emulador para desenvolvimento e teste.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Executar o emulador de armazenamento requer privilégios administrativos
É solicitado para obter credenciais de administrador quando executar o emulador de armazenamento. Isto só ocorre quando está a rubricar o emulador de armazenamento pela primeira vez. Depois de ter inicializado o emulador de armazenamento, não precisa de privilégios administrativos para o executar novamente.

Para obter mais informações, consulte [o Emulador de Armazenamento Azure para desenvolvimento e teste.](storage-use-emulator.md) Também pode inicializar o emulador de armazenamento no Visual Studio, que também exigirá privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Está a ter problemas em instalar o Azure SDK para .NET
Quando tenta instalar o SDK, falha na tentativa de instalar o emulador de armazenamento na sua máquina local. O registo de instalação contém uma das seguintes mensagens:

* CAQuietExec: Erro: Incapaz de aceder à instância SQL
* CAQuietExec: Erro: Incapaz de criar base de dados

A causa é um problema com a instalação localDB existente. Por predefinição, o emulador de armazenamento utiliza o LocalDB para persistir dados quando simula os serviços de armazenamento Azure. Pode redefinir a sua instância LocalDB executando os seguintes comandos numa janela de solicitação de comando antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O comando **de eliminação** remove quaisquer ficheiros de base de dados antigos de instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Você tem um problema diferente com um serviço de armazenamento
Se as secções anteriores de resolução de problemas não incluirem o problema que está a ter com um serviço de armazenamento, deverá adotar a seguinte abordagem para diagnosticar e resolver problemas.

* Verifique as suas métricas para ver se há alguma alteração no seu comportamento esperado da linha de base. A partir das métricas, poderá determinar se o problema é transitório ou permanente e quais as operações de armazenamento que o problema está a afetar.
* Pode utilizar as informações das métricas para ajudá-lo a pesquisar os dados de registo do lado do servidor para obter informações mais detalhadas sobre quaisquer erros que estejam a ocorrer. Estas informações podem ajudá-lo a resolver problemas e resolver o problema.
* Se as informações nos registos do lado do servidor não forem suficientes para resolver o problema com sucesso, pode utilizar os registos do lado do cliente da Biblioteca do Cliente de Armazenamento para investigar o comportamento da sua aplicação do cliente, e ferramentas como o Fiddler, Wireshark para investigar a sua rede.

Para obter mais informações sobre a utilização do Violinista, consulte "[Apêndice 1: Utilizar o violinista para capturar o tráfego HTTP e HTTPS]."

Para obter mais informações sobre a utilização do Wireshark, consulte "[Apêndice 2: Usar o Arame para capturar o tráfego da rede]."

## <a name="appendices"></a><a name="appendices"></a>Apêndices
Os apêndices descrevem várias ferramentas que poderá achar úteis quando está a diagnosticar e resolver problemas com o Azure Storage (e outros serviços). Estas ferramentas não fazem parte do Azure Storage e algumas são produtos de terceiros. Como tal, as ferramentas discutidas nestes apêndices não estão abrangidas por qualquer acordo de suporte que possa ter com o Microsoft Azure ou a Azure Storage, pelo que, como parte do seu processo de avaliação, deverá examinar as opções de licenciamento e suporte disponíveis dos fornecedores destas ferramentas.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Apêndice 1: Utilização do Violinista para capturar tráfego HTTP e HTTPS
[O Fiddler](https://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre a aplicação do seu cliente e o serviço de armazenamento Azure que está a utilizar.

> [!NOTE]
> O violinista pode descodificar o tráfego HTTPS; deve ler cuidadosamente a documentação do Fiddler para entender como o faz e compreender as implicações de segurança.
>
>

Este apêndice proporciona uma breve passagem de como configurar o Fiddler para capturar o tráfego entre a máquina local onde instalou o Fiddler e os serviços de armazenamento Azure.

Depois de ter lançado o Fiddler, começará a capturar o tráfego HTTP e HTTPS na sua máquina local. Seguem-se alguns comandos úteis para controlar o Violinista:

* Pare e comece a capturar o tráfego. No menu principal, vá ao **Arquivo** e, em seguida, clique em **'Capture Traffic'** para alternar a captura de vez em quando.
* Guarde os dados de tráfego capturados. No menu principal, vá ao **Arquivo,** clique em **Guardar**, e, em seguida, clique em **Todas as Sessões**: isto permite-lhe guardar o tráfego num ficheiro Session Archive. Pode recarregar um Session Archive mais tarde para análise, ou enviá-lo se solicitado ao suporte da Microsoft.

Para limitar a quantidade de tráfego que o Fiddler capta, pode utilizar filtros que configura no **separador Filtros.** A imagem que se segue mostra um filtro que captura apenas o tráfego enviado para o ponto final de armazenamento **contosoemaildist.table.core.windows.net:**

![Screenshot que mostra um filtro que captura apenas o tráfego enviado para o ponto final de armazenamento contosoemaildist.table.core.windows.net.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Apêndice 2: Utilização de arame para capturar tráfego de rede
[O Wireshark](https://www.wireshark.org/) é um analisador de protocolos de rede que permite visualizar informações detalhadas do pacote para uma vasta gama de protocolos de rede.

O procedimento a seguir mostra-lhe como capturar informações detalhadas do pacote para o tráfego da máquina local onde instalou o Wireshark no serviço de mesa na sua conta de armazenamento Azure.

1. Lance o arame na sua máquina local.
2. Na secção **Iniciar,** selecione a interface de rede local ou as interfaces que estão ligadas à internet.
3. Clique em **Opções de Captura**.
4. Adicione um filtro à caixa de texto **do filtro de captura.** Por exemplo, **o host contosoemaildist.table.core.windows.net** configurará a Wireshark para capturar apenas pacotes enviados para ou a partir do ponto final do serviço de mesa na conta de armazenamento **contosomaildista.** Confira a [lista completa de filtros de captura.](https://wiki.wireshark.org/CaptureFilters)

   ![Screenshot que mostra como adicionar um filtro à caixa de texto do filtro de captura.][6]
5. Clique em **Iniciar**. A Wireshark irá agora capturar todos os pacotes enviados para ou a partir do ponto final do serviço de mesa, uma vez que utiliza a aplicação do seu cliente na sua máquina local.
6. Quando terminar, no menu principal clique em **Captura** e depois **Pare**.
7. Para guardar os dados capturados num Ficheiro de Captura de Arame, no menu principal clique em **Ficheiro** e, em seguida, **Guarde**.

O WireShark irá destacar quaisquer erros que existam na janela da **lista de pacotes.** Também pode utilizar a janela **Expert Info** (clique em **Analisar,** em **seguida, Expert Info**) para ver um resumo de erros e avisos.

![Screenshot que mostra a janela Expert Info onde pode ver um resumo de erros e avisos.][7]

Também pode optar por visualizar os dados da TCP à medida que a camada de aplicação os vê clicando corretamente nos dados da TCP e selecionando **o Follow TCP Stream**. Isto é útil se capturar a sua lixeira sem um filtro de captura. Para obter mais informações, consulte [as correntes de TCP seguintes.](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html)

![Screenshot que mostra como ver os dados da TCP à medida que a camada de aplicação o vê.][8]

> [!NOTE]
> Para obter mais informações sobre a utilização do Wireshark, consulte o [Guia de Utilizadores de Arame.](https://www.wireshark.org/docs/wsug_html_chunked)
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Apêndice 4: Usar o Excel para visualizar métricas e registar dados
Muitas ferramentas permitem-lhe descarregar os dados de Métricas de Armazenamento do armazenamento de mesas Azure num formato delimitado que facilita o carregamento dos dados no Excel para visualização e análise. Os dados de registo de armazenamento do Azure Blob Storage já se encontra num formato delimitado que pode carregar no Excel. No entanto, terá de adicionar rubricas de colunas adequadas com base nas informações no [Formato de Registo de Armazenamento Analytics](/rest/api/storageservices/Storage-Analytics-Log-Format) e no Esquema de Tabela de Análise de [Armazenamento](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

Para importar os seus dados de Registo de Armazenamento no Excel depois de os transferir do armazenamento de bolhas:

* No menu **Dados,** clique **em Texto**.
* Navegue no ficheiro de registo que pretende visualizar e clique em **Importar**.
* No passo 1 do Assistente de Importação de **Texto,** selecione **Delimited**.

No passo 1 do Assistente de Importação de **Texto,** selecione **Semicolon** como o único delimiter e escolha a cotação dupla como **o qualificador de texto**. Em seguida, clique em **Terminar** e escolher onde colocar os dados no seu livro.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Apêndice 5: Monitorização com insights de aplicações para devOps Azure
Também pode utilizar a funcionalidade Application Insights para Azure DevOps como parte da monitorização de desempenho e disponibilidade. Esta ferramenta pode:

* Certifique-se de que o seu serviço web está disponível e responsivo. Quer a sua aplicação seja um site ou uma aplicação de dispositivo que utilize um serviço web, pode testar o seu URL a cada poucos minutos de locais de todo o mundo, e informá-lo se há algum problema.
* Diagnosticar rapidamente quaisquer problemas de desempenho ou exceções no seu serviço web. Descubra se o CPU ou outros recursos estão sendo esticados, obtenha traços de pilha de exceções, e facilmente procure através de vestígios de log. Se o desempenho da aplicação descer abaixo dos limites aceitáveis, a Microsoft pode enviar-lhe um e-mail. Pode monitorizar os serviços web .NET e Java.

Pode encontrar mais informações no [What is Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a análise no Azure Storage, consulte estes recursos:

* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Análise de armazenamento](storage-analytics.md)
* [Métricas de análise de armazenamento](storage-analytics-metrics.md)
* [Esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Registos de análise de armazenamento](storage-analytics-logging.md)
* [Formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introdução]: #introduction
[Como este guia é organizado]: #how-this-guide-is-organized

[Monitorização do seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorização da saúde do serviço]: #monitoring-service-health
[Capacidade de monitorização]: #monitoring-capacity
[Monitorizar a disponibilidade]: #monitoring-availability
[Monitorizar o desempenho]: #monitoring-performance

[Diagnóstico de problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de saúde de serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Erros de diagnóstico]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de registo de armazenamento]: #storage-logging-tools
[Utilização de ferramentas de registo de rede]: #using-network-logging-tools

[Rastreio de ponta a ponta]: #end-to-end-tracing
[Correlacionar dados de registo]: #correlating-log-data
[ID de pedido de cliente]: #client-request-id
[ID de pedido de servidor]: #server-request-id
[Carimbos de Data/Hora]: #timestamps

[Orientação para a resolução de problemas]: #troubleshooting-guidance
[As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Ocorrem atrasos inesperados na entrega de mensagens numa fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[As métricas apresentam um aumento do PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento transitório no PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente do erro do PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[As métricas apresentam um aumento do PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[As métricas apresentam um aumento do PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está a receber mensagens de HTTP 403 (Proibido)]: #the-client-is-receiving-403-messages
[O cliente está a receber mensagens de HTTP 404 (Não encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo eliminou anteriormente o objeto]: #client-previously-deleted-the-object
[Um problema de autorização da Assinatura de Acesso Partilhado (SAS)]: #SAS-authorization-issue
[O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (Conflito)]: #the-client-is-receiving-409-messages

[As métricas mostram que as entradas de registo de percentsuccess ou de registo de análise têm operações com o estado de transação dos ClientOtherErrors]: #metrics-show-low-percent-success
[As métricas da capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[O seu problema surge da utilização do emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[A funcionalidade "X" não está a funcionar no emulador de armazenamento]: #feature-X-is-not-working
[Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao utilizar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[Executar o emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Está a ter problemas em instalar o Azure SDK para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Você tem um problema diferente com um serviço de armazenamento]: #you-have-a-different-issue-with-a-storage-service

[Apêndices]: #appendices
[Apêndice 1: Utilização do Violinista para capturar tráfego HTTP e HTTPS]: #appendix-1
[Apêndice 2: Utilização de arame para capturar tráfego de rede]: #appendix-2
[Apêndice 4: Usar o Excel para visualizar métricas e registar dados]: #appendix-4
[Apêndice 5: Monitorização com insights de aplicações para devOps Azure]: #appendix-5

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
