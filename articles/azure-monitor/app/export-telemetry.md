---
title: A exportação contínua de telemetria do Application Insights | Documentos da Microsoft
description: Exportar dados de diagnóstico e utilização para o armazenamento no Microsoft Azure e transferi-lo a partir daí.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 71e70962a8c55d397b6261571cfef4a126d3e8b4
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307824"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria do Application Insights
Quer manter a sua telemetria por mais tempo do que o período de retenção padrão? Ou processá-los de alguma forma especializada? A exportação contínua é ideal para isso. Os eventos de que ver no portal do Application Insights podem ser exportados para o armazenamento no Microsoft Azure no formato JSON. A partir daí pode transferir os seus dados e escrever qualquer código que precisa para processá-lo.  

Antes de configurar a exportação contínua, existem algumas alternativas que talvez queira considerar:

* O botão de exportação na parte superior do painel de métricas ou de pesquisa permite-lhe transferir as tabelas e gráficos para uma planilha do Excel.

* [Análise](../../azure-monitor/app/analytics.md) fornece uma linguagem de consulta poderosa para a telemetria. Ele também pode exportar os resultados.
* Se pretende [explorar os seus dados no Power BI](../../azure-monitor/app/export-power-bi.md ), pode fazer isso sem utilizar a exportação contínua.
* O [REST API de acesso a dados](https://dev.applicationinsights.io/) permite-lhe aceder programaticamente a sua telemetria.
* Também pode acessar o programa de configuração [exportação contínua através do Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Após a exportação contínua copia os seus dados para o armazenamento (em que ele pode manter-se para, desde que gosta), ela ainda está disponível no Application Insights para o habitual [período de retenção](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração de armazenamento avançada de exportação contínua

A exportação contínua **não suporta** as seguintes funcionalidades de armazenamento do Azure/configurações:

* Usar [firewalls de armazenamento do VNET/Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) em conjunto com o armazenamento de Blobs do Azure.

* [Armazenamento imutável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) para o armazenamento de Blobs do Azure.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a> Criar uma exportação contínua
1. No recurso do Application Insights para a sua aplicação, abra a exportação contínua e selecione **adicionar**:

2. Escolha a telemetria de tipos de dados que pretende exportar.

3. Crie ou selecione um [conta de armazenamento do Azure](../../storage/common/storage-introduction.md) onde pretende armazenar os dados. Para obter mais informações sobre as opções de preços de armazenamento, visite o [oficial de página de preços](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Por predefinição, a localização de armazenamento será definida para a mesma região geográfica que seu recurso do Application Insights. Se armazenar numa região diferente, poderá incorrer em custos de transferência.

    ![Clique em Adicionar, destino de exportação, conta de armazenamento e, em seguida, criar um novo arquivo ou escolha um arquivo existente](./media/export-telemetry/02-add.png)

4. Crie ou selecione um contentor no armazenamento:

    ![Clique em tipos de eventos de escolha](./media/export-telemetry/create-container.png)

Depois de criar a exportação, ele começa vai. Obter dados que chegam depois de criar a exportação apenas.

Pode haver um atraso de menos de uma hora antes de aparecerem dados no armazenamento.

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Se pretender alterar os tipos de evento mais tarde, basta edite a exportação:

![Clique em tipos de eventos de escolha](./media/export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Para parar a exportação contínua

Para parar a exportação, clique em Desativar. Quando clicar em ativar novamente, a exportação irá reiniciar com novos dados. Não obtém os dados que chegaram no portal, enquanto a exportação foi desativada.

Para parar a exportação permanentemente, eliminá-lo. Se o fizer, não elimina os dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não é possível adicionar ou alterar uma exportação?
* Para adicionar ou alterar as exportações, precisa de direitos de acesso de proprietário, contribuinte ou contribuinte do Application Insights. [Saiba mais sobre as funções][roles].

## <a name="analyze"></a> Que eventos consegue?
Os dados exportados são a telemetria não processada que recebemos a partir da sua aplicação, exceto que podemos adicionar dados de localização que Calculamos do endereço IP do cliente.

Dados que foi ignorados pelo [amostragem](../../azure-monitor/app/sampling.md) não está incluído nos dados exportados.

Outras métricas calculadas não são incluídas. Por exemplo, podemos não exportar a utilização média da CPU, mas podemos exportar a telemetria não processados a partir do qual é calculada a média.

Os dados também incluem os resultados de qualquer [testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) que configurou.

> [!NOTE]
> **Amostragem.** Se a sua aplicação envia uma grande quantidade de dados, a funcionalidade de amostragem pode operar e enviar apenas uma fração de telemetria gerada. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Inspecionar os dados
Pode inspecionar o armazenamento diretamente no portal. Clique em **navegue**, selecione a sua conta de armazenamento e, em seguida, abra **contentores**.

Para inspecionar o armazenamento do Azure no Visual Studio, abra **View**, **Cloud Explorer**. (Se não tiver esse comando de menu, tem de instalar o SDK do Azure: Abra o **novo projeto** caixa de diálogo, expanda o elemento Visual C#/na Cloud e escolha **obter o Microsoft Azure SDK para .NET**.)

Quando abre o seu armazenamento de BLOBs, verá um contentor com um conjunto de ficheiros de blob. O URI de cada arquivo derivado de seu nome de recurso do Application Insights, sua chave de instrumentação, a telemetria-tipo/data/hora. (O nome do recurso é todo em minúsculas e a chave de instrumentação omite travessões.)

![Inspecione o armazenamento de Blobs com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e hora são UTC e quando a telemetria foi deposited no arquivo - não que ele foi gerado. Portanto, se escrever código para transferir os dados, ele pode passar linearmente através dos dados.

Eis a forma do caminho:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Onde

* `blobCreationTimeUtc` é hora de criação de BLOBs a interno no teste de armazenamento
* `blobDeliveryTimeUtc` é a hora quando o blob é copiado para o armazenamento de destino de exportação

## <a name="format"></a> Formato de dados
* Cada blob é um arquivo de texto que contém vários "\n'-separated linhas. Ela contém a telemetria processada durante um período de tempo de aproximadamente metade um minuto.
* Cada linha representa um ponto de dados de telemetria, como um pedido ou vista de página.
* Cada linha é um documento JSON formatado. Se pretende sentar-se e olhando para ele, abra-o no Visual Studio e selecione edite, avançado, formato de ficheiro:

![Ver a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

Horas de duração são em tiques, onde 10 000 tiques = 1 ms. Por exemplo, estes valores mostram um tempo de 1 ms para enviar uma solicitação do navegador, 3 ms para receber e 1.8 s para processar a página no navegador:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referência para os tipos de propriedade e os valores do modelo de dados detalhados.](export-data-model.md)

## <a name="processing-the-data"></a>Processar os dados
Em pequena escala, pode escrever algum código para extrair a distância de seus dados, lê-lo para uma folha de cálculo e assim por diante. Por exemplo:

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

Para obter um exemplo de código maior, veja [usando uma função de trabalho][exportasa].

## <a name="delete"></a>Eliminar os dados antigos
É responsável por gerir a sua capacidade de armazenamento e a eliminar os dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se voltar a gerar a chave de armazenamento...
Se alterar a chave para o armazenamento, a exportação contínua deixarão de funcionar. Verá uma notificação na sua conta do Azure.

Abra o painel de exportação contínua e editar a exportação. Editar o destino de exportação, mas deixar o mesmo armazenamento selecionado. Clique em OK para confirmar.

![Editar a exportação contínua, abertura e fechamento thee destino de exportação.](./media/export-telemetry/07-resetstore.png)

A exportação contínua será reiniciado.

## <a name="export-samples"></a>Exemplos de exportação

* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Exemplo de análise de Stream 2](export-stream-analytics.md)

Nas escalas maiores, considere [HDInsight](https://azure.microsoft.com/services/hdinsight/) -clusters do Hadoop na cloud. HDInsight fornece uma variedade de tecnologias para gerenciamento e análise de macrodados e pode utilizá-lo para processar os dados que tenham sido exportados do Application Insights.

## <a name="q--a"></a>P&R
* *Mas tudo o que eu quero que é um download único de um gráfico.*  

    Sim, pode fazer isso. Na parte superior do painel, clique em **exportar dados**.
* *Posso configurar uma exportação, mas não há nenhum dado no arquivo meu.*

    Application Insights recebeu qualquer telemetria da sua aplicação, uma vez que configurou a exportação? Receberá apenas novos dados.
* *Tentou configurar uma exportação, mas foi negado o acesso*

    Se a conta for propriedade de sua organização, tem de ser um membro dos grupos de proprietários ou contribuidores.
* *Pode exportar diretamente para o meu próprio arquivo no local?*

    Não, Desculpe. Nosso motor de exportação atualmente só funciona com o armazenamento do Azure neste momento.  
* *Existe algum limite para a quantidade de dados colocados no arquivo meu?*

    Não. Podemos irá manter enviar dados por push até eliminar a exportação. Deixaremos de se atingimos os limites externos para o armazenamento de BLOBs, mas isso é muito grande. Cabe-lhe a si controlar a quantidade de armazenamento que utilizar.  
* *O número de blobs que vejo no armazenamento?*

  * Para cada tipo de dados selecionados para exportar, um blob novo é criado a cada minuto (se os dados estão disponíveis).
  * Além disso, para aplicativos com tráfego intenso, unidades adicionais de partição são alocadas. Neste caso, cada unidade cria um blob a cada minuto.
* *Eu gerada novamente a chave para o meu armazenamento ou alterou o nome do contentor, e agora a exportação não funciona.*

    Editar a exportação e abrir o painel de destino de exportação. Deixe o mesmo armazenamento selecionado como antes e clique em OK para confirmar. Exportação será reiniciado. Se a alteração foi dentro de alguns nos últimos dias, não irá perder dados.
* *Pode interromper a exportação?*

    Sim. Clique em Desativar.

## <a name="code-samples"></a>Exemplos de código

* [Exemplo do Stream Analytics](export-stream-analytics.md)
* [Exportar para o SQL com o Stream Analytics][exportasa]
* [Referência para os tipos de propriedade e os valores do modelo de dados detalhados.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
