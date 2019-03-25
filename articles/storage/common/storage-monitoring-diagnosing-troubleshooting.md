---
title: Monitorizar, diagnosticar e resolver problemas do armazenamento do Azure | Documentos da Microsoft
description: Utilize funcionalidades como a análise de armazenamento, registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.subservice: common
ms.openlocfilehash: 6edb1abae91a675a3fe47b417a112f0951886aaf
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351920"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição geral
Diagnosticar e resolver problemas numa aplicação distribuída alojada num ambiente de nuvem podem ser mais complexas do que em ambientes tradicionais. Aplicativos podem ser implementados numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel ou em alguma combinação destes ambientes. Normalmente, o tráfego de rede da sua aplicação pode atravessar a redes públicas e privadas e seu aplicativo pode usar várias tecnologias de armazenamento, como tabelas de armazenamento do Microsoft Azure, Blobs, filas ou ficheiros, além de outros dados armazena, tais como relacional e bases de dados do documento.

Para gerir com êxito a tais aplicativos deve monitorizá-las de forma proativa e compreender como diagnosticar e resolver problemas de todos os aspetos da-los e suas tecnologias dependentes. Como um utilizador dos serviços de armazenamento do Azure, deve continuamente monitorizar os serviços de armazenamento, a sua aplicação utiliza para efetuar quaisquer alterações inesperadas no comportamento (por exemplo, mais lento do que os tempos de resposta habitual) e utilizar o registo para recolher dados de mais detalhados e analisar um problema no profundidade. As informações de diagnóstico que é obter a partir de monitorização e registo lhe ajudarão a determinar a causa de raiz do problema encontrado de seu aplicativo. Em seguida, pode resolver o problema e determinar os passos adequados que pode tomar para resolvê-lo. O armazenamento do Azure é um serviço do Azure e uma parte importante da maioria das soluções que os clientes a implementar para a infraestrutura do Azure de forma. O armazenamento do Azure inclui recursos para simplificar a monitorização, diagnosticar e resolver problemas de armazenamento em seus aplicativos com base na cloud.

> [!NOTE]
> Os ficheiros do Azure não suporta registo neste momento.
>

Para obter um guia prático para resolução de problemas ponto a ponto em aplicativos de armazenamento do Azure, veja [resolução de problemas com as métricas de armazenamento do Azure e o Registro em log, o AzCopy e o analisador de mensagens ponto a ponto](../storage-e2e-troubleshooting.md).

* [Introdução]
  * [Como este guia é organizado]
* [Monitorização do seu serviço de armazenamento]
  * [Monitorização do Estado de funcionamento do serviço]
  * [Capacidade de monitorização]
  * [Monitorização de disponibilidade]
  * [Monitorização do desempenho]
* [Diagnosticar problemas de armazenamento]
  * [Problemas de estado de funcionamento de serviço]
  * [Problemas de desempenho]
  * [Diagnosticar erros]
  * [Problemas de emulador de armazenamento]
  * [Ferramentas de registo de armazenamento]
  * [Usando ferramentas de registo de rede]
* [Rastreamento de ponta a ponta]
  * [Correlacionar dados de registo]
  * [ID de pedido do cliente]
  * [ID de pedido do servidor]
  * [Carimbos de data /]
