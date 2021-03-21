---
title: Carregar dados do SAP Business Warehouse
description: Utilize a Azure Data Factory para copiar dados do SAP Business Warehouse (BW)
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 3dabb6d5df0a74cc7ae2fb8b381ad9e0dfe04e63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370704"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo mostra como usar a Azure Data Factory para copiar dados do SAP Business Warehouse (BW) via Open Hub para Azure Data Lake Storage Gen2. Pode utilizar um processo semelhante para copiar dados para outras [lojas de dados de sumidouros suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!TIP]
> Para obter informações gerais sobre a cópia de dados da SAP BW, incluindo a integração do SAP BW Open Hub e o fluxo de extração delta, consulte [os dados de cópia do SAP Business Warehouse via Open Hub utilizando a Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory**: Se não tiver um, siga os passos para [criar uma fábrica de dados.](quickstart-create-data-factory-portal.md#create-a-data-factory)

- **SAP BW Open Hub Destination (OHD) com o tipo de destino "Table Database"**: Para criar um OHD ou para verificar se o seu OHD está configurado corretamente para integração da Data Factory, consulte a secção de configurações do [Destino Hub Aberto SAP BW](#sap-bw-open-hub-destination-configurations) deste artigo.

- **O utilizador da SAP BW necessita das seguintes permissões:**

  - Autorização para Chamadas de Função Remota (RFC) e SAP BW.
  - Permissões para a atividade "Executar" **do** S_SDSAUTH objeto de autorização.

- **Um [tempo de integração auto-hospedado (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) com conector SAP .NET 3.0**. Siga estes passos de configuração:

  1. Instale e registe o tempo de execução de integração auto-hospedado, versão 3.13 ou posterior. (Este processo é descrito mais tarde neste artigo.)

  2. Descarregue o [Conector SAP de 64 bits para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP e instale-o no mesmo computador que o IR auto-hospedado. Durante a instalação, certifique-se de que seleciona **Conjuntos de Instalação para GAC** na caixa de diálogo **de etapas de configuração opcional,** como mostra a seguinte imagem:

     ![Configurar a caixa de diálogo SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Faça uma cópia completa do SAP BW Open Hub

No portal do Azure, aceda à sua fábrica de dados. Selecione **Author & Monitor** para abrir a UI da Fábrica de Dados num separador.

1. Na página **'Iniciar's,** selecione **Copy Data** para abrir a ferramenta Dados de Cópia.

2. Na página **Propriedades,** especifique um **nome de Tarefa** e, em seguida, selecione **Seguinte**.

3. Na página **'Source data store',** selecione **+Criar nova ligação**. Selecione **SAP BW Open Hub** da galeria do conector e, em seguida, selecione **Continue**. Para filtrar os conectores, pode escrever **SAP** na caixa de pesquisa.

4. Na página **de ligação Especificar SAP BW Open Hub,** siga estes passos para criar uma nova ligação.

   ![Criar página de serviço ligada ao Hub Aberto SAP BW](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. A partir da lista de **tempo de execução de integração,** selecione um IR auto-hospedado existente. Ou, escolha criar um se ainda não tiver um.

      Para criar um novo IR auto-hospedado, selecione **+New**, e, em seguida, **selecione Self-hosted**. Introduza um **Nome** e, em seguida, selecione **Seguinte**. Selecione **a configuração Express** para instalar no computador atual ou siga os passos **de configuração manual** fornecidos.

      Como mencionado em [Pré-requisitos](#prerequisites), certifique-se de que tem o CONECTOR SAP para o Microsoft .NET 3.0 instalado no mesmo computador onde o IR auto-alojado está a funcionar.

   2. Preencha o **nome** do Servidor SAP BW , **Número do sistema,** **ID do cliente, Id** **do cliente** (se não for **EN**), Nome **de utilizador** e **Palavra-passe**.

   3. Selecione **a ligação de teste** para validar as definições e, em seguida, selecione **Terminar**.

   4. Uma nova ligação é criada. Selecione **Seguinte**.

5. Na página **Select Open Hub Destinations,** consulte os Destinos Open Hub que estão disponíveis no seu SAP BW. Selecione o OHD para copiar dados e, em seguida, selecione **Seguinte**.

   ![Selecione a tabela de destino do hub aberto SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se precisar de um. Se o seu OHD apenas contiver dados de uma única execução de processo de transferência de dados (DTP) com um único ID de pedido, ou se tiver a certeza de que o seu DTP está terminado e pretende copiar os dados, limpe a caixa de verificação **'Exclua** o Último Pedido'.

   Saiba mais sobre estas definições na secção de configurações do [Sap BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) deste artigo. **Selecione Validate** para verificar novamente quais os dados que serão devolvidos. Em seguida, selecione **Seguinte**.

   ![Configure o filtro SAP BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na página da **loja de dados destino,** selecione **+Criar nova ligação**  >  **Azure Data Lake Storage Gen2**  >  **Continue.**

8. Na página **de ligação de armazenamento de dados Azure Data,** siga estes passos para criar uma ligação.

   ![Criar uma página de serviço ligada à ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione a sua conta de  armazenamento de dados Gen2 da lista de drop-down name.
   2. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.

9. Na página Escolha o ficheiro de saída ou a página **de pasta,** **introduza o copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Seguinte**.

   ![Escolha a página da pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Na página de definição do **formato 'Ficheiro',** selecione **Seguinte** para utilizar as definições predefinidos.

    ![Especifique a página do formato do lavatório](media/load-sap-bw-data/specify-sink-format.png)

11. Na página **Definições,** expanda as **definições de desempenho**. Introduza um valor para **o paralelismo de cópia,** como 5 para carregar a partir de SAP BW em paralelo. Em seguida, selecione **Seguinte**.

    ![Configurar definições de cópias](media/load-sap-bw-data/configure-copy-settings.png)

12. Na página **Resumo,** reveja as definições. Em seguida, selecione **Seguinte**.

13. Na página **'Implantação',** selecione **Monitor** para monitorizar o gasoduto.

    ![Página de implementação](media/load-sap-bw-data/deployment.png)

14. Note que o **separador Monitor** no lado esquerdo da página é selecionado automaticamente. A coluna **Ações** inclui links para visualizar detalhes da atividade e para refazer o gasoduto.

    ![Vista de monitorização do gasoduto](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para visualizar os percursos de atividade que estão associados à execução do pipeline, selecione **Ver Operações Executada** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista de cursos de gasoduto, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Tela de monitorização da atividade](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorizar os detalhes da execução de cada atividade de cópia, selecione o link **Details,** que é um ícone de óculos abaixo **das Ações** na vista de monitorização da atividade. Os detalhes disponíveis incluem o volume de dados copiado da fonte para a pia, a produção de dados, as etapas de execução e a duração e as configurações utilizadas.

    ![Detalhes de monitorização da atividade](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para visualizar o **ID de pedido máximo,** volte à vista de monitorização da atividade e selecione **Output** em **Ações**.

    ![Tela de saída de atividade](media/load-sap-bw-data/activity-output.png)

    ![Vista de detalhes de saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental do SAP BW Open Hub

> [!TIP]
> Consulte [o fluxo de extração delta do conector DO SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o conector SAP BW Open Hub na Data Factory copia dados incrementais da SAP BW. Este artigo também pode ajudá-lo a entender a configuração básica do conector.

Agora, vamos continuar a configurar cópia incremental do SAP BW Open Hub.

A cópia incremental utiliza um mecanismo de "marca de alta água" que se baseia no ID do **pedido.** Esse ID é gerado automaticamente no SAP BW Open Hub Destination pelo DTP. O seguinte diagrama mostra este fluxo de trabalho:

![Gráfico de fluxo de fluxo de trabalho de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

Na fábrica **de** dados Vamos começar a página, selecione **Criar o pipeline do modelo** para usar o modelo incorporado.

1. Procure o **SAP BW** para encontrar e selecionar a **cópia incremental do modelo SAP BW para Azure Data Lake Storage Gen2.** Este modelo copia dados em Azure Data Lake Storage Gen2. Pode utilizar um fluxo de trabalho semelhante para copiar para outros tipos de pias.

2. Na página principal do modelo, selecione ou crie as três ligações seguintes e, em seguida, selecione **Utilize este modelo** no canto inferior direito da janela.

   - **Armazenamento Azure Blob**: Nesta passagem, utilizamos o armazenamento Azure Blob para armazenar a marca de água alta, que é o *ID de pedido copiado máximo*.
   - **SAP BW Open Hub**: Esta é a fonte para copiar dados de. Consulte a cópia completa anterior para obter uma configuração detalhada.
   - **Azure Data Lake Storage Gen2**: Este é o lavatório para copiar dados para. Consulte a cópia completa anterior para obter uma configuração detalhada.

   ![Cópia incremental do modelo SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Este modelo gera um pipeline com as seguintes três atividades e torna-as acorrentadas no sucesso: *Lookup,* *Copy Data* e *Web*.

   Vá ao separador **parâmetros** do gasoduto. Vê todas as configurações que precisa de fornecer.

   ![Cópia incremental da configuração DA SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique o nome da tabela Open Hub para copiar dados de.

   - **Data_Destination_Container**: Especifique o contentor Azure Data Lake Storage Gen2 para copiar dados. Se o recipiente não existir, a atividade de cópia da Data Factory cria uma durante a execução.
  
   - **Data_Destination_Directory**: Especifique o caminho da pasta sob o contentor Azure Data Lake Storage Gen2 para copiar dados para. Se o caminho não existir, a atividade de cópia da Data Factory cria um caminho durante a execução.
  
   - **HighWatermarkBlobContainer**: Especifique o recipiente para armazenar o valor de marca de água elevada.

   - **HighWatermarkBlobDirectory**: Especifique o caminho da pasta sob o recipiente para armazenar o valor da marca de alta água.

   - **Nome HighWatermarkBlobName**: Especifique o nome da bolha para armazenar o elevado valor da marca de água, tais como `requestIdCache.txt` . No armazenamento blob, vá para o caminho correspondente de HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, como *recipiente/caminho/requestIdCache.txt*. Crie uma bolha com conteúdo 0.

      ![Blob content](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Neste modelo, usamos webActivity para chamar Azure Logic Apps para definir o valor de alta marca de água no armazenamento blob. Ou, pode usar a Base de Dados Azure SQL para armazená-la. Utilize uma atividade de procedimento armazenada para atualizar o valor.

      Primeiro tem de criar uma aplicação lógica, como mostra a seguinte imagem. Em seguida, cole no **URL HTTP POST**.

      ![Configuração de aplicativos lógicos](media/load-sap-bw-data/logic-app-config.png)

      1. Aceda ao portal do Azure. Selecione um novo serviço **de Aplicações Lógicas.** Selecione **+Blank Logic App** para ir ao **Logic Apps Designer**.

      2. Criar um gatilho de **Quando um pedido HTTP é recebido**. Especificar o organismo de pedidos HTTP da seguinte forma:

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

      3. Adicione uma **ação de blob Create.** Para **o caminho da pasta** e o nome **Blob,** utilize os mesmos valores que configuraste anteriormente em *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* e *HighWatermarkBlobName*.

      4. Selecione **Guardar**. Em seguida, copie o valor do **URL HTTP POST** para utilizar no pipeline data Factory.

4. Depois de fornecer os parâmetros do pipeline Data Factory, selecione **Debug**  >  **Finish** para invocar uma corrida para validar a configuração. Ou, **selecione Publicar** para publicar todas as alterações e, em seguida, selecione **Adicionar gatilho** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino de hub aberto SAP BW

Esta secção introduz a configuração do lado SAP BW para utilizar o conector SAP BW Open Hub na Data Factory para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configure a extração delta em SAP BW

Se necessitar de cópia histórica e cópia incremental ou apenas de cópia incremental, configuure a extração delta em SAP BW.

1. Crie o Destino Open Hub. Pode criar o OHD na Transação SAP RSA1, que cria automaticamente o processo de transformação e transferência de dados necessário. Utilize as seguintes definições:

   - **ObjectType**: Pode utilizar qualquer tipo de objeto. Aqui, usamos **o InfoCube** como exemplo.
   - **Tipo de destino**: Selecione **Tabela de bases de dados**.
   - **Chave da tabela**: Selecione **Tecla técnica**.
   - **Extração**: Selecione **Manter os dados e inserir registos na tabela**.

   ![Criar caixa de diálogo de extração delta SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Criar caixa de diálogo de extração delta2 da SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Pode aumentar o número de processos de trabalho SAP em execução paralela para o DTP:

   ![A screenshot mostra definições para processamento paralelo onde pode selecionar o número de processos paralelos para o D T P.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende o DTP em cadeias de processos.

   Um DTP delta para um cubo só funciona se as filas necessárias não tão bem forem comprimidas. Certifique-se de que a compressão do cubo BW não está a funcionar antes do DTP para a mesa Open Hub. A forma mais fácil de o fazer é integrar o DTP nas suas cadeias de processo existentes. No exemplo seguinte, o DTP (para o OHD) é inserido na cadeia de processo entre os passos *de Ajuste* (rollup agregado) e *Colapso* (compressão do cubo).

   ![Criar gráfico de fluxo de cadeia de processo SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configure a extração completa em SAP BW

Além da extração delta, pode querer uma extração completa do mesmo SAP BW InfoProvider. Isto geralmente aplica-se se quiser fazer cópia completa, mas não incremental, ou se pretende [ressync extração delta](#resync-delta-extraction).

Não se pode ter mais do que um DTP para o mesmo OHD. Então, tens de criar um OHD adicional antes da extração delta.

![Criar SAP BW OHD cheio](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para uma carga completa OHD, escolha diferentes opções do que para a extração delta:

- Em OHD: Desista a opção **de extração** para **eliminar dados e inserir registos**. Caso contrário, os dados serão extraídos muitas vezes quando repetir o DTP numa cadeia de processos BW.

- No DTP: Definir **o modo de extração** para **o conjunto**. Tem de alterar o DTP automaticamente criado de **Delta** para **Full** imediatamente após a criação do OHD, como esta imagem mostra:

   ![Criar caixa de diálogo SAP BW OHD configurada para extração "completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector BW Open Hub da Data Factory: Desligue **o último pedido .** Caso contrário, nada será extraído.

Normalmente, execute o DTP completo manualmente. Ou, pode criar uma cadeia de processos para o DTP completo. É tipicamente uma cadeia separada que é independente das suas cadeias de processo existentes. Em qualquer dos casos, *certifique-se de que o DTP está terminado antes de iniciar a extração utilizando a cópia data factory*. Caso contrário, apenas os dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar extração delta pela primeira vez

A primeira extração delta é tecnicamente uma *extração completa.* Por predefinição, o conector SAP BW Open Hub exclui o último pedido quando copia dados. Para a primeira extração delta, nenhum dado é extraído pela atividade de cópia da Data Factory até que um DTP subsequente gere dados delta na tabela com um ID de pedido separado. Há duas maneiras de evitar este cenário:

- Desligue a última opção **de pedido excluir** para a primeira extração delta. Certifique-se de que o primeiro DTP delta está terminado antes de iniciar a extração delta da primeira vez.
-  Utilize o procedimento para ressímia da extração delta, conforme descrito na secção seguinte.

### <a name="resync-delta-extraction"></a>Extração delta da Resync

Os seguintes cenários alteram os dados em cubos SAP BW, mas não são considerados pelo DTP delta:

- Supressão seletiva SAP BW (de linhas utilizando qualquer condição de filtro)
- Supressão do pedido da SAP BW (de pedidos defeituosos)

Um Destino SAP Open Hub não é um alvo de dados controlado por data-mart (em todos os pacotes de suporte SAP BW desde 2015). Assim, pode eliminar dados de um cubo sem alterar os dados no OHD. Em seguida, deve ressync os dados do cubo com data factory:

1. Executar uma extração completa na Fábrica de Dados (utilizando um DTP completo em SAP).
2. Elimine todas as linhas na tabela Open Hub para o Delta DTP.
3. Desabrote o estado do DTP delta para **o Fetched**.

Depois disso, todas as extrações delta DTPs e Data Factory subsequentes funcionam como esperado.

Para definir o estado do DTP delta para **Fetched,** pode utilizar a seguinte opção para executar manualmente o DTP delta:

*Sem Transferência de Dados; Estado da Delta na Fonte: Recolhido*

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o suporte do conector SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Conector open hub do Armazém de Negócios SAP](connector-sap-business-warehouse-open-hub.md)
