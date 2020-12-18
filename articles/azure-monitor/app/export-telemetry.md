---
title: Exportação contínua de telemetria a partir de Insights de Aplicação ; Microsoft Docs
description: Exporte dados de diagnóstico e utilização para armazenamento no Microsoft Azure, e descarregue-os a partir daí.
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: a6f636ce9fe30c666f08935d5830eb0c12e6cb5e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674142"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria a partir do Application Insights
Deseja manter a sua telemetria por mais tempo do que o período padrão de retenção? Ou processá-lo de uma forma especializada? Exportação Contínua é ideal para isso. Os eventos que vê no portal Application Insights podem ser exportados para armazenamento no Microsoft Azure em formato JSON. A partir daí, pode descarregar os seus dados e escrever qualquer código necessário para os processar.  

> [!NOTE]
> A Exportação contínua só é suportada para os recursos clássicos do Application Insights. [Os recursos do Application Insights baseados na área de trabalho](./create-workspace-resource.md) têm de utilizar [definições de diagnóstico](./create-workspace-resource.md#export-telemetry).
>

Antes de configurar a exportação contínua, há algumas alternativas que talvez desemosse:

* O botão Exportar no topo de uma métrica ou separador de pesquisa permite transferir tabelas e gráficos para uma folha de cálculo do Excel.

* [A analytics](../log-query/log-query-overview.md) fornece uma poderosa linguagem de consulta para a telemetria. Também pode exportar resultados.
* Se procura explorar [os seus dados no Power BI,](./export-power-bi.md)pode fazê-lo sem utilizar a Exportação Contínua.
* O [Acesso de Dados REST API](https://dev.applicationinsights.io/) permite-lhe aceder programaticamente à sua telemetria.
* Também pode aceder à exportação contínua de configuração [via PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Depois de a Exportação Contínua copia os seus dados para armazenamento (onde pode ficar o tempo que quiser), ainda está disponível em Application Insights para o período de [retenção](./data-retention-privacy.md)habitual.

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração avançada de armazenamento de exportação contínua

A Exportação Contínua **não suporta** as seguintes funcionalidades/configurações de armazenamento Azure:

* Utilização de firewalls de [armazenamento VNET/Azure](../../storage/common/storage-network-security.md) em conjunto com o armazenamento Azure Blob.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Criar uma Exportação Contínua

> [!NOTE]
> Um pedido não pode exportar mais de 3TB de dados por dia. Se for exportado mais de 3TB por dia, a exportação será desativada. Para exportar sem limite, utilize [a exportação baseada em configurações de diagnóstico.](#diagnostic-settings-based-export)

1. No recurso Application Insights para a sua app em configuração à esquerda, abra exportação contínua e escolha **Adicionar:**

2. Escolha os tipos de dados de telemetria que pretende exportar.

3. Crie ou selecione uma [conta de armazenamento Azure](../../storage/common/storage-introduction.md) onde pretende armazenar os dados. Para obter mais informações sobre as opções de preços de armazenamento, visite a [página oficial de preços](https://azure.microsoft.com/pricing/details/storage/).

     Clique em Adicionar, Destino de Exportação, Conta de Armazenamento e, em seguida, crie uma nova loja ou escolha uma loja existente.

    > [!Warning]
    > Por predefinição, o local de armazenamento será definido para a mesma região geográfica que o seu recurso Application Insights. Se armazenar numa região diferente, poderá incorrer em taxas de transferência.

4. Criar ou selecionar um recipiente no armazenamento.

> [!NOTE]
> Uma vez criado a sua exportação, os dados recém-ingeridos começarão a fluir para o armazenamento da Azure Blob. A exportação contínua só transmitirá uma nova telemetria que seja criada/ingerida após a exportação contínua ter sido ativada. Os dados que existiam antes de ativar a exportação contínua não serão exportados e não existe qualquer forma de exportar retroativamente dados previamente criados através da exportação contínua.

Pode haver um atraso de cerca de uma hora antes que os dados apareçam no armazenamento.

Uma vez concluída a primeira exportação encontrará uma estrutura semelhante à seguinte no seu contentor de armazenamento Azure Blob: (Isto variará em função dos dados que está a recolher.)

|Nome | Descrição |
|:----|:------|
| [Disponibilidade](export-data-model.md#availability) | Relatórios [disponibilidade testes web](./monitor-web-app-availability.md).  |
| [Evento](export-data-model.md#events) | Eventos personalizados gerados pelo [TrackEvent()](./api-custom-events-metrics.md#trackevent). 
| [Exceções](export-data-model.md#exceptions) |Reporta [exceções](./asp-net-exceptions.md) no servidor e no navegador.
| [Mensagens](export-data-model.md#trace-messages) | Enviado por [TrackTrace](./api-custom-events-metrics.md#tracktrace), e pelos [adaptadores de registo.](./asp-net-trace-logs.md)
| [Métricas](export-data-model.md#metrics) | Gerado por chamadas métricas da API.
| [PerformanceCounters](export-data-model.md) | Contadores de desempenho recolhidos pela Application Insights.
| [Pedidos](export-data-model.md#requests)| Enviado por [TrackRequest.](./api-custom-events-metrics.md#trackrequest) Os módulos padrão usam isto para reportar o tempo de resposta do servidor, medido no servidor.| 

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Clique na exportação contínua e selecione a conta de armazenamento para editar.

### <a name="to-stop-continuous-export"></a>Para parar a exportação contínua

Para parar a exportação, clique em Desativar. Quando clicar em Ativar novamente, a exportação recomeçará com novos dados. Não vai receber os dados que chegaram ao portal enquanto a exportação foi desativada.

Para parar a exportação permanentemente, elimine-a. Ao fazê-lo, não elimina os seus dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não pode adicionar ou mudar uma exportação?
* Para adicionar ou alterar exportações, precisa dos direitos de acesso de Proprietário, Contribuidor ou Contribuidor do Application Insights. [Conheça os papéis.][roles]

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Que eventos obtém?
Os dados exportados são a telemetria bruta que recebemos da sua aplicação, exceto que adicionamos dados de localização, que calculamos a partir do endereço IP do cliente.

Os dados que tenham sido descartados por [amostragem](./sampling.md) não constam dos dados exportados.

Outras métricas calculadas não estão incluídas. Por exemplo, não exportamos uma utilização média do CPU, mas exportamos a telemetria bruta a partir da qual a média é calculada.

Os dados também incluem os resultados de quaisquer [testes web de disponibilidade](./monitor-web-app-availability.md) que tenha configurado.

> [!NOTE]
> **A provar.** Se a aplicação enviar muitos dados, a funcionalidade de amostragem poderá funcionar e enviar apenas uma fração da telemetria gerada. [Saiba mais sobre amostragem.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Inspecione os dados
Pode inspecionar o armazenamento diretamente no portal. Clique em casa no menu mais à esquerda, no topo onde diz "Serviços Azure" selecione **contas de armazenamento**, selecione o nome da conta de armazenamento, na página geral selecione **Blobs** em serviços e, finalmente, selecione o nome do recipiente.

Para inspecionar o armazenamento do Azure no Estúdio Visual, abra **a vista,** **o Cloud Explorer**. (Se não tiver o comando do menu, tem de instalar o Azure SDK: Abra o diálogo do **Novo Projeto,** expanda o Visual C#/Cloud e escolha **Obter Microsoft Azure SDK para .NET**.)

Quando abrir a sua loja de bolhas, verá um recipiente com um conjunto de ficheiros blob. O URI de cada ficheiro deriva do nome do recurso Application Insights, a sua chave de instrumentação, tipo de telemetria/data/hora. (O nome do recurso é tudo minúsculo, e a chave de instrumentação omite traços.)

![Inspecione a loja blob com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e a hora são UTC e são quando a telemetria foi depositada na loja - não o tempo que foi gerado. Portanto, se escrever código para descarregar os dados, pode mover-se linearmente através dos dados.

Aqui está a forma do caminho:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Onde

* `blobCreationTimeUtc` é o momento em que a bolha foi criada no armazenamento interno de encenação
* `blobDeliveryTimeUtc` é o momento em que a bolha é copiada para o armazenamento do destino de exportação

## <a name="data-format"></a><a name="format"></a> Formato de dados
* Cada bolha é um ficheiro de texto que contém várias linhas separadas de '\n'. Contém a telemetria processada durante um período de tempo de cerca de meio minuto.
* Cada linha representa um ponto de dados de telemetria, como um pedido ou vista de página.
* Cada linha é um documento JSON não testado. Se quiser ver as linhas, abra a bolha no Estúdio Visual e escolha **Editar**  >  Ficheiro de Formato **Avançado:**  >  

   ![Ver a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

As durações do tempo são em carrapatos, onde 10 000 carrapatos = 1 ms. Por exemplo, estes valores mostram um tempo de 1 ms para enviar um pedido do navegador, 3 ms para recebê-lo, e 1.8 s para processar a página no navegador:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Referência detalhada do modelo de dados para os tipos e valores da propriedade.](export-data-model.md)

## <a name="processing-the-data"></a>Processamento dos dados
Em pequena escala, pode escrever um código para separar os seus dados, lê-los numa folha de cálculo, e assim por diante. Por exemplo:

```csharp
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
```

Para obter uma amostra de código maior, consulte [utilizando uma função de trabalhador][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Elimine os seus dados antigos
É responsável pela gestão da sua capacidade de armazenamento e pela eliminação dos dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se regenerar a sua chave de armazenamento...
Se alterar a chave do armazenamento, a Exportação contínua deixará de funcionar. Verá uma notificação na conta do Azure.

Abra o separador Exportação Contínua e edite a exportação. Edite o Destino de Exportação, mas deixe o mesmo armazenamento selecionado. Clique em OK para confirmar.

A exportação contínua será reiniciada.

## <a name="export-samples"></a>Amostras de exportação

* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Amostra de Stream Analytics 2](export-stream-analytics.md)

Em escalas maiores, considere [hdInsight](https://azure.microsoft.com/services/hdinsight/) - aglomerados hadoop na nuvem. O HDInsight fornece uma variedade de tecnologias para gerir e analisar big data, e você poderia usá-lo para processar dados que foram exportados a partir de Application Insights.

## <a name="q--a"></a>Perguntas e Respostas
* *Mas tudo o que quero é um download único de um gráfico.*  

    Sim, podes fazer isso. No topo do separador, clique em **Dados de Exportação.**
* *Criei uma exportação, mas não há dados na minha loja.*

    O Application Insights recebeu alguma telemetria da sua app desde que criou a exportação? Só receberá novos dados.
* *Tentei criar uma exportação, mas foi-me negado acesso.*

    Se a conta é propriedade da sua organização, tem de ser membro dos grupos de proprietários ou contribuintes.
* *Posso exportar diretamente para a minha própria loja no local?*

    Não, desculpa. O nosso motor de exportação atualmente só funciona com armazenamento Azure neste momento.  
* *Há algum limite para a quantidade de dados que colocou na minha loja?*

    N.º Vamos continuar a empurrar os dados até apagares a exportação. Paramos se atingirmos os limites exteriores para o armazenamento de bolhas, mas isso é muito grande. Cabe-te a ti controlar o armazenamento que usas.  
* *Quantas bolhas devo ver no depósito?*

  * Para cada tipo de dados selecionado para exportar, é criada uma nova bolha a cada minuto (se os dados estiverem disponíveis).
  * Além disso, para aplicações com tráfego elevado, são atribuídas unidades de partição adicionais. Neste caso, cada unidade cria uma bolha a cada minuto.
* *Regenera a chave do meu armazenamento ou mudei o nome do contentor, e agora a exportação não funciona.*

    Edite a exportação e abra o separador destino de exportação. Deixe o mesmo armazenamento selecionado como antes e clique em OK para confirmar. A exportação vai recomeçar. Se a mudança foi nos últimos dias, não perderá dados.
* *Posso parar a exportação?*

    Yes. Clique em Disable.

## <a name="code-samples"></a>Exemplos de código

* [Amostra de Stream Analytics](export-stream-analytics.md)
* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Referência detalhada do modelo de dados para os tipos e valores da propriedade.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Exportação baseada em definições de diagnóstico

As definições de diagnóstico baseadas na exportação utilizam um esquema diferente do que a exportação contínua. Também suporta características que a exportação contínua não gosta:

* Contas de armazenamento Azure com vnet, firewalls e links privados.
* Exportação para centro de eventos.

Migrar para as definições de diagnóstico baseadas na exportação:

1. Desative a exportação contínua atual.
2. [Migrar a aplicação para o espaço de trabalho.](convert-classic-resource.md)
3. [Permitir a exportação de configurações de diagnóstico](create-workspace-resource.md#export-telemetry). Selecione **definições de diagnóstico > adicione a definição** de diagnóstico a partir do seu recurso Application Insights.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

