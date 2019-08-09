---
title: Monitorar, diagnosticar e solucionar problemas do armazenamento do Azure | Microsoft Docs
description: Use recursos como análise de armazenamento, registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 08c19daa0af226834ea70db8847e1637c2373351
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855355"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição geral
Diagnosticar e solucionar problemas em um aplicativo distribuído hospedado em um ambiente de nuvem pode ser mais complexo do que em ambientes tradicionais. Os aplicativos podem ser implantados em uma infraestrutura de PaaS ou IaaS, localmente, em um dispositivo móvel ou em alguma combinação desses ambientes. Normalmente, o tráfego de rede do aplicativo pode atravessar redes públicas e privadas e seu aplicativo pode usar várias tecnologias de armazenamento, como Armazenamento do Microsoft Azure tabelas, BLOBs, filas ou arquivos, além de outros armazenamentos de dados, como relacionais e bancos de dados de documentos.

Para gerenciar esses aplicativos com êxito, você deve monitorá-los proativamente e entender como diagnosticar e solucionar problemas de todos os aspectos deles e de suas tecnologias dependentes. Como um usuário dos serviços de armazenamento do Azure, você deve monitorar continuamente os serviços de armazenamento que seu aplicativo usa para quaisquer alterações inesperadas no comportamento (como tempos de resposta mais lentos do que os normais) e usar o registro em log para coletar dados mais detalhados e analisar um problema no Detalhado. As informações de diagnóstico obtidas no monitoramento e no registro em log ajudarão você a determinar a causa raiz do problema que seu aplicativo encontrou. Em seguida, você pode solucionar o problema e determinar as etapas apropriadas que você pode executar para corrigi-lo. O armazenamento do Azure é um serviço principal do Azure e forma uma parte importante da maioria das soluções que os clientes implantam na infraestrutura do Azure. O armazenamento do Azure inclui recursos para simplificar o monitoramento, o diagnóstico e a solução de problemas de armazenamento em seus aplicativos baseados em nuvem.

> [!NOTE]
> Os arquivos do Azure não dão suporte ao registro em log no momento.
>

Para obter um guia prático de solução de problemas de ponta a ponta em aplicativos de armazenamento do Azure, consulte solução de problemas de ponta [a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy](../storage-e2e-troubleshooting.md)e analisador de mensagem.

* [Introdução]
  * [Como este guia é organizado]
* [Monitorando seu serviço de armazenamento]
  * [Monitorando a integridade do serviço]
  * [Capacidade de monitoramento]
  * [Monitorando a disponibilidade]
  * [Monitorando o desempenho]
* [Diagnosticando problemas de armazenamento]
  * [Problemas de integridade do serviço]
  * [Problemas de desempenho]
  * [Diagnosticando erros]
  * [Problemas do emulador de armazenamento]
  * [Ferramentas de log de armazenamento]
  * [Usando as ferramentas de log de rede]
* [Rastreamento de ponta a ponta]
  * [Correlacionando dados de log]
  * [ID de solicitação do cliente]
  * [ID de solicitação do servidor]
  * [Carimbos]
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
  * [As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]
  * [O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]
  * [Pode encontrar problemas ao instalar o Azure SDK para .NET]
  * [Tem um problema com um serviço de armazenamento diferente]
  * [Solucionando problemas de VHDs em máquinas virtuais do Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Solucionando problemas de VHDs em máquinas virtuais Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Solucionando problemas de arquivos do Azure com o Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Solucionando problemas de arquivos do Azure com o Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Appendices]
  * [Apêndice 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]
  * [Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]
  * [Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]
  * [Apêndice 4: Usando o Excel para exibir métricas e dados de log]
  * [Apêndice 5: Monitoramento com o Application Insights para DevOps do Azure]

## <a name="introduction"></a>Introdução
Este guia mostra como usar recursos como Análise de Armazenamento do Azure, log do lado do cliente na biblioteca de cliente do armazenamento do Azure e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure.

![][1]

Este guia deve ser lido principalmente pelos desenvolvedores de serviços online que usam os serviços de armazenamento do Azure e os profissionais de ti responsáveis pelo gerenciamento desse serviços online. Os objetivos deste guia são:

* Para ajudá-lo a manter a integridade e o desempenho de suas contas de armazenamento do Azure.
* Para fornecer os processos e as ferramentas necessárias para ajudá-lo a decidir se um problema ou problema em um aplicativo está relacionado ao armazenamento do Azure.
* Para fornecer diretrizes acionáveis para resolver problemas relacionados ao armazenamento do Azure.

### <a name="how-this-guide-is-organized"></a>Como este guia é organizado
A seção "[monitorando seu serviço de armazenamento]" descreve como monitorar a integridade e o desempenho de seus serviços de armazenamento do Azure usando métricas de análise de armazenamento do Azure (métricas de armazenamento).

A seção "[Diagnosticando problemas de armazenamento]" descreve como diagnosticar problemas usando o log de análise de armazenamento do Azure (log de armazenamento). Ele também descreve como habilitar o log do lado do cliente usando as instalações em uma das bibliotecas de cliente, como a biblioteca de cliente de armazenamento para .NET ou o SDK do Azure para Java.

A seção "[rastreamento de ponta a ponta]" descreve como você pode correlacionar as informações contidas em vários arquivos de log e dados de métricas.

A seção "[Orientação na resolução de problemas]" fornece diretrizes de solução de problemas para alguns dos problemas comuns relacionados ao armazenamento que você pode encontrar.

Os "[Appendices]" incluem informações sobre como usar outras ferramentas, como Wireshark e Netmon, para analisar dados de pacote de rede, o Fiddler para analisar mensagens HTTP/HTTPS e o Microsoft Message Analyzer para correlacionar os dados de log.

