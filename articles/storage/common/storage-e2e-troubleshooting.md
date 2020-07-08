---
title: Resolução de problemas de operações de dados com diagnósticos e analisador de mensagens
titleSuffix: Azure Storage
description: Um tutorial que demonstra resolução de problemas de ponta a ponta com a Azure Storage Analytics, AzCopy e Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: ozgun
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: e94810d53bf28aeb03de32a440c56a95232d0c2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506349"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Resolução de problemas ponto a ponto com as métricas e o registo de Armazenamento do Azure, o AzCopy e o Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnosticar e resolver problemas é uma habilidade fundamental para construir e apoiar aplicações de clientes com o Microsoft Azure Storage. Devido à natureza distribuída de uma aplicação Azure, diagnosticar e resolver problemas e problemas de desempenho podem ser mais complexos do que nos ambientes tradicionais.

Neste tutorial, demonstramos como identificar certos erros que podem afetar o desempenho, e resolver esses erros de ponta a ponta utilizando ferramentas fornecidas pela Microsoft e pelo Azure Storage, de forma a otimizar a aplicação do cliente.

Este tutorial proporciona uma exploração prática de um cenário de resolução de problemas de ponta a ponta. Para obter um guia conceptual aprofundado para resolver problemas com as aplicações de armazenamento Azure, consulte [monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para resolução de problemas aplicações de armazenamento Azure

Para resolver problemas com as aplicações do cliente utilizando o Microsoft Azure Storage, pode utilizar uma combinação de ferramentas para determinar quando ocorreu um problema e qual poderá ser a causa do problema. Estas ferramentas incluem:

* **Azure Storage Analytics**. [A Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) fornece métricas e registos para o Armazenamento Azure.

  * **As métricas de armazenamento** rastreia as métricas de transação e as métricas de capacidade para a sua conta de armazenamento. Usando métricas, pode determinar como a sua aplicação está a executar de acordo com uma variedade de medidas diferentes. Consulte [o Esquema de Tabela de Métricas de Armazenamento Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) para obter mais informações sobre os tipos de métricas rastreadas pela Storage Analytics.
  * **O registo de registos** de armazenamento regista cada pedido aos serviços de Armazenamento Azure para um registo do lado do servidor. O registo rastreia dados detalhados para cada pedido, incluindo a operação realizada, o estado da operação e informações de latência. Consulte [o Formato de Registo de Analytics de Armazenamento](/rest/api/storageservices/Storage-Analytics-Log-Format) para obter mais informações sobre os dados de pedido e resposta que são escritos nos registos por Storage Analytics.

* **Portal Azure.** Pode configurar métricas e registar-se para a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Também pode visualizar gráficos e gráficos que mostrem como a sua aplicação está a funcionar ao longo do tempo, e configurar alertas para o notificar se a sua aplicação tiver um desempenho diferente do esperado para uma métrica especificada.

    Consulte [uma conta de armazenamento no portal Azure](storage-monitor-storage-account.md) para obter informações sobre a configuração da monitorização no portal Azure.
* **AzCopy.** Os registos de servidores para armazenamento Azure são armazenados como bolhas, para que possa utilizar o AzCopy para copiar as bolhas de registo para um diretório local para análise utilizando o Microsoft Message Analyzer. Consulte [os dados de Transferência com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) para obter mais informações sobre a AzCopy.
* **Analisador de mensagens microsoft**. O Message Analyzer é uma ferramenta que consome ficheiros de registo e exibe dados de registo num formato visual que facilita a filtragem, pesquisa e registo de dados de grupo em conjuntos úteis que pode usar para analisar erros e problemas de desempenho. Consulte [o Guia operativo do Analisador de Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o Analisador de Mensagens.

## <a name="about-the-sample-scenario"></a>Sobre o cenário da amostra

Para este tutorial, vamos examinar um cenário em que as métricas de Armazenamento Azure indicam uma taxa de sucesso baixa por cento para uma aplicação que chama de armazenamento Azure. A métrica da taxa de sucesso de baixo por cento (mostrada como **PercentSuccess** no [portal Azure](https://portal.azure.com) e nas tabelas de métricas) rastreia as operações que têm sucesso, mas que devolvem um código de estado HTTP superior a 299. Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com um estado de transação de **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de baixo por cento de sucesso, consulte [As Métricas mostram que as entradas de baixo percentsuccess ou de registo de análise têm operações com o estado de transação do ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

As operações de Armazenamento Azure podem devolver códigos de estado HTTP superiores a 299 como parte da sua funcionalidade normal. Mas estes erros em alguns casos indicam que poderá otimizar a aplicação do seu cliente para melhorar o desempenho.

Neste cenário, consideraremos uma taxa de sucesso baixa por cento abaixo dos 100%. No entanto, pode escolher um nível métrico diferente, de acordo com as suas necessidades. Recomendamos que, durante o teste da sua aplicação, estabeleça uma tolerância de base para as suas principais métricas de desempenho. Por exemplo, pode determinar, com base em testes, que a sua aplicação deve ter uma taxa de sucesso de 90%, ou 85%. Se os dados das suas métricas mostram que a aplicação está a desviar-se desse número, então pode investigar o que pode estar a causar o aumento.

Para o nosso cenário de amostra, uma vez estabelecidos que a taxa de sucesso por cento é inferior a 100%, vamos examinar os registos para encontrar os erros que se relacionam com as métricas, e usá-los para descobrir o que está causando a taxa de sucesso por cento mais baixa. Vamos ver especificamente os erros na gama 400. Então vamos investigar mais de perto 404 erros (não encontrados).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas de erros de 400 alcances

Os exemplos abaixo mostram uma amostra de cerca de 400 erros de alcance para pedidos contra o Armazenamento Azure Blob, e as suas possíveis causas. Qualquer um destes erros, bem como erros na gama 300 e na gama 500, podem contribuir para uma taxa de sucesso baixa por cento.

Note que as listas abaixo estão longe de estar completas. Consulte [códigos de estado e erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) na MSDN para obter mais informações sobre os erros gerais de armazenamento do Azure e sobre erros específicos de cada um dos serviços de armazenamento.

#### <a name="status-code-404-not-found-examples"></a>Código de Estado 404 (Não Encontrado) Exemplos

Ocorre quando uma operação de leitura contra um recipiente ou bolha falha porque a bolha ou o recipiente não são encontrados.

* Ocorre se um recipiente ou bolha tiver sido eliminado por outro cliente antes deste pedido.
* Ocorre se estiver a utilizar uma chamada API que cria o recipiente ou bolha depois de verificar se existe. As APIs CreateIfNotExists fazem uma chamada HEAD primeiro para verificar a existência do recipiente ou bolha; se não existir, é devolvido um erro de 404 e, em seguida, é feita uma segunda chamada PUT para escrever o recipiente ou bolha.

#### <a name="status-code-409-conflict-examples"></a>Código de Estado 409 (Conflito) Exemplos

* Ocorre se utilizar uma API create para criar um novo recipiente ou bolha, sem verificar primeiro a existência, e já existe um recipiente ou bolha com esse nome.
* Ocorre se um recipiente estiver a ser apagado e tentar criar um novo recipiente com o mesmo nome antes da operação de eliminação estar concluída.
* Ocorre se você especificar um arrendamento em um recipiente ou bolha, e já há um arrendamento presente.

#### <a name="status-code-412-precondition-failed-examples"></a>Código de Estado 412 (Pré-condição Falhada) Exemplos

* Ocorre quando a condição especificada por um cabeçalho condicional não foi cumprida.
* Ocorre quando o ID do arrendamento especificado não corresponde ao ID do arrendamento no recipiente ou bolha.

## <a name="generate-log-files-for-analysis"></a>Gerar ficheiros de registo para análise

Neste tutorial, usaremos o Message Analyzer para trabalhar com três tipos diferentes de ficheiros de registo, embora possa optar por trabalhar com qualquer um destes:

* O **registo do servidor**, que é criado quando ativa o registo de armazenamento Azure. O registo do servidor contém dados sobre cada operação chamada contra um dos serviços de Armazenamento Azure - blob, fila, tabela e ficheiro. O registo do servidor indica qual a operação chamada e que código de estado foi devolvido, bem como outros detalhes sobre o pedido e resposta.
* O **registo de clientes .NET,** que é criado quando ativa o registo do lado do cliente a partir da sua aplicação .NET. O registo do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.
* O **registo de vestígios de rede HTTP**, que recolhe dados em dados de pedido e resposta HTTPS, incluindo para operações contra o Azure Storage. Neste tutorial, geraremos o traço de rede através do Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar a registarese métricas do lado do servidor

Primeiro, precisamos de configurar o registo de armazenamento Azure e as métricas, para que tenhamos dados do lado do serviço para analisar. Pode configurar o registo e as métricas de várias maneiras - através do [portal Azure](https://portal.azure.com), utilizando o PowerShell, ou programáticamente. Consulte [Ativar as métricas](storage-analytics-metrics.md#enable-metrics-by-using-the-azure-portal) e [insefeir o registo](storage-analytics-logging.md#enable-storage-logging) para obter detalhes sobre a configuração do registo e métricas.

### <a name="configure-net-client-side-logging"></a>Configure .NET registo do lado do cliente

Para configurar a marcação do lado do cliente para uma aplicação .NET, ative os diagnósticos .NET no ficheiro de configuração da aplicação (web.config ou app.config). Consulte [o registo do lado do cliente com a Biblioteca do Cliente de Armazenamento .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) e [o registo do lado do cliente com o Microsoft Azure Storage SDK para Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) na MSDN para obter mais detalhes.

O registo do lado do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.

A Biblioteca do Cliente de Armazenamento armazena dados de registo do lado do cliente na localização especificada no ficheiro de configuração da aplicação (web.config ou app.config).

### <a name="collect-a-network-trace"></a>Recolher vestígios de rede

Pode utilizar o Message Analyzer para recolher um traço de rede HTTP/HTTPS enquanto a sua aplicação do cliente está em execução. O Analisador de Mensagens usa [o Violinista](https://www.telerik.com/fiddler) na parte de trás. Antes de recolher o rastreio da rede, recomendamos que configuure o Fiddler para registar o tráfego HTTPS não encriptado:

1. Instalar [o Violinista](https://www.telerik.com/download/fiddler).
2. Violino de lançamento.
3. Selecione **Ferramentas / Opções de Violino**.
4. No diálogo Opções, certifique-se de que os **CONNECTs de captura** e o **tráfego HTTPS de desencriptação** estão ambos selecionados, como mostrado abaixo.

![Configure opções de violinista](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para o tutorial, recolha e guarde um traço de rede primeiro no Message Analyzer, em seguida, crie uma sessão de análise para analisar o traço e os registos. Para recolher um traço de rede no Analisador de Mensagens:

1. No Analisador de Mensagens, selecione **Ficheiro / Traço Rápido HTTPS não encriptados**.
2. Os vestígios começarão imediatamente. **Selecione Stop** para parar o rastreio para que possamos configugá-lo apenas para rastrear o tráfego de armazenamento.
3. Selecione **Editar** para editar a sessão de rastreio.
4. Selecione o link **Configure** à direita do fornecedor **Microsoft-Pef-WebProxy** ETW.
5. No diálogo **Definições Avançadas,** clique no **separador 'Provedor'.**
6. No campo **Hostname Filter,** especifique os seus pontos finais de armazenamento, separados por espaços. Por exemplo, pode especificar os seus pontos finais da seguinte forma; alteração `storagesample` para o nome da sua conta de armazenamento:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Saia do diálogo e clique em **Reiniciar** para começar a recolher os vestígios com o filtro de nome anfitrião no lugar, de modo que apenas o tráfego da rede de armazenamento Azure esteja incluído no vestígio.

> [!NOTE]
> Depois de ter terminado de recolher os seus vestígios de rede, recomendamos vivamente que reverta as definições que pode ter alterado no Fiddler para desencriptar o tráfego HTTPS. No diálogo 'Opções de Violino', desmarcar as caixas de verificação **de controlo HTTPS HTTPS de Captura** e **Desencriptar https.**

Consulte [a utilização das funcionalidades de rastreio de rede](https://technet.microsoft.com/library/jj674819.aspx) na Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Rever dados de métricas no portal Azure

Uma vez que a sua aplicação esteja em execução por um período de tempo, pode rever os gráficos de métricas que aparecem no [portal Azure](https://portal.azure.com) para observar como o seu serviço tem funcionado.

Primeiro, navegue para a sua conta de armazenamento no portal Azure. Por predefinição, é apresentado um gráfico de monitorização com a **percentagem de sucesso** na lâmina da conta. Se já modificou previamente o gráfico para apresentar métricas diferentes, adicione a **percentagem de sucesso.**

Agora verá a **percentagem** de sucesso na tabela de monitorização, juntamente com quaisquer outras métricas que possa ter adicionado. No cenário que vamos investigar a seguir analisando os registos no Message Analyzer, a taxa de sucesso por cento é um pouco inferior a 100%.

Para obter mais detalhes sobre a adição e personalização de gráficos de métricas, consulte [as tabelas de métricas personalizadas](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Pode levar algum tempo para que os dados das suas métricas apareçam no portal Azure depois de permitir métricas de armazenamento. Isto porque as métricas de hora para a hora anterior não são exibidas no portal Azure até que a hora atual tenha decorrido. Além disso, as métricas minúsculas não são atualmente apresentadas no portal Azure. Assim, dependendo de quando ativar métricas, pode levar até duas horas para ver dados de métricas.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Use a AzCopy para copiar registos de servidores para um diretório local

O Azure Storage escreve dados de registo de servidores para bolhas, enquanto as métricas são escritas para as tabelas. As bolhas de log estão disponíveis no recipiente bem conhecido `$logs` para a sua conta de armazenamento. As bolhas de log são nomeadas hierárquicamente por ano, mês, dia e hora, para que possa localizar facilmente o intervalo de tempo que deseja investigar. Por exemplo, na `storagesample` conta, o recipiente para as bolhas de madeira para 01/02/2015, das 8 às 9 horas, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` . As bolhas individuais neste recipiente são nomeadas sequencialmente, a começar por `000000.log` .

Pode utilizar a ferramenta da linha de comando AzCopy para transferir estes ficheiros de registo do lado do servidor para uma localização à sua escolha na sua máquina local. Por exemplo, pode utilizar o seguinte comando para descarregar os ficheiros de registo para operações de blob que ocorreram a 2 de janeiro de 2015 para a `C:\Temp\Logs\Server` pasta; `<storageaccountname>` substitua-o pelo nome da sua conta de armazenamento:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

O AzCopy está disponível para download na página [Azure Downloads.](https://azure.microsoft.com/downloads/) Para obter mais informações sobre a utilização do AzCopy, consulte [os dados de transferência com o Utilitário AzCopy Command-Line](storage-use-azcopy.md).

Para obter informações adicionais sobre o descarregamento de registos do lado do servidor, consulte [os dados de registo de registo de registo de armazenamento de descarregamento](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilize o Analisador de Mensagens do Microsoft para analisar dados de registo

O Microsoft Message Analyzer é uma ferramenta para capturar, exibir e analisar tráfego de mensagens protocolares, eventos e outros sistemas ou mensagens de aplicação em cenários de resolução de problemas e diagnósticos. O Analisador de Mensagens também permite carregar, agregar e analisar dados a partir de registos e ficheiros de vestígios guardados. Para obter mais informações sobre o Analisador de Mensagens, consulte [o Guia operativo do Analisador de Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx).

O Message Analyzer inclui ativos para o Azure Storage que o ajudam a analisar registos de servidores, clientes e rede. Nesta secção, vamos discutir como usar essas ferramentas para abordar a questão do baixo sucesso nos registos de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Descarregue e instale o Message Analyzer e os Ativos de Armazenamento Azure

1. Baixar [O Analisador de Mensagens](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Analisador de mensagens de lançamento.
3. No menu **Ferramentas,** selecione **Gestor de Ativos.** No diálogo do Gestor de **Ativos,** selecione **Downloads**e, em seguida, filtre no **Azure Storage**. Você verá os Ativos de Armazenamento Azure, como mostrado na imagem abaixo.
4. Clique **em Sincronização Todos os itens exibidos** para instalar os Ativos de Armazenamento Azure. Os ativos disponíveis incluem:
   * **Regras de cor de armazenamento Azure:** As regras de cores do Azure Storage permitem-lhe definir filtros especiais que utilizam estilos de cor, texto e fonte para destacar mensagens que contenham informações específicas num traço.
   * **Gráficos de armazenamento Azure:** Os gráficos de armazenamento Azure são gráficos predefinidos que registam dados de registo de servidor de gráficos. Note que para utilizar gráficos de armazenamento Azure neste momento, só pode carregar o registo do servidor na Grelha de Análise.
   * **Azure Storage Parsers:** Os parsers de armazenamento Azure analisam os registos do cliente de Armazenamento Azure, servidor e HTTP para os exibir na Grelha de Análise.
   * **Filtros de armazenamento Azure:** Os filtros de armazenamento Azure são critérios predefinidos que pode utilizar para consultar os seus dados na Grelha de Análise.
   * **Layouts de vista de armazenamento Azure:** Os layouts de vista de armazenamento Azure são layouts de colunas predefinidos e agrupamentos na Grelha de Análise.
5. Reinicie o Analisador de Mensagens depois de ter instalado os ativos.

![Gestor de ativos do Analisador de Mensagens](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os ativos de Armazenamento Azure apresentados para efeitos deste tutorial.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importe os seus ficheiros de registo no Analisador de Mensagens

Pode importar todos os seus ficheiros de registo guardados (lado do servidor, lado do cliente e rede) para uma única sessão no Microsoft Message Analyzer para análise.

1. No menu **Ficheiro** no Microsoft Message Analyzer, clique em **New Session**e, em seguida, clique em **Blank Session**. No diálogo **New Session,** insira um nome para a sua sessão de análise. No painel 'Detalhes da **Sessão',** clique no botão **Ficheiros.**
2. Para carregar os dados de rastreio de rede gerados pelo Message Analyzer, clique em **Add Files**, navegue no local onde guardou o seu ficheiro .matp a partir da sua sessão de rastreio web, selecione o ficheiro .matp e clique em **Abrir**.
3. Para carregar os dados de registo do lado do servidor, clique em **Add Files**, navegue no local onde descarregou os registos do lado do servidor, selecione os ficheiros de registo para o intervalo de tempo que pretende analisar e clique em **Abrir**. Em seguida, no painel 'Detalhes da **Sessão',** desagure o drop-down da **configuração** de registo de registo de texto para cada ficheiro de registo do servidor para **O AzureStorageLog** para garantir que o Analisador de Mensagens do Microsoft pode analisar corretamente o ficheiro de registo.
4. Para carregar os dados de registo do lado do cliente, clique em **Add Files,** navegue no local onde guardou os registos do lado do cliente, selecione os ficheiros de registo que pretende analisar e clique em **Abrir**. Em seguida, no painel 'Detalhes da **Sessão',** desaponte o drop-down da **configuração** de registo de registo de texto para cada ficheiro de registo do lado do cliente para **AzureStorageClientDotNetV4** para garantir que o Analisador de Mensagens do Microsoft pode analisar corretamente o ficheiro de registo.
5. Clique em **Iniciar** no diálogo **New Session** para carregar e analisar os dados de registo. Os dados de registo são apresentados na Grelha de Análise do Analisador de Mensagens.

A imagem abaixo mostra uma sessão de exemplo configurada com ficheiros de registo de servidores, clientes e vestígios de rede.

![Configure sessão de analisador de mensagens](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Note que o Analisador de Mensagens carrega ficheiros de registo na memória. Se tiver um grande conjunto de dados de registo, irá querer filtrá-los para obter o melhor desempenho do Message Analyzer.

Primeiro, determine o prazo que está interessado em rever e mantenha este prazo o mais pequeno possível. Em muitos casos, você vai querer rever um período de minutos ou horas no máximo. Importe o menor conjunto de troncos que possa satisfazer as suas necessidades.

Se ainda tiver uma grande quantidade de dados de registo, então é melhor especificar um filtro de sessão para filtrar os dados de registo antes de os carregar. Na caixa **'Filtro de Sessão',** selecione o botão **Biblioteca** para escolher um filtro predefinido; por exemplo, escolha **o Filtro de Tempo Global I** dos filtros Azure Storage para filtrar num intervalo de tempo. Em seguida, pode editar os critérios do filtro para especificar o tempo de início e de fim do tempo para o intervalo que pretende ver. Também pode filtrar um determinado código de estado; por exemplo, pode optar por carregar apenas as entradas de registo em que o código de estado é 404.

Para obter mais informações sobre a importação de dados de registo no Microsoft Message Analyzer, consulte [a recuperação de dados de mensagens](https://technet.microsoft.com/library/dn772437.aspx) na TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilize o ID do pedido do cliente para correlacionar os dados do ficheiro de registo

A Biblioteca do Cliente de Armazenamento Azure gera automaticamente um ID de pedido exclusivo do cliente para cada pedido. Este valor é escrito no registo do cliente, no registo do servidor e no rastreio da rede, para que possa usá-lo para correlacionar dados nos três registos dentro do Message Analyzer. Consulte [o ID do pedido do Cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre o ID do pedido do cliente.

As secções abaixo descrevem como usar vistas de layout pré-configuradas e personalizadas para correlacionar e agrupar dados com base no ID de pedido do cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um layout de visualização para exibir na Grelha de Análise

Os Ativos de Armazenamento para Analisador de Mensagens incluem layouts de visualização de armazenamento Azure, que são vistas pré-configuradas que pode usar para exibir os seus dados com agrupamentos e colunas úteis para diferentes cenários. Também pode criar layouts de vista personalizados e guardá-los para reutilização.

A imagem abaixo mostra o menu **Ver Layout,** disponível selecionando **Ver Layout** a partir da fita da barra de ferramentas. Os layouts de vista para o Armazenamento Azure estão agrupados sob o nó **de Armazenamento Azure** no menu. Pode pesquisar `Azure Storage` na caixa de pesquisa para filtrar apenas os layouts da vista Azure Storage. Também pode selecionar a estrela ao lado de um layout de visualização para torná-la uma favorita e exibi-la no topo do menu.

![Ver menu layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, **selecione Grupo por ClientRequestID e Módulo**. Esta visualização de grupos de layout regista os dados dos três registos primeiro por ID de pedido do cliente, depois por ficheiro de registo de origem (ou **Módulo** no Analisador de Mensagens). Com esta vista, você pode perfurar um ID de pedido de cliente particular, e ver dados de todos os três ficheiros de registo para esse ID de pedido do cliente.

A imagem abaixo mostra esta vista de layout aplicada aos dados do registo da amostra, com um subconjunto de colunas apresentadas. Pode ver que para um determinado ID de pedido de cliente, a Grelha de Análise exibe dados a partir do registo do cliente, registo de servidor e rastreio de rede.

![Layout de vista de armazenamento Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Diferentes ficheiros de registo têm colunas diferentes, por isso, quando os dados de vários ficheiros de registo são apresentados na Grelha de Análise, algumas colunas podem não conter quaisquer dados para uma determinada linha. Por exemplo, na imagem acima, as linhas de registo do cliente não mostram quaisquer dados para as colunas **Timestamp**, **TimeElapsed**, **Source**e **Destination,** porque estas colunas não existem no registo do cliente, mas existem no rastreio da rede. Da mesma forma, a coluna **Timestamp** exibe dados de timetamp a partir do registo do servidor, mas não são apresentados dados para as colunas **TimeElapsed**, **Source**e **Destination,** que não fazem parte do registo do servidor.
>
>

Além de utilizar os layouts de vista de armazenamento Azure, também pode definir e guardar os seus próprios layouts de vista. Pode selecionar outros campos desejados para agrupar dados e guardar o agrupamento como parte do seu layout personalizado.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cor na Grelha de Análise

Os Ativos de Armazenamento também incluem regras de cores, que oferecem um meio visual para identificar diferentes tipos de erros na Grelha de Análise. As regras de cores predefinidas aplicam-se a erros HTTP, pelo que aparecem apenas para o registo do servidor e para o rastreio da rede.

Para aplicar regras de cores, selecione **Regras** de Cores da fita da barra de ferramentas. Você verá as regras de cor do Azure Storage no menu. Para o tutorial, selecione **Erros de Cliente (StatusCode entre 400 e 499)**, como mostra a imagem abaixo.

![Layout de vista de armazenamento Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de utilizar as regras de cores do Azure Storage, também pode definir e guardar as suas próprias regras de cores.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupo e filtrar dados de registo para encontrar erros de 400 gama

Em seguida, vamos agrupar e filtrar os dados de registo para encontrar todos os erros na gama 400.

1. Localizar a coluna **StatusCode** na Grelha de Análise, clicar com o botão direito no título da coluna e selecionar **Grupo**.
2. Em seguida, grupo na coluna **ClientRequestId.** Verá que os dados na Grelha de Análise são agora organizados por código de estado e por identificação de pedido de cliente.
3. Apresente a janela da ferramenta 'Visualizar' se ainda não estiver visualizada. Na fita da barra de ferramentas, selecione **Janelas de ferramentas**e, em seguida, **ver filtro**.
4. Para filtrar os dados de registo para exibir apenas erros de 400 gama, adicione os seguintes critérios de filtro à janela **'Visualizar filtro'** e clique em **Aplicar:**

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

A imagem abaixo mostra os resultados deste agrupamento e filtro. A expansão do campo **ClientRequestID** por baixo do agrupamento para o código de estado 409, por exemplo, mostra uma operação que resultou nesse código de estado.

![Layout de vista de armazenamento Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar este filtro, verá que as linhas do registo do cliente estão excluídas, uma vez que o registo do cliente não inclui uma coluna **StatusCode.** Para começar, vamos rever os registos de registos de registos de servidores e de rede para localizar 404 erros, e depois voltaremos ao registo do cliente para examinar as operações do cliente que os levaram.

> [!NOTE]
> Pode filtrar na coluna **StatusCode** e ainda exibir dados dos três registos, incluindo o registo do cliente, se adicionar uma expressão ao filtro que inclui entradas de registo em que o código de estado é nulo. Para construir esta expressão de filtro, utilize:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Este filtro devolve todas as linhas do registo do cliente e apenas as linhas a partir do registo do servidor e do registo HTTP onde o código de estado é superior a 400. Se o aplicar ao layout de visualização agrupado pelo ID e módulo de pedido do cliente, pode pesquisar ou deslocar-se através das entradas de registo para encontrar os que estão representados nos três registos.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de registo para encontrar 404 erros

Os Ativos de Armazenamento incluem filtros predefinidos que pode usar para reduzir os dados de registo para encontrar os erros ou tendências que procura. Em seguida, aplicaremos dois filtros predefinidos: um que filtra os registos de registos de registos de servidor e rede para 404 erros, e outro que filtra os dados numa faixa de tempo especificada.

1. Apresente a janela da ferramenta 'Visualizar' se ainda não estiver visualizada. Na fita da barra de ferramentas, selecione **Janelas de ferramentas**e, em seguida, **ver filtro**.
2. Na janela 'Visualizar filtro', selecione **Library**, e procure `Azure Storage` para encontrar os filtros de armazenamento Azure. Selecione o filtro para **404 mensagens (não encontradas) em todos os registos**.
3. Volte a exibir o menu **Da Biblioteca** e localize e selecione o Filtro **Tempo Global**.
4. Edite os intervalos mostrados no filtro ao intervalo que pretende visualizar. Isto ajudará a reduzir o leque de dados a analisar.
5. O filtro deve parecer semelhante ao exemplo abaixo. Clique **em Aplicar** para aplicar o filtro na Grelha de Análise.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Layout de vista de armazenamento Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analise os seus dados de registo

Agora que agrupou e filtra os seus dados, pode examinar os detalhes de pedidos individuais que geraram 404 erros. No layout de visualização atual, os dados são agrupados por ID de pedido do cliente, em seguida, por fonte de log. Uma vez que estamos a filtrar os pedidos onde o campo StatusCode contém 404, veremos apenas os dados de registo de servidor e rede, e não os dados de registo do cliente.

A imagem abaixo mostra um pedido específico em que uma operação Get Blob rendeu um 404 porque a bolha não existia. Note que algumas colunas foram removidas da vista padrão para exibir os dados relevantes.

![Servidor filtrado e registos de rastreio de rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, vamos correlacionar esta identificação de pedido de cliente com os dados de registo do cliente para ver que ações o cliente estava a tomar quando o erro aconteceu. Pode exibir uma nova vista da Grelha de Análise para esta sessão para visualizar os dados de registo do cliente, que abre num segundo separador:

1. Em primeiro lugar, copie o valor do campo **ClientRequestId** para a área de transferência. Pode fazê-lo selecionando qualquer uma das linhas, localizando o campo **ClientRequestId,** clicando à direita no valor dos dados e escolhendo **a Cópia 'ClientRequestId'.**
2. Na fita da barra de ferramentas, selecione **Novo Espectador**e, em seguida, selecione Grelha **de Análise** para abrir um novo separador. O novo separador mostra todos os dados nos seus ficheiros de registo, sem regras de agrupamento, filtragem ou cor.
3. Na fita da barra de ferramentas, selecione **Ver Layout**e, em seguida, selecione Todas **as Colunas de Clientes .NET** sob a secção **de Armazenamento Azure.** Este layout de visualização mostra dados do registo do cliente, bem como os registos de registos de registos de servidor e rede. Por predefinição, está classificado na coluna **MessageNumber.**
4. Em seguida, procure o registo do cliente para obter a identificação do pedido do cliente. Na fita da barra de ferramentas, selecione **'Localizar Mensagens'** e, em seguida, especificar um filtro personalizado no ID de pedido do cliente no campo **Localizar.** Utilize esta sintaxe para o filtro, especificando o ID do seu próprio cliente:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

O Analisador de Mensagens localiza e seleciona a primeira entrada de registo onde os critérios de pesquisa correspondem ao ID do pedido do cliente. No registo de clientes, existem várias entradas para cada ID de pedido de cliente, pelo que pode querer agrupar no campo **ClientRequestId** para facilitar a sua visão de todas juntas. A imagem abaixo mostra todas as mensagens no registo do cliente para o ID de pedido de cliente especificado.

![Registo do cliente mostrando 404 erros](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilizando os dados apresentados nos layouts de visualização nestes dois separadores, pode analisar os dados do pedido para determinar o que pode ter causado o erro. Também pode consultar os pedidos que precederam este para ver se um evento anterior pode ter levado ao erro de 404. Por exemplo, pode rever as entradas de registo de clientes anteriores a este ID de pedido do cliente para determinar se a bolha pode ter sido apagada, ou se o erro foi devido à aplicação do cliente que chama uma API CreateIfNotExists num recipiente ou bolha. No registo do cliente, pode encontrar o endereço da bolha no campo **Descrição;** nos registos de registos de registos de registos de servidor e rede, esta informação aparece no campo **Resumo.**

Assim que souber o endereço da bolha que deu o erro 404, pode investigar mais. Se pesquisar as entradas de registo de outras mensagens associadas a operações na mesma bolha, pode verificar se o cliente apagou previamente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento

Agora que está familiarizado com a utilização do Message Analyzer para analisar os seus dados de registo, pode analisar outros tipos de erros utilizando layouts de visualização, regras de cores e pesquisa/filtragem. As tabelas abaixo listam alguns problemas que pode encontrar e os critérios de filtro que pode utilizar para localizá-los. Para obter mais informações sobre a construção de filtros e o idioma de filtragem do Analisador de Mensagens, consulte [os dados de filtragem da mensagem](https://technet.microsoft.com/library/jj819365.aspx).

| Para investigar... | Use a expressão do filtro... | Expressão aplica-se a Registo (Cliente, Servidor, Rede, Tudo) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens em uma fila |AzureStorageClientDotNetV4.Description contém "Retrying failed operation". |Cliente |
| HTTP Aumento em PercentThrottlingError |HTTP. Resposta.StatusCode == 500 &#124;&#124; HTTP. Resposta.StatusCode == 503 |Rede |
| Aumento no PercentTimeoutError |HTTP. Resposta.StatusCode == 500 |Rede |
| Aumento em PercentTimeoutError (todos) |*StatusCode == 500 |Todos |
| Aumento do PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Cliente |
| Mensagens HTTP 403 (Proibido) |HTTP. Resposta.StatusCode == 403 |Rede |
| Mensagens HTTP 404 (Não encontrado) |HTTP. Resposta.StatusCode == 404 |Rede |
| 404 (todos) |*StatusCode == 404 |Todos |
| Problema de autorização da Assinatura de Acesso Partilhado (SAS) |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Rede |
| Mensagens HTTP 409 (Conflito) |HTTP. Resposta.StatusCode == 409 |Rede |
| 409 (todos) |*StatusCode == 409 |Todos |
| Entradas de registo de baixo percentrs ou analíticos têm operações com o estado de transação de ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Server |
| Aviso de Nagle |((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) e (Az)) e (Az)) e (Az)ureStorageLog.RequestPacketSize <1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS >= 200) |Server |
| Intervalo de tempo nos registos de servidor e rede |#Timestamp >= 2014-10-20T16:36:38 e #Timestamp <= 2014-10-20T16:36:39 |Servidor, Rede |
| Intervalo de tempo nos registos do Servidor |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 e AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre cenários de resolução de problemas no Azure Storage, consulte estes recursos:

* [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure (Monitor, diagnose, and troubleshoot Microsoft Azure Storage)](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Guia de Funcionamento do Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
