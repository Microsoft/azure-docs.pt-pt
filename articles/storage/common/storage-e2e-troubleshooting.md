---
title: Operações de dados de resolução de problemas com diagnósticos e analisador de mensagens
titleSuffix: Azure Storage
description: Um tutorial que demonstra problemas de ponta a ponta com o Azure Storage Analytics, a AzCopy e o Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8dc3c629830019a6c207c18f1783559e89512172
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610977"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Resolução de problemas ponto a ponto com as métricas e o registo de Armazenamento do Azure, o AzCopy e o Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnosticar e resolver problemas é uma habilidade fundamental para construir e apoiar aplicações de clientes com o Microsoft Azure Storage. Devido à natureza distribuída de uma aplicação Azure, os erros de diagnóstico e resolução de problemas e problemas de desempenho podem ser mais complexos do que em ambientes tradicionais.

Neste tutorial, demonstramos como identificar certos erros que podem afetar o desempenho, e resolver esses erros de ponta a ponta usando ferramentas fornecidas pela Microsoft e pelo Azure Storage, de forma a otimizar a aplicação do cliente.

Este tutorial proporciona uma exploração prática de um cenário de resolução de problemas de ponta a ponta. Para um guia conceptual aprofundado para resolver problemas as aplicações de armazenamento do Azure, consulte [monitor, diagnóstico e resolução de problemas do Armazenamento Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para resolução de problemas aplicações de armazenamento Azure

Para resolver as aplicações dos clientes utilizando o Microsoft Azure Storage, pode utilizar uma combinação de ferramentas para determinar quando ocorreu um problema e qual a causa do problema. Estas ferramentas incluem:

* **Análise de Armazenamento Azure.** [A Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) fornece métricas e exploração madeireira para o Armazenamento Azure.

  * **As métricas de armazenamento** rastreiam métricas de transação e métricas de capacidade para a sua conta de armazenamento. Utilizando métricas, pode determinar como a sua aplicação está a funcionar de acordo com uma variedade de medidas diferentes. Consulte o [Storage Analytics Metrics Table Schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) para obter mais informações sobre os tipos de métricas rastreadas pela Storage Analytics.
  * **O registo de registos de armazenamento** regista cada pedido para os serviços de Armazenamento Azure para um registo do lado do servidor. O registo regista dados detalhados para cada pedido, incluindo a operação realizada, o estado da operação e informações de latência. Consulte o [Formato de Registo de Armazenamento](/rest/api/storageservices/Storage-Analytics-Log-Format) Analytics para obter mais informações sobre os dados de pedido e resposta que estão escritos nos registos pelo Storage Analytics.

* **Portal Azure.** Pode configurar métricas e fazer login para a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Também pode ver gráficos e gráficos que mostram como a sua aplicação está a funcionar ao longo do tempo, e configurar alertas para notificá-lo se a sua aplicação funcionar de forma diferente do esperado para uma métrica especificada.

    Consulte o Monitor uma conta de [armazenamento no portal Azure](storage-monitor-storage-account.md) para obter informações sobre a configuração da monitorização no portal Azure.
* **AzCopy.** Os registos do servidor para o Armazenamento Azure são armazenados como bolhas, para que possa utilizar o AzCopy para copiar as bolhas de registo para um diretório local para análise utilizando o Microsoft Message Analyzer. Consulte os dados de transferência com o Utilitário de [Linha de Comando AzCopy](storage-use-azcopy.md) para obter mais informações sobre o AzCopy.
* **Microsoft Message Analyzer**. O Message Analyzer é uma ferramenta que consome ficheiros de registo e exibe dados de registo num formato visual que facilita o filtrar, pesquisar e registar dados de grupo em conjuntos úteis que pode utilizar para analisar erros e problemas de desempenho. Consulte o Guia Operativo do Analisador de [Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o Analisador de Mensagens.

## <a name="about-the-sample-scenario"></a>Sobre o cenário da amostra

Para este tutorial, vamos examinar um cenário em que as métricas de Armazenamento Azure indicam uma baixa taxa de sucesso para uma aplicação que chama armazenamento Azure. A métrica de baixa taxa de sucesso (mostrada como **PercentSuccess** no [portal Azure](https://portal.azure.com) e nas tabelas de métricas) acompanha operações que sucedem, mas que devolvem um código de estado HTTP superior a 299. Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com um estado de transação de **ClientesOtherErrors**. Para mais detalhes sobre a métrica de baixo sucesso, consulte Métricas que mostram que as entradas de [registo de baixo percentsucesso ou de analítico têm operações com o estado de transação do ClienteOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

As operações de armazenamento azure podem devolver códigos de estado HTTP superiores a 299 como parte da sua funcionalidade normal. Mas estes erros em alguns casos indicam que poderá otimizar a aplicação do seu cliente para um melhor desempenho.

Neste cenário, consideraremos uma taxa de sucesso baixa por cento abaixo dos 100%. No entanto, pode escolher um nível métrico diferente, de acordo com as suas necessidades. Recomendamos que durante os testes da sua aplicação, estabeleça uma tolerância de base para as suas principais métricas de desempenho. Por exemplo, pode determinar, com base em testes, que a sua aplicação deve ter uma taxa de sucesso consistente de 90%, ou 85%. Se os dados das suas métricas mostrarem que a aplicação está a desviar-se desse número, então pode investigar o que pode estar a causar o aumento.

Para o nosso cenário de amostragem, uma vez estabelecido que a métrica de taxa de sucesso por cento é inferior a 100%, examinaremos os registos para encontrar os erros que se relacionam com as métricas, e usá-los para descobrir o que está a causar a taxa de sucesso mais baixa. Vamos analisar especificamente os erros na faixa dos 400. Então vamos investigar mais de perto 404 erros (não encontrados).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas de erros de 400 alcances

Os exemplos abaixo mostram uma amostra de cerca de 400 erros de alcance para pedidos contra o Armazenamento de Blob Azure, e as suas possíveis causas. Qualquer um destes erros, bem como erros na gama 300 e na gama 500, podem contribuir para uma taxa de sucesso baixa por cento.

Note que as listas abaixo estão longe de estar completas. Consulte [o Status and Error Codes](https://msdn.microsoft.com/library/azure/dd179382.aspx) na MSDN para obter detalhes sobre os erros gerais de armazenamento do Azure e sobre erros específicos de cada um dos serviços de armazenamento.

#### <a name="status-code-404-not-found-examples"></a>Código de Estado 404 (Não Encontrado) Exemplos

Ocorre quando uma operação de leitura contra um recipiente ou bolha falha porque a bolha ou o recipiente não é encontrado.

* Ocorre se um recipiente ou bolha tiver sido apagado por outro cliente antes deste pedido.
* Ocorre se estiver a utilizar uma chamada API que cria o recipiente ou a bolha depois de verificar se existe. As APIs CreateIfNotExists fazem primeiro uma chamada HEAD para verificar a existência do recipiente ou da bolha; se não existir, é devolvido um erro 404 e, em seguida, uma segunda chamada PUT é feita para escrever o recipiente ou a bolha.

#### <a name="status-code-409-conflict-examples"></a>Exemplos do Código de Estado 409 (Conflito)

* Ocorre se utilizar uma API Create para criar um novo recipiente ou bolha, sem verificar a existência primeiro, e já existe um recipiente ou uma bolha com esse nome.
* Ocorre se um recipiente estiver a ser apagado e tentar criar um novo recipiente com o mesmo nome antes da operação de eliminação estar concluída.
* Ocorre se especificar um arrendamento num contentor ou bolha, e já existe um contrato de arrendamento presente.

#### <a name="status-code-412-precondition-failed-examples"></a>Código de Estado 412 (Pré-condição Falhou) Exemplos

* Ocorre quando a condição especificada por um cabeçalho condicional não foi satisfeita.
* Ocorre quando o ID de locação especificado não corresponde ao ID de locação no recipiente ou na bolha.

## <a name="generate-log-files-for-analysis"></a>Gerar ficheiros de registo para análise

Neste tutorial, usaremos o Analisador de Mensagens para trabalhar com três tipos diferentes de ficheiros de registo, embora possa optar por trabalhar com qualquer um destes:

* O registo do **servidor**, que é criado quando ativa o registo de armazenamento do Azure. O registo do servidor contém dados sobre cada operação chamada contra um dos serviços de Armazenamento Azure - blob, fila, mesa e ficheiro. O registo do servidor indica qual a operação chamada e qual o código de estado devolvido, bem como outros detalhes sobre o pedido e resposta.
* O registo do **cliente .NET**, que é criado quando permite a exploração de registo do lado do cliente a partir da sua aplicação .NET. O registo do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.
* O registo de rastreio da **rede HTTP,** que recolhe dados sobre os dados de pedido e resposta http/HTTPS, incluindo para operações contra o Armazenamento Azure. Neste tutorial, vamos gerar o rastreio da rede através do Analisador de Mensagens.

### <a name="configure-server-side-logging-and-metrics"></a>Configure a exploração madeireira e métricas do lado do servidor

Primeiro, precisamos configurar o registo de armazenamento do Azure e métricas, para que tenhamos dados do lado do serviço para analisar. Pode configurar a exploração madeireira e as métricas de várias maneiras - através do [portal Azure](https://portal.azure.com), utilizando o PowerShell, ou programáticamente. Consulte [as métricas de ativação](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) e ative a [exploração de registos](storage-analytics-logging.md#enable-storage-logging) para obter detalhes sobre a configuração da exploração madeireira e métricas.

### <a name="configure-net-client-side-logging"></a>Configure .NET registo do lado do cliente

Para configurar o registo do lado do cliente para uma aplicação .NET, ative os diagnósticos .NET no ficheiro de configuração da aplicação (web.config ou app.config). Consulte a registo do lado do cliente com a Biblioteca de [Clientes de Armazenamento .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) e a Marcação do [Cliente com o Microsoft Azure Storage SDK para Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) na MSDN para obter mais detalhes.

O registo do lado do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.

A Biblioteca do Cliente de Armazenamento armazena dados de registo do lado do cliente no local especificado no ficheiro de configuração da aplicação (web.config ou app.config).

### <a name="collect-a-network-trace"></a>Recolher um traço de rede

Pode utilizar o Analisador de Mensagens para recolher um traço de rede HTTP/HTTPS enquanto a sua aplicação de cliente está em execução. O Analisador de Mensagens usa [o Fiddler](https://www.telerik.com/fiddler) na parte de trás. Antes de recolher o rastreio da rede, recomendamos que configure o Fiddler para registar o tráfego HTTPS não encriptado:

1. Instale [o Violinista](https://www.telerik.com/download/fiddler).
2. Lança o Violinista.
3. Selecione **Ferramentas / Opções de violinista.**
4. No diálogo Opções, certifique-se de que **a Captura HTTPS CONNECTs** e **desencriptação HTTPS Tráfego** são ambos selecionados, como mostrado abaixo.

![Configure Opções de Violino](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para o tutorial, colete e guarde primeiro um traço de rede no Analisador de Mensagens, em seguida, crie uma sessão de análise para analisar os vestígios e os registos. Para recolher um vestígio de rede no Analisador de Mensagens:

1. No Analisador de Mensagens, selecione **Arquivo / Traço Rápido [ Traço Rápido] HTTPS não encriptado**.
2. O vestígio começará imediatamente. Selecione **Stop** para parar o vestígio para que possamos configurá-lo apenas para rastrear o tráfego de armazenamento.
3. **Selecione Editar** para editar a sessão de rastreio.
4. Selecione o link **Configure** à direita do fornecedor **Microsoft-Pef-WebProxy** ETW.
5. No diálogo **Definições Avançadas,** clique no separador **Fornecedor.**
6. No campo **Filtro de Nome anfitrião,** especifique os seus pontos finais de armazenamento, separados por espaços. Por exemplo, pode especificar os seus pontos finais da seguinte forma; alterar `storagesample` para o nome da sua conta de armazenamento:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Saia do diálogo e clique em **Reiniciar** para começar a recolher o vestígio com o filtro de nome de anfitrião no lugar, de modo que apenas o tráfego da rede de armazenamento Azure esteja incluído no vestígio.

> [!NOTE]
> Depois de ter terminado de recolher o seu rastreio de rede, recomendamos vivamente que reverta as definições que pode ter mudado no Fiddler para desencriptar o tráfego HTTPS. No diálogo Fiddler Options, desmarque as caixas de verificação **Desencriptação HTTPS** e **desencriptar https** caixas de verificação de tráfego.

Consulte [a utilização das funcionalidades](https://technet.microsoft.com/library/jj674819.aspx) de rastreio de rede na Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Analisar dados de métricas no portal Azure

Uma vez que a sua aplicação esteja em execução por um período de tempo, pode rever as tabelas de métricas que aparecem no [portal Azure](https://portal.azure.com) para observar como o seu serviço tem vindo a funcionar.

Primeiro, navegue para a sua conta de armazenamento no portal Azure. Por predefinição, é apresentado um gráfico de monitorização com a **métrica da percentagem** de sucesso na lâmina da conta. Se já modificou o gráfico para apresentar métricas diferentes, adicione a métrica **da percentagem** de sucesso.

Verá agora **a percentagem de sucesso** na tabela de monitorização, juntamente com quaisquer outras métricas que possa ter adicionado. No cenário vamos investigar a seguir analisando os registos no Analisador de Mensagens, a taxa de sucesso por cento é um pouco abaixo de 100%.

Para mais detalhes sobre a adição e personalização dos gráficos de métricas, consulte [Personalizar gráficos](storage-monitor-storage-account.md#customize-metrics-charts)de métricas .

> [!NOTE]
> Pode levar algum tempo para que os seus dados métricos apareçam no portal Azure depois de ativar as métricas de armazenamento. Isto porque as métricas horárias para a hora anterior não são exibidas no portal Azure até que a hora atual tenha decorrido. Além disso, as métricas minúsculas não são atualmente exibidas no portal Azure. Portanto, dependendo de quando ativar as métricas, pode levar até duas horas para ver os dados das métricas.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilize o AzCopy para copiar registos de servidores para um diretório local

O Azure Storage escreve dados de registo do servidor a bolhas, enquanto as métricas são escritas em tabelas. As bolhas de log estão `$logs` disponíveis no conhecido recipiente para a sua conta de armazenamento. As bolhas de log são nomeadas hierárquicamente por ano, mês, dia e hora, para que possa facilmente localizar o intervalo de tempo que pretende investigar. Por exemplo, `storagesample` na conta, o recipiente para as bolhas de madeira para 01/02/2015, das 8h às 9h, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. As bolhas individuais neste recipiente são nomeadas sequencialmente, começando por `000000.log`.

Pode utilizar a ferramenta de linha de comando AzCopy para descarregar estes ficheiros de registo do lado do servidor para uma localização à sua escolha na sua máquina local. Por exemplo, pode utilizar o seguinte comando para descarregar os ficheiros de registo para operações `C:\Temp\Logs\Server`blob que ocorreram no dia 2 de janeiro de 2015 para a pasta; substituir `<storageaccountname>` pelo nome da sua conta de armazenamento:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

O AzCopy está disponível para download na página [Descarregamentos do Azure.](https://azure.microsoft.com/downloads/) Para mais detalhes sobre a utilização do AzCopy, consulte os dados de transferência com o Utilitário de [Linha de Comando AzCopy](storage-use-azcopy.md).

Para obter informações adicionais sobre o download de registos do lado do servidor, consulte os dados de registo do registo do registo do registo do armazenamento de [depósitos](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilize o Microsoft Message Analyzer para analisar dados de registo

O Microsoft Message Analyzer é uma ferramenta para capturar, exibir e analisar o tráfego de mensagens protocolares, eventos e outras mensagens de sistema ou aplicação em cenários de resolução de problemas e diagnóstico. O Analisador de Mensagens também lhe permite carregar, agregar e analisar dados de ficheiros de registo e de rastreio guardados. Para mais informações sobre o Analisador de Mensagens, consulte o [Guia de Funcionamento do Analisador](https://technet.microsoft.com/library/jj649776.aspx)de Mensagens da Microsoft .

O Message Analyzer inclui ativos para o Armazenamento Azure que o ajudam a analisar registos de servidores, clientes e rede. Nesta secção, discutiremos como usar essas ferramentas para resolver a questão do baixo sucesso nos registos de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Descarregue e instale o Analisador de Mensagens e os Ativos de Armazenamento Azure

1. Descarregue [o Analisador de Mensagens](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Analisador de mensagens de lançamento.
3. A partir do menu **Ferramentas,** selecione Gestor de **Ativos.** No diálogo do Gestor de **Ativos,** selecione **Downloads**e, em seguida, filtre no **Armazenamento Azure**. Você verá os Ativos de Armazenamento Azure, como mostra a imagem abaixo.
4. Clique em **Sync Todos os itens apresentados** para instalar os Ativos de Armazenamento Azure. Os ativos disponíveis incluem:
   * Regras de cor de **armazenamento azure:** As regras de cores de armazenamento azure permitem definir filtros especiais que usam estilos de cor, texto e fonte para destacar mensagens que contenham informações específicas num traço.
   * **Gráficos de armazenamento azure:** Os gráficos de Armazenamento Azure são gráficos predefinidos que registam dados de registo de servidores gráficos. Note que, para utilizar as tabelas de armazenamento do Azure neste momento, só pode carregar o registo do servidor na Grelha de Análise.
   * **Parsers de armazenamento azure:** Os parsers de armazenamento Azure analisam os registos do cliente, servidor e HTTP do Azure Storage para os exibir na Grelha de Análise.
   * **Filtros de armazenamento Azure:** Os filtros de armazenamento azure são critérios predefinidos que pode utilizar para consultar os seus dados na Grelha de Análise.
   * Layouts de vista de **armazenamento azure:** Os layouts de vista de armazenamento azure são layouts de colunas predefinidos e agrupamentos na Grelha de Análise.
5. Reiniciar o Analisador de Mensagens depois de ter instalado os ativos.

![Gestor de ativos do Analisador de Mensagens](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os ativos de Armazenamento Azure apresentados para efeitos deste tutorial.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importe os seus ficheiros de registo para o Analisador de Mensagens

Pode importar todos os seus ficheiros de registo guardados (lado do servidor, lado do cliente e rede) numa única sessão no Microsoft Message Analyzer para análise.

1. No menu **'Ficheiro'** no Microsoft Message Analyzer, clique em **New Session**, e, em seguida, clique em **Blank Session**. No diálogo **New Session,** introduza um nome para a sua sessão de análise. No painel Detalhes da **Sessão,** clique no botão **Ficheiros.**
2. Para carregar os dados de rastreio de rede gerados pelo Analisador de Mensagens, clique em **Adicionar Ficheiros,** navegue para o local onde guardou o ficheiro .matp da sua sessão de rastreio web, selecione o ficheiro .matp e clique em **Open**.
3. Para carregar os dados de registo do lado do servidor, clique em **Adicionar Ficheiros,** navegue para o local onde descarregou os registos do lado do servidor, selecione os ficheiros de registo para o intervalo de tempo que pretende analisar e clique em **Abrir**. Em seguida, no painel detalhes da **sessão,** delineie a definição de **configuração** de registo de texto para cada ficheiro de registo do lado do servidor para **o AzureStorageLog** para garantir que o Microsoft Message Analyzer pode analisar corretamente o ficheiro de registo.
4. Para carregar os dados de registo do lado do cliente, clique em **Adicionar Ficheiros,** navegue para o local onde guardou os registos do lado do cliente, selecione os ficheiros de registo que pretende analisar e clique em **Abrir**. Em seguida, no painel detalhes da **sessão,** delineie a definição de **configuração** de registo de texto para cada ficheiro de registo do lado do cliente para **o AzureStorageClientDotNetV4** para garantir que o Microsoft Message Analyzer pode analisar corretamente o ficheiro de registo.
5. Clique em **Iniciar** o diálogo **New Session** para carregar e analisar os dados de registo. Os dados de registo mostram-se na Grelha de Análise de Analisadores de Mensagens.

A imagem abaixo mostra uma sessão de exemplo configurada com ficheiros de registo de servidores, clientes e rastreios de rede.

![Configure Sessão de Analisador de Mensagens](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Note que o Analisador de Mensagens carrega ficheiros de registo na memória. Se tiver um grande conjunto de dados de registo, irá querer filtrar para obter o melhor desempenho do Analisador de Mensagens.

Primeiro, determine o prazo que está interessado em rever, e mantenha este prazo o mais pequeno possível. Em muitos casos, você vai querer rever um período de minutos ou horas no máximo. Importe o menor conjunto de troncos que possa satisfazer as suas necessidades.

Se ainda tiver uma grande quantidade de dados de registo, então talvez queira especificar um filtro de sessão para filtrar os seus dados de registo antes de os carregar. Na caixa filtro de **sessão,** selecione o botão **Biblioteca** para escolher um filtro predefinido; por exemplo, escolha o **Filtro De Tempo Global I** dos filtros de armazenamento Azure para filtrar num intervalo de tempo. Em seguida, pode editar os critérios do filtro para especificar a marca de tempo inicial e final para o intervalo que pretende ver. Também pode filtrar um determinado código de estado; por exemplo, pode optar por carregar apenas entradas de registo quando o código de estado for 404.

Para obter mais informações sobre a importação de dados de registo no Microsoft Message Analyzer, consulte [a Recuperação de Dados](https://technet.microsoft.com/library/dn772437.aspx) de Mensagens no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilize o PEDIDO de cliente ID para correlacionar os dados de ficheiros de registo

A Biblioteca de Clientes de Armazenamento Azure gera automaticamente um ID de pedido único de cliente para cada pedido. Este valor está escrito no registo do cliente, no registo do servidor e no rastreio da rede, para que possa usá-lo para correlacionar os dados nos três registos dentro do Analisador de Mensagens. Consulte o [pedido de id do Cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre o ID do pedido do cliente.

As secções abaixo descrevem como usar vistas de layout pré-configuradas e personalizadas para correlacionar e dados de grupo com base no ID de pedido do cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um layout de visualização para exibir na Grelha de Análise

Os Ativos de Armazenamento para Analisador de Mensagens incluem layouts de vista de armazenamento Azure, que são vistas pré-configuradas que pode usar para exibir os seus dados com agrupamentos e colunas úteis para diferentes cenários. Também pode criar layouts de vista personalizadas e guardá-los para reutilização.

A imagem abaixo mostra o menu **'Ver Layout',** disponível selecionando o **View Layout** da fita da barra de ferramentas. Os layouts de vista para o Armazenamento Azure estão agrupados sob o nó de **Armazenamento Azure** no menu. Pode pesquisar `Azure Storage` na caixa de pesquisa para filtrar apenas os layouts de vista de armazenamento azure. Também pode selecionar a estrela ao lado de um layout de visualização para torná-la uma favorita e exibi-la no topo do menu.

![Ver menu layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, selecione **Grouped by ClientRequestID e Módulo**. Esta visualização grupos de layout registam dados dos três registos primeiro por id pedido do cliente, em seguida, por ficheiro de registo de origem (ou **Módulo** no Analisador de Mensagens). Com esta visão, você pode aprofundar um determinado pedido de identificação do cliente, e ver dados dos três ficheiros de registo para esse pedido de identificação do cliente.

A imagem abaixo mostra esta vista de layout aplicada aos dados de registo da amostra, com um subconjunto de colunas apresentadas. Pode ver que para um determinado pedido de cliente ID, a Grelha de Análise exibe dados do registo do cliente, registo do servidor e rastreio de rede.

![Layout de vista de armazenamento azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Diferentes ficheiros de registo têm colunas diferentes, pelo que quando os dados de vários ficheiros de registo são apresentados na Grelha de Análise, algumas colunas podem não conter quaisquer dados para uma determinada linha. Por exemplo, na imagem acima, as linhas de registo do cliente não mostram quaisquer dados para as colunas **Timestamp**, **TimeElapsed**, **Source**e **Destination,** porque estas colunas não existem no registo do cliente, mas existem no rastreio da rede. Da mesma forma, a coluna **Timestamp** exibe dados de carimbo supor o tempo do registo do servidor, mas não são apresentados dados para as colunas **TimeElapsed**, **Source**e **Destination,** que não fazem parte do registo do servidor.
>
>

Além de utilizar os layouts de vista azure storage, também pode definir e guardar os layouts de vista próprias. Pode selecionar outros campos desejados para agrupar dados e guardar o agrupamento como parte do seu layout personalizado também.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cores na Grelha de Análise

Os Ativos de Armazenamento também incluem regras de cores, que oferecem um meio visual para identificar diferentes tipos de erros na Grelha de Análise. As regras de cores predefinidas aplicam-se aos erros HTTP, pelo que aparecem apenas para o registo do servidor e para o rastreio da rede.

Para aplicar regras de cores, selecione Regras de **Cor** da fita da barra de ferramentas. Você verá as regras de cores de armazenamento azure no menu. Para o tutorial, selecione **Erros de Cliente (StatusCode entre 400 e 499)**, como mostra a imagem abaixo.

![Layout de vista de armazenamento azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de usar as regras de cores de armazenamento Azure, também pode definir e salvar as suas próprias regras de cores.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Dados de registo de grupo e filtro para encontrar erros de 400 alcances

Em seguida, vamos agrupar e filtrar os dados de registo para encontrar todos os erros na gama 400.

1. Localize a coluna **StatusCode** na Grelha de Análise, clique na direção da coluna e selecione **Grupo**.
2. Em seguida, grupo na coluna **ClientRequestId.** Verá que os dados na Grelha de Análise são agora organizados por código de estado e por id de pedido do cliente.
3. Exiba a janela da ferramenta Ver Filtro se ainda não estiver visualizada. Na fita da barra de ferramentas, selecione **Tool Windows,** em seguida, **ver filtro**.
4. Para filtrar os dados de registo para visualizar apenas erros de 400 alcances, adicione os seguintes critérios de filtro à janela **'Ver Filtro'** e clique **Em Aplicar:**

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

A imagem abaixo mostra os resultados deste agrupamento e filtro. Expandir o campo **ClientRequestID** por baixo do agrupamento para o código de estado 409, por exemplo, mostra uma operação que resultou nesse código de estado.

![Layout de vista de armazenamento azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Após a aplicação deste filtro, verá que as filas do registo do cliente estão excluídas, uma vez que o registo do cliente não inclui uma coluna **'StatusCode'.** Para começar, vamos rever os registos de registos do servidor e da rede para localizar 404 erros, e depois voltaremos ao registo do cliente para examinar as operações do cliente que os levaram.

> [!NOTE]
> Pode filtrar na coluna **StatusCode** e ainda exibir dados dos três registos, incluindo o registo do cliente, se adicionar uma expressão ao filtro que inclui entradas de registo onde o código de estado é nulo. Para construir esta expressão de filtro, utilize:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Este filtro devolve todas as linhas do registo do cliente e apenas linhas do registo do servidor e registo HTTP onde o código de estado é superior a 400. Se o aplicar no layout de visualização agrupado pelo ID e módulo de pedido do cliente, pode pesquisar ou percorrer as entradas de registo para encontrar aqueles onde os três registos estão representados.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de registo para encontrar 404 erros

Os Ativos de Armazenamento incluem filtros predefinidos que pode utilizar para reduzir os dados de registo para encontrar os erros ou tendências que procura. Em seguida, aplicaremos dois filtros predefinidos: um que filtra os registos do servidor e da rede para 404 erros, e um que filtra os dados num intervalo de tempo especificado.

1. Exiba a janela da ferramenta Ver Filtro se ainda não estiver visualizada. Na fita da barra de ferramentas, selecione **Tool Windows,** em seguida, **ver filtro**.
2. Na janela 'Ver Filtro', selecione **Biblioteca**, e procure `Azure Storage` os filtros de armazenamento Azure. Selecione o filtro para **404 mensagens (não encontradas) em todos os registos**.
3. Volte a mostrar o menu **da Biblioteca** e localize e selecione o Filtro De **Tempo Global**.
4. Edite os selos de tempo mostrados no filtro ao alcance que pretende ver. Isto ajudará a reduzir o leque de dados para analisar.
5. O seu filtro deve parecer semelhante ao exemplo abaixo. Clique **Aplicar** para aplicar o filtro na Grelha de Análise.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Layout de vista de armazenamento azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar os seus dados de registo

Agora que agrupou e filtra os seus dados, pode examinar os detalhes de pedidos individuais que geraram 404 erros. No layout de visualização atual, os dados são agrupados por id pedido do cliente, em seguida, por fonte de registo. Uma vez que estamos a filtrar pedidos onde o campo StatusCode contém 404, veremos apenas os dados de rastreio do servidor e da rede, e não os dados de registo do cliente.

A imagem abaixo mostra um pedido específico onde uma operação Get Blob rendeu um 404 porque a bolha não existia. Note que algumas colunas foram removidas da vista padrão para exibir os dados relevantes.

![Registos de rastreio de servidor filtrado e de rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, correlacionaremos este cliente solicitando identificação com os dados de registo do cliente para ver que ações o cliente estava tomando quando o erro aconteceu. Pode apresentar uma nova vista De análise para esta sessão para visualizar os dados de registo do cliente, que se abre num segundo separador:

1. Em primeiro lugar, copie o valor do campo **ClientRequestId** para a área de redação. Pode fazê-lo selecionando qualquer linha, localizando o campo **ClientRequestId,** clicando à direita no valor de dados e escolhendo **copy 'ClientRequestId'.**
2. Na fita da barra de ferramentas, selecione **New Viewer**e, em seguida, selecione **Analysis Grid** para abrir um novo separador. O novo separador mostra todos os dados dos seus ficheiros de registo, sem agrupar, filtrar ou colorir regras.
3. Na fita da barra de ferramentas, selecione **'Layout' de visualização**e, em seguida, selecione **Todas as Colunas** de Cliente .NET sob a secção **de Armazenamento Azure.** Este layout de visualização mostra dados do registo do cliente, bem como os registos de rastreio do servidor e da rede. Por defeito, está classificado na coluna **MessageNumber.**
4. Em seguida, procure no registo do cliente a identificação do pedido do cliente. Na fita da barra de ferramentas, selecione **Encontrar Mensagens**e, em seguida, especificar um filtro personalizado no ID de pedido do cliente no campo **Find.** Utilize esta sintaxe para o filtro, especificando o id de pedido do seu próprio cliente:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

O Analisador de Mensagens localiza e seleciona a primeira entrada de registo onde os critérios de pesquisa correspondem ao ID de pedido do cliente. No registo do cliente, existem várias entradas para cada cliente solicitar ID, pelo que poderá querer agrupá-los no campo **ClientRequestId** para facilitar a sua visão de todos juntos. A imagem abaixo mostra todas as mensagens no registo do cliente para o ID de pedido de cliente especificado.

![Registo de clientes mostrando 404 erros](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilizando os dados mostrados nos layouts de visualização nestes dois separadores, pode analisar os dados do pedido para determinar o que pode ter causado o erro. Pode também analisar os pedidos que precederam este para ver se um evento anterior pode ter levado ao erro de 404. Por exemplo, pode rever as entradas de registo do cliente anteriores a este id de pedido de cliente para determinar se a bolha pode ter sido eliminada, ou se o erro foi devido à aplicação do cliente chamando uma API CreateIfNotExists num recipiente ou bolha. No registo do cliente, pode encontrar o endereço da bolha no campo **Descrição;** nos registos de rastreios do servidor e da rede, esta informação aparece no campo **Resumo.**

Assim que souberes qual é a morada da bolha que deu origem ao erro 404, podes investigar mais. Se pesquisar as entradas de registo para outras mensagens associadas a operações na mesma bolha, pode verificar se o cliente apagou previamente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento

Agora que está familiarizado com a utilização do Message Analyzer para analisar os seus dados de registo, pode analisar outros tipos de erros utilizando layouts de visualização, regras de cores e pesquisa/filtragem. As tabelas abaixo listam alguns problemas que pode encontrar e os critérios de filtro que pode utilizar para localizá-los. Para obter mais informações sobre a construção de filtros e o idioma de filtragem do Analisador de Mensagens, consulte os dados da [mensagem de filtragem](https://technet.microsoft.com/library/jj819365.aspx).

| Para investigar... | Use a expressão do filtro... | Expressão aplica-se ao registo (Cliente, Servidor, Rede, Tudo) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens numa fila |AzureStorageClientDotNetV4.Descrição contém "Retry failed operation". |Cliente |
| HTTP Aumento no PercentThrottlingError |HTTP. Resposta.StatusCode == 500 &#124;&#124; HTTP. Resposta.StatusCode == 503 |Rede |
| Aumento do PercentTimeoutError |HTTP. Resposta.StatusCode == 500 |Rede |
| Aumento do PercentTimeoutError (todos) |*StatusCode == 500 |Todos |
| Aumento do PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Cliente |
| Mensagens HTTP 403 (Proibido) |HTTP. Resposta.StatusCode == 403 |Rede |
| Mensagens HTTP 404 (Não encontrado) |HTTP. Resposta.StatusCode == 404 |Rede |
| 404 (todos) |*StatusCode == 404 |Todos |
| Problema de autorização da Assinatura de Acesso Partilhado (SAS) |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Rede |
| Mensagens HTTP 409 (Conflito) |HTTP. Resposta.StatusCode == 409 |Rede |
| 409 (todos) |*StatusCode == 409 |Todos |
| As entradas de registo low percentSuccess ou analytics têm operações com o estado de transação do ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Servidor |
| Aviso de Nagle |((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) e (AzureStorageLog.RequestPacketSize <1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS >= 200) |Servidor |
| Intervalo de tempo nos registos de Servidor e Rede |#Timestamp >= 2014-10-20T16:36:38 e #Timestamp <= 2014-10-20T16:36:39 |Servidor, Rede |
| Intervalo de tempo nos registos do Servidor |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 e AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Servidor |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre cenários de resolução de problemas de ponta a ponta no Armazenamento Azure, consulte estes recursos:

* [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure (Monitor, diagnose, and troubleshoot Microsoft Azure Storage)](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Guia de Funcionamento do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
