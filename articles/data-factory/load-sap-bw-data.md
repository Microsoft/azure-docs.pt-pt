---
title: Carregar dados do SAP Business Warehouse, utilizando o Azure Data Factory | Documentos da Microsoft
description: Utilizar o Azure Data Factory para copiar dados do SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 4cd61db3ec0e8d88c9b1c6d6ba427b120b3f1af1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66152423"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse com o Azure Data Factory

Este artigo mostra como utilizar o Azure Data Factory para copiar dados do SAP Business Warehouse (BW) através de Hub aberto para a geração 2 de armazenamento do Azure Data Lake. Pode utilizar um processo semelhante para copiar dados para si [arquivos de dados de sink suportados](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Para obter informações gerais, como copiar dados do SAP BW, incluindo a integração de Hub aberto do SAP BW e fluxo de extração de delta, consulte [copiar dados do SAP Business Warehouse através de Hub aberto com o Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory**: Se não tiver um, siga os passos para [criar uma fábrica de dados](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW aberto Hub destino (OHD) com o tipo de destino "Tabela de banco de dados"** : Para criar um OHD ou para verificar se a sua OHD está configurado corretamente para a integração do Data Factory, consulte a [configurações de SAP BW aberto Hub destino](#sap-bw-open-hub-destination-configurations) seção deste artigo.

- **O utilizador de SAP BW necessita das seguintes permissões**:

  - Autorização para chamadas de função remota (RFC) e do SAP BW.
  - Permissões para a atividade de "Executar" do **S_SDSAUTH** objeto de autorização.

- **R [integration runtime (IR) autoalojado](concepts-integration-runtime.md#self-hosted-integration-runtime) com o conector de SAP .NET 3.0**. Siga estes passos de configuração:

  1. Instalar e registar o runtime de integração autoalojado, versão 3.13 ou posterior. (Este processo é descrito neste artigo.)

  2. Transfira o [conector de SAP de 64 bits para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) partir do site do SAP e instalá-lo no mesmo computador que o ir autoalojado. Durante a instalação, certifique-se de que seleciona **instalar Assemblies ao GAC** no **passos de configuração opcionais** caixa de diálogo, como mostra a imagem seguinte:

     ![Configurar a caixa de diálogo do conector do SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Fazer uma cópia completa do Hub aberto do SAP BW

No portal do Azure, vá para a fábrica de dados. Selecione **criar e monitorizar** para abrir a IU do Data Factory num separador à parte.

1. Sobre o **Vamos começar** página, selecione **copiar dados** para abrir a ferramenta copiar dados.

2. Sobre o **propriedades** , especifique um **nome da tarefa**e, em seguida, selecione **seguinte**.

3. Sobre o **o arquivo de dados de origem** página, selecione **+ criar nova ligação**. Selecione **Hub aberto do SAP BW** da galeria do conector e, em seguida, selecione **continuar**. Para filtrar os conectores, pode digitar **SAP** na caixa de pesquisa.

4. Sobre o **ligação do Hub aberto BW SAP especificar** página, siga estes passos para criar uma nova ligação.

   ![Criar a página de serviço ligado de Hub aberto do SAP BW](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Partir do **ligar através do runtime de integração** , selecione um ir de hospedagem interna existente. Em alternativa, optar por criar um se ainda não tiver uma.

      Para criar um novo Runtime de integração autoalojado, selecione **+ novo**e, em seguida, selecione **autoalojado**. Introduza um **Name**e, em seguida, selecione **próxima**. Selecione **configuração rápida** para instalar no computador atual ou siga a **configuração Manual** passos que são fornecidos.

      Conforme mencionado na [pré-requisitos](#prerequisites), certifique-se de que tenha o conector do SAP para o Microsoft .NET 3.0 instalado no mesmo computador onde está a executar o runtime de integração autoalojado.

   2. Preencha o SAP BW **nome do servidor**, **número de sistema**, **ID de cliente** **linguagem** (se diferente do **EN**) , **Nome de utilizador**, e **palavra-passe**.

   3. Selecione **Testar ligação** para validar as definições e, em seguida, selecione **concluir**.

   4. É criada uma nova ligação. Selecione **Seguinte**.

5. Sobre o **selecionar destinos de Hub aberto** página, procure os destinos de Hub aberto que estão disponíveis no seu SAP BW. Selecione o OHD para copiar dados de e, em seguida, selecione **seguinte**.

   ![Selecione a tabela de destino de Hub aberto do SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se precisa de um. Se sua OHD contém apenas os dados a partir de uma execução de processo (DTP) de transferência de dados única com um ID de pedido único, ou tem a certeza de que sua DTP estiver concluída e que pretende copiar os dados, limpe o **excluir o último pedido** caixa de verificação.

   Saiba mais sobre estas definições na [configurações de SAP BW aberto Hub destino](#sap-bw-open-hub-destination-configurations) seção deste artigo. Selecione **Validate** para verificar novamente os dados que vão ser devolvidos. Em seguida, selecione **Seguinte**.

   ![Configurar o filtro de Hub aberto do SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Sobre o **o arquivo de dados de destino** página, selecione **+ criar nova ligação** > **Gen2 de armazenamento do Azure Data Lake**  >   **Continuar**.

8. Sobre o **ligação de especificar o Azure Data Lake armazenamento** página, siga estes passos para criar uma ligação.

   ![Criar uma página de serviço ligado de geração 2 do ADLS](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione a sua conta com capacidade de armazenamento do Data Lake Gen2 do **nome** na lista pendente.
   2. Selecione **concluir** para criar a ligação. Em seguida, selecione **Seguinte**.

9. Sobre o **escolher o ficheiro de saída ou a pasta** página, introduza **copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Seguinte**.

   ![Escolha a página da pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Sobre o **formato de ficheiro de definição** página, selecione **próxima** para utilizar as predefinições.

    ![Especificar a página de formato de sink](media/load-sap-bw-data/specify-sink-format.png)

11. Sobre o **configurações** página, expanda **as definições de desempenho**. Introduza um valor para **grau de paralelismo de cópia** como 5 para carregar a partir de SAP BW em paralelo. Em seguida, selecione **Seguinte**.

    ![Configurar as definições de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. Sobre o **resumo** , reveja as definições. Em seguida, selecione **Seguinte**.

13. Sobre o **implantação** página, selecione **Monitor** para monitorizar o pipeline.

    ![Página de implementação](media/load-sap-bw-data/deployment.png)

14. Tenha em atenção que o **Monitor** separador no lado esquerdo da página é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes de execução de atividade e voltar a executar o pipeline.

    ![Vista de monitorização de pipelines](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione **ver execuções de atividades** no **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Ecrã de monitorização da atividade](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** link, que é um ícone de óculos abaixo **ações** na vista de monitorização da atividade. Detalhes disponíveis incluem o volume de dados copiados da origem para o sink, a taxa de transferência de dados, a passos de execução e a duração e configurações utilizadas.

    ![Monitorizar os detalhes de atividade](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para ver os **máximo ID do pedido**, vá para o modo de exibição e selecione monitorização da atividade **saída** sob **ações**.

    ![Ecrã de saída da atividade](media/load-sap-bw-data/activity-output.png)

    ![Vista de detalhes de saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Fazer uma cópia incremental a partir do Hub aberto do SAP BW

> [!TIP]
> Ver [fluxo de extração de delta de conector de Hub aberto do SAP BW](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o conector do Hub aberto do SAP BW no Data Factory copia dados incrementais do SAP BW. Este artigo também pode ajudar a compreender a configuração do conector básica.

Agora, vamos continuar a configurar a cópia incremental do Hub aberto do SAP BW.

Cópia incremental usa um mecanismo de "limite superior de tamanho", que se baseia a **ID do pedido**. Esse ID é automaticamente gerada no destino de Hub do SAP BW aberto pelo DTP. O diagrama seguinte mostra este fluxo de trabalho:

![Fluxograma de fluxo de trabalho de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

Na fábrica de dados **Vamos começar** página, selecione **criar o pipeline de modelo** para utilizar o modelo interno.

1. Procure **SAP BW** para localizar e selecionar o **Incremental copiar do SAP BW, para a geração 2 de armazenamento do Azure Data Lake** modelo. Este modelo copia os dados na geração 2 de armazenamento do Azure Data Lake. Pode usar um fluxo de trabalho semelhante para copiar para outros tipos de sink.

2. Na página de principal do modelo, selecione ou crie as seguintes ligações de três e, em seguida, selecione **Utilize este modelo** no canto inferior direito da janela.

   - **Armazenamento de Blobs do Azure**: Nestas instruções, utilizamos o armazenamento de Blobs do Azure para armazenar a marca d'água alta, o que é o *max copiados ID do pedido*.
   - **Hub do SAP BW aberto**: Esta é a origem para copiar dados a partir de. Consulte o passo a passo completo cópia anterior para a configuração detalhada.
   - **Azure Data Lake Storage Gen2**: Este é o sink para copiar dados para. Consulte o passo a passo completo cópia anterior para a configuração detalhada.

   ![Cópia incremental do modelo de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Este modelo gera um pipeline com as seguintes três atividades e os torna encadeados no sucesso: *Pesquisa*, *copiar dados*, e *Web*.

   Vá para o pipeline **parâmetros** separador. Ver todas as configurações que tem de fornecer.

   ![Cópia incremental da configuração de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique o nome da tabela Hub aberto para copiar dados a partir de.

   - **ADLSGen2SinkPath**: Especifique o caminho de geração 2 de armazenamento do Azure Data Lake de destino para copiar dados para o. Se o caminho não existir, a atividade de cópia do Data Factory cria um caminho durante a execução.

   - **HighWatermarkBlobPath**: Especifique o caminho para armazenar o valor de limite superior de tamanho, tais como `container/path`.

   - **HighWatermarkBlobName**: Especifique o nome do blob para armazenar o valor de limite superior de tamanho, tais como `requestIdCache.txt`. No armazenamento de BLOBs, vá para o caminho correspondente do HighWatermarkBlobPath + HighWatermarkBlobName, tal como *container/path/requestIdCache.txt*. Crie um blob com conteúdo 0.

      ![Conteúdo do blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Neste modelo, usamos WebActivity para chamar o Azure Logic Apps para definir o valor de limite superior de tamanho no armazenamento de Blobs. Em alternativa, pode utilizar a base de dados do Azure SQL para armazená-lo. Utilize uma atividade de procedimento armazenado para atualizar o valor.

      Tem de criar uma aplicação lógica, como mostra a imagem seguinte. Em seguida, cole a **URL do HTTP POST**.

      ![Configuração da aplicação lógica](media/load-sap-bw-data/logic-app-config.png)

      1. Aceda ao portal do Azure. Selecione uma nova **Logic Apps** serviço. Selecione **+ aplicação lógica em branco** até **estruturador de aplicações lógicas**.

      2. Criar um acionador de **pedido de HTTP de uma quando é recebido**. Especifique o corpo do pedido HTTP da seguinte forma:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Adicionar uma **Create blob** ação. Para **caminho da pasta** e **nome do Blob**, utilize os mesmos valores que configurou anteriormente no **HighWatermarkBlobPath** e **HighWatermarkBlobName**.

      4. Selecione **Guardar**. Em seguida, copie o valor da **URL do HTTP POST** para utilizar o pipeline de fábrica de dados.

4. Depois de fornecer os parâmetros de pipeline do Data Factory, selecione **depurar** > **concluir** para invocar uma execução para validar a configuração. Em alternativa, selecione **publicar tudo** para publicar as alterações e, em seguida, selecione **acionador** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino de Hub aberto do SAP BW

Esta secção apresenta a configuração do lado do SAP BW para utilizar o conector de Hub aberto do SAP BW no Data Factory para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurar a extração de delta no SAP BW

Se precisar de cópia histórica e cópia incremental ou apenas cópia incremental, configure a extração de delta no SAP BW.

1. Crie o destino de Hub aberto. Pode criar o OHD no RSA1 de transação do SAP, que cria automaticamente o processo de transferência de dados e transformação necessária. Utilize as seguintes definições:

   - **ObjectType**: Pode usar qualquer tipo de objeto. Aqui, podemos usar **consulta do InfoCube** como exemplo.
   - **Tipo de destino**: Selecione **tabela de base de dados**.
   - **Chave da tabela**: Selecione **chave técnico**.
   - **Extração**: Selecione **manter os dados e registos de inserção na tabela**.

   ![Criar caixa de diálogo de extração do SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Criar caixa de diálogo do SAP BW OHD delta2 extração](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Pode aumentar o número de paralelo executar processos de trabalho do SAP para o DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende DTP em cadeias de processo.

   Um delta DTP para um cubo só funciona se ainda não foram comprimidas as linhas necessárias. Certifique-se de que compressão de cubo BW não está a executar antes do DTP para a tabela de Hub aberto. A maneira mais fácil de fazer isso é integrar o DTP suas cadeias de processo existente. No exemplo a seguir, DTP (para OHD) é inserida na cadeia de processo entre o *ajustar* (rollup de agregação) e *fechar* passos (compressão de cubo).

   ![Criar o gráfico de fluxo de cadeia de processo de SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurar a extração completa no SAP BW

Além de extração de delta, pode desejar uma extração completa da mesma BW InfoProvider da SAP. Isso normalmente se aplica se pretender que a para completa, cópia, mas não incremental, ou se pretender [ressincronizar a extração de delta](#resync-delta-extraction).

Não pode ter mais do que um DTP para o mesmo OHD. Por isso, tem de criar um OHD adicional antes de extração de delta.

![Criar o SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para uma carga completa OHD, escolha opções diferentes do que para extração de delta:

- No OHD: Definir o **extração** a opção de **eliminar dados e registos de inserir**. Caso contrário, os dados serão extraídos muitas vezes, quando repetir DTP numa cadeia de processo BW.

- No DTP: Definir **modo de extração** ao **completo**. Tem de alterar o DTP criado automaticamente da **Delta** ao **completo** imediatamente após o OHD é criado, como mostra a esta imagem:

   ![Criar o SAP BW OHD caixa de diálogo configurada para extração de "Completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector do Hub de Open BW do Data Factory: Desativar **excluir o último pedido**. Caso contrário, nada será extraído.

Normalmente, executa o DTP completa manualmente. Em alternativa, pode criar uma cadeia de processo para o DTP completa. Normalmente, é uma cadeia separada independente da sua cadeias de processo existente. Em ambos os casos *Certifique-se de que o DTP é concluída antes de começar a extração, utilizando a cópia do Data Factory*. Caso contrário, apenas dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar a extração de delta pela primeira vez

A primeira extração de delta é tecnicamente uma *completa de extração*. Por predefinição, o conector de Hub aberto do SAP BW exclui o último pedido quando ele copia os dados. Para a primeira extração de delta, nenhum dado é extraído pela atividade de cópia do Data Factory até que um DTP subsequente gera dados delta da tabela com um ID do pedido separada. Existem duas formas de evitar este cenário:

- Desativar a **excluir o último pedido** opção para a primeira extração de delta. Certifique-se de que o delta primeiro DTP é concluído antes de começar a extração de delta pela primeira vez.
-  Utilize o procedimento para ressincronizar a extração de delta, conforme descrito na secção seguinte.

### <a name="resync-delta-extraction"></a>Volte a sincronizar a extração de delta

Os seguintes cenários de alterar os dados nos cubos do SAP BW, mas não são considerados por DTP de diferenças:

- Eliminação seletiva do SAP BW (de linhas utilizando qualquer condição de filtro)
- Eliminação de pedido de SAP BW (de pedidos com falhas)

Um destino de Hub aberto do SAP não é um destino de dados de data mart controlado (em todos os pacotes de suporte de SAP BW desde 2015). Dessa forma, pode eliminar os dados de um cubo sem alterar os dados no OHD. Tem, em seguida, volte a sincronizar os dados do cubo com o Data Factory:

1. Execute uma extração completa no Data Factory (ao utilizar um DTP completo no SAP).
2. Elimine todas as linhas na tabela de Hub aberto para o delta DTP.
3. Definir o estado do delta DTP para **obtidas**.

Depois disso, todas as diferenças subsequentes DTPs e extrações de delta do Data Factory funcionam conforme esperado.

Para definir o estado do delta DTP para **obtidas**, pode utilizar a seguinte opção para executar o delta DTP manualmente:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o suporte de conector de Hub aberto do SAP BW:

> [!div class="nextstepaction"]
>[Conector do Hub de Open Business Warehouse SAP](connector-sap-business-warehouse-open-hub.md)
