---
title: Solucionando problemas do armazenamento do Azure com o diagnóstico & o analisador de mensagem | Microsoft Docs
description: Um tutorial que demonstra a solução de problemas de ponta a ponta com o Análise de Armazenamento do Azure, o AzCopy e o Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/15/2017
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2ca81280bed52508c606a5a693fe0162837ac117
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854617"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Solução de problemas de ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e analisador de mensagem
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnosticar e solucionar problemas é uma importante habilidade para criar e dar suporte a aplicativos cliente com Armazenamento do Microsoft Azure. Devido à natureza distribuída de um aplicativo do Azure, o diagnóstico e a solução de problemas de erros e de desempenho podem ser mais complexos do que em ambientes tradicionais.

Neste tutorial, demonstramos como identificar determinados erros que podem afetar o desempenho e solucionar esses erros de ponta a ponta usando ferramentas fornecidas pela Microsoft e pelo armazenamento do Azure, a fim de otimizar o aplicativo cliente.

Este tutorial fornece uma exploração prática de um cenário de solução de problemas de ponta a ponta. Para obter um guia conceitual detalhado para solução de problemas de aplicativos de armazenamento do Azure, consulte [monitorar, diagnosticar e solucionar problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para solução de problemas de aplicativos de armazenamento do Azure
Para solucionar problemas de aplicativos cliente usando Armazenamento do Microsoft Azure, você pode usar uma combinação de ferramentas para determinar quando um problema ocorreu e qual pode ser a causa do problema. Estas ferramentas incluem:

* **Análise de armazenamento do Azure**. [Análise de armazenamento do Azure](/rest/api/storageservices/Storage-Analytics) fornece métricas e log para o armazenamento do Azure.

  * As métricas de **armazenamento** acompanham as métricas da transação e as métricas de capacidade para sua conta de armazenamento. Usando as métricas, você pode determinar como seu aplicativo está sendo executado de acordo com uma variedade de medidas diferentes. Consulte [análise de armazenamento esquema da tabela](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) de métricas para obter mais informações sobre os tipos de métricas controladas pelo análise de armazenamento.
  * O **log de armazenamento** registra cada solicitação para os serviços de armazenamento do Azure em um log do lado do servidor. O log rastreia dados detalhados para cada solicitação, incluindo a operação executada, o status da operação e as informações de latência. Confira [análise de armazenamento formato de log](/rest/api/storageservices/Storage-Analytics-Log-Format) para obter mais informações sobre os dados de solicitação e resposta gravados nos logs por análise de armazenamento.

* **Portal do Azure**. Você pode configurar as métricas e o registro em log para sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Você também pode exibir gráficos e grafos que mostram como o aplicativo está sendo executado ao longo do tempo e configurar alertas para notificá-lo se seu aplicativo for executado de forma diferente do esperado para uma métrica especificada.

    Consulte [monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar o monitoramento no portal do Azure.
* **AzCopy**. Os logs de servidor do armazenamento do Azure são armazenados como BLOBs, de modo que você pode usar AzCopy para copiar os blobs de log para um diretório local para análise usando o Microsoft Message Analyzer. Consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md) para obter mais informações sobre AzCopy.
* **Analisador de mensagem da Microsoft**. O analisador de mensagem é uma ferramenta que consome arquivos de log e exibe dados de log em um formato visual que torna mais fácil filtrar, Pesquisar e agrupar dados de log em conjuntos úteis que você pode usar para analisar erros e problemas de desempenho. Consulte [o guia de operação do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o analisador de mensagem.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de exemplo
Para este tutorial, examinaremos um cenário em que as métricas de armazenamento do Azure indicam uma taxa de êxito de porcentagem baixa para um aplicativo que chama o armazenamento do Azure. A métrica de taxa de êxito de porcentagem baixa (mostrada como **PercentSuccess** no [portal do Azure](https://portal.azure.com) e nas tabelas de métricas) controla as operações bem-sucedidas, mas que retornam um código de status http maior que 299. Nos arquivos de log de armazenamento do lado do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de sucesso de porcentagem baixa, consulte as [métricas mostram baixa PercentSuccess ou as entradas de log de análise têm operações com o status de transação de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

As operações de armazenamento do Azure podem retornar códigos de status HTTP maiores que 299 como parte de sua funcionalidade normal. Mas esses erros, em alguns casos, indicam que você pode otimizar o aplicativo cliente para melhorar o desempenho.

Nesse cenário, vamos considerar uma taxa de êxito de porcentagem baixa para ser qualquer coisa abaixo de 100%. No entanto, você pode escolher um nível de métrica diferente de acordo com suas necessidades. É recomendável que, durante o teste do seu aplicativo, você estabeleça uma tolerância de linha de base para as métricas de desempenho principais. Por exemplo, você pode determinar, com base em testes, que seu aplicativo deve ter uma taxa de êxito de porcentagem consistente de 90% ou 85%. Se os dados de métricas mostrarem que o aplicativo é deviating desse número, você poderá investigar o que pode estar causando o aumento.

Para nosso cenário de exemplo, depois de estabelecermos que a métrica de taxa de êxito percentual está abaixo de 100%, examinaremos os logs para encontrar os erros que correlacionam com as métricas e as utilizamos para descobrir o que está causando a taxa de êxito percentual inferior. Veremos especificamente os erros no intervalo de 400. Em seguida, examinaremos mais atentamente os erros 404 (não encontrados).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas de erros de intervalo 400
Os exemplos a seguir mostram uma amostragem de alguns erros de intervalo 400 para solicitações no armazenamento de BLOBs do Azure e suas possíveis causas. Qualquer um desses erros, bem como erros no intervalo de 300 e o intervalo de 500, pode contribuir para uma taxa de êxito de porcentagem baixa.

Observe que as listas abaixo estão longe de serem concluídas. Consulte [status e códigos de erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no MSDN para obter detalhes sobre erros gerais de armazenamento do Azure e sobre erros específicos de cada um dos serviços de armazenamento.

**Exemplos de código de status 404 (não encontrado)**

Ocorre quando uma operação de leitura em um contêiner ou BLOB falha porque o BLOB ou o contêiner não foi encontrado.

* Ocorre se um contêiner ou BLOB foi excluído por outro cliente antes desta solicitação.
* Ocorre se você estiver usando uma chamada à API que cria o contêiner ou BLOB depois de verificar se ele existe. As APIs CreateIfNotExists fazem uma chamada HEAD primeiro para verificar a existência do contêiner ou BLOB; Se não existir, um erro 404 será retornado e uma segunda chamada PUT será feita para gravar o contêiner ou o blob.

**Exemplos de código de status 409 (conflito)**

* Ocorre se você usar uma API de criação para criar um novo contêiner ou BLOB, sem verificar a existência primeiro, e um contêiner ou BLOB com esse nome já existir.
* Ocorre se um contêiner está sendo excluído e você tenta criar um novo contêiner com o mesmo nome antes que a operação de exclusão seja concluída.
* Ocorre se você especificar uma concessão em um contêiner ou BLOB, e já houver uma concessão presente.

**Exemplos de código de status 412 (falha na pré-condição)**

* Ocorre quando a condição especificada por um cabeçalho condicional não foi atendida.
* Ocorre quando a ID de concessão especificada não corresponde à ID de concessão no contêiner ou BLOB.

## <a name="generate-log-files-for-analysis"></a>Gerar arquivos de log para análise
Neste tutorial, usaremos o analisador de mensagem para trabalhar com três tipos diferentes de arquivos de log, embora você possa optar por trabalhar com qualquer um deles:

* O **log do servidor**, que é criado quando você habilita o log de armazenamento do Azure. O log do servidor contém dados sobre cada operação chamada em um dos serviços de armazenamento do Azure-Blob, fila, tabela e arquivo. O log do servidor indica qual operação foi chamada e qual código de status foi retornado, bem como outros detalhes sobre a solicitação e a resposta.
* O **log do cliente .net**, que é criado quando você habilita o log do lado do cliente de dentro de seu aplicativo .net. O log do cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.
* O **log de rastreamento de rede http**, que coleta dados em dados de solicitação e resposta http/https, incluindo operações no armazenamento do Azure. Neste tutorial, vamos gerar o rastreamento de rede por meio do analisador de mensagem.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar registro em log e métricas do lado do servidor
Primeiro, precisaremos configurar o log e as métricas do armazenamento do Azure para que tenhamos dados do lado do serviço para análise. Você pode configurar o registro em log e as métricas de várias maneiras, por meio do [portal do Azure](https://portal.azure.com), usando o PowerShell ou programaticamente. Consulte [habilitar métricas](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) e [habilitar o registro em log](storage-analytics-logging.md#enable-storage-logging) para obter detalhes sobre como configurar o log e as métricas.

### <a name="configure-net-client-side-logging"></a>Configurar o log do lado do cliente .NET
Para configurar o log do lado do cliente para um aplicativo .NET, habilite o diagnóstico do .NET no arquivo de configuração do aplicativo (Web. config ou app. config). Consulte [log do lado do cliente com a biblioteca de cliente de armazenamento .net](https://msdn.microsoft.com/library/azure/dn782839.aspx) e [o log do lado do cliente com o SDK do armazenamento do Microsoft Azure para Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) no MSDN para obter detalhes.

O log do lado do cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.

A biblioteca de cliente de armazenamento armazena dados de log do lado do cliente no local especificado no arquivo de configuração do aplicativo (Web. config ou app. config).

### <a name="collect-a-network-trace"></a>Coletar um rastreamento de rede
Você pode usar o analisador de mensagem para coletar um rastreamento de rede HTTP/HTTPS enquanto o aplicativo cliente está em execução. O analisador de mensagem usa o [Fiddler](https://www.telerik.com/fiddler) no back-end. Antes de coletar o rastreamento de rede, recomendamos que você configure o Fiddler para registrar o tráfego HTTPS não criptografado:

1. Instale o [Fiddler](https://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
3. Selecione **ferramentas | Opções do Fiddler**.
4. Na caixa de diálogo opções, verifique se a opção **capturar https conecta** e descriptografar **tráfego HTTPS** está selecionada, conforme mostrado abaixo.

![Configurar opções do Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para o tutorial, colete e salve um rastreamento de rede primeiro no analisador de mensagem e, em seguida, crie uma sessão de análise para analisar o rastreamento e os logs. Para coletar um rastreamento de rede no analisador de mensagem:

1. No analisador de mensagens, selecione **arquivo | Rastreamento rápido | HTTPS**não criptografado.
2. O rastreamento será iniciado imediatamente. Selecione **parar** para interromper o rastreamento para que possamos configurá-lo para rastrear somente o tráfego de armazenamento.
3. Selecione **Editar** para editar a sessão de rastreamento.
4. Selecione o link **Configurar** à direita do provedor de ETW **Microsoft-PEF-WebProxy** .
5. Na caixa de diálogo **Configurações avançadas** , clique na guia **provedor** .
6. No campo **filtro de nome de host** , especifique os pontos de extremidade de armazenamento, separados por espaços. Por exemplo, você pode especificar os pontos de extremidade da seguinte maneira; Altere `storagesample` para o nome da sua conta de armazenamento:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Saia da caixa de diálogo e clique em **reiniciar** para começar a coletar o rastreamento com o filtro de nome de host em vigor, para que apenas o tráfego de rede do armazenamento do Azure seja incluído no rastreamento.

> [!NOTE]
> Depois de concluir a coleta do rastreamento de rede, é altamente recomendável que você Reverta as configurações que você pode ter alterado no Fiddler para descriptografar o tráfego HTTPS. Na caixa de diálogo opções do Fiddler, desmarque as caixas de seleção **capturar https conecta** e descriptografar **tráfego HTTPS** .
>
>

Consulte [usando os recursos de rastreamento de rede](https://technet.microsoft.com/library/jj674819.aspx) no TechNet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Examinar dados de métricas no portal do Azure
Depois que o aplicativo estiver em execução por um período de tempo, você poderá examinar os gráficos de métricas que aparecem no [portal do Azure](https://portal.azure.com) para observar como o serviço foi executado.

Primeiro, navegue até sua conta de armazenamento na portal do Azure. Por padrão, um gráfico de monitoramento com a métrica **percentual de êxito** é exibido na folha da conta. Se você tiver modificado o gráfico anteriormente para exibir diferentes métricas, adicione a métrica de **percentual de sucesso** .

Agora você verá a **porcentagem de sucesso** no gráfico de monitoramento, juntamente com quaisquer outras métricas que você possa ter adicionado. No cenário que investigaremos em seguida analisando os logs no analisador de mensagem, a taxa de sucesso percentual é um pouco abaixo de 100%.

Para obter mais detalhes sobre como adicionar e personalizar gráficos de métricas, consulte [personalizar gráficos](storage-monitor-storage-account.md#customize-metrics-charts)de métricas.

> [!NOTE]
> Pode levar algum tempo para que os dados de métrica apareçam no portal do Azure depois de habilitar as métricas de armazenamento. Isso ocorre porque as métricas por hora da hora anterior não são exibidas no portal do Azure até que a hora atual tenha decorrido. Além disso, as métricas de minuto não são atualmente exibidas no portal do Azure. Portanto, dependendo de quando você habilita as métricas, pode levar até duas horas para ver os dados de métricas.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Usar o AzCopy para copiar os logs do servidor para um diretório local
O armazenamento do Azure grava dados de log do servidor em BLOBs, enquanto as métricas são gravadas em tabelas. Os blobs de log estão disponíveis no contêiner conhecido `$logs` para sua conta de armazenamento. Os blobs de log são nomeados hierarquicamente por ano, mês, dia e hora, para que você possa localizar facilmente o intervalo de tempo que deseja investigar. Por exemplo, na `storagesample` conta, o contêiner para os blobs de log para 01/02/2015, de 8-9 AM, é. `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` Os BLOBs individuais nesse contêiner são nomeados em sequência, começando com `000000.log`.

Você pode usar a ferramenta de linha de comando AzCopy para baixar esses arquivos de log do lado do servidor para um local de sua escolha no computador local. Por exemplo, você pode usar o seguinte comando para baixar os arquivos de log para operações de BLOB que ocorreram em 2 de janeiro de 2015 `C:\Temp\Logs\Server`para a `<storageaccountname>` pasta; substituir pelo nome da sua conta de `<storageaccountkey>` armazenamento e pela chave de acesso da conta :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
O AzCopy está disponível para download na página de [downloads do Azure](https://azure.microsoft.com/downloads/) . Para obter detalhes sobre como usar o AzCopy, consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

Para obter informações adicionais sobre como baixar logs no servidor, consulte [baixar dados de log](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)de log de armazenamento.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Usar o Microsoft Message Analyzer para analisar dados de log
O Microsoft Message Analyzer é uma ferramenta para capturar, exibir e analisar tráfego de mensagens de protocolo, eventos e outras mensagens de sistema ou aplicativo em cenários de diagnóstico e solução de problemas. O analisador de mensagem também permite carregar, agregar e analisar dados de logs e arquivos de rastreamento salvos. Para obter mais informações sobre o analisador de mensagem, consulte [o guia de operação do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

O analisador de mensagem inclui ativos para o armazenamento do Azure que ajudam a analisar os logs de servidor, cliente e rede. Nesta seção, discutiremos como usar essas ferramentas para resolver o problema de baixo percentual de êxito nos logs de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Baixar e instalar o analisador de mensagem e os ativos de armazenamento do Azure
1. Baixe o analisador de [mensagem](https://www.microsoft.com/download/details.aspx?id=44226) do centro de download da Microsoft e execute o instalador.
2. Inicie o analisador de mensagem.
3. No menu **ferramentas** , selecione **Gerenciador de ativos**. Na caixa de diálogo **Gerenciador de ativos** , selecione **downloads**e filtre no **armazenamento do Azure**. Você verá os ativos de armazenamento do Azure, conforme mostrado na imagem abaixo.
4. Clique em **sincronizar todos os itens exibidos** para instalar os ativos de armazenamento do Azure. Os ativos disponíveis incluem:
   * **Regras de cores do armazenamento do Azure:** As regras de cores de armazenamento do Azure permitem que você defina filtros especiais que usam estilos de cor, texto e fonte para realçar mensagens que contêm informações específicas em um rastreamento.
   * **Gráficos de armazenamento do Azure:** Os gráficos de armazenamento do Azure são gráficos predefinidos que os dados de log do Graph Server. Observe que para usar os gráficos de armazenamento do Azure no momento, você só pode carregar o log do servidor na grade de análise.
   * **Analisadores de armazenamento do Azure:** Os analisadores de armazenamento do Azure analisam os logs de cliente de armazenamento do Azure, servidor e HTTP para exibi-los na grade de análise.
   * **Filtros de armazenamento do Azure:** Os filtros de armazenamento do Azure são critérios predefinidos que você pode usar para consultar seus dados na grade de análise.
   * **Layouts de exibição do armazenamento do Azure:** Os layouts de exibição do armazenamento do Azure são layouts e agrupamentos de coluna predefinidos na grade de análise.
5. Reinicie o analisador de mensagem depois de instalar os ativos.

![Gerenciador de ativos do Message Analyzer](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os ativos de armazenamento do Azure mostrados para os fins deste tutorial.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importar os arquivos de log para o analisador de mensagem
Você pode importar todos os arquivos de log salvos (no lado do servidor, do cliente e da rede) para uma única sessão no analisador de mensagem da Microsoft para análise.

1. No menu **arquivo** do analisador de mensagens da Microsoft, clique em **nova sessão**e em **sessão em branco**. Na caixa de diálogo **nova sessão** , insira um nome para a sessão de análise. No painel **detalhes da sessão** , clique no botão **arquivos** .
2. Para carregar os dados de rastreamento de rede gerados pelo analisador de mensagem, clique em **Adicionar arquivos**, navegue até o local em que você salvou o arquivo. arquivo MATP da sua sessão de rastreamento da Web, selecione o arquivo. arquivo MATP e clique em **abrir**.
3. Para carregar os dados de log do servidor, clique em **Adicionar arquivos**, navegue até o local onde você baixou os logs do servidor, selecione os arquivos de log para o intervalo de tempo que você deseja analisar e clique em **abrir**. Em seguida, no painel **detalhes da sessão** , defina a lista suspensa configuração de log de **texto** para cada arquivo de log do lado do servidor para **AzureStorageLog** para garantir que o analisador de mensagem da Microsoft possa analisar o arquivo de log corretamente.
4. Para carregar os dados de log do lado do cliente, clique em **Adicionar arquivos**, navegue até o local em que você salvou os logs do lado do cliente, selecione os arquivos de log que deseja analisar e clique em **abrir**. Em seguida, no painel **detalhes da sessão** , defina a lista suspensa configuração de log de **texto** para cada arquivo de log do lado do cliente para **AzureStorageClientDotNetV4** para garantir que o analisador de mensagem da Microsoft possa analisar o arquivo de log corretamente.
5. Clique em **Iniciar** na caixa de diálogo **nova sessão** para carregar e analisar os dados de log. Os dados de log são exibidos na grade de análise do analisador de mensagens.

A imagem abaixo mostra uma sessão de exemplo configurada com o servidor, cliente e arquivos de log de rastreamento de rede.

![Configurar sessão do analisador de mensagem](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observe que o analisador de mensagem carrega arquivos de log na memória. Se você tiver um grande conjunto de dados de log, será recomendável filtrá-lo para obter o melhor desempenho do analisador de mensagem.

Primeiro, determine o período de tempo que você está interessado em revisar e mantenha esse período o mais pequeno possível. Em muitos casos, você desejará examinar um período de minutos ou horas no máximo. Importe o menor conjunto de logs que podem atender às suas necessidades.

Se você ainda tiver uma grande quantidade de dados de log, talvez queira especificar um filtro de sessão para filtrar os dados de log antes de carregá-los. Na caixa **filtro de sessão** , selecione o botão **biblioteca** para escolher um filtro predefinido; por exemplo, escolha **filtro de tempo global I** dos filtros de armazenamento do Azure para filtrar em um intervalo de tempo. Em seguida, você pode editar os critérios de filtro para especificar o carimbo de data/hora inicial e final para o intervalo que você deseja ver. Você também pode filtrar um código de status específico; por exemplo, você pode optar por carregar apenas as entradas de log em que o código de status é 404.

Para obter mais informações sobre como importar dados de log para o Microsoft Message Analyzer, consulte [recuperando dados de mensagem](https://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Usar a ID de solicitação do cliente para correlacionar os dados do arquivo de log
A biblioteca de cliente de armazenamento do Azure gera automaticamente uma ID de solicitação de cliente exclusiva para cada solicitação. Esse valor é gravado no log do cliente, no log do servidor e no rastreamento de rede, para que você possa usá-lo para correlacionar dados em todos os três logs no analisador de mensagem. Consulte [ID de solicitação do cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre a ID de solicitação do cliente.

As seções a seguir descrevem como usar exibições de layout pré-configuradas e personalizadas para correlacionar e agrupar dados com base na ID de solicitação do cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um layout de exibição para exibir na grade de análise
Os ativos de armazenamento do analisador de mensagem incluem layouts de exibição de armazenamento do Azure, que são exibições pré-configuradas que você pode usar para exibir seus dados com agrupamentos e colunas úteis para diferentes cenários. Você também pode criar layouts de exibição personalizados e salvá-los para reutilização.

A imagem abaixo mostra o menu **Exibir layout** , disponível selecionando **Exibir layout** na faixa de opção da barra de ferramentas. Os layouts de exibição do armazenamento do Azure são agrupados no nó **armazenamento do Azure** no menu. Você pode procurar `Azure Storage` na caixa de pesquisa para filtrar somente os layouts de exibição do armazenamento do Azure. Você também pode selecionar a estrela ao lado de um layout de exibição para torná-lo um favorito e exibi-lo na parte superior do menu.

![Menu Exibir layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, selecione **agrupado por ClientRequestID e módulo**. Esse layout de exibição agrupa dados de log de todos os três logs primeiro pela ID de solicitação do cliente e, em seguida, pelo arquivo de log de origem (ou **módulo** no analisador de mensagem). Com essa exibição, você pode fazer uma busca detalhada em uma ID de solicitação de cliente específica e ver os dados de todos os três arquivos de log para essa ID de solicitação de cliente.

A imagem abaixo mostra esse modo de exibição de layout aplicado aos dados de log de exemplo, com um subconjunto de colunas exibido. Você pode ver que, para uma ID de solicitação de cliente específica, a grade de análise exibe dados do log do cliente, do log do servidor e do rastreamento de rede.

![Layout de exibição do armazenamento do Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Arquivos de log diferentes têm colunas diferentes, portanto, quando dados de vários arquivos de log são exibidos na grade de análise, algumas colunas não podem conter nenhum dado para uma determinada linha. Por exemplo, na imagem acima, as linhas de log do cliente não mostram nenhum dado para as colunas **timestamp**, decorrido, **origem**e **destino** , porque essas colunas não existem no log do cliente, mas existem no rastreamento de rede . Da mesma forma, a coluna **timestamp** exibe dados de carimbo de data/hora do log do servidor, mas nenhum dado é exibido para as colunas decorridas, **origem**e **destino** , que não fazem parte do log do servidor.
>
>

Além de usar os layouts de exibição do armazenamento do Azure, você também pode definir e salvar seus próprios layouts de exibição. Você pode selecionar outros campos desejados para agrupar dados e salvar o agrupamento como parte do seu layout personalizado também.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cor à grade de análise
Os ativos de armazenamento também incluem regras de cores, que oferecem um meio Visual para identificar diferentes tipos de erros na grade de análise. As regras de cor predefinidas se aplicam a erros de HTTP e, portanto, aparecem apenas para o log do servidor e o rastreamento de rede.

Para aplicar regras de cor, selecione **regras de cores** na faixa de opções da barra de ferramentas. Você verá as regras de cores do armazenamento do Azure no menu. Para o tutorial, selecione **erros de cliente (StatusCode entre 400 e 499)** , conforme mostrado na imagem abaixo.

![Layout de exibição do armazenamento do Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de usar as regras de cores de armazenamento do Azure, você também pode definir e salvar suas próprias regras de cor.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Agrupar e filtrar dados de log para encontrar erros de intervalo de 400
Em seguida, agruparemos e filtraremos os dados de log para localizar todos os erros no intervalo de 400.

1. Localize a coluna **StatusCode** na grade de análise, clique com o botão direito do mouse no título da coluna e selecione **grupo**.
2. Em seguida, agrupe na coluna **ClientRequestId** . Você verá que os dados na grade de análise agora estão organizados por código de status e por ID de solicitação do cliente.
3. Exiba a janela de ferramentas de filtro de exibição se ela ainda não estiver exibida. Na faixa de opções da barra de ferramentas, selecione **janelas de ferramentas**e, em seguida, **Exibir filtro**.
4. Para filtrar os dados de log para exibir apenas erros de intervalo 400, adicione os seguintes critérios de filtro à janela **filtro de exibição** e clique em **aplicar**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

A imagem abaixo mostra os resultados desse Agrupamento e filtro. Expandir o campo **ClientRequestID** sob o agrupamento para o código de status 409, por exemplo, mostra uma operação que resultou nesse código de status.

![Layout de exibição do armazenamento do Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar esse filtro, você verá que as linhas do log do cliente são excluídas, pois o log do cliente não inclui uma coluna **StatusCode** . Para começar, examinaremos os logs de rastreamento do servidor e da rede para localizar erros 404 e, em seguida, retornaremos ao log do cliente para examinar as operações do cliente que levaram a eles.

> [!NOTE]
> Você pode filtrar na coluna **StatusCode** e ainda exibir dados de todos os três logs, incluindo o log do cliente, se você adicionar uma expressão ao filtro que inclui entradas de log em que o código de status é nulo. Para construir essa expressão de filtro, use:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Esse filtro retorna todas as linhas do log do cliente e somente as linhas do log do servidor e do log HTTP em que o código de status é maior que 400. Se você aplicá-lo ao layout de exibição agrupado por ID de solicitação do cliente e módulo, poderá pesquisar ou percorrer as entradas de log para localizar onde todos os três logs são representados.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de log para encontrar erros 404
Os ativos de armazenamento incluem filtros predefinidos que você pode usar para restringir os dados de log para localizar os erros ou as tendências que você está procurando. Em seguida, aplicaremos dois filtros predefinidos: um que filtra os logs de rastreamento do servidor e da rede para erros 404 e um que filtra os dados em um intervalo de tempo especificado.

1. Exiba a janela de ferramentas de filtro de exibição se ela ainda não estiver exibida. Na faixa de opções da barra de ferramentas, selecione **janelas de ferramentas**e, em seguida, **Exibir filtro**.
2. Na janela filtro de exibição, selecione **biblioteca**e pesquise `Azure Storage` para localizar os filtros de armazenamento do Azure. Selecione o filtro para **mensagens 404 (não encontradas) em todos os logs**.
3. Exiba o menu **biblioteca** novamente e localize e selecione o **filtro de tempo global**.
4. Edite os carimbos de data/hora mostrados no filtro para o intervalo que você deseja exibir. Isso ajudará a restringir o intervalo de dados a serem analisados.
5. O filtro deve ser semelhante ao exemplo abaixo. Clique em **aplicar** para aplicar o filtro à grade de análise.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Layout de exibição do armazenamento do Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar os dados de log
Agora que você tiver agrupado e filtrado seus dados, poderá examinar os detalhes de solicitações individuais que geraram 404 erros. No layout de exibição atual, os dados são agrupados pela ID de solicitação do cliente e, em seguida, pela origem do log. Como estamos filtrando solicitações em que o campo StatusCode contém 404, veremos apenas os dados de rastreamento do servidor e da rede, não os dados de log do cliente.

A figura abaixo mostra uma solicitação específica em que uma operação get blob gerou um 404 porque o BLOB não existia. Observe que algumas colunas foram removidas da exibição padrão para exibir os dados relevantes.

![Logs de rastreamento de servidor e rede filtrados](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, correlacionaremos essa ID de solicitação do cliente com os dados de log do cliente para ver quais ações o cliente estava assumindo quando o erro ocorreu. Você pode exibir um novo modo de exibição de grade de análise para esta sessão para exibir os dados de log do cliente, que são abertos em uma segunda guia:

1. Primeiro, copie o valor do campo **ClientRequestId** para a área de transferência. Você pode fazer isso selecionando uma das linhas, localizando o campo **ClientRequestId** , clicando com o botão direito do mouse no valor de dados e escolhendo **Copiar ' ClientRequestId '** .
2. Na faixa de opções da barra de ferramentas, selecione **novo visualizador**e, em seguida, selecione **grade de análise** para abrir uma nova guia. A nova guia mostra todos os dados em seus arquivos de log, sem agrupamento, filtragem ou regras de cor.
3. Na faixa de opções da barra de ferramentas, selecione **Exibir layout**e, em seguida, selecione **todas as colunas do cliente .net** na seção **armazenamento do Azure** . Esse layout de exibição mostra dados do log do cliente, bem como os logs de rastreamento do servidor e da rede. Por padrão, ele é classificado na coluna **MessageNumber** .
4. Em seguida, procure a ID de solicitação do cliente no log do cliente. Na faixa de opções da barra de ferramentas, selecione **localizar mensagens**e especifique um filtro personalizado na ID de solicitação do cliente no campo **Localizar** . Use esta sintaxe para o filtro, especificando sua própria ID de solicitação de cliente:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

O analisador de mensagem localiza e seleciona a primeira entrada de log em que os critérios de pesquisa correspondem à ID de solicitação do cliente. No log do cliente, há várias entradas para cada ID de solicitação do cliente, portanto, talvez você queira agrupá-las no campo **ClientRequestId** para facilitar a visualização de todas elas. A figura abaixo mostra todas as mensagens no log do cliente para a ID de solicitação do cliente especificada.

![Log do cliente mostrando erros 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Usando os dados mostrados nos layouts de exibição nessas duas guias, você pode analisar os dados da solicitação para determinar o que pode ter causado o erro. Você também pode examinar as solicitações que precederam esta para ver se um evento anterior pode ter levado ao erro 404. Por exemplo, você pode examinar as entradas de log do cliente que antecederam essa ID de solicitação do cliente para determinar se o blob pode ter sido excluído ou se o erro ocorreu devido ao aplicativo cliente chamando uma API CreateIfNotExists em um contêiner ou BLOB. No log do cliente, você pode encontrar o endereço do blob no campo **Descrição** ; nos logs de rastreamento do servidor e da rede, essas informações são exibidas no campo **Resumo** .

Depois de saber o endereço do blob que gerou o erro 404, você pode investigar mais. Se você pesquisar as entradas de log para outras mensagens associadas às operações no mesmo BLOB, poderá verificar se o cliente excluiu previamente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento
Agora que você está familiarizado com o uso do analisador de mensagem para analisar os dados de log, é possível analisar outros tipos de erros usando layouts de exibição, regras de cores e pesquisa/filtragem. As tabelas a seguir listam alguns problemas que você pode encontrar e os critérios de filtro que você pode usar para localizá-los. Para obter mais informações sobre como construir filtros e a linguagem de filtragem do analisador de mensagem, consulte [filtragem de dados de mensagem](https://technet.microsoft.com/library/jj819365.aspx).

| Para investigar... | Usar expressão de filtro... | A expressão se aplica ao log (cliente, servidor, rede, tudo) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens em uma fila |AzureStorageClientDotNetV4. Description contém "repetindo a operação com falha". |Cliente |
| Aumento de HTTP em PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Rede |
| Aumento em PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Rede |
| Aumento em PercentTimeoutError (todos) |*StatusCode   == 500 |Todos |
| Aumento em PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Cliente |
| Mensagens HTTP 403 (proibido) |HTTP.Response.StatusCode   == 403 |Rede |
| Mensagens HTTP 404 (não encontradas) |HTTP.Response.StatusCode   == 404 |Rede |
| 404 (todos) |*StatusCode   == 404 |Todos |
| Problema de autorização da Assinatura de Acesso Partilhado (SAS) |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Rede |
| Mensagens HTTP 409 (conflito) |HTTP.Response.StatusCode   == 409 |Rede |
| 409 (todos) |*StatusCode   == 409 |Todos |
| Baixa PercentSuccess ou entradas de log de análise têm operações com status de transação de ClientOtherErrors |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Servidor |
| Aviso de Nagle |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) e (AzureStorageLog. RequestPacketSize < 1460) e (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS > = 200) |Servidor |
| Intervalo de tempo nos logs de servidor e de rede |#Timestamp > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39 |Servidor, rede |
| Intervalo de tempo nos logs do servidor |AzureStorageLog. Timestamp > = 2014-10-20T16:36:38 e AzureStorageLog. Timestamp < = 2014-10-20T16:36:39 |Servidor |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como solucionar problemas de cenários de ponta a ponta no armazenamento do Azure, consulte estes recursos:

* [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Guia de operação do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
