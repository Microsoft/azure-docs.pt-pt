---
title: Resolução de problemas do armazenamento do Azure com o diagnóstico e Message Analyzer | Documentos da Microsoft
description: Um tutorial que demonstra ponto-a-ponto de resolução de problemas com a análise de armazenamento do Azure, o AzCopy e o Microsoft Message Analyzer
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ac30888c9f54c5dc88cb72aeec0f3db81d5a99dc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004948"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Resolução de problemas de ponto a ponto com métricas de armazenamento do Azure e o Registro em log, o AzCopy e o analisador de mensagens
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnóstico e resolução de problemas são uma habilidade importante para a criação e suporte aos aplicativos de cliente com o armazenamento do Microsoft Azure. Devido à natureza distribuída da aplicação do Azure, diagnosticar e resolver erros e problemas de desempenho podem ser mais complexas do que em ambientes tradicionais.

Neste tutorial, vamos demonstrar como identificar determinados erros que podem afetar o desempenho e resolver esses erros de ponto a ponto com ferramentas fornecidas pela Microsoft e de armazenamento do Azure, para otimizar o aplicativo cliente.

Este tutorial disponibiliza uma exploração prática de um cenário de resolução de problemas de ponto-a-ponto. Para obter um guia conceitual detalhado para resolução de problemas de aplicações de armazenamento do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para resolução de problemas de aplicações de armazenamento do Azure
Para resolver problemas de aplicativos cliente usando o armazenamento do Microsoft Azure, pode utilizar uma combinação de ferramentas para determinar quando ocorreu um problema e o que pode ser a causa do problema. Estas ferramentas incluem:

* **Análise de armazenamento do Azure**. [Análise de armazenamento do Azure](/rest/api/storageservices/Storage-Analytics) fornece métricas e registo do armazenamento do Azure.
  
  * **Métricas de armazenamento** controla as métricas de transação e métricas de capacidade para a sua conta de armazenamento. Uso de métricas, pode determinar o desempenho do seu aplicativo, de acordo com uma variedade de medidas diferentes. Ver [esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) para obter mais informações sobre os tipos de métricas rastreadas pela análise de armazenamento.
  * **Registo de armazenamento** registos de cada solicitação para os serviços de armazenamento do Azure para um registo do lado do servidor. O registo de controla dados detalhados para cada pedido, incluindo a operação realizada, o estado da operação e informações de latência. Ver [formato de registo de análise de armazenamento](/rest/api/storageservices/Storage-Analytics-Log-Format) para obter mais informações sobre os dados de solicitação e resposta, que são escritos nos registos pela análise de armazenamento.

