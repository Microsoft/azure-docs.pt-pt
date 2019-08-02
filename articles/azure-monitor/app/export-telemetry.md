---
title: Exportação contínua de telemetria de Application Insights | Microsoft Docs
description: Exporte dados de diagnóstico e de uso para armazenamento em Microsoft Azure e baixe-os a partir daí.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mbullwin
ms.openlocfilehash: 3238abcbcbc4d776e3736b13d5b32149c642649c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516953"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria do Application Insights
Deseja manter sua telemetria por mais tempo do que o período de retenção padrão? Ou processá-lo de alguma maneira especializada? A exportação contínua é ideal para isso. Os eventos que você vê no portal de Application Insights podem ser exportados para armazenamento em Microsoft Azure no formato JSON. A partir daí, você pode baixar seus dados e escrever qualquer código necessário para processá-los.  

Antes de configurar a exportação contínua, há algumas alternativas que talvez você queira considerar:

* O botão Exportar na parte superior de uma guia métricas ou Pesquisar permite transferir tabelas e gráficos para uma planilha do Excel.

* O [Analytics](../../azure-monitor/app/analytics.md) fornece uma linguagem de consulta avançada para telemetria. Ele também pode exportar resultados.
* Se você estiver procurando [explorar seus dados no Power bi](../../azure-monitor/app/export-power-bi.md ), poderá fazer isso sem usar a exportação contínua.
* A [API REST de acesso a dados](https://dev.applicationinsights.io/) permite que você acesse sua telemetria programaticamente.
* Você também pode acessar a configuração de [exportação contínua por meio do PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Depois que a exportação contínua copia seus dados para o armazenamento (onde ele pode permanecer enquanto você quiser), ele ainda está disponível em Application Insights para o [período de retenção](../../azure-monitor/app/data-retention-privacy.md)normal.

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração de armazenamento avançado de exportação contínua

A exportação contínua **não dá suporte** aos seguintes recursos/configurações do armazenamento do Azure:

* Uso de [firewalls de armazenamento VNET/Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) em conjunto com o armazenamento de BLOBs do Azure.

* [Armazenamento imutável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) para o armazenamento de BLOBs do Azure.

* [Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a>Criar uma exportação contínua

1. No recurso de Application Insights para seu aplicativo em configurar à esquerda, abra exportação contínua e escolha **Adicionar**:

2. Escolha os tipos de dados de telemetria que você deseja exportar.

3. Crie ou selecione uma [conta de armazenamento do Azure](../../storage/common/storage-introduction.md) na qual você deseja armazenar os dados. Para obter mais informações sobre as opções de preços de armazenamento, visite a [página de preços oficial](https://azure.microsoft.com/pricing/details/storage/).

     Clique em Adicionar, exportar destino, conta de armazenamento e, em seguida, crie um novo repositório ou escolha um repositório existente.

    > [!Warning]
    > Por padrão, o local de armazenamento será definido para a mesma região geográfica que o recurso de Application Insights. Se você armazenar em uma região diferente, poderá incorrer em encargos de transferência.

4. Crie ou selecione um contêiner no armazenamento.

Depois de criar a exportação, ela começa a ir. Você só obtém dados que chegam depois de criar a exportação.

Pode haver um atraso de cerca de uma hora antes que os dados apareçam no armazenamento.

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Clique em exportação contínua e selecione a conta de armazenamento a ser editada.

### <a name="to-stop-continuous-export"></a>Para parar a exportação contínua

Para interromper a exportação, clique em Desabilitar. Quando você clica em habilitar novamente, a exportação será reiniciada com novos dados. Você não obterá os dados que chegaram ao portal enquanto a exportação estava desabilitada.

Para interromper a exportação permanentemente, exclua-a. Isso não exclui os dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não é possível adicionar ou alterar uma exportação?
* Para adicionar ou alterar as exportações, você precisa de direitos de acesso de proprietário, colaborador ou Application Insights colaborador. [Saiba mais sobre as funções][roles].

## <a name="analyze"></a>Quais eventos você obtém?
Os dados exportados são a telemetria bruta que recebemos de seu aplicativo, exceto que adicionamos dados de local, que calculamos a partir do endereço IP do cliente.

Os dados que foram descartados por [amostragem](../../azure-monitor/app/sampling.md) não são incluídos nos dados exportados.

Outras métricas calculadas não são incluídas. Por exemplo, não exportamos a utilização média da CPU, mas exportamos a telemetria bruta da qual a média é calculada.

Os dados também incluem os resultados de qualquer [teste de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md) que você configurou.

> [!NOTE]
> **Exemplos.** Se seu aplicativo enviar muitos dados, o recurso de amostragem poderá operar e enviar apenas uma fração da telemetria gerada. [Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a>Inspecionar os dados
Você pode inspecionar o armazenamento diretamente no Portal. Clique em início no menu à extrema esquerda, na parte superior em que diz "serviços do Azure" selecionar **contas de armazenamento**, selecione o nome da conta de armazenamento, na página Visão geral, selecione **BLOBs** em serviços e, por fim, selecione o nome do contêiner.

Para inspecionar o armazenamento do Azure no Visual Studio, abra o **modo de exibição**, **Cloud Explorer**. (Se você não tiver esse comando de menu, precisará instalar o SDK do Azure: Abra a caixa de diálogo **novo projeto** , C#expanda Visual/Cloud e escolha **obter Microsoft Azure SDK para .net**.)

Ao abrir seu repositório de BLOB, você verá um contêiner com um conjunto de arquivos de BLOB. O URI de cada arquivo derivado de seu Application Insights nome do recurso, sua chave de instrumentação, tipo de telemetria/data/hora. (O nome do recurso está em letras minúsculas e a chave de instrumentação omite traços.)

![Inspecione o repositório de blob com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e hora são UTC e são quando a telemetria foi depositada na loja, não a hora em que foi gerada. Portanto, se você escrever código para baixar os dados, ele poderá mover-se linearmente pelos dados.

Aqui está a forma do caminho:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Onde

* `blobCreationTimeUtc`é a hora em que o blob foi criado no armazenamento de preparo interno
* `blobDeliveryTimeUtc`é a hora em que o blob é copiado para o armazenamento de destino de exportação

## <a name="format"></a>Formato de dados
* Cada blob é um arquivo de texto que contém várias linhas separadas por "\n". Ele contém a telemetria processada durante um período de aproximadamente meio minuto.
* Cada linha representa um ponto de dados de telemetria, como uma solicitação ou uma exibição de página.
* Cada linha é um documento JSON não formatado. Se você quiser sentar-se, abra-o no Visual Studio e escolha Editar, avançado, arquivo de formato:

![Exibir a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

As durações de tempo estão em tiques, em que 10 000 tiques = 1 ms. Por exemplo, esses valores mostram um tempo de 1 ms para enviar uma solicitação do navegador, 3 ms para recebê-lo e 1,8 s para processar a página no navegador:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referência de modelo de dados detalhado para os tipos de propriedade e valores.](export-data-model.md)

## <a name="processing-the-data"></a>Processando os dados
Em uma pequena escala, você pode escrever um código para extrair seus dados, lê-los em uma planilha e assim por diante. Por exemplo:

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

Para obter um exemplo de código maior, consulte [usando uma função de trabalho][exportasa].

## <a name="delete"></a>Excluir os dados antigos
Você é responsável por gerenciar sua capacidade de armazenamento e excluir os dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se você regenerar sua chave de armazenamento...
Se você alterar a chave para seu armazenamento, a exportação contínua deixará de funcionar. Você verá uma notificação em sua conta do Azure.

Abra a guia exportação contínua e edite a exportação. Edite o destino de exportação, mas apenas deixe o mesmo armazenamento selecionado. Clique em OK para confirmar.

A exportação contínua será reiniciada.

## <a name="export-samples"></a>Exportar amostras

* [Exportar para SQL usando o Stream Analytics][exportasa]
* [Exemplo de Stream Analytics 2](export-stream-analytics.md)

Em escalas maiores, considere os clusters [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop na nuvem. O HDInsight fornece uma variedade de tecnologias para gerenciar e analisar Big Data, e você pode usá-lo para processar dados que foram exportados do Application Insights.

## <a name="q--a"></a>P&R
* *Mas tudo o que eu quero é um download único de um gráfico.*  

    Sim, você pode fazer isso. Na parte superior da guia, clique em **exportar dados**.
* *Configurei uma exportação, mas não há dados no meu repositório.*

    Application Insights recebeu qualquer telemetria do seu aplicativo desde que você configurou a exportação? Você só receberá novos dados.
* *Tentei configurar uma exportação, mas o acesso foi negado*

    Se a conta pertence à sua organização, você precisa ser membro dos grupos proprietários ou colaboradores.
* *Posso exportar diretamente para meu próprio repositório local?*

    Não, desculpe. Atualmente, nosso mecanismo de exportação só funciona com o armazenamento do Azure no momento.  
* *Há algum limite para a quantidade de dados que você coloca em meu repositório?*

    Não. Continuaremos enviando dados por push até que você exclua a exportação. Vamos parar se atingirmos os limites externos do armazenamento de BLOBs, mas isso é bem enorme. Cabe a você controlar a quantidade de armazenamento que você usa.  
* *Quantos BLOBs devo ver no armazenamento?*

  * Para cada tipo de dados selecionado para exportação, um novo BLOB é criado a cada minuto (se os dados estiverem disponíveis).
  * Além disso, para aplicativos com alto tráfego, unidades de partição adicionais são alocadas. Nesse caso, cada unidade cria um blob a cada minuto.
* *Eu regerei a chave para o meu armazenamento ou alterei o nome do contêiner, e agora a exportação não funciona.*

    Edite a exportação e abra a guia destino de exportação. Deixe o mesmo armazenamento selecionado como antes e clique em OK para confirmar. A exportação será reiniciada. Se a alteração tiver sido nos últimos dias, você não perderá dados.
* *Posso pausar a exportação?*

    Sim. Clique em Desabilitar.

## <a name="code-samples"></a>Exemplos de código

* [Exemplo de Stream Analytics](export-stream-analytics.md)
* [Exportar para SQL usando o Stream Analytics][exportasa]
* [Referência de modelo de dados detalhado para os tipos de propriedade e valores.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
