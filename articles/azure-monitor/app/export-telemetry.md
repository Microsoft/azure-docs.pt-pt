---
title: Exportação contínua de telemetria a partir de Insights de Aplicação [ Microsoft Docs
description: Exportar dados de diagnóstico e utilização para armazenamento no Microsoft Azure, e descarregá-lo a partir daí.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 7284e6305b1028cbcb62041ff8196d06250f4414
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744868"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria a partir do Application Insights
Quer manter a sua telemetria por mais tempo do que o período padrão de retenção? Ou processá-lo de alguma forma especializada? A Exportação Contínua é ideal para isso. Os eventos que vê no portal Application Insights podem ser exportados para armazenamento no Microsoft Azure em formato JSON. A partir daí, pode descarregar os seus dados e escrever qualquer código que precise para processá-lo.  

> [!NOTE]
> A exportação contínua é suportada apenas para os recursos clássicos da Application Insights. [Os recursos de Insights de Aplicação baseados no espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/app/create-workspace-resource) devem utilizar [configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/app/create-workspace-resource#export-telemetry).
>

Antes de configurar a exportação contínua, existem algumas alternativas que talvez deseja considerar:

* O botão Export na parte superior de uma métrica ou separador de pesquisa permite transferir tabelas e gráficos para uma folha de cálculo do Excel.

* [A análise](../../azure-monitor/app/analytics.md) fornece uma poderosa linguagem de consulta para telemetria. Também pode exportar resultados.
* Se procura explorar [os seus dados no Power BI,](../../azure-monitor/app/export-power-bi.md )pode fazê-lo sem utilizar a Exportação Contínua.
* O [Data access REST API](https://dev.applicationinsights.io/) permite-lhe aceder à sua telemetria programáticamente.
* Também pode aceder à [instalação de exportação contínua através da Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Após a Exportação Contínua copia os seus dados para armazenamento (onde pode ficar o tempo que quiser), ainda está disponível em Application Insights para o período de [retenção](../../azure-monitor/app/data-retention-privacy.md)habitual .

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração avançada de armazenamento avançado de exportação contínua

A Exportação Contínua **não suporta** as seguintes funcionalidades/configurações de armazenamento Azure:

* Utilização de [firewalls de armazenamento VNET/Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) em conjunto com o armazenamento Azure Blob.

* [Armazenamento imutável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) para armazenamento Azure Blob.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Criar uma Exportação Contínua

1. No recurso Application Insights para a sua app sob configuração à esquerda, abra a Exportação Contínua e escolha **Adicionar:**

2. Escolha os tipos de dados de telemetria que pretende exportar.

3. Crie ou selecione uma conta de [armazenamento Azure](../../storage/common/storage-introduction.md) onde pretende armazenar os dados. Para mais informações sobre as opções de preços de armazenamento, visite a [página oficial](https://azure.microsoft.com/pricing/details/storage/)de preços .

     Clique em Adicionar, Destino de Exportação, Conta de Armazenamento e, em seguida, criar uma nova loja ou escolher uma loja existente.

    > [!Warning]
    > Por padrão, o local de armazenamento será definido para a mesma região geográfica que o seu recurso Application Insights. Se armazenar numa região diferente, poderá incorrer em taxas de transferência.

4. Crie ou selecione um recipiente no armazenamento.

Uma vez criada a sua exportação, começa a funcionar. Só obtém dados que chegam depois de criar a exportação.

Pode haver um atraso de cerca de uma hora antes que os dados apareçam no armazenamento.

Uma vez concluída a primeira exportação, encontrará uma estrutura semelhante à seguinte no seu recipiente de armazenamento Azure Blob: (Isto variará consoante os dados que está a recolher.)

|Name | Descrição |
|:----|:------|
| [Disponibilidade](export-data-model.md#availability) | Relatórios de disponibilidade de [testes web](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Evento](export-data-model.md#events) | Eventos personalizados gerados pelo [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Exceções](export-data-model.md#exceptions) |Reporta [exceções](../../azure-monitor/app/asp-net-exceptions.md) no servidor e no navegador.
| [Mensagens](export-data-model.md#trace-messages) | Enviado por [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), e pelos adaptadores de [exploração madeireira](../../azure-monitor/app/asp-net-trace-logs.md).
| [Métricas](export-data-model.md#metrics) | Gerado por chamadas API métricas.
| [Contadores de Desempenho](export-data-model.md) | Contadores de Desempenho recolhidos pela Application Insights.
| [Pedidos](export-data-model.md#requests)| Enviado por [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isto para reportar o tempo de resposta do servidor, medido no servidor.| 

### <a name="to-edit-continuous-export"></a>Para editar exportação contínua

Clique na exportação contínua e selecione a conta de armazenamento para editar.

### <a name="to-stop-continuous-export"></a>Para parar a exportação contínua

Para parar a exportação, clique em Desativar. Quando clicar em Ativar novamente, a exportação recomeçará com novos dados. Não conseguirá os dados que chegaram ao portal enquanto a exportação foi desativada.

Para parar a exportação permanentemente, apague-a. Ao fazê-lo, não apaga os seus dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não pode adicionar ou mudar uma exportação?
* Para adicionar ou alterar exportações, precisa de Direitos de Acesso do Proprietário, Colaborador ou De Aplicação Insights. [Saiba sobre papéis.][roles]

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Que eventos obtém?
Os dados exportados são a telemetria bruta que recebemos da sua aplicação, exceto que adicionamos dados de localização, que calculamos a partir do endereço IP do cliente.

Os dados que foram descartados por [amostragem](../../azure-monitor/app/sampling.md) não estão incluídos nos dados exportados.

Outras métricas calculadas não estão incluídas. Por exemplo, não exportamos a utilização média do CPU, mas exportamos a telemetria bruta a partir da qual a média é calculada.

Os dados também incluem os resultados de qualquer disponibilidade de [testes web](../../azure-monitor/app/monitor-web-app-availability.md) que tenha configurado.

> [!NOTE]
> **A provar.** Se a sua aplicação enviar muitos dados, a função de amostragem pode funcionar e enviar apenas uma fração da telemetria gerada. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Inspecione os dados
Pode inspecionar o armazenamento diretamente no portal. Clique em casa no menu mais à esquerda, na parte superior onde diz "Serviços Azure" selecione **contas de armazenamento,** selecione o nome da conta de armazenamento, na página de visão geral selecione **Blobs** sob serviços e, finalmente, selecione o nome do recipiente.

Para inspecionar o armazenamento azure em Visual Studio, **vista**aberta, **Cloud Explorer.** (Se não tiver esse comando de menu, tem de instalar o Azure SDK: Abra o diálogo **do Novo Projeto,** expanda visual C#/Cloud e escolha **Get Microsoft Azure SDK para .NET**.)

Quando abrir a sua loja de blob, verá um contentor com um conjunto de ficheiros blob. O URI de cada ficheiro derivado do nome do recurso Application Insights, a sua chave de instrumentação, o tipo de telemetria/data/hora. (O nome do recurso é todo minúsculo, e a chave de instrumentação omite traços.)

![Inspecione a loja de bolhas com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e a hora são UTC e são quando a telemetria foi depositada na loja - não o tempo que foi gerado. Portanto, se escrever código para descarregar os dados, pode mover-se linearmente através dos dados.

Aqui está a forma do caminho:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Onde

* `blobCreationTimeUtc`é o momento em que blob foi criado no armazenamento interno de encenação
* `blobDeliveryTimeUtc`é o momento em que a bolha é copiada para o armazenamento de destino de exportação

## <a name="data-format"></a><a name="format"></a>Formato de dados
* Cada bolha é um ficheiro de texto que contém várias linhas separadas '\n'. Contém a telemetria processada durante um período de tempo de aproximadamente meio minuto.
* Cada linha representa um ponto de dados de telemetria, como um pedido ou visualização de página.
* Cada linha é um documento JSON não formado. Se quiser sentar-se e olhar para ele, abra-o no Estúdio Visual e escolha Editar, Avançado, Arquivo formato:

![Ver a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

As durações do tempo estão em carrapatos, onde 10 000 carrapatos = 1 ms. Por exemplo, estes valores mostram um tempo de 1 ms para enviar um pedido do navegador, 3 ms para recebê-lo, e 1,8 s para processar a página no navegador:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referência detalhada do modelo de dados para os tipos e valores de propriedade.](export-data-model.md)

## <a name="processing-the-data"></a>Processamento dos dados
Em pequena escala, pode escrever algum código para desmontar os seus dados, lê-los numa folha de cálculo, e assim por diante. Por exemplo:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Para obter uma amostra de código maior, consulte [o uso de uma função de trabalhador][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Elimine os seus dados antigos
É responsável por gerir a sua capacidade de armazenamento e apagar os dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se regenerar a sua chave de armazenamento...
Se alterar a chave do seu armazenamento, a exportação contínua deixará de funcionar. Verá uma notificação na sua conta Azure.

Abra o separador Exportação Contínua e edite a sua exportação. Editar o Destino de Exportação, mas deixe o mesmo armazenamento selecionado. Clique em OK para confirmar.

A exportação contínua reiniciará.

## <a name="export-samples"></a>Amostras de exportação

* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Amostra de Análise de Fluxo 2](export-stream-analytics.md)

Em escalas maiores, considere os clusters [HDInsight](https://azure.microsoft.com/services/hdinsight/) - Hadoop na nuvem. O HDInsight fornece uma variedade de tecnologias para gerir e analisar big data, e você poderia usá-lo para processar dados que foram exportados de Application Insights.

## <a name="q--a"></a>Perguntas e Respostas
* *Mas tudo o que quero é um download único de um gráfico.*  

    Sim, podes fazer isso. Na parte superior do separador, clique em Dados de **Exportação**.
* *Eu fiz uma exportação, mas não há dados na minha loja.*

    A Application Insights recebeu alguma telemetria da sua app desde que criou a exportação? Só receberá novos dados.
* *Tentei criar uma exportação, mas foi-me negado acesso.*

    Se a conta for propriedade da sua organização, tem de ser membro dos grupos de proprietários ou colaboradores.
* *Posso exportar direto para a minha própria loja no local?*

    Não, desculpa. O nosso motor de exportação atualmente só funciona com armazenamento Azure neste momento.  
* *Há algum limite para a quantidade de dados que colocou na minha loja?*

    Não. Vamos continuar a empurrar dados até que apague a exportação. Vamos parar se atingirmos os limites exteriores para o armazenamento de bolhas, mas isso é enorme. Cabe-te a ti controlar a quantidade de armazenamento que usas.  
* *Quantas bolhas devo ver no armazém?*

  * Para cada tipo de dados selecionados para exportar, é criada uma nova bolha a cada minuto (se os dados estiverem disponíveis).
  * Além disso, para aplicações com tráfego elevado, são atribuídas unidades de partição adicionais. Neste caso, cada unidade cria uma bolha a cada minuto.
* *Regenerava a chave do meu armazenamento ou mudei o nome do contentor, e agora a exportação não funciona.*

    Editar a exportação e abrir o separador de destino de exportação. Deixe o mesmo armazenamento selecionado como antes e clique em OK para confirmar. A exportação vai recomeçar. Se a mudança foi nos últimos dias, não perderá dados.
* *Posso parar a exportação?*

    Sim. Clique em Disable.

## <a name="code-samples"></a>Exemplos de código

* [Amostra de Análise de Fluxo](export-stream-analytics.md)
* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Referência detalhada do modelo de dados para os tipos e valores de propriedade.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