* **Portal do Azure**. Pode configurar métricas e registo para a sua conta de armazenamento na [portal do Azure](https://portal.azure.com). Também pode ver tabelas e gráficos que mostram como seu aplicativo está sendo executada ao longo do tempo e configurar alertas para notificá-lo se seu aplicativo executa de forma diferente do que o esperado para uma métrica especificada.
  
    Ver [monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar a monitorização no portal do Azure.
* **AzCopy**. Os registos do servidor do armazenamento do Azure são armazenados como blobs, pelo que pode utilizar o AzCopy para copiar os blobs de registo para um diretório local para análise com o Microsoft Message Analyzer. Ver [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md) para obter mais informações sobre o AzCopy.
* **Microsoft Message Analyzer**. Analisador de mensagens é uma ferramenta que consome os ficheiros de registo e apresenta dados de registo num formato visual que torna mais fácil para os dados de registo de grupo, pesquisa e filtro em conjuntos útil que pode usar para analisar os erros e problemas de desempenho. Ver [operacional Guia do analisador de mensagens Microsoft](https://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o analisador de mensagens.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de exemplo
Para este tutorial, vamos examinar um cenário em que as métricas de armazenamento do Azure indica uma taxa de baixa percentagem de êxito para uma aplicação que chama o armazenamento do Azure. A métrica de taxa de baixa percentagem de êxito (mostrado como **PercentSuccess** no [portal do Azure](https://portal.azure.com) e nas tabelas métricas) controla as operações que tenha êxito, mas que retornam um código de estado HTTP é maior do que 299. Nos ficheiros de registo de armazenamento do lado do servidor, estas operações são registadas com o estado da transação **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de baixa percentagem de êxito, consulte [as métricas apresentam PercentSuccess baixa ou entradas do log analytics tem operações com o estado de transação de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operações de armazenamento do Azure podem retornar códigos de estado HTTP maior do que 299 como parte de sua funcionalidade normal. Mas esses erros em alguns casos a indicar que poderá ser capaz de otimizar a sua aplicação de cliente para um melhor desempenho.

Neste cenário, analisaremos a uma taxa de baixa percentagem de êxito que seja algo inferior a 100%. No entanto, pode escolher um nível de métrica diferente, consoante as suas necessidades. Recomendamos que durante o teste da sua aplicação, estabeleça uma tolerância de linha de base para as suas métricas de chave de desempenho. Por exemplo, poderá determinar, com base em teste, que seu aplicativo deve ter uma taxa de percentagem de êxito consistente de 90% ou 85%. Se os dados de métricas mostram que o aplicativo tenha se desviado desse número, em seguida, pode investigar o que poderá estar a causar o aumento.

Para o nosso cenário de exemplo, uma vez que Estabelecemos que a métrica de taxa de percentagem de êxito é inferior a 100%, examinaremos os registos para localizar os erros que está correlacionado com as métricas e utilizá-los para descobrir o que está causando a menor taxa de percentagem de êxito. Vamos ver especificamente a erros no intervalo de 400. Em seguida, vamos mais de perto investigar 404 erros (não encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas dos erros de intervalo de 400
Os exemplos abaixo apresenta uma amostra de alguns erros de intervalo de 400 para pedidos relativos a armazenamento de Blobs do Azure e suas causas possíveis. Qualquer um desses erros, bem como erros no intervalo de 300 e o intervalo de 500, pode contribuir com uma taxa de baixa percentagem de êxito.

Tenha em atenção que as listas abaixo estão longe de ser concluída. Ver [Status e códigos de erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no MSDN para obter detalhes sobre os erros gerais de armazenamento do Azure e sobre erros específicos de cada um dos serviços de armazenamento.

**Exemplos de código 404 (não encontrado) de estado**

Ocorre quando uma operação de leitura em relação a um contentor ou blob falha porque o blob ou contentor não foi encontrado.

* Ocorre se um contentor ou blob foi eliminado por outro cliente antes deste pedido.
* Ocorre se estiver a utilizar uma chamada à API que cria o contentor ou blob depois de verificar se existe. As APIs de CreateIfNotExists fazer uma chamada de cabeça em primeiro lugar para verificar a existência do contentor ou blob; Se não existir, é devolvido um erro 404 e, em seguida, uma segunda chamada PUT é feita para escrever o contentor ou blob.

**409 (conflito) Exemplos de código de estado**

* Ocorre se utilizar uma API de criar para criar um novo contentor ou blob, sem verificar a existência pela primeira vez, e um contentor ou blob com esse nome já existe.
* Ocorre se está a ser eliminado um contentor, e está tentando criar um contentor novo com o mesmo nome, antes da operação de eliminação foi concluída.
* Ocorre se especificar uma concessão num contentor ou blob e, já existe uma concessão presente.

**O código de estado 412 (falhada de pré-condição) Exemplos**

* Ocorre quando a condição especificada por um cabeçalho condicional não foi cumprida.
* Ocorre quando o ID da concessão especificado não coincide com o ID da concessão no contentor ou blob.

## <a name="generate-log-files-for-analysis"></a>Gerar ficheiros de registo para análise
Neste tutorial, vamos utilizar Message Analyzer para trabalhar com três tipos diferentes de arquivos de log, embora poderia optar por trabalhar com qualquer um destes:

* O **registo de servidor**, qual é criado quando ativar o registo de armazenamento do Azure. O registo de servidor contém dados sobre cada operação chamada em relação a um dos serviços de armazenamento do Azure - blob, fila, tabela e ficheiro. O registo do servidor indica qual operação foi chamada e um código de estado de que foi retornados, bem como outros detalhes sobre o pedido e resposta.
* O **registo de cliente .NET**, qual é criado quando ativar o registo do lado do cliente de dentro de seu aplicativo .NET. O registo de cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.
* O **registo de rastreio de rede HTTP**, que recolhe dados sobre HTTP/HTTPS solicitação e resposta de dados, incluindo para operações de armazenamento do Azure. Neste tutorial, iremos gerar o rastreio de rede por meio do analisador de mensagens.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar o registo do lado do servidor e as métricas
Em primeiro lugar, precisamos configurar o registo de armazenamento do Azure e as métricas, para que tenhamos que os dados da aplicação cliente para analisar. Pode configurar o registo e as métricas de diversas formas - através da [portal do Azure](https://portal.azure.com), com o PowerShell, ou por meio de programação. Ver [ativar as métricas de armazenamento e visualização de dados de métricas](https://msdn.microsoft.com/library/azure/dn782843.aspx) e [ativar o registo de armazenamento e acesso a dados de registo](https://msdn.microsoft.com/library/azure/dn782840.aspx) no MSDN para obter detalhes sobre como configurar o registo e as métricas.

**Através do portal do Azure**

Para configurar o registo e métricas para o seu armazenamento de contas utilizando o [portal do Azure](https://portal.azure.com), siga as instruções em [monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md).

> [!NOTE]
> Não é possível definir a métrica de minuto com o portal do Azure. No entanto, recomendamos que defini-los para efeitos deste tutorial e para investigar problemas de desempenho com a sua aplicação. Pode definir a métrica de minuto com o PowerShell, conforme mostrado abaixo, ou usando programaticamente a biblioteca de cliente de armazenamento.
> 
> Tenha em atenção que o portal do Azure não é possível apresentar a métrica de minuto, apenas as métricas por hora.
> 
> 

**Via PowerShell**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para começar a utilizar com o PowerShell para o Azure, veja [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Utilize o [Add-AzAccount](/powershell/module/servicemanagement/azure/add-azureaccount) cmdlet para adicionar a sua conta de utilizador do Azure para a janela do PowerShell:
   
    ```powershell
    Add-AzAccount
    ```

2. Na **iniciar sessão no Microsoft Azure** janela, escreva o endereço de e-mail e a palavra-passe associada à sua conta. O Azure autentica e guarda as informações das credenciais e, em seguida, fecha a janela.
3. Defina a conta de armazenamento predefinida para a conta de armazenamento que está a utilizar para o tutorial ao executar estes comandos na janela do PowerShell:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Ative o registo de armazenamento para o serviço de BLOBs:
   
    ```powershell
    Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Ativar as métricas de armazenamento para o serviço de BLOBs, certificar-se de que defina **- MetricsType** para `Minute`:
   
    ```powershell
    Set-AzStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurar o registo do lado do cliente de .NET
Para configurar o registo do lado do cliente para um aplicativo .NET, ative o diagnóstico de .NET no ficheiro de configuração do aplicativo (Web. config ou App. config). Ver [lado do cliente de registo com a biblioteca de cliente de armazenamento de .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) e [lado do cliente de registo com o Microsoft Azure Storage SDK para Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) no MSDN para obter detalhes.

O registo do lado do cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.

A biblioteca de cliente de armazenamento armazena dados de registo do lado do cliente na localização especificada no ficheiro de configuração do aplicativo (Web. config ou App. config).

### <a name="collect-a-network-trace"></a>Recolher um rastreio de rede
Pode utilizar o analisador de mensagens para recolher um rastreio de rede HTTP/HTTPS, enquanto a aplicação cliente está em execução. Usos do analisador de mensagens [Fiddler](https://www.telerik.com/fiddler) no back-end. Antes de recolher o rastreio de rede, recomendamos que configure o Fiddler para registrar o tráfego HTTPS não criptografado:

1. Instale [Fiddler](https://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
3. Selecione **ferramentas | Opções de fiddler**.
4. Na caixa de diálogo Opções, certifique-se de que **capturar liga-se de HTTPS** e **desencriptar o tráfego HTTPS** são ambos selecionadas, conforme mostrado abaixo.

![Configurar opções do Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para este tutorial, recolher e guardar um rastreio de rede pela primeira vez no analisador de mensagens, em seguida, criar uma sessão de análise para analisar os registos e o rastreio. Para recolher um rastreio de rede no analisador de mensagens:

1. No analisador de mensagens, selecione **arquivo | Rastreio rápido | Sem encriptação HTTPS**.
2. O rastreio serão iniciadas imediatamente. Selecione **parar** para parar o rastreio, de modo que podemos configurar para apenas o tráfego de armazenamento rastreio.
3. Selecione **editar** para editar a sessão de rastreio.
4. Selecione o **configurar** ligação à direita do **Microsoft-Pef-WebProxy** provedor do ETW.
5. Na **definições avançadas** caixa de diálogo, clique nas **fornecedor** separador.
6. Na **filtro de nome de anfitrião** campo, especifique os pontos finais de armazenamento, separados por espaços. Por exemplo, pode especificar os pontos finais da seguinte forma; alterar `storagesample` para o nome da conta de armazenamento:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Sair da caixa de diálogo e clique em **reiniciar** para começar a recolher o rastreio com o filtro de nome de anfitrião no local, para que o tráfego de rede de armazenamento do Azure apenas está incluído no rastreio.

> [!NOTE]
> Depois de concluir a recolha de rastreio de rede, recomendamos vivamente que reverter as definições que pode ter alterado no Fiddler para desencriptar o tráfego HTTPS. Na caixa de diálogo Opções do Fiddler, desmarcar a **capturar liga-se de HTTPS** e **desencriptar o tráfego HTTPS** caixas de verificação.
> 
> 

Ver [usando os recursos de rastreio de rede](https://technet.microsoft.com/library/jj674819.aspx) no Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Rever os dados de métricas no portal do Azure
Depois de seu aplicativo tiver sido executado durante um período de tempo, pode rever os gráficos de métricas que são apresentados no [portal do Azure](https://portal.azure.com) para observar como o serviço de desempenho.

Em primeiro lugar, navegue até à sua conta de armazenamento no portal do Azure. Por predefinição, uma monitorização de gráfico com o **percentagem de êxito** métrica é apresentada no painel da conta. Se anteriormente tiver modificado o gráfico para apresentar diferentes métricas, adicione a **percentagem de êxito** métrica.

Verá agora **percentagem de êxito** no gráfico de monitorização, juntamente com quaisquer outras métricas que possa ter adicionado. O cenário, que vamos investigar junto ao analisar os registos no analisador de mensagens, a taxa de percentagem de êxito é um pouco inferior a 100%.

Para obter mais detalhes sobre como adicionar e personalizar gráficos de métricas, veja [personalizar gráficos de métricas](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Pode demorar algum tempo para os seus dados de métricas a aparecer no portal do Azure depois de ativar as métricas de armazenamento. Isto acontece porque as métricas de hora a hora para a hora anterior não são apresentadas no portal do Azure até que a hora atual tiver sido decorrido. Além disso, métrica de minuto atualmente não é apresentada no portal do Azure. Por isso, dependendo de quando ativar as métricas, pode demorar até duas horas para ver os dados de métricas.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilizar o AzCopy para copiar os registos do servidor para um diretório local
O armazenamento do Azure escreve dados de registo do servidor blobs, enquanto as métricas são escritas tabelas. Blobs de registo estão disponíveis no bem conhecidos `$logs` contentor para a sua conta de armazenamento. Blobs de registo são nomeados hierarquicamente por ano, mês, dia e hora, para que possa localizar facilmente o intervalo de tempo que pretende investigar. Por exemplo, no `storagesample` é a conta, o contentor para os blobs de registo para 01/02/2015, de 8 a 9 am, `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Os blobs individuais neste contentor são nomeados sequencialmente, começando com `000000.log`.

Pode usar a ferramenta de linha de comandos do AzCopy para transferir estes ficheiros de registo do lado do servidor para uma localização à sua escolha no seu computador local. Por exemplo, pode utilizar o seguinte comando para transferir os ficheiros de registo para operações de BLOBs que ocorreu em 2 de Janeiro de 2015 para a pasta `C:\Temp\Logs\Server`; substituir `<storageaccountname>` pelo nome da sua conta de armazenamento e `<storageaccountkey>` pela chave de acesso da conta :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
O AzCopy é disponível para download no [transferências do Azure](https://azure.microsoft.com/downloads/) página. Para obter detalhes sobre como utilizar o AzCopy, veja [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

Para obter mais informações sobre como transferir os registos do lado do servidor, consulte [registo de armazenamento de transferir dados de registo](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilizar o Microsoft Message Analyzer para analisar dados de registo
Microsoft Message Analyzer é uma ferramenta para capturar, exibição e análise de tráfego, eventos e outras mensagens de sistema ou aplicativo em cenários de resolução de problemas e diagnóstico de mensagens de protocolo. Analisador de mensagens também permite-lhe carregar, Agregar e analisar dados de registo e guardar ficheiros de rastreio. Para obter mais informações sobre o analisador de mensagens, consulte [operacional Guia do analisador de mensagens Microsoft](https://technet.microsoft.com/library/jj649776.aspx).

Analisador de mensagens inclui recursos para o armazenamento do Azure que o ajudam a analisar servidor, cliente e registos de rede. Nesta seção, abordaremos como usar essas ferramentas para resolver o problema de baixa percentagem de êxito nos registos de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Baixe e instale o Message Analyzer e dos recursos de armazenamento do Azure
1. Baixe [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) da Microsoft Centro de transferências e execute o instalador.
2. Inicie o analisador de mensagens.
3. Partir do **ferramentas** menu, selecione **Gestor do Asset Intelligence**. Na **Gestor do Asset Intelligence** caixa de diálogo, selecione **Downloads**, em seguida, filtre por **armazenamento do Azure**. Verá os recursos de armazenamento do Azure, conforme mostrado na imagem abaixo.
4. Clique em **sincronização de todos os itens exibidos** para instalar os recursos de armazenamento do Azure. Os recursos disponíveis incluem:
   * **Regras de cor de armazenamento do Azure:** As regras de cor de armazenamento do Azure permitem-lhe definir os filtros especiais que utilizam a cor, texto e estilos de tipo de letra para realçar mensagens que contêm informações específicas num rastreamento.
   * **Gráficos de armazenamento do Azure:** Os gráficos de armazenamento do Azure são gráficos predefinidos que criar um gráfico de dados de registo do servidor. Tenha em atenção que para utilizar gráficos de armazenamento do Azure neste momento, pode apenas carregar o registo de servidor para a grade de análise.
   * **Analisadores de armazenamento do Azure:** Os analisadores de armazenamento do Azure analisar registos de HTTP, servidor e cliente de armazenamento do Azure para exibi-las na grelha de análise.
   * **Filtros de armazenamento do Azure:** Os filtros de armazenamento do Azure são os critérios predefinidos que pode utilizar para consultar os dados na grelha de análise.
   * **Esquemas de vista de armazenamento do Azure:** Esquemas de vista de armazenamento do Azure são layouts de coluna predefinida e os agrupamentos na grelha de análise.
5. Reinicie o Message Analyzer depois de instalar os ativos.

![Gestor do Asset Intelligence de analisador de mensagens](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os recursos de armazenamento do Azure mostrados para efeitos deste tutorial.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Importar os ficheiros de registo para o analisador de mensagens
Pode importar todos os ficheiros de registo guardadas (lado do servidor, do lado do cliente e rede) para uma única sessão no Microsoft Message Analyzer para análise.

1. Sobre o **ficheiros** menu no Microsoft Message Analyzer, clique em **nova sessão**e, em seguida, clique em **sessão em branco**. Na **nova sessão** caixa de diálogo, introduza um nome para a sua sessão de análise. Na **detalhes da sessão** painel, clique nas **ficheiros** botão.
2. Para carregar os dados de rastreio de rede gerados pelo analisador de mensagens, clique em **Add Files**, navegue até à localização onde guardou o ficheiro de .matp da sua sessão de rastreio de web, selecione o ficheiro de .matp e clique em **abrir**.
3. Para carregar os dados de registo do lado do servidor, clique em **Add Files**, navegue até à localização onde transferiu os registos do lado do servidor, selecione os ficheiros de registo para o intervalo de tempo que pretende analisar e clique em **aberto**. Em seguida, no **detalhes da sessão** painel, defina o **configuração de registo de texto** pendente para cada ficheiro de registo do lado do servidor para **AzureStorageLog** para se certificar de que Microsoft Message Analyzer pode analisar o ficheiro de registo corretamente.
4. Para carregar os dados de registo do lado do cliente, clique em **Add Files**, navegue até à localização onde guardou os registos do lado do cliente, selecione os ficheiros de registo que pretende analisar e clique em **aberto**. Em seguida, no **detalhes da sessão** painel, defina o **configuração de registo de texto** menu pendente para cada ficheiro de registo do lado do cliente para **AzureStorageClientDotNetV4** para garantir que Microsoft Message Analyzer pode analisar o ficheiro de registo corretamente.
5. Clique em **começar** no **nova sessão** caixa de diálogo para carregar e analisar os dados de registo. Os dados de registo são apresentados na grelha de análise do analisador de mensagem.

A figura a seguir mostra uma sessão de exemplo configurada com o servidor, cliente e ficheiros de registo de rastreio de rede.

![Configurar a sessão do analisador de mensagens](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Tenha em atenção que o analisador de mensagens carrega ficheiros de registo na memória. Se tiver um grande conjunto de dados de registo, convém filtrá-la para conseguir o melhor desempenho do analisador de mensagens.

Em primeiro lugar, determine o intervalo de tempo que esteja interessado na revisão e manter este período de tempo o menor possível. Em muitos casos vai querer rever um período de minutos ou horas no máximo. Importe o mais pequeno conjunto de registos que podem satisfazer as suas necessidades.

Se ainda terá uma grande quantidade de dados de registo, em seguida, pode pretender especificar uma sessão de filtro para filtrar os dados de registo antes de carregá-lo. Na **filtro de sessão** caixa, selecione a **biblioteca** botão para escolher um filtro predefinido; por exemplo, escolha **Global tempo filtro I** do armazenamento do Azure filtros a filtrar num intervalo de tempo. Em seguida, pode editar os critérios de filtro para especificar o iniciais e finais timestamp durante o intervalo que pretende ver. Também pode filtrar num código de estado específico; Por exemplo, pode optar por carregar apenas entradas de registo em que o código de estado é 404.

Para obter mais informações sobre como importar dados de registo para o Microsoft Message Analyzer, consulte [obter dados de mensagem](https://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilize o ID do pedido de cliente para correlacionar dados de ficheiro de registo
Biblioteca de cliente de armazenamento do Azure gera automaticamente um ID de pedido de cliente exclusivos para cada solicitação. Este valor é escrito para o registo de cliente, o registo de servidor e o rastreio de rede, para que possa utilizá-lo para correlacionar dados em todos os três logs no analisador de mensagens. Ver [ID de pedido de cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter mais informações sobre o cliente pedir ID.

As secções abaixo descrevem como utilizar as vistas de layout pré-configurados e personalizados para correlacionar e dados de grupo com base no ID de pedido do cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecionar um esquema de vista para apresentar na grelha de análise
Os recursos de armazenamento para o analisador de mensagens incluem esquemas de vista de armazenamento de Azure, que são exibições pré-configuradas que pode utilizar para apresentar os dados com agrupamentos útil e colunas para diferentes cenários. Também pode criar layouts de modo de exibição personalizado e salvá-los para reutilização.

A imagem abaixo mostra a **vista de Layout** menu, disponível ao selecionar **Layout de vista** a partir do Friso de barra de ferramentas. As esquemas de vista do armazenamento do Azure são agrupadas sob o **armazenamento do Azure** nó no menu. Pode procurar `Azure Storage` na caixa de pesquisa para filtrar no armazenamento do Azure ver apenas os layouts. Também pode selecionar a estrela junto a um esquema de visualização para torná-lo um favorito e apresentá-lo na parte superior do menu.

![Menu do modo de exibição esquema](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, selecione **agrupados por ClientRequestID e módulo**. Grupos de layout esta vista registos de dados de todos os registos de três pela primeira vez por ID de pedido do cliente, em seguida, ao ficheiro de registo de origem (ou **módulo** no analisador de mensagens). Com esta vista, pode desagregar para um ID de pedido de cliente específico e ver dados a partir de todos os três ficheiros de registo para esse pedido de cliente ID.

A imagem abaixo mostra esta vista de esquema aplicada para os dados de registo de exemplo, com um subconjunto de colunas exibidas. Pode ver que, para obter um ID de pedido de cliente específico, a grade de análise apresenta dados do registo de cliente, o registo do servidor e o rastreio de rede.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Ficheiros de registo diferentes têm colunas diferentes, portanto, quando os dados de vários ficheiros de registo são exibidos na grade de análise, algumas colunas não podem conter quaisquer dados para uma determinada linha. Por exemplo, na imagem acima, as linhas de registo de cliente não mostrar todos os dados para o **Timestamp**, **TimeElapsed**, **origem**, e **destino**colunas, uma vez que estas colunas não existem no registo de cliente, mas existem no rastreio de rede. Da mesma forma, o **Timestamp** coluna apresenta dados de timestamp de registo do servidor, mas não serem apresentados dados para o **TimeElapsed**, **origem**, e  **Destino** colunas, que não fazem parte do registo do servidor.
> 
> 

Além de utilizar as esquemas de vista de armazenamento do Azure, também pode definir e salvar seus próprios esquemas de vista. Pode selecionar outros campos pretendidos para o agrupamento de dados e guardar o agrupamento como parte do seu esquema personalizado também.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cor para a grade de análise
Os recursos de armazenamento também incluir regras de cor, o que significa que um elemento visual identificar diferentes tipos de erros na grelha de análise da oferta. As regras de cores predefinidas aplicam-se a erros HTTP, para que serem apresentados apenas para o rastreio de rede e de registo do servidor.

Para aplicar as regras de cor, selecione **as regras de cor** a partir do Friso de barra de ferramentas. Verá as regras de cor de armazenamento do Azure no menu. Para este tutorial, selecione **erros de cliente (StatusCode compreendido entre 400 e 499)**, conforme mostrado na imagem abaixo.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de utilizar as regras de cor de armazenamento do Azure, também pode definir e salvar suas próprias regras de cor.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupo e filtrar dados de registo para localizar os erros de intervalo de 400
Em seguida, vamos de grupo e filtrar os dados de registo para localizar todos os erros no intervalo de 400.

1. Localize a **StatusCode** coluna na grelha de análise, com a coluna de cabeçalho e selecione o botão direito **grupo**.
2. Em seguida, agrupar pela **ClientRequestId** coluna. Verá que os dados na grelha de análise agora estão organizados por código de estado e por ID de pedido do cliente.
3. Exiba a janela da ferramenta de filtro de vista se ele não estiver em exibição. Na faixa de opções a barra de ferramentas, selecione **Windows de ferramenta**, em seguida, **View Filter**.
4. Para filtrar os dados de registo para apresentar apenas os erros de intervalo de 400, adicione os seguintes critérios de filtro para o **View Filter** janela e clique em **aplicar**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

A figura a seguir mostra os resultados deste agrupamento e o filtro. Expandir a **ClientRequestID** campo abaixo o agrupamento para o código de estado 409, por exemplo, mostra uma operação que resultaram nesse código de estado.

![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar este filtro, verá que linhas do registo de cliente são excluídas, como o cliente do registo não inclui um **StatusCode** coluna. Para começar, vamos rever o servidor e os registos de rastreio de rede para localizar 404 erros, e vamos voltar para o registo de cliente para examinar as operações de cliente que levou a eles.

> [!NOTE]
> Pode filtrar os **StatusCode** coluna e, ainda, com dados de exibição de todos os registos de três, incluindo o registo de cliente, se adicionar uma expressão para o filtro que inclui entradas de registo em que o código de estado é nulo. Para construir esta expressão de filtro, utilize:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Este filtro devolve todas as linhas do cliente no registo e apenas as linhas do registo de servidor e o registo HTTP em que o código de estado é maior do que 400. Se aplicá-lo para o esquema de vista agrupado por ID de pedido de cliente e o módulo, pode procurar ou desloque-se através de entradas de registo para encontrar aqueles em que todos os três logs são representados.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de registo para localizar 404 erros
Os recursos de armazenamento incluir filtros predefinidos que pode utilizar para limitar os dados de registo para localizar o erros ou tendências que procura. Em seguida, vamos aplicar dois filtros predefinidos: uma que filtra o servidor e os registos de rastreio de rede para 404 erros e outra que filtra os dados num intervalo de tempo especificado.

1. Exiba a janela da ferramenta de filtro de vista se ele não estiver em exibição. Na faixa de opções a barra de ferramentas, selecione **Windows de ferramenta**, em seguida, **View Filter**.
2. Na janela Filtro de visualização, selecione **biblioteca**e uma procura `Azure Storage` para localizar o armazenamento do Azure filtros. Selecione o filtro para **404 (não encontrado) de mensagens em todos os registos**.
3. Apresentar o **biblioteca** menu novamente e localize e selecione o **filtro de tempo Global**.
4. Edite os carimbos de data / mostrada o filtro para o intervalo que quer ver. Isto irá ajudar a limitar o intervalo de dados para analisar.
5. O filtro deve ter um aspeto semelhante ao exemplo abaixo. Clique em **aplicar** para aplicar o filtro para a grade de análise.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Esquema de vista de armazenamento do Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analise os seus dados de registo
Agora que tenha agrupadas e filtradas seus dados, pode examinar os detalhes de pedidos individuais que geraram 404 erros. No layout da vista atual, os dados são agrupados por ID de pedido de cliente, em seguida, pela origem de log. Uma vez que o está a filtrar em pedidos em que o campo de StatusCode contém 404, vamos ver apenas o servidor e os dados de rastreio de rede, não os dados de registo do cliente.

A figura a seguir mostra um pedido específico em que uma operação de obter o Blob gerou um 404, porque o blob não existe. Tenha em atenção que algumas colunas foram removidas da vista do padrão para exibir os dados relevantes.

![Servidor filtrado e registos de rastreio de rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, iremos irá correlacionar este ID de pedido de cliente com os dados de registo de cliente para ver quais ações o cliente estava a demorar quando ocorreu o erro. Pode exibir uma nova vista de grelha de análise para esta sessão ver os dados de registo do cliente, que abrirá numa segunda guia:

1. Em primeiro lugar, copie o valor do **ClientRequestId** campo na área de transferência. Pode fazê-lo selecionar qualquer uma das linhas, localizando os **ClientRequestId** campo, clicando com o botão direito no valor de dados e escolher **cópia 'ClientRequestId'**.
2. Da faixa de opções de barra de ferramentas, selecione **novo Visualizador**, em seguida, selecione **análise de grade** para abrir um novo separador. A nova guia mostra todos os dados nos seus ficheiros de registo, sem agrupamento, filtragem ou as regras de cor.
3. Da faixa de opções de barra de ferramentas, selecione **vista de Layout**, em seguida, selecione **todas as colunas de cliente .NET** sob o **armazenamento do Azure** secção. Este esquema de vista mostra os dados do cliente log, bem como os registos de rastreio do servidor e da rede. Por predefinição é classificado no **MessageNumber** coluna.
4. Em seguida, procurar o registo de cliente para o ID de pedido de cliente. Na faixa de opções a barra de ferramentas, selecione **encontrar mensagens**, em seguida, especifique um filtro personalizado no ID do pedido de cliente no **encontrar** campo. Utilize esta sintaxe para o filtro, especificando o seu próprio ID de pedido do cliente:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Analisador de mensagens localiza e seleciona a primeira entrada de registo em que os critérios de pesquisa corresponde ao ID de pedido do cliente. No registo do cliente, existem várias entradas para cada ID de pedido do cliente, portanto, será conveniente para agrupá-los no **ClientRequestId** campo para facilitar a vê-los todos juntos. A figura a seguir mostra todas as mensagens no registo de cliente para o cliente especificado ID do pedido.

![Erros de 404 mostrando de registo de cliente](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilizar os dados mostrados nos layouts de exibição desses dois separadores, pode analisar os dados de pedido para determinar o que pode ter causado o erro. Pode também ver pedidos que precedido este aqui para ver se um evento anterior pode levaram para o erro 404. Por exemplo, pode rever as entradas de registo do cliente anterior a este ID de pedido de cliente para determinar se o blob pode ter sido eliminado, ou se o erro se deveu a aplicação de cliente chamar uma API de CreateIfNotExists num contentor ou blob. No registo do cliente, pode encontrar o endereço do blob a **Descrição** campo; no servidor e os registos de rastreio de rede, estas informações aparecem na **resumo** campo.

Sabe o endereço do blob que gerou o erro 404, pode investigar mais. Se pesquisar as entradas de registo para outras mensagens associadas a operações no mesmo blob, pode verificar se o cliente eliminado anteriormente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento
Agora que está familiarizado com o Message Analyzer para analisar os seus dados de registo, pode analisar os outros tipos de erros de utilizar a vista de layouts, as regras de cor e a pesquisa/filtragem. As tabelas a seguir lista alguns problemas que poderá encontrar e os critérios de filtro, pode usar para localizá-los. Para obter mais informações sobre a construção de filtros e o idioma de filtragem do Message Analyzer, consulte [dados de filtragem de mensagens](https://technet.microsoft.com/library/jj819365.aspx).

| Para investigar... | Utilize a expressão de filtro... | Expressão aplica-se ao registo (cliente, servidor, rede, todos os) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens numa fila |AzureStorageClientDotNetV4.Description contém "Repetir falha na operação." |Cliente |
| Aumento de HTTP percentthrottlingerror |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Rede |
| Aumentar percenttimeouterror |HTTP.Response.StatusCode   == 500 |Rede |
| Aumentar percenttimeouterror (todos) |*StatusCode   == 500 |Todos |
| Aumentar percentnetworkerror |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Cliente |
| Mensagens HTTP 403 (proibido) |HTTP.Response.StatusCode   == 403 |Rede |
| HTTP 404 (não encontrado) mensagens |HTTP.Response.StatusCode   == 404 |Rede |
| 404 (todos) |*StatusCode   == 404 |Todos |
| Partilhado o problema de autorização de assinatura de acesso (SAS) |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Rede |
| HTTP 409 (conflito) mensagens |HTTP.Response.StatusCode   == 409 |Rede |
| 409 (todos) |*StatusCode   == 409 |Todos |
| Entradas de registo PercentSuccess baixa ou análise tem operações com o estado de transação de ClientOtherErrors |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Servidor |
| Aviso de Nagle |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) e (AzureStorageLog.RequestPacketSize < 1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Servidor |
| Intervalo de tempo nos registos de servidor e da rede |#Timestamp > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39 |Servidor de rede |
| Intervalo de tempo em registos do servidor |AzureStorageLog.Timestamp   >= 2014-10-20T16:36:38 and AzureStorageLog.Timestamp <=   2014-10-20T16:36:39 |Servidor |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre os cenários de resolução de problemas ponto a ponto no armazenamento do Azure, veja estes recursos:

* [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)
* [Guia de operação do analisador de mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