* [Orientação na resolução de problemas]
  * [As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]
  * [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
  * [As métricas apresentam uma AverageServerLatency alta]
  * [Ocorrem atrasos inesperados na entrega de mensagens numa fila]
  * [As métricas apresentam um aumento do PercentThrottlingError]
  * [As métricas apresentam um aumento do PercentTimeoutError]
  * [As métricas apresentam um aumento do PercentNetworkError]
  * [O cliente está a receber mensagens HTTP 403 (proibido)]
  * [O cliente está a receber mensagens HTTP 404 (não for encontrado)]
  * [O cliente está a receber mensagens de HTTP 409 (conflito)]
  * [Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]
  * [Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]
  * [O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]
  * [Pode encontrar problemas ao instalar o Azure SDK para .NET]
  * [Tem um problema com um serviço de armazenamento diferente]
  * [VHDs de resolução de problemas em máquinas de virtuais do Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [VHDs de resolução de problemas em máquinas virtuais do Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Resolução de problemas de ficheiros do Azure com o Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Resolução de problemas de ficheiros do Azure com Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Appendices]
  * [Apêndice 1: Com o Fiddler para capturar o tráfego HTTP e HTTPS]
  * [Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]
  * [Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]
  * [Apêndice 4: Com o Excel para ver as métricas e registos de dados]
  * [Apêndice 5: Monitorização com o Application Insights para DevOps do Azure]

## <a name="introduction"></a>Introdução
Este guia mostra-lhe como utilizar funcionalidades como a análise de armazenamento do Azure, do lado do cliente de registo na biblioteca de cliente de armazenamento do Azure e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas do armazenamento do Azure relacionados com problemas.

![][1]

Este guia destina-se para ser lida principalmente por desenvolvedores de serviços online que utilizam serviços de armazenamento do Azure e os profissionais de TI responsável por gerenciar esses serviços online. Os objetivos deste guia são:

* Para o ajudar a manter o estado de funcionamento e o desempenho das suas contas de armazenamento do Azure.
* Para lhe fornecer os processos necessários e as ferramentas para ajudar a decidir se um problema num aplicativo ou o problema está relacionado ao armazenamento do Azure.
* Para fornecer orientação acionável para resolver problemas relacionados com o armazenamento do Azure.

### <a name="how-this-guide-is-organized"></a>Como este guia é organizado
A secção "[Monitorização do seu serviço de armazenamento]" descreve como monitorizar o estado de funcionamento e desempenho dos seus serviços de armazenamento do Azure com métricas de análise de armazenamento do Azure (métricas de armazenamento).

A secção "[diagnosticar problemas de armazenamento]" descreve como diagnosticar problemas com o registo do Azure Storage Analytics (registo de armazenamento). Ele também descreve como ativar o registo do lado do cliente com as instalações de uma das bibliotecas de cliente, como a biblioteca de cliente de armazenamento para .NET ou o Azure SDK para Java.

A secção "[rastreamento de ponta a ponta]" descreve como pode correlacionar as informações contidas em vários ficheiros de registo e dados de métricas.

A secção "[Orientação na resolução de problemas]" fornece orientações de resolução de problemas para alguns dos comuns relacionados com o armazenamento de problemas poderá encontrar.

A "[Appendices]" incluem informações sobre como utilizar outras ferramentas como Wireshark e Netmon, para analisar dados de pacote, o Fiddler para analisar mensagens HTTP/HTTPS, de rede e o Microsoft Message Analyzer para correlacionar dados de log.

## <a name="monitoring-your-storage-service"></a>Monitorização do seu serviço de armazenamento
Se estiver familiarizado com a monitorização de desempenho do Windows, pode pensar métricas de armazenamento como sendo um equivalente de armazenamento do Azure de contadores do Monitor de desempenho do Windows. Métricas de armazenamento, encontrará um conjunto abrangente de métricas (contadores na terminologia de Monitor de desempenho do Windows), como a disponibilidade do serviço, número total de pedidos ao serviço ou a percentagem de pedidos com êxito ao serviço. Para obter uma lista completa das métricas disponíveis, consulte [esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). Pode especificar se pretende que o serviço de armazenamento para recolher e agregar as métricas a cada hora ou a cada minuto. Para obter mais informações sobre como ativar as métricas e monitorizar as suas contas de armazenamento, consulte [ativar as métricas de armazenamento e visualizar dados de métricas](https://go.microsoft.com/fwlink/?LinkId=510865).

Pode escolher as métricas de hora a hora que pretende apresentar no [portal do Azure](https://portal.azure.com) e configure as regras que o notificam os administradores por e-mail sempre que uma métrica de hora a hora excede um limiar específico. Para obter mais informações, consulte [receber notificações de alerta](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

O serviço de armazenamento recolhe métricas com um melhor esforço, mas não pode registrar todas as operações de armazenamento.

No portal do Azure, pode ver as métricas, como disponibilidade, total de pedidos e números de latência média de uma conta de armazenamento. Uma regra de notificação também foi definida para alertar um administrador se disponibilidade cair abaixo de um determinado nível. De visualizar estes dados, uma área possíveis para investigação é a percentagem de êxito do serviço de tabela a ser inferior a 100% (para obter mais informações, consulte a secção "[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]").

Deve monitorizar continuamente as aplicações do Azure para garantir que estão em bom estado e de desempenho conforme o esperado:

* Estabelecer algumas métricas de linha de base para a aplicação que irá permitir-lhe comparar dados atuais e identificar eventuais alterações significativas no comportamento do armazenamento do Azure e a sua aplicação. Em muitos casos, os valores de suas métricas de linha de base será específico ao aplicativo e deve estabelecer quando estiver a testar a aplicação de desempenho.
* Métrica de minuto de registro e usá-los para monitorizar ativamente para erros inesperados e anomalias, como o aumento de erro de contagem de ou taxas de pedidos.
* Métricas de hora a hora de gravação e utilizá-las para monitorizar, como os valores médios médio de contagens de erros e as taxas de pedidos.
* Investigar problemas potenciais com ferramentas de diagnóstico, conforme discutido posteriormente na seção "[diagnosticar problemas de armazenamento]."

Os gráficos na imagem a seguir ilustram como o que ocorre para métricas por hora média pode ocultar picos na atividade. As métricas de hora a hora apresentada mostrar uma taxa constante de pedidos, durante o minuto métricas revelam as flutuações que realmente estão a ocorrer.

![][3]

O resto desta secção descreve o que deve monitorar de métricas e por que.

### <a name="monitoring-service-health"></a>Monitorização do Estado de funcionamento do serviço
Pode utilizar o [portal do Azure](https://portal.azure.com) para ver o estado de funcionamento do serviço de armazenamento (e outros serviços do Azure) em todas as regiões do Azure em todo o mundo. Monitorização permite-lhe ver imediatamente se um problema fora do seu controlo está a afetar o serviço de armazenamento na região que utilizar para a sua aplicação.

O [portal do Azure](https://portal.azure.com) também pode fornecer notificações de incidentes que afetam os vários serviços do Azure.
Nota: Estas informações estavam anteriormente disponíveis, juntamente com dados históricos, sobre o [Dashboard de serviço do Azure](https://status.azure.com).

Embora o [portal do Azure](https://portal.azure.com) recolhe informações de estado de funcionamento de dentro dos datacenters do Azure (monitorização do avesso), precisa também considerar adotar uma abordagem de fora para dentro para gerar transações sintéticas que acedem a periodicamente seu aplicativo web alojadas no Azure de várias localizações. Serviços oferecidos pela [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) e Application Insights para DevOps do Azure são exemplos desta abordagem. Para obter mais informações sobre o Application Insights para DevOps do Azure, consulte o apêndice "[apêndice 5: Monitorização com o Application Insights para DevOps do Azure](#appendix-5). "

### <a name="monitoring-capacity"></a>Capacidade de monitorização
Métricas de armazenamento só armazena as métricas de capacidade para o serviço de BLOBs como blobs, normalmente, a conta para a maior proporção de dados armazenados (no momento da escrita, não é possível utilizar as métricas de armazenamento para monitorizar a capacidade das tabelas e filas). Pode encontrar estes dados no **$MetricsCapacityBlob** se tiver ativado a monitorização para o serviço de Blob de tabela. Métricas de armazenamento regista estes dados uma vez por dia, e pode usar o valor do **RowKey** para determinar se a linha contém uma entidade que se relaciona com dados de utilizador (valor **dados**) ou dados de análise (o valor **analytics**). Cada entidade armazenada contém informações sobre a quantidade de armazenamento utilizado (**capacidade** medido em bytes) e o número atual de contentores (**ContainerCount**) e blobs (**ObjectCount** ) em utilização na conta de armazenamento. Para obter mais informações sobre as métricas de capacidade armazenadas no **$MetricsCapacityBlob** da tabela, consulte [esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Deve monitorar esses valores para um aviso inicial que está prestes a atingir os limites de capacidade da sua conta de armazenamento. No portal do Azure, pode adicionar regras de alerta para notificá-lo se a utilização de armazenamento agregado excede ou cair abaixo de limites que especificar.
>
>

Para obter ajuda a estimativa do tamanho de vários objetos de armazenamento, como blobs, veja a mensagem de blogue [Noções básicas sobre faturação do Azure Storage – largura de banda, transações e capacidade](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorização de disponibilidade
Deve monitorar a disponibilidade dos serviços de armazenamento na sua conta de armazenamento ao monitorizar o valor a **disponibilidade** coluna nas tabelas de métricas de hora a hora ou minuto — **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. O **disponibilidade** coluna contém um valor de percentagem que indica a disponibilidade de serviço ou a operação de API representado pela linha (a **RowKey** mostra se a linha contém métricas para o serviço como um todo ou para uma operação de API específica).

Qualquer valor inferior a 100% indica que alguns pedidos de armazenamento estão a falhar. Pode ver por que motivo são falhar ao examinar as demais colunas nos dados das métricas que mostram o número de pedidos com tipos de erro diferente, tal como **ServerTimeoutError**. Deve esperar para ver **disponibilidade** Outono temporariamente inferior a 100% por motivos como tempos limite de servidor transitória enquanto o serviço move as partições para melhor pedido de balanceamento de carga; a lógica de repetição na aplicação de cliente deve lidar com essas condições intermitentes. O artigo [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](https://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que as métricas de armazenamento inclui no seu **disponibilidade** cálculo.

Na [portal do Azure](https://portal.azure.com), pode adicionar regras de alerta para notificá-lo se **disponibilidade** para um serviço cai abaixo de um limite que especificou.

A "[Orientação na resolução de problemas]" secção deste guia descreve alguns problemas comuns do serviço de armazenamento relacionada com a disponibilidade.

### <a name="monitoring-performance"></a>Monitorização do desempenho
Para monitorizar o desempenho dos serviços de armazenamento, pode utilizar as seguintes métricas das tabelas de métricas de hora a hora e minuto.

* Os valores da **apresentam uma AverageE2ELatency** e **uma AverageServerLatency** colunas mostram o tempo médio que o serviço de armazenamento ou tipo de operação de API está a demorar para processar solicitações. **Apresentam uma AverageE2ELatency** é uma medida de latência de ponto-a-ponto que inclui o tempo necessário para ler o pedido e enviar a resposta, além do tempo necessário para processar o pedido (por isso inclui a latência de rede depois do pedido atinge o armazenamento serviço); **Uma AverageServerLatency** é uma medida de apenas o tempo de processamento e, portanto, exclui qualquer latência de rede relacionados com a comunicar com o cliente. Consulte a secção "[as métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]" mais adiante neste guia para obter uma discussão sobre por que pode haver uma grande diferença entre estes dois valores.
* Os valores da **TotalIngress** e **TotalEgress** colunas a mostrar a quantidade total de dados, em bytes, recebidos pelo e vai partido do seu serviço de armazenamento ou por meio de um tipo de operação de API específico.
* Os valores da **TotalRequests** coluna Mostrar o número total de pedidos que o serviço de armazenamento da operação de API está a receber. **TotalRequests** é o número total de pedidos que recebe o serviço de armazenamento.

Normalmente, irá monitorizar para alterações inesperadas em qualquer um destes valores como um indicador de que tem um problema que requerem a investigação.

Na [portal do Azure](https://portal.azure.com), pode adicionar regras de alerta para notificá-lo se qualquer uma das métricas de desempenho para este serviço valor atingido for inferior ou exceder um limiar que especificar.

A "[Orientação na resolução de problemas]" secção deste guia descreve alguns problemas de serviço de armazenamento comuns relacionados ao desempenho.

## <a name="diagnosing-storage-issues"></a>Diagnosticar problemas de armazenamento
Existem várias maneiras que pode tornar-se atento um problema ou o problema em seu aplicativo, incluindo:

* Uma falha de principais que faz com que o aplicativo falhar ou deixe de funcionar.
* Alterações significativas entre os valores de linha de base nas métricas que está a monitorizar, tal como descrito na seção anterior "[Monitorização do seu serviço de armazenamento]."
* Relatórios de utilizadores da sua aplicação que alguma operação específica não foi concluída conforme esperado ou que algum recurso não está a funcionar.
* Erros gerados no seu aplicativo que aparecem nos ficheiros de registo ou por meio de algum outro método de notificação.

Geralmente, os problemas relacionados com serviços de armazenamento do Azure se encaixam em uma das quatro amplas categorias:

* A aplicação tem um problema de desempenho, o comunicado pelos seus utilizadores ou reveladas pelas alterações nas métricas de desempenho.
* Existe um problema com a infraestrutura de armazenamento do Azure num ou mais regiões.
* Seu aplicativo é encontrar um erro, o comunicado pelos seus utilizadores ou reveladas devido a um aumento de uma das métricas de contagem de erro, monitorizar.
* Durante o desenvolvimento e teste, possa estar usando o emulador de armazenamento local; Talvez encontre alguns problemas que referem-se especificamente à utilização do emulador de armazenamento.

As secções seguintes descrevem os passos que deve seguir para diagnosticar e resolver problemas em cada uma destas quatro categorias. A secção "[Orientação na resolução de problemas]" mais adiante neste guia, fornece mais detalhes para alguns problemas comuns que podem ser encontrados.

### <a name="service-health-issues"></a>Problemas de estado de funcionamento de serviço
Problemas de estado de funcionamento do serviço, normalmente, estão fora do seu controlo. O [portal do Azure](https://portal.azure.com) fornece informações sobre os problemas em curso com serviços do Azure, incluindo serviços de armazenamento. Se tiver optado por armazenamento Georredundante com acesso de leitura quando criou a conta de armazenamento, em seguida, se seus dados não estiver disponíveis na localização primária, seu aplicativo pode mudar temporariamente para a cópia só de leitura na localização secundária. Para ler a partir do secundário, a aplicação tem de ser capaz de alternar entre a utilização das localizações de armazenamento primário e secundário e ser capaz de trabalhar num modo de funcionalidade reduzida com dados só de leitura. As bibliotecas de cliente de armazenamento do Azure permitem-lhe definir uma política de repetição pode ler a partir do armazenamento secundário no caso de falha de uma leitura da armazenamento primário. Seu aplicativo também precisa estar ciente de que os dados na localização secundária são eventualmente consistentes. Para obter mais informações, consulte a mensagem de blogue [opções de redundância de armazenamento do Azure e o acesso de leitura armazenamento Georredundante](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de desempenho
O desempenho de uma aplicação pode ser subjetivo, sobretudo na perspetiva do utilizador. Por conseguinte, é importante ter métricas de linha base disponíveis para o ajudar a identificar onde poderá existir um problema de desempenho. Vários fatores podem afetar o desempenho de um serviço de armazenamento do Azure da perspectiva de aplicativo do cliente. Esses fatores poderão funcionar no serviço de armazenamento, no cliente ou na infraestrutura de rede; por isso é importante ter uma estratégia para identificar a origem do problema de desempenho.

Depois de identificar a localização de probabilidade da causa do problema de métricas de desempenho, em seguida, pode utilizar os ficheiros de registo para encontrar informações detalhadas para diagnosticar e resolver o problema ainda mais.

A secção "[Orientação na resolução de problemas]" mais adiante neste guia, fornece mais informações sobre alguns problemas comuns relacionados ao desempenho que podem ser encontrados.

### <a name="diagnosing-errors"></a>Diagnosticar erros
Os utilizadores da sua aplicação notificá-lo de erros comunicados pela aplicação de cliente. Métricas de armazenamento também regista as contagens dos tipos de erro diferentes dos seus serviços de armazenamento, tal como **NetworkError**, **ClientTimeoutError**, ou **AuthorizationError**. Enquanto as métricas de armazenamento apenas regista as contagens dos tipos de erro diferente, pode obter mais detalhes sobre pedidos individuais, examinando o lado do servidor, do lado do cliente e registos de rede. Normalmente, o código de estado HTTP devolvido pelo serviço de armazenamento fornecem uma indicação de por que o pedido falhou.

> [!NOTE]
> Lembre-se de que deve esperar para ver alguns erros intermitentes: por exemplo, os erros devido a condições de rede transitórios ou erros de aplicações.
>
>

Os recursos seguintes são úteis para compreender os códigos de estado e erro relacionados com o armazenamento:

* [Códigos de erro de API de REST comuns](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Códigos de Erro do Serviço de Blobs](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Códigos de erro do serviço de fila](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Códigos de erro do serviço de tabela](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Códigos de erro do serviço de arquivo](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento
O SDK do Azure inclui um emulador de armazenamento, que pode executar numa estação de trabalho de desenvolvimento. Esse emulador simula a maior parte do comportamento dos serviços de armazenamento do Azure e é útil durante o desenvolvimento e teste, permitindo-lhe executar aplicações que utilizam serviços de armazenamento do Azure sem a necessidade de uma subscrição do Azure e uma conta de armazenamento do Azure.

A "[Orientação na resolução de problemas]" secção deste guia descreve alguns problemas comuns encontrados utilizando o emulador do storage.

### <a name="storage-logging-tools"></a>Ferramentas de registo de armazenamento
Registo de armazenamento fornece o registo do lado do servidor de pedidos de armazenamento na sua conta de armazenamento do Azure. Para obter mais informações sobre como ativar o registo do lado do servidor e acessar os dados de registo, consulte [ativar o registo de armazenamento e acesso a dados de registo](https://go.microsoft.com/fwlink/?LinkId=510867).

A biblioteca de cliente de armazenamento para .NET permite-lhe recolher dados de registo do lado do cliente que se relaciona com operações de armazenamento executadas pelo seu aplicativo. Para obter mais informações, veja [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868) (Registo do Lado do Cliente com a Biblioteca de Clientes de Armazenamento .NET).

> [!NOTE]
> Em algumas circunstâncias (por exemplo, falhas de autorização de SAS), um utilizador pode comunicar um erro para o qual não pode encontrar nenhum dado de pedido nos registos de armazenamento do lado do servidor. Pode utilizar as capacidades de registo da biblioteca de clientes de armazenamento para investigar se a causa do problema é no cliente ou utilizar ferramentas de monitorização de rede para investigar a rede.
>
>

### <a name="using-network-logging-tools"></a>Usando ferramentas de registo de rede
Pode capturar o tráfego entre o cliente e servidor para fornecer informações detalhadas sobre os dados que o cliente e o servidor estão a trocar e as condições de rede subjacente. Ferramentas de registo de rede útil incluem:

* [Fiddler](https://www.telerik.com/fiddler) é um proxy que lhe permite examinar os cabeçalhos e os dados do payload de mensagens de solicitação e resposta HTTP e HTTPS de depuração na web gratuita. Para obter mais informações, consulte [apêndice 1: Com o Fiddler para capturar o tráfego HTTP e HTTPS](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) e [Wireshark](https://www.wireshark.org/) é rede gratuita analisadores de protocolo que permitem-lhe ver informações detalhadas do pacote para uma vasta gama de protocolos de rede. Para obter mais informações sobre o Wireshark, consulte "[apêndice 2: Usando o Wireshark para capturar o tráfego de rede](#appendix-2)".
* Microsoft Message Analyzer é uma ferramenta da Microsoft, que prevalece sobre Netmon e que, para além da captura de dados de pacote de rede, ajuda-o a ver e analisar os dados de registo a partir de outras ferramentas. Para obter mais informações, consulte "[apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede](#appendix-3)".
* Se quiser executar um teste de conectividade básica para verificar se o seu computador cliente pode ligar ao serviço de armazenamento do Azure através da rede, é possível fazer isso através da norma **ping** ferramenta no cliente. No entanto, pode utilizar o [ **tcping** ferramenta](https://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de registo do registo de armazenamento e a biblioteca de cliente de armazenamento será suficientes para diagnosticar um problema, mas em alguns cenários, poderá ter as informações mais detalhadas que podem fornecer essas ferramentas de registo de rede. Por exemplo, com o Fiddler para ver mensagens de HTTP e HTTPS permite-lhe ver os dados de cabeçalho e payload enviados de e para os serviços de armazenamento, o que permitem que examinar a forma como uma aplicação cliente repete a operações de armazenamento. Analisadores de protocolo, como o Wireshark funcionam ao nível do pacote, permitindo-lhe ver dados TCP, o que permitem que a solucionar problemas de conectividade e de perda de pacotes. Analisador de mensagens pode operar em camadas HTTP e TCP.

## <a name="end-to-end-tracing"></a>Rastreamento de ponta a ponta
Rastreio ponto-a-ponto utilizando uma variedade de arquivos de log é uma técnica útil para investigar os problemas potenciais. Pode utilizar as informações de data/hora a partir dos seus dados de métricas como uma indicação de como começar a procurar nos ficheiros de registo para obter as informações detalhadas que irão ajudá-lo a resolver o problema.

### <a name="correlating-log-data"></a>Correlacionar dados de registo
Ao visualizar registos de aplicações cliente, rede, rastreios e pedidos de registo de armazenamento de servidor é fundamental para conseguir correlacionar entre os ficheiros de registo diferente. Os ficheiros de registo incluem um número de campos diferentes que são úteis como identificadores de correlação. O ID de pedido do cliente é o campo mais úteis para usar para correlacionar entradas nos registos diferentes. No entanto, às vezes, pode ser útil utilizar o ID de pedido do servidor ou carimbos. As secções seguintes fornecem mais detalhes sobre estas opções.

### <a name="client-request-id"></a>ID de pedido do cliente
A biblioteca de cliente de armazenamento gera automaticamente um ID de pedido de cliente exclusivos para cada solicitação.

* No registo do lado do cliente que cria a biblioteca de cliente de armazenamento, o ID do pedido de cliente aparece no **ID de pedido de cliente** campo de cada entrada de registo relacionadas com o pedido.
* Num rastreio de rede, como um capturado pelo Fiddler, o ID de pedido do cliente é visível nas mensagens de pedido como o **x-ms-client-request-id** valor do cabeçalho HTTP.
* No registo de registo do armazenamento de servidor, o ID de pedido do cliente é apresentado na coluna de ID de pedido de cliente.

> [!NOTE]
> É possível que vários pedidos para partilhar o mesmo ID de pedido do cliente, uma vez que o cliente pode atribuir este valor (embora a biblioteca de cliente de armazenamento atribui automaticamente um novo valor). Quando o cliente tenta novamente, todas as tentativas de partilham o mesmo ID de pedido de cliente. No caso de um lote enviado do cliente, o batch tem um ID de pedido de cliente único.
>
>

### <a name="server-request-id"></a>ID de pedido do servidor
O serviço de armazenamento automaticamente gera IDs de pedido do servidor.

* No registo de registo do armazenamento de servidor, o ID de pedido do servidor é apresentado o **cabeçalho de ID do pedido** coluna.
* Num rastreamento de rede, como um capturado pelo Fiddler, o ID de pedido do servidor é apresentado nas mensagens de resposta, como o **x-ms-request-id** valor do cabeçalho HTTP.
* No registo do lado do cliente que cria a biblioteca de cliente de armazenamento, o ID de pedido do servidor é apresentado na **operação texto** coluna para a entrada de registo mostrar os detalhes da resposta do servidor.

> [!NOTE]
> O serviço de armazenamento sempre atribui um servidor único ID do pedido para cada solicitação que for recebida, portanto, cada tentativa de repetição do cliente e de cada operação incluída num lote tem um ID de pedido de servidor exclusivo.
>
>

Se a biblioteca de cliente de armazenamento emitir uma **StorageException** no cliente, o **RequestInformation** propriedade contém um **RequestResult** objeto que inclui um  **ServiceRequestID** propriedade. Também pode acessar um **RequestResult** objeto a partir de um **OperationContext** instância.

O exemplo de código abaixo demonstra como definir um personalizado **ClientRequestId** valor ao anexar um **OperationContext** o pedido para o serviço de armazenamento de objeto. Ela também mostra como obter o **ServerRequestId** valor da mensagem de resposta.

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

### <a name="timestamps"></a>Carimbos de data /
Também pode utilizar carimbos para localizar entradas de registo relacionados, mas tenha cuidado de qualquer defasagem entre o cliente e servidor que possam existir. Procurar mais ou menos 15 minutos para que correspondam a entradas de servidor com base em timestamp no cliente. Lembre-se de que os metadados do blob para os blobs que contém as métricas indica o intervalo de tempo para as métricas armazenados no blob. Neste intervalo de tempo é útil se tiver muitos de métricas de blobs para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Documentação de orientação de resolução de problemas
Esta secção ajuda-o com o diagnóstico e resolução de problemas de alguns dos problemas comuns seu aplicativo poderá encontrar ao utilizar os serviços de armazenamento do Azure. Utilize a lista abaixo para localizar as informações relevantes para o problema específico.

**Árvore de decisão de resolução de problemas**

---
O seu problema se relaciona com o desempenho de um dos serviços de armazenamento?

* [As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]
* [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
* [As métricas apresentam uma AverageServerLatency alta]
* [Ocorrem atrasos inesperados na entrega de mensagens numa fila]

---
O seu problema se relaciona com a disponibilidade de um dos serviços de armazenamento?

* [As métricas apresentam um aumento do PercentThrottlingError]
* [As métricas apresentam um aumento do PercentTimeoutError]
* [As métricas apresentam um aumento do PercentNetworkError]

---
 A aplicação cliente recebe uma resposta do HTTP 4XX (como 404) de um serviço de armazenamento?

* [O cliente está a receber mensagens HTTP 403 (proibido)]
* [O cliente está a receber mensagens HTTP 404 (não for encontrado)]
* [O cliente está a receber mensagens de HTTP 409 (conflito)]

---
[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]

---
[Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]

---
[Ocorrerem reinícios inesperados de máquinas virtuais que têm um grande número de VHDs ligadas]

---
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]

---
[Pode encontrar problemas ao instalar o Azure SDK para .NET]

---
[Tem um problema com um serviço de armazenamento diferente]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa
A ilustração abaixo do [portal do Azure](https://portal.azure.com) ferramenta de monitoramento mostra um exemplo em que o **apresentam uma AverageE2ELatency** é significativamente maior do que o **uma AverageServerLatency**.

![][4]

O serviço de armazenamento apenas calcula a métrica **apresentam uma AverageE2ELatency** para pedidos com êxito e, ao contrário **uma AverageServerLatency**, inclui o tempo que o cliente utiliza para enviar os dados e receber confirmação por parte do serviço de armazenamento. Por conseguinte, uma diferença entre **apresentam uma AverageE2ELatency** e **uma AverageServerLatency** pode ser devido ao aplicativo cliente que está a ser lento responder ou devido a condições na rede.

> [!NOTE]
> Também pode ver **E2ELatency** e **ServerLatency** para operações de armazenamento individuais no registo de armazenamento de registos de dados.
>
>

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente
Motivos possíveis para o cliente a responder lentamente incluem ter um número limitado de ligações disponíveis ou threads, ou ser poucos recursos, tais como CPU, memória ou rede de largura de banda. Poderá conseguir resolver o problema ao modificar o código de cliente a ser mais eficiente (por exemplo usando chamadas assíncronas para o serviço de armazenamento) ou com uma Máquina Virtual maiores (com mais memória e mais núcleos).

Para os serviços de tabela e fila, o algoritmo de Nagle também pode causar alta **apresentam uma AverageE2ELatency** em comparação com a **uma AverageServerLatency**: para obter mais informações, consulte a postagem [do Nagle Algoritmo não é amigável em direção de pequenos pedidos](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Pode desativar o algoritmo de Nagle no código utilizando o **ServicePointManager** classe na **System.Net** espaço de nomes. Deve fazê-lo antes de efetuar todas as chamadas para a tabela ou serviços de fila em seu aplicativo, uma vez que isto não afeta as ligações que já estão abrir. O exemplo seguinte é proveniente de **Application_Start** método numa função de trabalho.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Verifique os registos de lado do cliente para ver quantas solicitações que a aplicação cliente está a enviar e verifique a existência gerais do .NET relacionados a afunilamentos de desempenho no seu cliente, como CPU, coleta de lixo do .NET, utilização da rede ou memória. Como ponto de partida para aplicações de cliente .NET de resolução de problemas, consulte [depuração, rastreamento e criação de perfil](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência de rede
Normalmente, alta latência de ponto-a-ponto causada pela rede é devido a condições transitórias. Pode investigar os dois problemas de rede transitórias e persistentes como pacotes ignorados, utilizando ferramentas como Wireshark ou o Microsoft Message Analyzer.

Para obter mais informações sobre como utilizar o Wireshark para resolver problemas de rede, consulte "[apêndice 2: Usando o Wireshark para capturar o tráfego de rede]. "

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer para resolver problemas de rede, consulte "[apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]. "

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada
Neste cenário, a causa mais provável é um atraso nos pedidos de armazenamento atingir o serviço de armazenamento. Deve investigar por que solicitações do cliente são não tornando-o por meio de para o serviço de Blobs.

Uma razão possível para o cliente atrasando enviando solicitações é que há um número limitado de ligações disponíveis ou threads.

Também verificar se o cliente está a efetuar várias tentativas e investigue o motivo, se for. Para determinar se o cliente está a efetuar várias repetições, pode:

* Examine os registos de análise de armazenamento. Se várias repetições estavam a acontecer, verá várias operações com o mesmo ID de pedido do cliente, mas com IDs de pedido de servidor diferente.
* Examine os registos de cliente. O registo verboso indica que ocorreu uma repetição.
* Depurar seu código e verificar as propriedades do **OperationContext** associado à solicitação do objeto. Se a operação foi repetida, o **RequestResults** propriedade incluirá o pedido de servidor exclusivo vários IDs. Também pode verificar as horas de início e de fim para cada pedido. Para obter mais informações, consulte o código de exemplo na secção [ID de pedido do servidor].

Se não há problemas no cliente, deve investigar potenciais problemas de rede, tais como a perda de pacotes. Pode usar ferramentas como Wireshark ou o Microsoft Message Analyzer para investigar problemas de rede.

Para obter mais informações sobre como utilizar o Wireshark para resolver problemas de rede, consulte "[apêndice 2: Usando o Wireshark para capturar o tráfego de rede]. "

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer para resolver problemas de rede, consulte "[apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]. "

### <a name="metrics-show-high-AverageServerLatency"></a>As métricas apresentam uma AverageServerLatency alta
No caso de alta **uma AverageServerLatency** para pedidos de transferência de blob, deve utilizar os registos de registo de armazenamento para ver se existem pedidos repetidos para o mesmo blob (ou conjunto de blobs). Para pedidos de carregamento de BLOBs, deve investigar o bloco de tamanho, o cliente é usando (por exemplo, blocos menor do que 64 KB de tamanho pode resultar em sobrecargas, a menos que as leituras são também em menos de 64K segmentos) e, se vários clientes estão a carregar blocos para o mesmo blob em metry llel. Também deve verificar as métricas de minuto para picos de no número de pedidos que resulta na ultrapassagem por segundo destinos de escalabilidade: Consulte também "[as métricas apresentam um aumento do percenttimeouterror]."

Se vir alto **uma AverageServerLatency** para download de blob pedidos quando são repetidos solicita o mesmo blob ou conjunto de blobs, em seguida, deve considerar a colocação em cache estas blobs com o Cache do Azure ou a entrega de conteúdos do Azure Rede (CDN). Para pedidos de carregamento, pode melhorar o débito ao utilizar um tamanho de bloco maior. Para consultas de tabelas, também é possível implementar a colocação em cache do lado do cliente nos clientes que executam as mesmas operações de consulta e onde os dados não alterados com freqüência.

Alta **uma AverageServerLatency** valores também podem ser um sintoma de inadequado de tabelas ou consultas que o resultado em operações de análise ou que seguem o antipadrão preceder/acréscimo. Para obter mais informações, consulte "[as métricas apresentam um aumento do percentthrottlingerror]".

> [!NOTE]
> Pode encontrar uma lista de verificação abrangente desempenho lista de verificação aqui: [Desempenho de armazenamento do Microsoft Azure e lista de verificação de escalabilidade](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Ocorrem atrasos inesperados na entrega de mensagens numa fila
Se estiver a sofrer um atraso entre a hora a que se um aplicativo adiciona uma mensagem numa fila e o tempo, torna-se disponível para ser lido a partir da fila, em seguida, deve levar os passos seguintes para diagnosticar o problema:

* Certifique-se de que o aplicativo é adicionar com êxito as mensagens na fila. Verifique se a aplicação não está a tentar novamente a **AddMessage** método várias vezes antes que os sucedem. Os registos de biblioteca de clientes de armazenamento irão mostrar qualquer tentativas repetidas de operações de armazenamento.
* Certifique-se de que não existe nenhum relógio distorção entre a função de trabalho que adiciona a mensagem à fila e a função de trabalho que lê a mensagem da fila que faz com que seja apresentada como se existe um atraso no processamento.
* Verifique se a função de trabalho que lê as mensagens da fila está a falhar. Se um cliente de fila chama o **GetMessage** , mas não conseguirá responder com uma confirmação de método, a mensagem permanece invisível na fila até que o **invisibilityTimeout** expire. Neste momento, a mensagem se torna disponível para ser novamente processada.
* Verifique se o comprimento da fila está a crescer ao longo do tempo. Isto pode ocorrer se não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros threads de trabalho são colocar na fila. Também verificar as métricas para ver se estão a falhar pedidos de eliminação e o dequeue contam com mensagens, que podem indicar tentativas falhadas repetidas de excluir a mensagem.
* Examine os registos de registo do armazenamento de mensagens em fila para quaisquer operações de fila que têm maior que o esperado **E2ELatency** e **ServerLatency** valores ao longo de um período de tempo maior que o habitual.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>As métricas apresentam um aumento do percentthrottlingerror
Erros de limitação ocorrerem quando exceder os destinos de escalabilidade de um serviço de armazenamento. As limitações do serviço de armazenamento para se certificar de que nenhum cliente único ou de inquilino podem utilizar o serviço às custas de outros utilizadores. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre destinos de escalabilidade para contas de armazenamento e metas de desempenho para partições dentro de contas de armazenamento.

Se o **PercentThrottlingError** métrica apresentam um aumento na percentagem de pedidos que estão a falhar com um erro de limitação, precisa investigar um dos dois cenários:

* [Aumento transitório PercentThrottlingError]
* [Aumento permanente PercentThrottlingError erro]

Um aumento **PercentThrottlingError** muitas vezes ocorre ao mesmo tempo que um aumento no número de pedidos de armazenamento, ou quando são inicialmente carga testar a sua aplicação. Isto pode também se manifestar no cliente como "503 servidor ocupado" ou mensagens de estado HTTP "500 tempo limite da operação" de operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório percentthrottlingerror
Se vir um aumento de valor de **PercentThrottlingError** que ela coincida com períodos de grande atividade para o aplicativo, implementar uma exponencial (não linear) estratégia de término para repetições em seu cliente. Término repetições reduzem a carga de imediato na partição e ajudar a sua aplicação para suavizar os picos no tráfego. Para obter mais informações sobre como implementar as políticas de repetição usando a biblioteca de cliente de armazenamento, consulte [espaço de nomes de retrypolicies, se](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Talvez também veja picos no valor da **PercentThrottlingError** que não coincidam com períodos de grande atividade para a aplicação: aqui a causa mais provável é o serviço de armazenamento mover partições para melhorar o balanceamento de carga.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente de erro do PercentThrottlingError
Se vir um valor elevado consistentemente para **PercentThrottlingError** seguir um aumento permanente em seus volumes de transações ou quando estiver a efetuar o carregamento inicial testa na sua aplicação, em seguida, é necessário avaliar como a aplicação está a utilizar as partições de armazenamento e quer que esteja a alcançar os destinos de escalabilidade para uma conta de armazenamento. Por exemplo, se vir erros numa fila (o que conta como uma única partição) de limitação, em seguida, deve considerar a utilização filas adicionais para distribuir as transações entre várias partições. Se vir erros numa tabela de limitação, precisa considerar o uso de um esquema de particionamento diferente para propagar suas transações em várias partições através de uma vasta gama de valores de chave de partição. Uma causa comum deste problema é o prepend/acrescentar antipadrão em que selecionar a data como a chave de partição e, em seguida, todos os dados de um determinado dia é escrito uma partição: sob carga, isso pode resultar num afunilamento de escrita. Ou considere uma estrutura de criação de partições diferente ou avaliar se utilizar o armazenamento de BLOBs pode ser uma solução melhor. Também verificar se a limitação está ocorrendo como resultado de picos de tráfego e investigar as formas de suavização seu padrão de pedidos.

Se distribuir suas transações em várias partições, ainda deve estar atento os limites de escalabilidade definidas para a conta de armazenamento. Por exemplo, se utilizou dez filas cada o máximo de 2000 mensagens de 1KB por segundo de processamento, será atingiu o limite global de 20 000 mensagens por segundo para a conta de armazenamento. Se tiver de processar mais de 20.000 entidades por segundo, deve considerar a utilização de várias contas de armazenamento. Também deve ter em mente que o tamanho de seus pedidos e entidades tem um impacto sobre o quando o serviço de armazenamento regula a seus clientes: Se tiver o maior de pedidos e entidades, pode ser otimizados mais cedo.

Design de consulta ineficientes também pode causar a atingir os limites de escalabilidade para partições de tabela. Por exemplo, uma consulta com um filtro que seleciona apenas 1% das entidades numa partição, mas que analisa todas as entidades numa partição terá de aceder a cada entidade. Cada entidade que leia contam para o número total de transações nessa partição; Por conseguinte, pode facilmente atingir as metas de escalabilidade.

> [!NOTE]
> Os testes de desempenho devem revelar qualquer designs de consulta ineficiente em seu aplicativo.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>As métricas apresentam um aumento do percenttimeouterror
As métricas apresentam um aumento na **PercentTimeoutError** para um de seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um grande volume de mensagens de estado HTTP "500 tempo limite da operação" de operações de armazenamento.

> [!NOTE]
> Poderá ver erros de tempo limite temporariamente porque o serviço de armazenamento a carga de pedidos de saldos ao mover uma partição para um novo servidor.
>
>

O **PercentTimeoutError** métrica é uma agregação das métricas seguintes: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**,  **AnonymousServerTimeoutError**, e **SASServerTimeoutError**.

Os tempos limite de servidor são causados por um erro no servidor. Os tempos limite de cliente uma vez que uma operação no servidor excedeu o tempo limite especificado pelo cliente; Por exemplo, um cliente usando a biblioteca de cliente de armazenamento pode definir um tempo limite para uma operação usando o **ServerTimeout** propriedade da **QueueRequestOptions** classe.

Tempos limite de servidor indica um problema com o serviço de armazenamento que requer mais investigação. Pode utilizar métricas para ver se está a atingir os limites de escalabilidade para o serviço e para identificar os picos de tráfego, que poderá estar a causar esse problema. Se o problema é intermitente, poderá ser devido ao balanceamento de carga atividade no serviço. Se o problema é persistente e não é causado pelo seu aplicativo atingir os limites de escalabilidade do serviço, deve gerar um problema de suporte. Para tempos limite de cliente, deve decidir se o tempo limite é definido como um valor adequado no cliente e de qualquer alteração, o valor de tempo limite definido no cliente ou investigar como pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo ao otimizar as consultas de tabela ou reduzir o tamanho das mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>As métricas apresentam um aumento do percentnetworkerror
As métricas apresentam um aumento na **PercentNetworkError** para um de seus serviços de armazenamento. O **PercentNetworkError** métrica é uma agregação das métricas seguintes: **NetworkError**, **AnonymousNetworkError**, e **SASNetworkError**. Estes ocorrem quando o serviço de armazenamento detetar um erro de rede quando o cliente faz um pedido de armazenamento.

A causa mais comum deste erro é um cliente a desligar antes de um tempo limite expira no serviço de armazenamento. Investigar o código no seu cliente para compreender por que e quando o cliente se desliga do serviço de armazenamento. Também pode utilizar o Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. Essas ferramentas são descritas na [Appendices].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está a receber mensagens HTTP 403 (proibido)
Se a aplicação cliente estiver a gerar erros HTTP 403 (Proibido), uma das causas prováveis é o cliente estar a utilizar uma Assinatura de Acesso Partilhado (SAS) expirada quando envia um pedido de armazenamento (embora outras causas possíveis incluam distorção do relógio, chaves inválidas e cabeçalhos vazios). Se uma chave de SAS expirada for a causa, não verá entradas nos dados do Registo de Armazenamento do lado do servidor. A tabela seguinte mostra um exemplo de registo do lado do cliente gerado pela biblioteca de cliente de armazenamento que ilustra este problema ocorrer:

| Origem | Verbosidade | Verbosidade | ID de pedido de cliente | Texto de operação |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A iniciar a operação com a localização primária por modo de local PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A partir de uma solicitação síncrona para <https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A aguardar resposta. |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab-... |Excepção emitida ao aguardar a resposta: O servidor remoto devolveu um erro: (403) Proibido. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |Resposta recebida. Código de estado 403, ID do pedido de = = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, MD5 de conteúdo = ETag =. |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab-... |Ocorreu uma excepção durante a operação: O servidor remoto devolveu um erro: Proibido (403).... |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A verificar se a operação deve ser repetida. Contagem de repetições = 0, o código de estado HTTP 403, exceção de = = o servidor remoto devolvido um erro: Proibido (403).... |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A localização seguinte foi definida como principal, com base no modo de local. |
| Microsoft.WindowsAzure.Storage |Erro |1 |85d077ab-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: (403) Proibido. |

Neste cenário, deve investigar por que o token SAS está prestes a expirar antes do cliente envia o token para o servidor:

* Normalmente, não deve definir uma hora de início quando cria uma SAS para um cliente utilizar imediatamente. Se existirem pequenas diferenças horárias entre o anfitrião que gera a SAS com a hora atual e o serviço de armazenamento, é possível que o serviço de armazenamento receba uma SAS que ainda não seja válida.
* Não defina um tempo de expiração demasiado curto numa SAS. Novamente, pequenas diferenças horárias entre o anfitrião que gera a SAS e o serviço de armazenamento podem levar uma SAS a expirar aparentemente mais cedo do que o previsto.
* Faz o parâmetro de versão na chave de SAS (por exemplo **sv = 2015-04-05**) corresponde à versão da biblioteca de clientes de armazenamento que está a utilizar? Recomendamos que utilize sempre a versão mais recente do [biblioteca de clientes de armazenamento](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Se voltar a gerar as chaves de acesso ao armazenamento, quaisquer tokens de SAS existentes podem ser invalidados. Este problema pode ocorrer se gerar tokens de SAS com um tempo de expiração longo para aplicações cliente na cache.

Se estiver a utilizar a Biblioteca de Cliente de Armazenamento para gerar tokens de SAS, é fácil criar um token válido. No entanto, se estiver usando a API de REST de armazenamento e construir os tokens de SAS manualmente, consulte [delegar acesso com uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>O cliente está a receber mensagens de HTTP 404 (não encontrado)
Se a aplicação cliente receber uma mensagem de HTTP 404 (Não encontrado) do servidor, isso implica que o objeto que o cliente estava a tentar utilizar (por exemplo, um blob, contentor, entidade, tabela ou fila) não existe no serviço de armazenamento. Existem vários motivos possíveis para tal, como:

* [O cliente ou outro processo anteriormente eliminou o objeto]
* [Um problema de autorização de assinatura de acesso partilhado (SAS)]
* [No código JavaScript do lado do cliente não tem permissão para aceder ao objeto]
* [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou de outro processo anteriormente eliminou o objeto
Em cenários em que o cliente está a tentar ler, atualizar ou eliminar dados num serviço de armazenamento é normalmente mais fácil identificar nos registos do lado do servidor, uma operação anterior que eliminou o objeto em questão do serviço de armazenamento. Muitas vezes, os dados de registo mostram outro utilizador ou processo eliminou o objeto. No registo de registo do armazenamento de servidor, o tipo de operação e colunas de pedido-objeto-key mostram quando um cliente eliminado um objeto.

No cenário em que um cliente está a tentar inserir um objeto, ele pode não ser imediatamente óbvio por que isso resulta numa resposta HTTP 404 (não encontrado), uma vez que o cliente está a criar um novo objeto. No entanto, se o cliente é a criação de um blob tem de ser capaz de encontrar o contentor de BLOBs, se o cliente é a criação de uma mensagem tem de ser capaz de encontrar uma fila, e se o cliente é adicionar uma linha tem de ser capaz de localizar a tabela.

Pode utilizar o registo do lado do cliente da biblioteca de cliente de armazenamento para obter uma compreensão mais detalhada de quando o cliente envia pedidos específicos ao serviço de armazenamento.

O registo do lado do cliente seguinte gerado pela biblioteca cliente de armazenamento ilustra o problema quando o cliente não é possível localizar o contentor para o blob está a criar. Este registo inclui detalhes sobre as seguintes operações de armazenamento:

| ID do pedido | Operação |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** método para eliminar o contentor de Blobs. Tenha em atenção que esta operação inclui um **HEAD** pedido para verificar a existência do contentor. |
| e2d06d78… |**CreateIfNotExists** método para criar o contentor de Blobs. Tenha em atenção que esta operação inclui um **HEAD** pedido que verifica a existência do contentor. O **HEAD** devolve uma mensagem 404, mas continua. |
| de8b1c3c-... |**UploadFromStream** método para criar o blob. O **colocar** pedido falhar com uma mensagem 404 |

Entradas de registo:

| ID do pedido | Texto de operação |
| --- | --- |
| 07b26a5d-... |A partir de uma solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |A aguardar resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado 200, ID do pedido de = = eeead849... MD5 de conteúdo = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o restante da operação. |
| 07b26a5d-... |Baixar o corpo da resposta. |
| 07b26a5d-... |Operação concluída com êxito. |
| 07b26a5d-... |A partir de uma solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |A aguardar resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 202, ID do pedido = 6ab2a4cf-..., MD5 de conteúdo = ETag =. |
| 07b26a5d-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o restante da operação. |
| 07b26a5d-... |Baixar o corpo da resposta. |
| 07b26a5d-... |Operação concluída com êxito. |
| e2d06d78-... |A partir de uma solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |A aguardar resposta. |
| de8b1c3c-... |A partir de uma solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |A preparar para escrever dados de pedido. |
| e2d06d78-... |Excepção emitida ao aguardar a resposta: O servidor remoto devolveu um erro: (404) não encontrado.... |
| e2d06d78-... |Resposta recebida. Código de estado 404, ID do pedido de = = 353ae3bc-..., MD5 de conteúdo = ETag =. |
| e2d06d78-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o restante da operação. |
| e2d06d78-... |Baixar o corpo da resposta. |
| e2d06d78-... |Operação concluída com êxito. |
| e2d06d78-... |A partir de uma solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |A aguardar resposta. |
| de8b1c3c-... |Escrita de dados de pedido. |
| de8b1c3c-... |A aguardar resposta. |
| e2d06d78-... |Excepção emitida ao aguardar a resposta: O servidor remoto devolveu um erro: Conflito (409).... |
| e2d06d78-... |Resposta recebida. Código de estado 409, ID do pedido de = = c27da20e-..., MD5 de conteúdo = ETag =. |
| e2d06d78-... |Baixar o corpo da resposta de erro. |
| de8b1c3c-... |Excepção emitida ao aguardar a resposta: O servidor remoto devolveu um erro: (404) não encontrado.... |
| de8b1c3c-... |Resposta recebida. Código de estado 404, ID do pedido de = = 0eaeab3e-..., MD5 de conteúdo = ETag =. |
| de8b1c3c-... |Ocorreu uma excepção durante a operação: O servidor remoto devolveu um erro: (404) não encontrado.... |
| de8b1c3c-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: (404) não encontrado.... |
| e2d06d78-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: Conflito (409).... |

Neste exemplo, o registo mostra que o cliente é intermediárias pedidos a partir da **CreateIfNotExists** método (pedido ID e2d06d78...) com as solicitações do **UploadFromStream** método (de8b1c3c-...). Esta intercalação ocorre porque o aplicativo cliente é invocar esses métodos de forma assíncrona. Modificar o código assíncrono no cliente para se certificar de que cria o contentor antes de tentar carregar quaisquer dados para um blob nesse contentor. Idealmente, deve criar todos os seus contentores com antecedência.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso partilhado (SAS)
Se a aplicação cliente tenta utilizar uma chave SAS que não inclui as permissões necessárias para a operação, o serviço de armazenamento devolve uma mensagem de HTTP 404 (não encontrado) ao cliente. Ao mesmo tempo, também verá um valor diferente de zero para **SASAuthorizationError** nas métricas.

A tabela seguinte mostra uma mensagem de registo do lado do servidor de exemplo de ficheiro de registo registo de armazenamento:

| Name | Value |
| --- | --- |
| Hora de início de pedido | 2014-05-30T06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Estado do pedido     | SASAuthorizationError        |
| Código de estado de HTTP   | 404                          |
| Tipo de autenticação| SAs                          |
| Tipo de serviço       | Blobs                         |
| URL do Pedido        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Cabeçalho de ID do pedido  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID de pedido de cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigar por que a aplicação cliente está a tentar realizar uma operação para o qual ele não foram concedido permissões.

#### <a name="JavaScript-code-does-not-have-permission"></a>Código de JavaScript do lado do cliente não tem permissão para aceder ao objeto
Se estiver a utilizar um cliente JavaScript e o serviço de armazenamento está a devolver mensagens de HTTP 404, verifique se existem para os seguintes erros de JavaScript no browser:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Pode usar as ferramentas de desenvolvedor F12 no Internet Explorer para rastrear as mensagens trocadas entre o browser e o serviço de armazenamento quando estiver a resolver problemas de JavaScript do lado do cliente.
>
>

Estes erros ocorrerem porque o navegador da web implementa a [diretiva de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy) proveniente de restrição de segurança que impede que uma página da web chamar uma API num domínio diferente do domínio da página.

Para contornar o problema de JavaScript, pode configurar a Cross Origin Resource Sharing (CORS) para o serviço de armazenamento, que o cliente está a aceder. Para obter mais informações, consulte [suporte de Cross-Origin Resource Sharing (CORS) para serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).

O exemplo de código seguinte mostra como configurar o serviço de BLOBs para permitir que o JavaScript em execução no domínio da Contoso aceder a um blob no seu serviço de armazenamento de BLOBs:

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

#### <a name="network-failure"></a>Falha de rede
Em algumas circunstâncias, pacotes de rede perdida podem levar ao serviço de armazenamento do HTTP 404 mensagens a devolver ao cliente. Por exemplo, quando a aplicação cliente está a eliminar uma entidade do serviço tabela verá o cliente gera um relatório de exceção de armazenamento um "HTTP 404 (não encontrado)" mensagem de estado do serviço tabela. Quando examinar a tabela no serviço de armazenamento de tabela, verá que o serviço eliminar a entidade como requerido.

Os detalhes da exceção no cliente incluem o ID de pedido (7e84f12d...) atribuído pelo serviço de tabela para o pedido: pode usar essas informações para localizar os detalhes de pedido nos registos do armazenamento do lado do servidor ao pesquisar no **cabeçalho de id de pedido**  coluna no ficheiro de registo. Também pode utilizar as métricas para identificar quando falhas, como este ocorrerem e, em seguida, procure os ficheiros de registo com base no tempo que as métricas registadas este erro. Esta entrada de log mostra que a eliminação falhou com uma mensagem de estado de "Client outro erro HTTP (404)". A mesma entrada de registo também inclui o ID do pedido gerado pelo cliente no **client-request-id** coluna (813ea74f...).

O registo do lado do servidor também inclui a entrada de outra com o mesmo **client-request-id** valor (813ea74f...) para o êxito de eliminação para a mesma entidade e do mesmo cliente. Esta operação de eliminação com êxito ocorria muito pouco tempo antes da falha ao eliminar o pedido.

A causa mais provável deste cenário é que o cliente enviou um pedido de eliminação para a entidade para o serviço de tabela, que foi concluída com êxito, mas não recebeu uma confirmação do servidor (talvez devido a um problema de rede temporária). O cliente, em seguida, repetir automaticamente a operação (com o mesmo **client-request-id**), e esta repetição falhou porque a entidade já tinha sido eliminada.

Se este problema ocorrer com freqüência, deve investigar por que o cliente está a falhar receber confirmações do serviço tabela. Se o problema é intermitente, deve interceptar o erro "O HTTP (404) não encontrado" e registrá-lo no cliente, mas permitir que o cliente continue.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está a receber mensagens HTTP 409 (conflito)
A tabela seguinte mostra um extrato de registo do lado do servidor para duas operações de cliente: **DeleteIfExists** seguido imediatamente por **CreateIfNotExists** usando o mesmo nome de contentor de Blobs. Cada operação de cliente resulta em dois pedidos enviados para o servidor, primeiro uma **GetContainerProperties** pedido para verificar se o contentor existe, seguido da **DeleteContainer** ou  **CreateContainer** pedido.

| Carimbo de data/hora | Operação | Resultado | Nome do contentor | ID de pedido de cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

O código no aplicativo cliente elimina e, em seguida, recria imediatamente um contentor de Blobs com o mesmo nome: o **CreateIfNotExists** método (pedido de cliente ID bc881924-...), eventualmente, falhar com o erro de HTTP 409 (conflito). Quando um cliente elimina blobs, contentores, tabelas ou filas, tem de passar um curto período até o nome ficar novamente disponível.

A aplicação cliente deve utilizar nomes de contentor exclusivos sempre que criar novos contentores se o padrão de eliminação/recriação for comum.

### <a name="metrics-show-low-percent-success"></a>As métricas apresentam PercentSuccess baixa ou entradas do log analytics tem operações com o estado de transação de ClientOtherErrors
O **PercentSuccess** métrica captura a percentagem de operações que foram bem-sucedidas, com base no respetivo código de estado de HTTP. Contagem de operações com códigos de estado de 2XX conclusão com êxito, ao passo que as operações com códigos de Estado nos intervalos de 3XX, 4XX e 5XX são contabilizadas como sem êxito e inferior a **PercentSuccess** valor de métrica. Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com o estado da transação **ClientOtherErrors**.

É importante observar que essas operações foram concluídas com êxito e, portanto, não afetam o outras métricas, como a disponibilidade. Alguns exemplos de operações que ser executado com êxito, mas que pode resultar em códigos de estado HTTP sem êxito incluem:

* **ResourceNotFound** (não encontrado 404), por exemplo a partir de um pedido GET para um blob que não existe.
* **ResourceAlreadyExists** (409 Conflito), por exemplo a partir de um **CreateIfNotExist** operação onde o recurso já existe.
* **ConditionNotMet** (não modificado 304), por exemplo a partir de uma operação condicional, como quando um cliente envia um **ETag** valor e um HTTP **If-None-Match** cabeçalho para solicitar uma imagem apenas se tiver foi atualizado desde a última operação.

Pode encontrar uma lista de códigos de erro de REST API comuns que os serviços de armazenamento retornam na página [códigos de erro da API de REST comuns](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento
Se vir repentinos, alterações inesperadas na utilização de capacidade na sua conta de armazenamento, pode investigar os motivos pelos quais ao consultar primeiro as métricas de disponibilidade; Por exemplo, um aumento no número de pedidos podem originar um aumento na quantidade de armazenamento de BLOBs que estiver a utilizar como operações de limpeza de específico do aplicativo, que talvez esperasse para ser liberando espaço não podem funcionar como esperado (por exemplo de falha ao eliminar uma vez que os tokens SAS utilizados para libertar algum espaço expiraram).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>O problema surge de utilizar o emulador de armazenamento para desenvolvimento ou teste
Normalmente, utilizar o emulador de armazenamento durante o desenvolvimento e teste para evitar a necessidade de uma conta de armazenamento do Azure. Os problemas comuns que podem ocorrer quando estiver a utilizar o emulador de armazenamento são:

* [Funcionalidade "X" não está a funcionar no emulador do armazenamento]
* [Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento]
* [Executar o emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>Funcionalidade "X" não está a funcionar no emulador do armazenamento
O emulador de armazenamento não suporta todas as funcionalidades dos serviços de armazenamento do Azure, como o serviço de ficheiros. Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md).

Para esses recursos que não suporta o emulador de armazenamento, utilize o serviço de armazenamento do Azure na cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento
Está a testar a aplicação que utiliza a biblioteca de cliente de armazenamento contra as chamadas de método e o emulador de armazenamento local, tal como **CreateIfNotExists** falhar com a mensagem de erro "o valor para um dos cabeçalhos de HTTP não está correto no formato." Isto indica que a versão do emulador de armazenamento que está a utilizar não suporta a versão da biblioteca de clientes de armazenamento que está a utilizar. A biblioteca de cliente de armazenamento adiciona o cabeçalho **x-ms-version** a todos os pedidos faz. Se o emulador de armazenamento não reconhece o valor de **x-ms-version** cabeçalho, esta o rejeita o pedido.

Pode utilizar os registos de cliente da biblioteca de armazenamento para ver o valor do **cabeçalho x-ms-version** seja o envio. Também pode ver o valor do **cabeçalho x-ms-version** se utilizar o Fiddler para rastrear os pedidos a partir da sua aplicação de cliente.

Este cenário ocorre, normalmente, se instalar e utilizar a versão mais recente da biblioteca de clientes de armazenamento sem atualizar o emulador de armazenamento. Deve instalar a versão mais recente do emulador de armazenamento ou utilizar o armazenamento na cloud em vez do emulador para desenvolvimento e teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Executar o emulador de armazenamento requer privilégios administrativos
Lhe é pedidas as credenciais de administrador quando executa o emulador de armazenamento. Isto só ocorre quando estão a inicializar o emulador de armazenamento pela primeira vez. Depois de ter inicializado o emulador de armazenamento, não precisa de privilégios administrativos para executá-lo novamente.

Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md). Também pode inicializar o emulador de armazenamento no Visual Studio, que também irá precisar de privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Tiver com problemas ao instalar o Azure SDK para .NET
Quando tentar instalar o SDK, falhará a tentar instalar o emulador de armazenamento no seu computador local. O registo de instalação contém uma das seguintes mensagens:

* CAQuietExec:  Erro: Não é possível aceder à instância SQL
* CAQuietExec:  Erro: Não é possível criar a base de dados

A causa é um problema com a instalação existente do LocalDB. Por predefinição, o emulador de armazenamento utiliza LocalDB para manter os dados quando ele simula os serviços de armazenamento do Azure. Pode repor a sua instância do LocalDB, executando os seguintes comandos numa janela do prompt de comando antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O **eliminar** comando remove todos os ficheiros da base de dados antigos de instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Tiver uma questão diferente com um serviço de armazenamento
Se as secções de resolução de problemas anteriores não incluem o problema que estiver a ter com um serviço de armazenamento, deve adotar a seguinte abordagem para diagnosticar e resolver o problema.

* Verifique as suas métricas para ver se há qualquer mudança do comportamento da sua linha de base esperada. Entre as métricas, pode ser capaz de determinar se o problema é transitória ou permanente e as operações de armazenamento que o problema está a afetar.
* Pode utilizar as informações de métricas para o ajudar a pesquisar os seus dados de registo do lado do servidor para obter informações mais detalhadas sobre os erros que estão a ocorrer. Estas informações podem ajudá-lo a resolver o problema.
* Se as informações nos registos do lado do servidor não são suficientes para resolver o problema com êxito, pode utilizar os registos do lado do cliente de biblioteca de clientes de armazenamento para investigar o comportamento da aplicação cliente e ferramentas como o Fiddler, Wireshark e a Microsoft Analisador de mensagens para investigar a sua rede.

Para obter mais informações sobre como utilizar o Fiddler, consulte "[apêndice 1: Com o Fiddler para capturar o tráfego HTTP e HTTPS]. "

Para obter mais informações sobre como utilizar o Wireshark, consulte "[apêndice 2: Usando o Wireshark para capturar o tráfego de rede]. "

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer, consulte "[apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]. "

## <a name="appendices"></a>Apêndices
Os apêndices descrevem várias ferramentas que podem ser úteis quando estiver a diagnosticar e resolver problemas com o armazenamento do Azure (e outros serviços). Essas ferramentas não fazem parte do armazenamento do Azure e algumas são produtos de terceiros. Como tal, as ferramentas discutidas estes apêndices não são abrangidas por qualquer contrato de suporte, pode ter com o Microsoft Azure ou o armazenamento do Azure e, portanto, como parte do seu processo de avaliação deve examinar as opções de licenciamento e suporte disponíveis a partir do fornecedores dessas ferramentas.

### <a name="appendix-1"></a>Apêndice 1: Com o Fiddler para capturar o tráfego HTTP e HTTPS
[Fiddler](https://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre a aplicação cliente e o serviço de armazenamento do Azure que está a utilizar.

> [!NOTE]
> Fiddler possível decodificar o tráfego HTTPS; Leia a documentação do Fiddler cuidadosamente para compreender a forma como consegue fazê-lo e para compreender as implicações de segurança.
>
>

Este apêndice fornece um breve passo a passo de como configurar o Fiddler para capturar o tráfego entre o computador local em que instalou o Fiddler e os serviços de armazenamento do Azure.

Depois que tiver iniciado o Fiddler, ele começará a capturar o tráfego HTTP e HTTPS no seu computador local. Seguem-se alguns comandos úteis para controlar o Fiddler:

* Parar e iniciar a captura de tráfego. No menu principal, aceda a **arquivo** e, em seguida, clique em **capturar tráfego** para alternar a capturar e desativar.
* Guarde os dados de tráfego capturada. No menu principal, aceda a **arquivo**, clique em **guardar**e, em seguida, clique em **todas as sessões**: Isto permite-lhe guardar o tráfego num arquivo do arquivo de sessão. Pode recarregar um arquivo de sessão mais tarde para análise, ou enviá-lo, se solicitado ao suporte da Microsoft.

Para limitar a quantidade de tráfego que captura de Fiddler, pode utilizar filtros que configurou o **filtros** separador. Captura de ecrã seguinte mostra um filtro que captura apenas o tráfego enviado para o **contosoemaildist.table.core.windows.net** ponto final de armazenamento:

![][5]

### <a name="appendix-2"></a>Apêndice 2: Usando o Wireshark para capturar o tráfego de rede
[O Wireshark](https://www.wireshark.org/) é um analisador de protocolo de rede permite-lhe ver informações detalhadas do pacote para uma vasta gama de protocolos de rede.

O procedimento seguinte mostra como capturar informações detalhadas do pacote para o tráfego do computador local onde instalou o Wireshark no serviço tabela na sua conta de armazenamento do Azure.

1. Inicie o Wireshark no seu computador local.
2. Na **iniciar** secção, selecione a interface de rede local ou interfaces que estão ligados à internet.
3. Clique em **capturar opções**.
4. Adicionar um filtro para o **capturar filtro** caixa de texto. Por exemplo, **alojar contosoemaildist.table.core.windows.net** irá configurar o Wireshark para capturar apenas os pacotes enviados para ou a partir do ponto de final de serviço de tabela no **contosoemaildist** conta de armazenamento. Veja a [uma lista completa de filtros de capturar](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Clique em **Iniciar**. O Wireshark agora irá capturar todos os pacotes enviar de ou para o ponto de extremidade do serviço de tabela como utilizar a aplicação cliente no seu computador local.
6. Quando tiver terminado, do menu principal, clique em **capturar** e, em seguida **parar**.
7. Para guardar os dados capturados num arquivo de captura do Wireshark, no menu principal clique **arquivo** e, em seguida **guardar**.

O WireShark irá realçar os erros que existem nos **packetlist** janela. Também pode utilizar o **especialista em informações** janela (clique em **Analyze**, em seguida, **especialista em informações**) para ver um resumo dos erros e avisos.

![][7]

Também pode optar por ver os dados TCP como a camada de aplicativo vê-lo ao clicar com o botão direito sobre os dados TCP e selecionar **siga TCP Stream**. Isto é útil se capturou o despejo sem um filtro de captura. Para obter mais informações, consulte [seguintes fluxos do TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Para obter mais informações sobre como utilizar o Wireshark, consulte a [Guia do usuário do Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3"></a>Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede
Pode utilizar o Microsoft Message Analyzer para capturar o tráfego HTTP e HTTPS de forma semelhante para o Fiddler e capturar o tráfego de rede de forma semelhante para o Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar uma sessão de rastreio de web usando o Microsoft Message Analyzer
Para configurar uma sessão de rastreio de web para tráfego HTTP e HTTPS com o Microsoft Message Analyzer, execute a aplicação Microsoft Message Analyzer e, em seguida, no **arquivo** menu, clique em **captura/rastreio**. Na lista de cenários de rastreamento disponíveis, selecione **Proxy de Web**. Em seguida, no **configuração do cenário de rastreio** no painel a **HostnameFilter** caixa de texto, adicione os nomes dos pontos finais de armazenamento (pode consultar esses nomes no [portal do Azure](https://portal.azure.com)). Por exemplo, se o nome da sua conta de armazenamento do Azure for **contosodata**, deve adicionar o seguinte para o **HostnameFilter** caixa de texto:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Um caractere de espaço separa os nomes de anfitrião.
>
>

Quando estiver pronto para iniciar a recolha de dados de rastreio, clique nas **Start With** botão.

Para obter mais informações sobre o Microsoft Message Analyzer **Proxy de Web** de rastreio, consulte [fornecedor de Microsoft-PEF-WebProxy](https://technet.microsoft.com/library/jj674814.aspx).

O incorporado **Proxy de Web** rastreio no Microsoft Message Analyzer se baseia no Fiddler; ele pode capturar o tráfego HTTPS do lado do cliente e apresentar mensagens HTTPS não encriptadas. O **Proxy de Web** rastrear funciona ao configurar um proxy local para todos os tráfegos HTTP e HTTPS, que concede-lhe acesso às mensagens não encriptadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de rede com o Microsoft Message Analyzer
Além de utilizar o Microsoft Message Analyzer **Proxy de Web** trace para capturar os detalhes do tráfego HTTP/HTTPs entre o aplicativo cliente e o serviço de armazenamento, também pode utilizar o incorporado **camada de Link Local**  trace para capturar informações de pacote de rede. Esta permite que capture dados semelhantes ao que pode capturar com o Wireshark e diagnosticar problemas de rede, como pacotes de ignorados.

Captura de ecrã seguinte mostra um exemplo **Local de ligação de camada** rastreio com algumas **informativa** mensagens no **DiagnosisTypes** coluna. Clicar num ícone de **DiagnosisTypes** coluna mostra os detalhes da mensagem. Neste exemplo, o servidor retransmitido mensagem #305 porque não recebeu uma confirmação do cliente:

![][9]

Quando cria a sessão de rastreio no Microsoft Message Analyzer, é possível especificar filtros para reduzir a quantidade de ruído no rastreio. Sobre o **capturar / de rastreio** página onde define o rastreio, clique no **configurar** ligação junto a **Microsoft-Windows-NDIS-PacketCapture**. Captura de ecrã seguinte mostra uma configuração de que filtros de tráfego TCP para os endereços IP dos três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreio de camada de Link Microsoft Message Analyzer Local, consulte [fornecedor de Microsoft-PEF-NDIS-PacketCapture](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Apêndice 4: Com o Excel para ver as métricas e registos de dados
Muitas ferramentas permitem-lhe transferir os dados das métricas de armazenamento do armazenamento de tabelas do Azure num formato delimitado, que torna mais fácil carregar os dados para o Excel para visualização e análise. Dados de registo de armazenamento do armazenamento de Blobs do Azure já estão num formato delimitado, que pode ser carregado para o Excel. No entanto, terá de adicionar cabeçalhos de coluna apropriado com base nas informações em [formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx) e [esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar os dados de registo de armazenamento para o Excel, depois de transferir a partir do armazenamento de BLOBs:

* Sobre o **dados** menu, clique em **do texto**.
* Procure o ficheiro de registo que pretende visualizar e clique em **importação**.
* No passo 1 do **Assistente de importação de texto**, selecione **delimitado**.

No passo 1 do **Assistente de importação de texto**, selecione **ponto e vírgula** como o delimitador único e escolha aspas duplas como o **qualificador de texto**. Em seguida, clique em **concluir** e escolha onde pretende colocar os dados no seu livro.

### <a name="appendix-5"></a>Apêndice 5: Monitorização com o Application Insights para DevOps do Azure
Também pode utilizar o recurso do Application Insights para DevOps do Azure como parte de seu desempenho e a monitorização de disponibilidade. Essa ferramenta pode:

* Certifique-se de que o serviço web está disponível e é reativo. Se a aplicação é um web site ou uma aplicação de dispositivo que utiliza um serviço da web, pode testar o seu URL intervalos de poucos minutos a partir de localizações em todo o mundo e informá-lo a se existe um problema.
* Diagnostique rapidamente quaisquer problemas de desempenho ou exceções no seu serviço web. Descubra se a CPU ou de outros recursos estão a ser transferidos, obter rastreamentos de pilha de exceções e pesquisar facilmente por meio de rastreios de registos. Se o desempenho da aplicação descer abaixo dos limites aceitáveis, a Microsoft pode enviar uma mensagem de e-mail. Pode monitorar serviços da web .NET e Java.

Pode encontrar mais informações em [o que é o Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a análise no armazenamento do Azure, veja estes recursos:

* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Análise de armazenamento](storage-analytics.md)
* [Métricas da análise de armazenamento](storage-analytics-metrics.md)
* [Esquema de tabela de métricas da análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Registos de análise de armazenamento](storage-analytics-logging.md)
* [Formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introdução]: #introduction
[Como este guia é organizado]: #how-this-guide-is-organized

[Monitorização do seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorização do Estado de funcionamento do serviço]: #monitoring-service-health
[Capacidade de monitorização]: #monitoring-capacity
[Monitorização de disponibilidade]: #monitoring-availability
[Monitorização do desempenho]: #monitoring-performance

[Diagnosticar problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de estado de funcionamento de serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnosticar erros]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de registo de armazenamento]: #storage-logging-tools
[Usando ferramentas de registo de rede]: #using-network-logging-tools

[Rastreamento de ponta a ponta]: #end-to-end-tracing
[Correlacionar dados de registo]: #correlating-log-data
[ID de pedido do cliente]: #client-request-id
[ID de pedido do servidor]: #server-request-id
[Carimbos de data /]: #timestamps

[Orientação na resolução de problemas]: #troubleshooting-guidance
[As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Ocorrem atrasos inesperados na entrega de mensagens numa fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[As métricas apresentam um aumento do PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento transitório PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente PercentThrottlingError erro]: #permanent-increase-in-PercentThrottlingError
[As métricas apresentam um aumento do PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[As métricas apresentam um aumento do PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está a receber mensagens HTTP 403 (proibido)]: #the-client-is-receiving-403-messages
[O cliente está a receber mensagens HTTP 404 (não for encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo anteriormente eliminou o objeto]: #client-previously-deleted-the-object
[Um problema de autorização de assinatura de acesso partilhado (SAS)]: #SAS-authorization-issue
[No código JavaScript do lado do cliente não tem permissão para aceder ao objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (conflito)]: #the-client-is-receiving-409-messages

[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]: #metrics-show-low-percent-success
[Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[Funcionalidade "X" não está a funcionar no emulador do armazenamento]: #feature-X-is-not-working
[Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" ao utilizar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[Executar o emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Pode encontrar problemas ao instalar o Azure SDK para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Tem um problema com um serviço de armazenamento diferente]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[Apêndice 1: Com o Fiddler para capturar o tráfego HTTP e HTTPS]: #appendix-1
[Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]: #appendix-2
[Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]: #appendix-3
[Apêndice 4: Com o Excel para ver as métricas e registos de dados]: #appendix-4
[Apêndice 5: Monitorização com o Application Insights para DevOps do Azure]: #appendix-5

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