## <a name="monitoring-your-storage-service"></a>Monitorando seu serviço de armazenamento
Se você estiver familiarizado com o monitoramento de desempenho do Windows, poderá considerar as métricas de armazenamento como sendo um equivalente do armazenamento do Azure dos contadores do monitor de desempenho do Windows. Em métricas de armazenamento, você encontrará um conjunto abrangente de métricas (contadores na terminologia do monitor de desempenho do Windows), como a disponibilidade do serviço, o número total de solicitações de serviço ou a porcentagem de solicitações bem-sucedidas para o serviço. Para obter uma lista completa das métricas disponíveis, consulte [análise de armazenamento esquema de tabela](https://msdn.microsoft.com/library/azure/hh343264.aspx)de métricas. Você pode especificar se deseja que o serviço de armazenamento colete e agregue métricas a cada hora ou a cada minuto. Para obter mais informações sobre como habilitar métricas e monitorar suas contas de armazenamento, consulte Habilitando métricas de [armazenamento e exibindo dados](https://go.microsoft.com/fwlink/?LinkId=510865)de métricas.

Você pode escolher quais métricas por hora deseja exibir no [portal do Azure](https://portal.azure.com) e configurar regras que notificam os administradores por email sempre que uma métrica por hora excede um limite específico. Para obter mais informações, consulte [receber notificações de alerta](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

O serviço de armazenamento coleta métricas usando um melhor esforço, mas pode não registrar cada operação de armazenamento.

No portal do Azure, você pode exibir métricas como disponibilidade, total de solicitações e números de latência média para uma conta de armazenamento. Uma regra de notificação também foi configurada para alertar um administrador se a disponibilidade cair abaixo de um determinado nível. Da exibição desses dados, uma área possível para investigação é a porcentagem de sucesso do serviço tabela abaixo de 100% (para obter mais informações, consulte a seção "as[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]").

Você deve monitorar continuamente seus aplicativos do Azure para garantir que eles estejam íntegros e funcionando conforme o esperado:

* Estabelecer algumas métricas de linha de base para o aplicativo que permitirá que você compare os dados atuais e identifique quaisquer alterações significativas no comportamento do armazenamento do Azure e do seu aplicativo. Os valores de suas métricas de linha de base serão, em muitos casos, específicos ao aplicativo e você deverá estabelecer isso quando estiver testando o desempenho de seu aplicativo.
* Gravar métricas de minuto e usá-las para monitorar ativamente erros inesperados e anomalias, como picos de contagens de erros ou taxas de solicitação.
* Gravar métricas por hora e usá-las para monitorar valores médios, como contagens médias de erros e taxas de solicitação.
* Investigar possíveis problemas usando as ferramentas de diagnóstico, conforme discutido posteriormente na seção "[Diagnosticando problemas de armazenamento]".

Os gráficos na imagem a seguir ilustram como a média que ocorre para métricas por hora podem ocultar picos na atividade. As métricas por hora aparecem para mostrar uma taxa constante de solicitações, enquanto as métricas de minuto revelam as flutuações que estão realmente ocorrendo.

![][3]

O restante desta seção descreve quais métricas você deve monitorar e por quê.

### <a name="monitoring-service-health"></a>Monitorando a integridade do serviço
Você pode usar o [portal do Azure](https://portal.azure.com) para exibir a integridade do serviço de armazenamento (e outros serviços do Azure) em todas as regiões do Azure em todo o mundo. O monitoramento permite que você veja imediatamente se um problema fora do seu controle está afetando o serviço de armazenamento na região que você usa para seu aplicativo.

O [portal do Azure](https://portal.azure.com) também pode fornecer notificações de incidentes que afetam os vários serviços do Azure.
Nota: Essas informações estavam disponíveis anteriormente, juntamente com os dados históricos, no [painel de serviços do Azure](https://status.azure.com).

Embora o [portal do Azure](https://portal.azure.com) colete informações de integridade de dentro dos data centers do Azure (monitoramento interno), você também pode considerar a adoção de uma abordagem externa para gerar transações sintéticas que acessam periodicamente sua web hospedada no Azure aplicativo de vários locais. Os serviços oferecidos pelo [dynaTrace](https://www.dynatrace.com/en/synthetic-monitoring) e pelo Application insights para DevOps do Azure são exemplos dessa abordagem. Para obter mais informações sobre Application insights do Azure DevOps, consulte o apêndice["Apêndice 5: Monitoramento com o Application Insights do Azure](#appendix-5)DevOps. "

### <a name="monitoring-capacity"></a>Capacidade de monitoramento
As métricas de armazenamento armazenam somente métricas de capacidade para o serviço blob porque os BLOBs normalmente se descontam para a maior proporção de dados armazenados (no momento da gravação, não é possível usar as métricas de armazenamento para monitorar a capacidade de suas tabelas e filas). Você poderá encontrar esses dados na tabela **$MetricsCapacityBlob** se tiver habilitado o monitoramento para o serviço BLOB. As métricas de armazenamento registram esses dados uma vez por dia e você pode usar o valor de **RowKey** para determinar se a linha contém uma entidade relacionada a dados de usuário ( **dados**de valor) ou dados de análise (valor **Analytics**). Cada entidade armazenada contém informações sobre a quantidade de armazenamento usada (**capacidade** medida em bytes) e o número atual de contêineres (**ContainerCount**) e blobs (objectCount) em uso na conta de armazenamento. Para obter mais informações sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob** , consulte [análise de armazenamento esquema de tabela](https://msdn.microsoft.com/library/azure/hh343264.aspx)de métricas.

> [!NOTE]
> Você deve monitorar esses valores para obter um aviso antecipado de que está se aproximando dos limites de capacidade de sua conta de armazenamento. No portal do Azure, você pode adicionar regras de alerta para notificá-lo se o uso de armazenamento agregado exceder ou cair abaixo dos limites que você especificar.
>
>

Para obter ajuda para estimar o tamanho de vários objetos de armazenamento, como BLOBs, consulte a postagem no blog [noções básicas sobre cobrança de armazenamento do Azure – largura de banda, transações e capacidade](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorando a disponibilidade
Você deve monitorar a disponibilidade dos serviços de armazenamento em sua conta de armazenamento monitorando o valor na coluna **disponibilidade** nas tabelas de métricas por hora ou minuto – **$MetricsHourPrimaryTransactionsBlob**, **$ MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable** **$MetricsMinutePrimaryTransactionsQueue** , **$MetricsCapacityBlob**. A coluna **disponibilidade** contém um valor percentual que indica a disponibilidade do serviço ou a operação de API representada pela linha (o **RowKey** mostra se a linha contém métricas para o serviço como um todo ou para uma operação de API específica) .

Qualquer valor menor que 100% indica que algumas solicitações de armazenamento estão falhando. Você pode ver por que eles estão falhando examinando as outras colunas nos dados de métricas que mostram os números de solicitações com tipos de erro diferentes, como **ServerTimeoutError**. Você deve esperar que a **disponibilidade** fique temporariamente abaixo de 100% por motivos como tempos limite de servidor transitório enquanto o serviço move partições para uma solicitação de balanceamento de carga melhor; a lógica de repetição no aplicativo cliente deve lidar com essas condições intermitentes. O artigo [análise de armazenamento mensagens de status e operações registradas](https://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que as métricas de armazenamento incluem em seu cálculo de **disponibilidade** .

No [portal do Azure](https://portal.azure.com), você pode adicionar regras de alerta para notificá-lo se a **disponibilidade** de um serviço estiver abaixo de um limite especificado.

A seção "[Orientação na resolução de problemas]" deste guia descreve alguns dos problemas de serviço de armazenamento comuns relacionados à disponibilidade.

### <a name="monitoring-performance"></a>Monitorando o desempenho
Para monitorar o desempenho dos serviços de armazenamento, você pode usar as seguintes métricas das tabelas de métricas por hora e minuto.

* Os valores nas colunas **AverageE2ELatency** e **AverageServerLatency** mostram o tempo médio que o serviço de armazenamento ou o tipo de operação de API está demorando para processar solicitações. **AverageE2ELatency** é uma medida de latência de ponta a ponta que inclui o tempo necessário para ler a solicitação e enviar a resposta além do tempo necessário para processar a solicitação (portanto, inclui a latência de rede quando a solicitação atinge o armazenamento serviço); **AverageServerLatency** é uma medida apenas do tempo de processamento e, portanto, exclui qualquer latência de rede relacionada à comunicação com o cliente. Consulte a seção "as[As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]" posteriormente neste guia para obter uma discussão sobre por que pode haver uma diferença significativa entre esses dois valores.
* Os valores nas colunas **TotalIngress** e **TotalEgress** mostram a quantidade total de dados, em bytes, chegando e saindo do serviço de armazenamento ou por meio de um tipo de operação de API específica.
* Os valores na coluna **TotalRequests** mostram o número total de solicitações que o serviço de armazenamento da operação de API está recebendo. **TotalRequests** é o número total de solicitações que o serviço de armazenamento recebe.

Normalmente, você monitorará alterações inesperadas em qualquer um desses valores como um indicador de que você tem um problema que requer investigação.

No [portal do Azure](https://portal.azure.com), você pode adicionar regras de alerta para notificá-lo se qualquer uma das métricas de desempenho desse serviço cair abaixo ou exceder um limite especificado.

A seção "[Orientação na resolução de problemas]" deste guia descreve alguns dos problemas de serviço de armazenamento comuns relacionados ao desempenho.

## <a name="diagnosing-storage-issues"></a>Diagnosticando problemas de armazenamento
Há várias maneiras que você pode estar atento a um problema ou problema em seu aplicativo, incluindo:

* Uma grande falha que faz com que o aplicativo falhe ou pare de funcionar.
* Alterações significativas de valores de linha de base nas métricas que você está monitorando, conforme descrito na seção anterior "[monitorando seu serviço de armazenamento]".
* Relatórios de usuários de seu aplicativo que alguma operação específica não foi concluída conforme o esperado ou que algum recurso não está funcionando.
* Erros gerados em seu aplicativo que aparecem em arquivos de log ou por meio de algum outro método de notificação.

Normalmente, os problemas relacionados aos serviços de armazenamento do Azure se enquadram em uma das quatro categorias amplas:

* Seu aplicativo tem um problema de desempenho, relatado por seus usuários ou revelado por alterações nas métricas de desempenho.
* Há um problema com a infraestrutura de armazenamento do Azure em uma ou mais regiões.
* Seu aplicativo está encontrando um erro, relatado por seus usuários ou revelado por um aumento em uma das métricas de contagem de erros que você monitora.
* Durante o desenvolvimento e o teste, você pode estar usando o emulador de armazenamento local; Você pode encontrar alguns problemas relacionados especificamente ao uso do emulador de armazenamento.

As seções a seguir descrevem as etapas que você deve seguir para diagnosticar e solucionar problemas em cada uma dessas quatro categorias. A seção "[Orientação na resolução de problemas]" mais adiante neste guia fornece mais detalhes para alguns problemas comuns que você pode encontrar.

### <a name="service-health-issues"></a>Problemas de integridade do serviço
Os problemas de integridade do serviço normalmente estão fora do seu controle. O [portal do Azure](https://portal.azure.com) fornece informações sobre quaisquer problemas contínuos com os serviços do Azure, incluindo serviços de armazenamento. Se você tiver optado pelo armazenamento com redundância geográfica com acesso de leitura quando criou sua conta de armazenamento, se seus dados ficarem indisponíveis no local primário, seu aplicativo poderá mudar temporariamente para a cópia somente leitura no local secundário. Para ler a partir do secundário, seu aplicativo deve ser capaz de alternar entre usar os locais de armazenamento primário e secundário e ser capaz de trabalhar em um modo de funcionalidade reduzido com dados somente leitura. As bibliotecas de cliente de armazenamento do Azure permitem que você defina uma política de repetição que pode ler do armazenamento secundário caso uma leitura do armazenamento primário falhe. Seu aplicativo também precisa estar ciente de que os dados no local secundário são eventualmente consistentes. Para obter mais informações, consulte a postagem do blog [Opções de redundância de armazenamento do Azure e armazenamento com redundância geográfica com acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de desempenho
O desempenho de uma aplicação pode ser subjetivo, sobretudo na perspetiva do utilizador. Por conseguinte, é importante ter métricas de linha base disponíveis para o ajudar a identificar onde poderá existir um problema de desempenho. Muitos fatores podem afetar o desempenho de um serviço de armazenamento do Azure da perspectiva do aplicativo cliente. Esses fatores podem operar no serviço de armazenamento, no cliente ou na infraestrutura de rede; Portanto, é importante ter uma estratégia para identificar a origem do problema de desempenho.

Depois de identificar o local provável da causa do problema de desempenho das métricas, você poderá usar os arquivos de log para encontrar informações detalhadas para diagnosticar e solucionar o problema ainda mais.

A seção "[Orientação na resolução de problemas]" mais adiante neste guia fornece mais informações sobre alguns problemas comuns relacionados ao desempenho que você pode encontrar.

### <a name="diagnosing-errors"></a>Diagnosticando erros
Os usuários do seu aplicativo podem notificá-lo de erros relatados pelo aplicativo cliente. As métricas de armazenamento também registram contagens de tipos de erro diferentes de seus serviços de armazenamento, como **NetworkError**, **ClientTimeoutError**ou **AuthorizationError**. Enquanto as métricas de armazenamento registram apenas contagens de tipos de erro diferentes, você pode obter mais detalhes sobre solicitações individuais examinando os logs de servidor, do lado do cliente e da rede. Normalmente, o código de status HTTP retornado pelo serviço de armazenamento fornecerá uma indicação de por que a solicitação falhou.

> [!NOTE]
> Lembre-se de que você deve esperar ver alguns erros intermitentes: por exemplo, erros devido a condições transitórias de rede ou erros de aplicativo.
>
>

Os recursos seguintes são úteis para compreender os códigos de estado e erro relacionados com o armazenamento:

* [Códigos de erro comuns da API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Códigos de Erro do Serviço de Blobs](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Códigos de erro do serviço fila](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Códigos de erro do serviço tabela](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Códigos de erro do serviço de arquivo](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas do emulador de armazenamento
O SDK do Azure inclui um emulador de armazenamento que você pode executar em uma estação de trabalho de desenvolvimento. Esse emulador simula a maior parte do comportamento dos serviços de armazenamento do Azure e é útil durante o desenvolvimento e o teste, permitindo que você execute aplicativos que usam os serviços de armazenamento do Azure sem a necessidade de uma assinatura do Azure e uma conta de armazenamento do Azure.

A seção "[Orientação na resolução de problemas]" deste guia descreve alguns problemas comuns encontrados usando o emulador de armazenamento.

### <a name="storage-logging-tools"></a>Ferramentas de log de armazenamento
O log de armazenamento fornece registro em log de solicitações de armazenamento no lado do servidor em sua conta de armazenamento do Azure. Para obter mais informações sobre como habilitar o registro em log do lado do servidor e acessar os dados de log, consulte Habilitando o [log de armazenamento e acessando dados de log](https://go.microsoft.com/fwlink/?LinkId=510867).

A biblioteca de cliente de armazenamento para .NET permite que você colete dados de log do lado do cliente relacionados às operações de armazenamento executadas pelo seu aplicativo. Para obter mais informações, veja [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868) (Registo do Lado do Cliente com a Biblioteca de Clientes de Armazenamento .NET).

> [!NOTE]
> Em algumas circunstâncias (como falhas de autorização SAS), um usuário pode relatar um erro para o qual você pode encontrar nenhum dado de solicitação nos logs de armazenamento do lado do servidor. Você pode usar os recursos de log da biblioteca de cliente de armazenamento para investigar se a causa do problema está no cliente ou usar as ferramentas de monitoramento de rede para investigar a rede.
>
>

### <a name="using-network-logging-tools"></a>Usando as ferramentas de log de rede
Você pode capturar o tráfego entre o cliente e o servidor para fornecer informações detalhadas sobre os dados que o cliente e o servidor estão trocando e as condições de rede subjacentes. As ferramentas de log de rede úteis incluem:

* O [Fiddler](https://www.telerik.com/fiddler) é um proxy de depuração Web gratuito que permite que você examine os cabeçalhos e os dados de conteúdo de mensagens de solicitação e resposta http e HTTPS. Para obter mais informações, [consulte o apêndice 1: Usando o Fiddler para capturar o tráfego](#appendix-1)http e HTTPS.
* [Monitor de rede da Microsoft (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) e [Wireshark](https://www.wireshark.org/) são analisadores de protocolo de rede gratuitos que permitem que você exiba informações detalhadas de pacote para uma ampla gama de protocolos de rede. Para obter mais informações sobre o Wireshark,[consulte o "apêndice 2: Usando o Wireshark para capturar o](#appendix-2)tráfego de rede ".
* O Microsoft Message Analyzer é uma ferramenta da Microsoft que substitui o Netmon e que, além da captura de dados de pacotes de rede, ajuda você a exibir e analisar os dados de log capturados de outras ferramentas. Para obter mais informações, consulte[o "Apêndice 3: Usando o Microsoft Message Analyzer para capturar o](#appendix-3)tráfego de rede ".
* Se você quiser executar um teste de conectividade básico para verificar se o computador cliente pode se conectar ao serviço de armazenamento do Azure pela rede, não é possível fazer isso usando a ferramenta de **ping** padrão no cliente. No entanto, você pode usar a [ferramenta **tcping** ](https://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de log do log de armazenamento e da biblioteca de cliente de armazenamento serão suficientes para diagnosticar um problema, mas em alguns cenários, talvez seja necessário obter informações mais detalhadas que essas ferramentas de log de rede podem fornecer. Por exemplo, usar o Fiddler para exibir mensagens HTTP e HTTPS permite exibir dados de cabeçalho e de carga enviados de e para os serviços de armazenamento, o que permite que você examine como um aplicativo cliente tenta operações de armazenamento novamente. Os analisadores de protocolo, como o Wireshark, operam no nível de pacote, permitindo que você exiba dados TCP, o que permite solucionar problemas de conectividade e pacotes perdidos. O analisador de mensagem pode operar em camadas HTTP e TCP.

## <a name="end-to-end-tracing"></a>Rastreamento de ponta a ponta
O rastreamento de ponta a ponta usando uma variedade de arquivos de log é uma técnica útil para investigar possíveis problemas. Você pode usar as informações de data/hora de seus dados de métrica como uma indicação de onde começar a procurar nos arquivos de log para obter as informações detalhadas que o ajudarão a solucionar o problema.

### <a name="correlating-log-data"></a>Correlacionando dados de log
Ao exibir logs de aplicativos cliente, rastreamentos de rede e log de armazenamento do lado do servidor, é essencial ser capaz de correlacionar as solicitações nos diferentes arquivos de log. Os arquivos de log incluem um número de campos diferentes que são úteis como identificadores de correlação. A ID de solicitação do cliente é o campo mais útil a ser usado para correlacionar entradas em logs diferentes. No entanto, às vezes, pode ser útil usar a ID de solicitação do servidor ou os carimbos de data/hora. As seções a seguir fornecem mais detalhes sobre essas opções.

### <a name="client-request-id"></a>ID de solicitação do cliente
A biblioteca de cliente de armazenamento gera automaticamente uma ID de solicitação de cliente exclusiva para cada solicitação.

* No log do lado do cliente que a biblioteca de cliente de armazenamento cria, a ID de solicitação do cliente é exibida no campo **ID de solicitação do cliente** de cada entrada de log relacionada à solicitação.
* Em um rastreamento de rede como um capturado pelo Fiddler, a ID de solicitação do cliente é visível em mensagens de solicitação como o valor do cabeçalho http **x-MS-Client-Request-ID** .
* No log de armazenamento do lado do servidor, a ID de solicitação do cliente aparece na coluna ID de solicitação do cliente.

> [!NOTE]
> É possível que várias solicitações compartilhem a mesma ID de solicitação de cliente, pois o cliente pode atribuir esse valor (embora a biblioteca de cliente de armazenamento atribua um novo valor automaticamente). Quando o cliente tenta novamente, todas as tentativas compartilham a mesma ID de solicitação do cliente. No caso de um lote enviado do cliente, o lote tem uma única ID de solicitação de cliente.
>
>

### <a name="server-request-id"></a>ID de solicitação do servidor
O serviço de armazenamento gera automaticamente IDs de solicitação do servidor.

* No log de armazenamento do lado do servidor, a ID de solicitação do servidor aparece na coluna de **cabeçalho ID da solicitação** .
* Em um rastreamento de rede como um capturado pelo Fiddler, a ID de solicitação do servidor aparece em mensagens de resposta como o valor do cabeçalho http **x-MS-Request-ID** .
* No log do lado do cliente que a biblioteca de cliente de armazenamento cria, a ID de solicitação do servidor é exibida na coluna **texto da operação** para a entrada de log mostrando os detalhes da resposta do servidor.

> [!NOTE]
> O serviço de armazenamento sempre atribui uma ID de solicitação de servidor exclusiva a cada solicitação recebida, para que cada tentativa de repetição do cliente e de cada operação incluída em um lote tenha uma ID de solicitação de servidor exclusiva.
>
>

Se a biblioteca de cliente de armazenamento gerar uma StorageException no cliente, a propriedade **RequestInformation** conterá um objeto **RequestResult** que inclui uma propriedade de um **pedido** . Você também pode acessar um objeto **RequestResult** de uma instância do **OperationContext** .

O exemplo de código abaixo demonstra como definir um valor de **ClientRequestId** personalizado anexando um objeto **OperationContext** à solicitação ao serviço de armazenamento. Ele também mostra como recuperar o valor de **ServerRequestId** da mensagem de resposta.

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

### <a name="timestamps"></a>Carimbos
Você também pode usar carimbos de data/hora para localizar entradas de log relacionadas, mas tenha cuidado com qualquer distorção de relógio entre o cliente e o servidor que podem existir. Pesquise mais ou menos 15 minutos para corresponder entradas do lado do servidor com base no carimbo de data/hora no cliente. Lembre-se de que os metadados de BLOB para os blobs que contêm métricas indicam o intervalo de tempo para as métricas armazenadas no BLOB. Esse intervalo de tempo será útil se você tiver muitos blobs de métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Diretrizes de solução de problemas
Esta seção irá ajudá-lo com o diagnóstico e a solução de problemas de alguns dos problemas comuns que seu aplicativo pode encontrar ao usar os serviços de armazenamento do Azure. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Árvore de decisão de solução de problemas**

---
Seu problema está relacionado ao desempenho de um dos serviços de armazenamento?

* [As métricas apresentam uma AverageE2ELatency alta e uma AverageServerLatency baixa]
* [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
* [As métricas apresentam uma AverageServerLatency alta]
* [Ocorrem atrasos inesperados na entrega de mensagens numa fila]

---
Seu problema está relacionado à disponibilidade de um dos serviços de armazenamento?

* [As métricas apresentam um aumento do PercentThrottlingError]
* [As métricas apresentam um aumento do PercentTimeoutError]
* [As métricas apresentam um aumento do PercentNetworkError]

---
 O aplicativo cliente está recebendo uma resposta HTTP 4XX (como 404) de um serviço de armazenamento?

* [O cliente está a receber mensagens HTTP 403 (proibido)]
* [O cliente está a receber mensagens HTTP 404 (não for encontrado)]
* [O cliente está a receber mensagens de HTTP 409 (conflito)]

---
[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]

---
[As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]

---
[Você está enfrentando reinicializações inesperadas de máquinas virtuais que têm um grande número de VHDs anexados]

---
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]

---
[Pode encontrar problemas ao instalar o Azure SDK para .NET]

---
[Tem um problema com um serviço de armazenamento diferente]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency
A ilustração abaixo da ferramenta de monitoramento de [portal do Azure](https://portal.azure.com) mostra um exemplo em que o **AverageE2ELatency** é significativamente maior do que o **AverageServerLatency**.

![][4]

O serviço de armazenamento calcula apenas a métrica **AverageE2ELatency** para solicitações bem-sucedidas e, ao contrário de **AverageServerLatency**, inclui o tempo que o cliente leva para enviar os dados e receber a confirmação do serviço de armazenamento. Portanto, uma diferença entre **AverageE2ELatency** e **AverageServerLatency** pode ser devido ao aplicativo cliente estar lento para responder ou devido a condições na rede.

> [!NOTE]
> Você também pode exibir **E2ELatency** e **ServerLatency** para operações de armazenamento individuais nos dados de log de log de armazenamento.
>
>

#### <a name="investigating-client-performance-issues"></a>Investigando problemas de desempenho do cliente
As possíveis razões para o cliente responder lentamente incluem ter um número limitado de conexões ou threads disponíveis, ou ter poucos recursos, como CPU, memória ou largura de banda de rede. Talvez seja possível resolver o problema modificando o código do cliente para ser mais eficiente (por exemplo, usando chamadas assíncronas para o serviço de armazenamento) ou usando uma máquina virtual maior (com mais núcleos e mais memória).

Para os serviços tabela e fila, o algoritmo Nagle também pode causar alta **AverageE2ELatency** em comparação com **AverageServerLatency**: para obter mais informações, consulte o [algoritmo do post Nagle não é amigável em direção a pequenas solicitações](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Você pode desabilitar o algoritmo Nagle no código usando a classe **ServicePointManager** no namespace **System.net** . Você deve fazer isso antes de fazer qualquer chamada para os serviços de tabela ou fila em seu aplicativo, pois isso não afeta as conexões que já estão abertas. O exemplo a seguir vem do método **Application_Start** em uma função de trabalho.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Você deve verificar os logs do lado do cliente para ver quantas solicitações seu aplicativo cliente está enviando e verificar afunilamentos de desempenho relacionados ao .NET geral em seu cliente, como CPU, coleta de lixo do .NET, utilização de rede ou memória. Como ponto de partida para a solução de problemas de aplicativos cliente .NET, consulte [depuração, rastreamento e criação de perfil](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigando problemas de latência de rede
Normalmente, a alta latência de ponta a ponta causada pela rede é devido a condições transitórias. Você pode investigar os problemas de rede transitórios e persistentes, como pacotes descartados usando ferramentas como o Wireshark ou o Microsoft Message Analyzer.

Para obter mais informações sobre como usar o Wireshark para solucionar problemas[Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]tráfego de rede. "

Para obter mais informações sobre como usar o Microsoft Message Analyzer para solucionar problemas[Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]tráfego de rede. "

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas mostram baixa AverageE2ELatency e baixa AverageServerLatency, mas o cliente está sofrendo alta latência
Nesse cenário, a causa mais provável é um atraso nas solicitações de armazenamento que atingem o serviço de armazenamento. Você deve investigar por que as solicitações do cliente não estão fazendo isso no serviço BLOB.

Um motivo possível para o cliente atrasar o envio de solicitações é que há um número limitado de conexões ou threads disponíveis.

Verifique também se o cliente está executando várias tentativas e investigue o motivo, se for. Para determinar se o cliente está executando várias tentativas, você pode:

* Examine os logs de Análise de Armazenamento. Se várias repetições estiverem ocorrendo, você verá várias operações com a mesma ID de solicitação de cliente, mas com IDs de solicitação de servidor diferentes.
* Examine os logs do cliente. O log detalhado indicará que houve uma nova tentativa.
* Depure seu código e verifique as propriedades do objeto **OperationContext** associado à solicitação. Se a operação for repetida, a propriedade **RequestResults** incluirá várias IDs de solicitação de servidor exclusivas. Você também pode verificar as horas de início e de término de cada solicitação. Para obter mais informações, consulte o exemplo de código na seção [ID de solicitação do servidor].

Se não houver nenhum problema no cliente, investigue possíveis problemas de rede, como perda de pacotes. Você pode usar ferramentas como o Wireshark ou o Microsoft Message Analyzer para investigar problemas de rede.

Para obter mais informações sobre como usar o Wireshark para solucionar problemas[Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]tráfego de rede. "

Para obter mais informações sobre como usar o Microsoft Message Analyzer para solucionar problemas[Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]tráfego de rede. "

### <a name="metrics-show-high-AverageServerLatency"></a>As métricas mostram alta AverageServerLatency
No caso de alta **AverageServerLatency** para solicitações de download de BLOB, você deve usar os logs de log de armazenamento para ver se há solicitações repetidas para o mesmo BLOB (ou conjunto de BLOBs). Para solicitações de upload de BLOB, você deve investigar o tamanho de bloco que o cliente está usando (por exemplo, blocos com menos de 64 K de tamanho podem resultar em sobrecargas, a menos que as leituras também estejam em blocos inferiores a 64 K) e se vários clientes estiverem carregando blocos para o mesmo blob em para LELO. Você também deve verificar as métricas por minuto em busca de picos no número de solicitações que resultam na excedente os destinos de escalabilidade por segundo: Consulte também "as[As métricas apresentam um aumento do PercentTimeoutError]".

Se você estiver vendo alta **AverageServerLatency** para solicitações de download de blob quando houver solicitações repetidas no mesmo BLOB ou conjunto de BLOBs, deverá considerar armazenar esses BLOBs em cache usando o cache do Azure ou a CDN (rede de distribuição de conteúdo) do Azure. Para solicitações de upload, você pode melhorar a taxa de transferência usando um tamanho de bloco maior. Para consultas a tabelas, também é possível implementar o cache do lado do cliente em clientes que executam as mesmas operações de consulta e onde os dados não são alterados com frequência.

Valores de alta **AverageServerLatency** também podem ser um sintoma de tabelas ou consultas mal projetadas que resultam em operações de verificação ou que seguem o antipadrão Append/preceder. Para obter mais informações, consulte "as[As métricas apresentam um aumento do PercentThrottlingError]".

> [!NOTE]
> Você pode encontrar uma lista de verificação de desempenho abrangente da lista de verificação aqui: [Lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Você está enfrentando atrasos inesperados na entrega de mensagens em uma fila
Se você estiver enfrentando um atraso entre a hora em que um aplicativo adiciona uma mensagem a uma fila e o tempo que ela torna disponível para leitura da fila, execute as seguintes etapas para diagnosticar o problema:

* Verifique se o aplicativo está adicionando as mensagens à fila com êxito. Verifique se o aplicativo não está repetindo o Método AddMessage várias vezes antes de ter sucesso. Os logs da biblioteca de cliente de armazenamento mostrarão novas tentativas repetidas de operações de armazenamento.
* Verifique se não há nenhuma distorção de relógio entre a função de trabalho que adiciona a mensagem à fila e a função de trabalho que lê a mensagem da fila que a torna exibida como se houver um atraso no processamento.
* Verifique se a função de trabalho que lê as mensagens da fila está falhando. Se um cliente de fila chamar o método **GetMessage** , mas não responder com uma confirmação, a mensagem permanecerá invisível na fila até que o período de **invisibilityTimeout** expire. Neste ponto, a mensagem fica disponível para processamento novamente.
* Verifique se o comprimento da fila está crescendo ao longo do tempo. Isso pode ocorrer se você não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros trabalhadores estão colocando na fila. Verifique também as métricas para ver se as solicitações de exclusão estão falhando e se o remover da fila as mensagens, o que pode indicar tentativas de falha repetidas para excluir a mensagem.
* Examine os logs de log de armazenamento para qualquer operação de fila que tenha mais de **E2ELatency** e valores de **ServerLatency** esperados em um período de tempo maior do que o normal.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>As métricas mostram um aumento em PercentThrottlingError
Os erros de limitação ocorrem quando você excede as metas de escalabilidade de um serviço de armazenamento. O serviço de armazenamento limita-se a garantir que nenhum cliente ou locatário possa usar o serviço às custas de outros. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre metas de escalabilidade para contas de armazenamento e metas de desempenho para partições nas contas de armazenamento.

Se a métrica **PercentThrottlingError** mostrar um aumento na porcentagem de solicitações que estão falhando com um erro de limitação, você precisará investigar um dos dois cenários:

* [Aumento transitório PercentThrottlingError]
* [Aumento permanente PercentThrottlingError erro]

Um aumento em **PercentThrottlingError** geralmente ocorre ao mesmo tempo que um aumento no número de solicitações de armazenamento ou quando você está inicialmente testando seu aplicativo. Isso também pode se manifestar no cliente como mensagens de status HTTP "503 Server Busy" ou "500 tempo limite de operação" de operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório em PercentThrottlingError
Se você estiver vendo picos no valor de **PercentThrottlingError** que coincidem com períodos de alta atividade para o aplicativo, implemente uma estratégia de retirada exponencial (não linear) para novas tentativas no cliente. As repetições de retirada reduzem a carga imediata na partição e ajudam seu aplicativo a suavizar picos de tráfego. Para obter mais informações sobre como implementar políticas de repetição usando a biblioteca de cliente de armazenamento, consulte o [namespace Microsoft. Azure. Storage. RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Você também pode ver picos no valor de **PercentThrottlingError** que não coincidem com períodos de alta atividade para o aplicativo: a causa mais provável aqui é o serviço de armazenamento que move partições para melhorar o balanceamento de carga.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente no erro de PercentThrottlingError
Se você estiver vendo um valor consistentemente alto para **PercentThrottlingError** seguindo um aumento permanente em seus volumes de transação ou quando estiver executando os testes de carga inicial em seu aplicativo, será necessário avaliar como seu aplicativo é usar partições de armazenamento e se está se aproximando das metas de escalabilidade de uma conta de armazenamento. Por exemplo, se você estiver vendo erros de limitação em uma fila (que conta como uma única partição), deverá considerar o uso de filas adicionais para distribuir as transações entre várias partições. Se você estiver vendo erros de limitação em uma tabela, precisará considerar o uso de um esquema de particionamento diferente para distribuir suas transações entre várias partições usando um intervalo maior de valores de chave de partição. Uma causa comum desse problema é o antipadrão preceder/acrescentar em que você seleciona a data como a chave de partição e, em seguida, todos os dados em um determinado dia são gravados em uma partição: sob carga, isso pode resultar em um afunilamento de gravação. Considere um design de particionamento diferente ou avalie se usar o armazenamento de BLOBs pode ser uma solução melhor. Verifique também se a limitação está ocorrendo como resultado de picos em seu tráfego e investigue maneiras de suavizar seu padrão de solicitações.

Se você distribuir suas transações em várias partições, ainda deverá estar atento aos limites de escalabilidade definidos para a conta de armazenamento. Por exemplo, se você usou dez filas, cada uma processando o máximo de 2.000 1 KB de mensagens por segundo, você estará no limite geral de 20.000 mensagens por segundo para a conta de armazenamento. Se você precisar processar mais de 20.000 entidades por segundo, considere o uso de várias contas de armazenamento. Você também deve ter em mente que o tamanho de suas solicitações e entidades tem um impacto quando o serviço de armazenamento limita seus clientes: se você tiver mais solicitações e entidades, você poderá ser limitado mais cedo.

O design de consulta ineficiente também pode fazer com que você atinja os limites de escalabilidade para partições de tabela. Por exemplo, uma consulta com um filtro que seleciona apenas um percentual das entidades em uma partição, mas que examina todas as entidades em uma partição precisará acessar cada entidade. Cada entidade lida será contada para o número total de transações nessa partição; Portanto, você pode acessar facilmente as metas de escalabilidade.

> [!NOTE]
> Seu teste de desempenho deve revelar qualquer design de consulta ineficiente em seu aplicativo.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>As métricas mostram um aumento em PercentTimeoutError
Suas métricas mostram um aumento no **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um alto volume de mensagens de status HTTP de "tempo limite de operação 500" de operações de armazenamento.

> [!NOTE]
> Você poderá ver erros de tempo limite temporariamente à medida que o serviço de armazenamento balancear a carga de solicitações movendo uma partição para um novo servidor.
>
>

A métrica **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**e **SASServerTimeoutError**.

Os tempos limite do servidor são causados por um erro no servidor. Os tempos limite do cliente acontecem porque uma operação no servidor excedeu o tempo limite especificado pelo cliente; por exemplo, um cliente que usa a biblioteca de cliente de armazenamento pode definir um tempo limite para uma operação usando a propriedade **servertimeou** da classe **QueueRequestOptions** .

Os tempos limite do servidor indicam um problema com o serviço de armazenamento que requer mais investigação. Você pode usar métricas para ver se está atingindo os limites de escalabilidade para o serviço e para identificar qualquer pico no tráfego que possa estar causando esse problema. Se o problema for intermitente, pode ser devido à atividade de balanceamento de carga no serviço. Se o problema for persistente e não for causado por seu aplicativo atingir os limites de escalabilidade do serviço, você deverá gerar um problema de suporte. Para tempos limite do cliente, você deve decidir se o tempo limite está definido como um valor apropriado no cliente e alterar o valor de tempo limite definido no cliente ou investigar como você pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizando sua tabela consulta ou redução do tamanho de suas mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>As métricas mostram um aumento em PercentNetworkError
Suas métricas mostram um aumento no **PercentNetworkError** para um dos seus serviços de armazenamento. A métrica **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError**e **SASNetworkError**. Isso ocorre quando o serviço de armazenamento detecta um erro de rede quando o cliente faz uma solicitação de armazenamento.

A causa mais comum desse erro é a desconexão de um cliente antes do tempo limite expirar no serviço de armazenamento. Investigue o código em seu cliente para entender por que e quando o cliente se desconecta do serviço de armazenamento. Você também pode usar o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. Essas ferramentas são descritas nos [Appendices].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está recebendo mensagens HTTP 403 (proibido)
Se a aplicação cliente estiver a gerar erros HTTP 403 (Proibido), uma das causas prováveis é o cliente estar a utilizar uma Assinatura de Acesso Partilhado (SAS) expirada quando envia um pedido de armazenamento (embora outras causas possíveis incluam distorção do relógio, chaves inválidas e cabeçalhos vazios). Se uma chave de SAS expirada for a causa, não verá entradas nos dados do Registo de Armazenamento do lado do servidor. A tabela a seguir mostra um exemplo do log do lado do cliente gerado pela biblioteca de cliente de armazenamento que ilustra esse problema ocorrendo:

| Source | Verbosidade | Verbosidade | ID de pedido do cliente | Texto da operação |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |Iniciando a operação com o local primário por modo de local PrimaryOnly. |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |Iniciando solicitação síncrona para<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |Aguardando resposta. |
| Microsoft.Azure.Storage |Aviso |2 |-... 85d077ab |Exceção gerada ao aguardar a resposta: O servidor remoto devolveu um erro: (403) Proibido. |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |Resposta recebida. Código de status = 403, ID da solicitação = 9d67c64a-64ed-4B0D-9515-3b14bbcdc63d, Content-MD5 =, ETag =. |
| Microsoft.Azure.Storage |Aviso |2 |-... 85d077ab |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (403) proibido.. |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |Verificando se a operação deve ser repetida. Contagem de repetições = 0, código de status HTTP = 403, exceção = o servidor remoto retornou um erro: (403) proibido.. |
| Microsoft.Azure.Storage |Information |3 |-... 85d077ab |O próximo local foi definido como primário, com base no modo de localização. |
| Microsoft.Azure.Storage |Erro |1 |-... 85d077ab |A política de repetição não permitiu uma nova tentativa. Falha com o servidor remoto retornou um erro: (403) Proibido. |

Nesse cenário, você deve investigar por que o token SAS expira antes que o cliente envie o token para o servidor:

* Normalmente, não deve definir uma hora de início quando cria uma SAS para um cliente utilizar imediatamente. Se existirem pequenas diferenças horárias entre o anfitrião que gera a SAS com a hora atual e o serviço de armazenamento, é possível que o serviço de armazenamento receba uma SAS que ainda não seja válida.
* Não defina um tempo de expiração demasiado curto numa SAS. Novamente, pequenas diferenças horárias entre o anfitrião que gera a SAS e o serviço de armazenamento podem levar uma SAS a expirar aparentemente mais cedo do que o previsto.
* O parâmetro de versão na chave SAS (por exemplo, **VA = 2015-04-05**) corresponde à versão da biblioteca de cliente de armazenamento que você está usando? Recomendamos que você sempre use a versão mais recente da [biblioteca de cliente de armazenamento](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Se voltar a gerar as chaves de acesso ao armazenamento, quaisquer tokens de SAS existentes podem ser invalidados. Este problema pode ocorrer se gerar tokens de SAS com um tempo de expiração longo para aplicações cliente na cache.

Se estiver a utilizar a Biblioteca de Cliente de Armazenamento para gerar tokens de SAS, é fácil criar um token válido. No entanto, se você estiver usando a API REST de armazenamento e construindo os tokens SAS manualmente, consulte [delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>O cliente está recebendo mensagens HTTP 404 (não encontrado)
Se a aplicação cliente receber uma mensagem de HTTP 404 (Não encontrado) do servidor, isso implica que o objeto que o cliente estava a tentar utilizar (por exemplo, um blob, contentor, entidade, tabela ou fila) não existe no serviço de armazenamento. Existem vários motivos possíveis para tal, como:

* [O cliente ou outro processo anteriormente eliminou o objeto]
* [Um problema de autorização de assinatura de acesso partilhado (SAS)]
* [O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]
* [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou outro processo excluiu anteriormente o objeto
Em cenários em que o cliente está tentando ler, atualizar ou excluir dados em um serviço de armazenamento, normalmente é fácil identificar no log do lado do servidor uma operação anterior que excluiu o objeto em questão do serviço de armazenamento. Geralmente, os dados de log mostram que outro usuário ou processo excluiu o objeto. No log de armazenamento do lado do servidor, as colunas tipo de operação e chave de objeto solicitada mostram quando um cliente excluiu um objeto.

No cenário em que um cliente está tentando inserir um objeto, pode não ser imediatamente óbvio porque isso resulta em uma resposta HTTP 404 (não encontrada), dado que o cliente está criando um novo objeto. No entanto, se o cliente estiver criando um blob, ele deverá ser capaz de localizar o contêiner de BLOBs, se o cliente estiver criando uma mensagem, ele deverá ser capaz de encontrar uma fila e, se o cliente estiver adicionando uma linha, ele deverá ser capaz de localizar a tabela.

Você pode usar o log do lado do cliente da biblioteca do cliente de armazenamento para obter uma compreensão mais detalhada de quando o cliente envia solicitações específicas ao serviço de armazenamento.

O seguinte log do lado do cliente gerado pela biblioteca de cliente de armazenamento ilustra o problema quando o cliente não consegue encontrar o contêiner para o blob que está sendo criado. Esse log inclui detalhes das seguintes operações de armazenamento:

| ID do pedido | Operação |
| --- | --- |
| -... 07b26a5d |Método **DeleteIfExists** para excluir o contêiner de BLOB. Observe que essa operação inclui uma solicitação **Head** para verificar a existência do contêiner. |
| e2d06d78… |Método **CreateIfNotExists** para criar o contêiner de BLOB. Observe que essa operação inclui uma solicitação **Head** que verifica a existência do contêiner. A **cabeça** retorna uma mensagem 404, mas continua. |
| -... de8b1c3c |Método **UploadFromStream** para criar o blob. A solicitação **Put** falha com uma mensagem 404 |

Entradas de log:

| ID do pedido | Texto da operação |
| --- | --- |
| -... 07b26a5d |Iniciando a solicitação síncrona https://domemaildist.blob.core.windows.net/azuremmblobcontainer para. |
| -... 07b26a5d |StringToSign = HEAD............ x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: terça-feira, 03 de junho de 2014 10:33:11 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: contêiner. |
| -... 07b26a5d |Aguardando resposta. |
| -... 07b26a5d |Resposta recebida. Código de status = 200, ID da solicitação = eeead849-... Content-MD5 =, ETag = &quot;0x8D14D2DC63D059B&quot;. |
| -... 07b26a5d |Os cabeçalhos de resposta foram processados com êxito, prosseguindo com o restante da operação. |
| -... 07b26a5d |Baixando o corpo da resposta. |
| -... 07b26a5d |Operação concluída com êxito. |
| -... 07b26a5d |Iniciando a solicitação síncrona https://domemaildist.blob.core.windows.net/azuremmblobcontainer para. |
| -... 07b26a5d |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| -... 07b26a5d |Aguardando resposta. |
| -... 07b26a5d |Resposta recebida. Código de status = 202, ID da solicitação = 6ab2a4cf-..., Content-MD5 =, ETag =. |
| -... 07b26a5d |Os cabeçalhos de resposta foram processados com êxito, prosseguindo com o restante da operação. |
| -... 07b26a5d |Baixando o corpo da resposta. |
| -... 07b26a5d |Operação concluída com êxito. |
| e2d06d78-... |Iniciando a solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............ x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: terça-feira, 03 de junho de 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. ResType: contêiner. |
| e2d06d78-... |Aguardando resposta. |
| -... de8b1c3c |Iniciando a solicitação síncrona https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt para. |
| -... de8b1c3c |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| -... de8b1c3c |Preparando para gravar dados de solicitação. |
| e2d06d78-... |Exceção gerada ao aguardar a resposta: O servidor remoto devolveu um erro: (404) não encontrado.. |
| e2d06d78-... |Resposta recebida. Código de status = 404, ID da solicitação = 353ae3bc-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Os cabeçalhos de resposta foram processados com êxito, prosseguindo com o restante da operação. |
| e2d06d78-... |Baixando o corpo da resposta. |
| e2d06d78-... |Operação concluída com êxito. |
| e2d06d78-... |Iniciando a solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Aguardando resposta. |
| -... de8b1c3c |Gravando dados de solicitação. |
| -... de8b1c3c |Aguardando resposta. |
| e2d06d78-... |Exceção gerada ao aguardar a resposta: O servidor remoto devolveu um erro: (409) conflito.. |
| e2d06d78-... |Resposta recebida. Código de status = 409, ID da solicitação = c27da20e-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Baixando o corpo da resposta de erro. |
| -... de8b1c3c |Exceção gerada ao aguardar a resposta: O servidor remoto devolveu um erro: (404) não encontrado.. |
| -... de8b1c3c |Resposta recebida. Código de status = 404, ID da solicitação = 0eaeab3e-..., Content-MD5 =, ETag =. |
| -... de8b1c3c |Exceção lançada durante a operação: O servidor remoto devolveu um erro: (404) não encontrado.. |
| -... de8b1c3c |A política de repetição não permitiu uma nova tentativa. Falha com o servidor remoto retornou um erro: (404) não encontrado.. |
| e2d06d78-... |A política de repetição não permitiu uma nova tentativa. Falha com o servidor remoto retornou um erro: (409) conflito.. |

Neste exemplo, o log mostra que o cliente está intercalando solicitações do método **CreateIfNotExists** (ID da solicitação e2d06d78...) com as solicitações do método **UploadFromStream** (de8b1c3c-...). Essa intercalação acontece porque o aplicativo cliente está invocando esses métodos de forma assíncrona. Modifique o código assíncrono no cliente para garantir que ele crie o contêiner antes de tentar carregar quaisquer dados para um blob nesse contêiner. O ideal é que você crie todos os contêineres antecipadamente.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso compartilhado (SAS)
Se o aplicativo cliente tentar usar uma chave SAS que não inclua as permissões necessárias para a operação, o serviço de armazenamento retornará uma mensagem HTTP 404 (não encontrado) para o cliente. Ao mesmo tempo, você verá também um valor diferente de zero para **SASAuthorizationError** nas métricas.

A tabela a seguir mostra um exemplo de mensagem de log do lado do servidor do arquivo de log de log de armazenamento:

| Nome | Valor |
| --- | --- |
| Hora de início da solicitação | 2014-05-30T06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Estado do pedido     | SASAuthorizationError        |
| Código de estado de HTTP   | 404                          |
| Tipo de autenticação| Rígido                          |
| Tipo de serviço       | Blob                         |
| URL do Pedido        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Cabeçalho da ID da solicitação  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID de pedido do cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigue por que o aplicativo cliente está tentando executar uma operação para a qual não recebeu permissões.

#### <a name="JavaScript-code-does-not-have-permission"></a>O código JavaScript do lado do cliente não tem permissão para acessar o objeto
Se você estiver usando um cliente JavaScript e o serviço de armazenamento estiver retornando mensagens HTTP 404, verifique os seguintes erros de JavaScript no navegador:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Você pode usar o Ferramentas para Desenvolvedores F12 no Internet Explorer para rastrear as mensagens trocadas entre o navegador e o serviço de armazenamento ao solucionar problemas de JavaScript do lado do cliente.
>
>

Esses erros ocorrem porque o navegador da Web implementa a mesma restrição de segurança de [política de origem](https://www.w3.org/Security/wiki/Same_Origin_Policy) que impede que uma página da Web chame uma API em um domínio diferente do domínio do qual a página vem.

Para contornar o problema de JavaScript, você pode configurar o CORS (compartilhamento de recursos entre origens) para o serviço de armazenamento que o cliente está acessando. Para obter mais informações, consulte [suporte a CORS (compartilhamento de recursos entre origens) para serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).

O exemplo de código a seguir mostra como configurar seu serviço blob para permitir que o JavaScript em execução no domínio contoso acesse um blob em seu serviço de armazenamento de BLOBs:

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
Em algumas circunstâncias, os pacotes de rede perdidos podem levar o serviço de armazenamento retornando mensagens HTTP 404 para o cliente. Por exemplo, quando o aplicativo cliente está excluindo uma entidade do serviço tabela, você vê que o cliente lança uma exceção de armazenamento relatando uma mensagem de status "HTTP 404 (não encontrado)" do serviço tabela. Ao investigar a tabela no serviço de armazenamento de tabela, você verá que o serviço excluiu a entidade conforme solicitado.

Os detalhes da exceção no cliente incluem a ID da solicitação (7e84f12d...) atribuída pelo serviço tabela para a solicitação: você pode usar essas informações para localizar os detalhes da solicitação nos logs de armazenamento do lado do servidor pesquisando na coluna **solicitação-ID-cabeçalho** em o arquivo de log. Você também pode usar as métricas para identificar quando falhas, como isso ocorre, e então Pesquisar os arquivos de log com base no tempo em que as métricas registraram esse erro. Essa entrada de log mostra que a exclusão falhou com uma mensagem de status "erro de outro cliente HTTP (404)". A mesma entrada de log também inclui a ID da solicitação gerada pelo cliente na coluna **cliente-solicitação-ID** (813ea74f...).

O log do lado do servidor também inclui outra entrada com o mesmo valor de **Client-Request-ID** (813ea74f...) para uma operação de exclusão bem-sucedida para a mesma entidade e do mesmo cliente. Essa operação de exclusão bem-sucedida ocorreu logo antes da solicitação de exclusão com falha.

A causa mais provável desse cenário é que o cliente enviou uma solicitação de exclusão para a entidade para o serviço tabela, que teve êxito, mas não recebeu uma confirmação do servidor (talvez devido a um problema de rede temporário). Em seguida, o cliente repetirá a operação automaticamente (usando a mesma **ID de cliente-solicitação**) e essa nova tentativa falhará porque a entidade já tinha sido excluída.

Se esse problema ocorrer com frequência, você deve investigar por que o cliente está falhando em receber confirmações do serviço tabela. Se o problema for intermitente, você deverá interceptar o erro "HTTP (404) não encontrado" e o registrar no cliente, mas permitir que o cliente continue.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está recebendo mensagens HTTP 409 (conflito)
A tabela a seguir mostra uma extração do log do lado do servidor para duas operações do cliente: **DeleteIfExists** seguido imediatamente por **CreateIfNotExists** usando o mesmo nome de contêiner de BLOB. Cada operação do cliente resulta em duas solicitações enviadas ao servidor, primeiro uma solicitação getcontainerproperties para verificar se o contêiner existe, seguido pela solicitação **DeleteContainer** ou **createcontêiner** .

| Timestamp | Operação | Resultado | Nome do contentor | ID de pedido do cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

O código no aplicativo cliente exclui e, em seguida, recria imediatamente um contêiner de BLOB usando o mesmo nome: o método **CreateIfNotExists** (ID de solicitação do cliente bc881924-...) eventualmente falha com o erro http 409 (conflito). Quando um cliente elimina blobs, contentores, tabelas ou filas, tem de passar um curto período até o nome ficar novamente disponível.

A aplicação cliente deve utilizar nomes de contentor exclusivos sempre que criar novos contentores se o padrão de eliminação/recriação for comum.

### <a name="metrics-show-low-percent-success"></a>As métricas mostram baixa PercentSuccess ou as entradas de log de análise têm operações com o status de transação de ClientOtherErrors
A métrica **PercentSuccess** captura a porcentagem de operações que foram bem-sucedidas com base em seu código de status http. Operações com códigos de status de contagem de 2XX como bem-sucedidas, enquanto as operações com códigos de status nos intervalos 3XX, 4XX e 5XX são contadas como sem êxito e diminuem o valor de métrica **PercentSuccess** . Nos arquivos de log de armazenamento do lado do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**.

É importante observar que essas operações foram concluídas com êxito e, portanto, não afetam outras métricas, como disponibilidade. Alguns exemplos de operações que são executadas com êxito, mas que podem resultar em códigos de status HTTP malsucedidos incluem:

* **ResourceNotFound** (Não encontrado 404), por exemplo, de uma solicitação GET para um blob que não existe.
* **ResourceAlreadyExists** (Conflito 409), por exemplo, de uma operação **CreateIfNotExist** em que o recurso já existe.
* **ConditionNotMet** (Não modificado 304), por exemplo, de uma operação condicional, como quando um cliente envia um valor de **ETag** e um cabeçalho http **If-None-Match** para solicitar uma imagem somente se ela tiver sido atualizada desde a última operação.

Você pode encontrar uma lista de códigos de erro comuns da API REST que os serviços de armazenamento retornam na página [códigos de erro comuns da API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento
Se você vir alterações repentinas e inesperadas no uso da capacidade em sua conta de armazenamento, poderá investigar os motivos examinando primeiro as métricas de disponibilidade; por exemplo, um aumento no número de solicitações de exclusão com falha pode levar a um aumento na quantidade de armazenamento de BLOBs que você está usando como operações de limpeza específicas do aplicativo que talvez você deva liberar espaço de disco pode não estar funcionando conforme o esperado (por exemplo, , porque os tokens SAS usados para liberar espaço expiraram).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Seu problema surge com o uso do emulador de armazenamento para desenvolvimento ou teste
Normalmente, você usa o emulador de armazenamento durante o desenvolvimento e o teste para evitar o requisito de uma conta de armazenamento do Azure. Os problemas comuns que podem ocorrer quando você está usando o emulador de armazenamento são:

* [O recurso "X" não está funcionando no emulador de armazenamento]
* [Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]
* [A execução do emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>O recurso "X" não está funcionando no emulador de armazenamento
O emulador de armazenamento não dá suporte a todos os recursos dos serviços de armazenamento do Azure, como o serviço de arquivo. Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md).

Para os recursos para os quais o emulador de armazenamento não dá suporte, use o serviço de armazenamento do Azure na nuvem.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento
Você está testando seu aplicativo que usa a biblioteca de cliente de armazenamento em relação ao emulador de armazenamento local e chamadas de método como **CreateIfNotExists** falham com a mensagem de erro "o valor de um dos cabeçalhos HTTP não está no formato correto". Isso indica que a versão do emulador de armazenamento que você está usando não oferece suporte à versão da biblioteca de cliente de armazenamento que você está usando. A biblioteca de cliente de armazenamento adiciona o cabeçalho **x-MS-Version** a todas as solicitações que ele faz. Se o emulador de armazenamento não reconhecer o valor no cabeçalho **x-MS-Version** , ele rejeitará a solicitação.

Você pode usar os logs do cliente de biblioteca de armazenamento para ver o valor do **cabeçalho x-MS-Version** que está enviando. Você também pode ver o valor do **cabeçalho x-MS-Version** se usar o Fiddler para rastrear as solicitações do aplicativo cliente.

Esse cenário normalmente ocorre se você instalar e usar a versão mais recente da biblioteca de cliente de armazenamento sem Atualizar o emulador de armazenamento. Você deve instalar a versão mais recente do emulador de armazenamento ou usar o armazenamento em nuvem em vez do emulador para desenvolvimento e teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>A execução do emulador de armazenamento requer privilégios administrativos
As credenciais de administrador são solicitadas quando você executa o emulador de armazenamento. Isso ocorre apenas quando você está inicializando o emulador de armazenamento pela primeira vez. Depois de inicializar o emulador de armazenamento, você não precisa de privilégios administrativos para executá-lo novamente.

Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md). Você também pode inicializar o emulador de armazenamento no Visual Studio, o que também exigirá privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Você está encontrando problemas ao instalar o SDK do Azure para .NET
Quando você tenta instalar o SDK, ele falha ao tentar instalar o emulador de armazenamento no computador local. O log de instalação contém uma das seguintes mensagens:

* CAQuietExec  Erro: Não é possível acessar a instância do SQL
* CAQuietExec  Erro: Não é possível criar o banco de dados

A causa é um problema com a instalação de LocalDB existente. Por padrão, o emulador de armazenamento usa o LocalDB para manter os dados quando simula os serviços de armazenamento do Azure. Você pode redefinir sua instância de LocalDB executando os comandos a seguir em uma janela de prompt de comando antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O comando **excluir** remove todos os arquivos de banco de dados antigos das instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Você tem um problema diferente com um serviço de armazenamento
Se as seções anteriores de solução de problemas não incluírem o problema que você está tendo com um serviço de armazenamento, adote a seguinte abordagem para diagnosticar e solucionar o problema.

* Verifique suas métricas para ver se há alguma alteração de seu comportamento de linha de base esperado. A partir das métricas, você poderá determinar se o problema é transitório ou permanente e quais operações de armazenamento o problema está afetando.
* Você pode usar as informações de métricas para ajudá-lo a Pesquisar os dados de log do lado do servidor para obter informações mais detalhadas sobre os erros que estão ocorrendo. Essas informações podem ajudá-lo a solucionar problemas e resolver o problema.
* Se as informações nos logs do lado do servidor não forem suficientes para solucionar o problema com êxito, você poderá usar os logs do lado do cliente da biblioteca de cliente de armazenamento para investigar o comportamento do seu aplicativo cliente e ferramentas como o Fiddler, o Wireshark e o Microsoft Analisador de mensagem para investigar sua rede.

Para obter mais informações sobre como usar o Fiddler[Apêndice 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]http e https ".

Para obter mais informações sobre como usar o Wireshark[Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]tráfego de rede. "

Para obter mais informações sobre como usar o Microsoft Message Analyzer[Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]tráfego de rede. "

## <a name="appendices"></a>Apêndices
Os apêndices descrevem várias ferramentas que podem ser úteis quando você estiver diagnosticando e Solucionando problemas com o armazenamento do Azure (e outros serviços). Essas ferramentas não fazem parte do armazenamento do Azure e outras são produtos de terceiros. Dessa forma, as ferramentas discutidas nesses apêndices não são cobertas por nenhum contrato de suporte que você possa ter com Microsoft Azure ou armazenamento do Azure e, portanto, como parte do processo de avaliação, você deve examinar as opções de licenciamento e suporte disponíveis no provedores dessas ferramentas.

### <a name="appendix-1"></a>Apêndice 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS
O [Fiddler](https://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre o aplicativo cliente e o serviço de armazenamento do Azure que você está usando.

> [!NOTE]
> O Fiddler pode decodificar o tráfego HTTPS; Você deve ler cuidadosamente a documentação do Fiddler para entender como ele faz isso e entender as implicações de segurança.
>
>

Este apêndice fornece uma breve explicação de como configurar o Fiddler para capturar o tráfego entre o computador local em que você instalou o Fiddler e os serviços de armazenamento do Azure.

Depois de iniciar o Fiddler, ele começará a capturar o tráfego HTTP e HTTPS em seu computador local. A seguir estão alguns comandos úteis para controlar o Fiddler:

* Pare e inicie a captura de tráfego. No menu principal, vá para **arquivo** e clique em **capturar tráfego** para ativar e desativar a captura.
* Salvar dados de tráfego capturados. No menu principal, vá para **arquivo**, clique em **salvar**e clique em **todas as sessões**: isso permite que você salve o tráfego em um arquivo de sessão. Você pode recarregar um arquivo de sessão posteriormente para análise ou enviá-lo se solicitado ao suporte da Microsoft.

Para limitar a quantidade de tráfego capturada pelo Fiddler, você pode usar filtros que você configura na guia **filtros** . A captura de tela a seguir mostra um filtro que captura somente o tráfego enviado para o ponto de extremidade de armazenamento **contosoemaildist.Table.Core.Windows.net** :

![][5]

### <a name="appendix-2"></a>Apêndice 2: Usando o Wireshark para capturar o tráfego de rede
O [Wireshark](https://www.wireshark.org/) é um analisador de protocolo de rede que permite exibir informações detalhadas de pacotes para uma ampla gama de protocolos de rede.

O procedimento a seguir mostra como capturar informações detalhadas de pacote para o tráfego do computador local em que você instalou o Wireshark para o serviço tabela em sua conta de armazenamento do Azure.

1. Inicie o Wireshark em seu computador local.
2. Na seção **Iniciar** , selecione a interface de rede local ou as interfaces que estão conectadas à Internet.
3. Clique em **Opções de captura**.
4. Adicione um filtro à caixa de texto **filtro de captura** . Por exemplo, o **host contosoemaildist.Table.Core.Windows.net** configurará o Wireshark para capturar somente os pacotes enviados de ou para o ponto de extremidade do serviço tabela na conta de armazenamento **contosoemaildist** . Confira a [lista completa de filtros de captura](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Clique em **Iniciar**. Agora, o Wireshark capturará todos os pacotes enviados de ou para o ponto de extremidade do serviço tabela à medida que você usar o aplicativo cliente no computador local.
6. Quando terminar, no menu principal, clique em **capturar** e em **parar**.
7. Para salvar os dados capturados em um arquivo de captura do Wireshark, no menu principal, clique em **arquivo** e em **salvar**.

O WireShark realçará todos os erros existentes na janela **pacote de pacotes** . Você também pode usar a janela **informações de especialista** (clique em **analisar**e, em seguida, informações de **especialistas**) para exibir um resumo de erros e avisos.

![][7]

Você também pode optar por exibir os dados TCP conforme a camada de aplicativo o vê clicando com o botão direito do mouse nos dados TCP e selecionando **seguir fluxo TCP**. Isso será útil se você tiver capturado o despejo sem um filtro de captura. Para obter mais informações, consulte os [fluxos de TCP a seguir](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Para obter mais informações sobre como usar o Wireshark, consulte o [Guia do usuário do Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3"></a>Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede
Você pode usar o Microsoft Message Analyzer para capturar o tráfego HTTP e HTTPS de forma semelhante ao Fiddler e capturar o tráfego de rede de forma semelhante ao Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar uma sessão de rastreamento da Web usando o analisador de mensagem da Microsoft
Para configurar uma sessão de rastreamento da Web para o tráfego HTTP e HTTPS usando o Microsoft Message Analyzer, execute o aplicativo Analisador de mensagens da Microsoft e, em seguida, no menu **arquivo** , clique em **capturar/rastrear**. Na lista de cenários de rastreamento disponíveis, selecione **proxy Web**. Em seguida, no painel de **configuração do cenário de rastreamento** , na caixa de texto **HostnameFilter** , adicione os nomes dos seus pontos de extremidade de armazenamento (você pode procurar esses nomes na [portal do Azure](https://portal.azure.com)). Por exemplo, se o nome da sua conta de armazenamento do Azure for **contosodata**, você deverá adicionar o seguinte à caixa de texto **HostnameFilter** :

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Um caractere de espaço separa os nomes de host.
>
>

Quando estiver pronto para começar a coletar dados de rastreamento, clique no botão **Iniciar com** .

Para obter mais informações sobre o rastreamento de **proxy da Web** do Microsoft Message Analyzer, consulte [provedor Microsoft-PEF-WebProxy](https://technet.microsoft.com/library/jj674814.aspx).

O rastreamento de **proxy da Web** interno no analisador de mensagem da Microsoft baseia-se no Fiddler; Ele pode capturar o tráfego HTTPS do lado do cliente e exibir mensagens HTTPS não criptografadas. O rastreamento de **proxy Web** funciona Configurando um proxy local para todo o tráfego HTTP e HTTPS que fornece acesso a mensagens não criptografadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticando problemas de rede usando o Microsoft Message Analyzer
Além de usar o rastreamento de **proxy da Web** do Microsoft Message Analyzer para capturar detalhes do tráfego http/https entre o aplicativo cliente e o serviço de armazenamento, você também pode usar o rastreamento de **camada de link local** interno para capturar a rede informações de pacote. Isso permite que você capture dados semelhantes aos que você pode capturar com o Wireshark e diagnostique problemas de rede, como pacotes descartados.

A captura de tela a seguir mostra um exemplo de rastreamento de **camada de link local** com algumas mensagens informativas na coluna **DiagnosisTypes** . Clicar em um ícone na coluna **DiagnosisTypes** mostra os detalhes da mensagem. Neste exemplo, o servidor retransmitiu a mensagem #305 porque ela não recebeu uma confirmação do cliente:

![][9]

Ao criar a sessão de rastreamento no analisador de mensagens da Microsoft, você pode especificar filtros para reduzir a quantidade de ruído no rastreamento. Na página **capturar/rastrear** na qual você define o rastreamento, clique no link **Configurar** ao lado de **Microsoft-Windows-NDIS-PacketCapture**. A captura de tela a seguir mostra uma configuração que filtra o tráfego TCP para os endereços IP de três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreamento de camada de link local do Microsoft Message Analyzer, consulte [provedor Microsoft-PEF-NDIS-PacketCapture](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Apêndice 4: Usando o Excel para exibir métricas e dados de log
Muitas ferramentas permitem que você baixe os dados de métricas de armazenamento do armazenamento de tabelas do Azure em um formato delimitado que facilita a carga dos dados no Excel para exibição e análise. Os dados de log de armazenamento do armazenamento de BLOBs do Azure já estão em um formato delimitado que você pode carregar no Excel. No entanto, você precisará adicionar títulos de coluna apropriados com base nas informações em [análise de armazenamento formato de log](https://msdn.microsoft.com/library/azure/hh343259.aspx) e [análise de armazenamento esquema de tabela](https://msdn.microsoft.com/library/azure/hh343264.aspx)de métricas.

Para importar os dados de log de armazenamento para o Excel depois de baixá-los do armazenamento de BLOBs:

* No menu **dados** , clique em **do texto**.
* Navegue até o arquivo de log que você deseja exibir e clique em **importar**.
* Na etapa 1 do **Assistente de importação de texto**, selecione delimitado.

Na etapa 1 do **Assistente de importação de texto**, selecione **ponto e vírgula** como o único delimitador e escolha aspas duplas como o qualificador de **texto**. Em seguida, clique em **concluir** e escolha onde inserir os dados em sua pasta de trabalho.

### <a name="appendix-5"></a>Apêndice 5: Monitoramento com o Application Insights para DevOps do Azure
Você também pode usar o recurso Application Insights para o Azure DevOps como parte de seu monitoramento de desempenho e disponibilidade. Essa ferramenta pode:

* Verifique se o serviço Web está disponível e respondendo. Se seu aplicativo é um site ou um aplicativo de dispositivo que usa um serviço Web, ele pode testar sua URL a cada poucos minutos de locais em todo o mundo e informar se há um problema.
* Diagnostique rapidamente quaisquer problemas de desempenho ou exceções em seu serviço Web. Descubra se a CPU ou outros recursos estão sendo ampliados, obtenha rastreamentos de pilha de exceções e pesquise facilmente por meio de rastreamentos de log. Se o desempenho do aplicativo cair abaixo dos limites aceitáveis, a Microsoft poderá enviar um email para você. Você pode monitorar os serviços Web .NET e Java.

Você pode encontrar mais informações em [o que é Application insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre análise no armazenamento do Azure, consulte estes recursos:

* [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Análise de armazenamento](storage-analytics.md)
* [Métricas de análise de armazenamento](storage-analytics-metrics.md)
* [Esquema de tabela de métricas da análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Logs de análise de armazenamento](storage-analytics-logging.md)
* [Formato de log da análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introdução]: #introduction
[Como este guia é organizado]: #how-this-guide-is-organized

[Monitorando seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorando a integridade do serviço]: #monitoring-service-health
[Capacidade de monitoramento]: #monitoring-capacity
[Monitorando a disponibilidade]: #monitoring-availability
[Monitorando o desempenho]: #monitoring-performance

[Diagnosticando problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de integridade do serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnosticando erros]: #diagnosing-errors
[Problemas do emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de log de armazenamento]: #storage-logging-tools
[Usando as ferramentas de log de rede]: #using-network-logging-tools

[Rastreamento de ponta a ponta]: #end-to-end-tracing
[Correlacionando dados de log]: #correlating-log-data
[ID de solicitação do cliente]: #client-request-id
[ID de solicitação do servidor]: #server-request-id
[Carimbos]: #timestamps

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
[O código JavaScript do lado do cliente não tem permissão para aceder ao objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (conflito)]: #the-client-is-receiving-409-messages

[Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]: #metrics-show-low-percent-success
[As métricas de capacidade mostram um aumento inesperado no uso da capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[O recurso "X" não está funcionando no emulador de armazenamento]: #feature-X-is-not-working
[Erro "o valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[A execução do emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Pode encontrar problemas ao instalar o Azure SDK para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Tem um problema com um serviço de armazenamento diferente]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[Apêndice 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]: #appendix-1
[Apêndice 2: Usando o Wireshark para capturar o tráfego de rede]: #appendix-2
[Apêndice 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]: #appendix-3
[Apêndice 4: Usando o Excel para exibir métricas e dados de log]: #appendix-4
[Apêndice 5: Monitoramento com o Application Insights para DevOps do Azure]: #appendix-5

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
