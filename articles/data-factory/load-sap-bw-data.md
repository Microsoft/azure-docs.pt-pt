---
title: Dados de carga do SAP Business Warehouse
description: Utilize a Azure Data Factory para copiar dados do SAP Business Warehouse (BW)
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 971871c28bd1b38b134c04b0334fbe99d1d655c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440167"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse utilizando a Azure Data Factory

Este artigo mostra como usar a Azure Data Factory para copiar dados do SAP Business Warehouse (BW) via Open Hub para azure Data Lake Storage Gen2. Pode utilizar um processo semelhante para copiar dados para outras lojas de [dados de sink suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!TIP]
> Para obter informações gerais sobre a cópia de dados da SAP BW, incluindo a integração do SAP BW Open Hub e o fluxo de extração delta, consulte [os dados copiados do SAP Business Warehouse via Open Hub utilizando](connector-sap-business-warehouse-open-hub.md)a Azure Data Factory .

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory**: Se não tiver um, siga os passos para [criar uma fábrica](quickstart-create-data-factory-portal.md#create-a-data-factory)de dados .

- **SAP BW Open Hub Destination (OHD) com o tipo**de destino "Tabela de Bases de Dados" : Para criar um OHD ou para verificar se o seu OHD está configurado corretamente para integração na Fábrica de Dados, consulte a secção de configurações de destino [sap BW Open Hub](#sap-bw-open-hub-destination-configurations) Destination deste artigo.

- **O utilizador SAP BW necessita das seguintes permissões:**

  - Autorização para chamadas de função remota (RFC) e SAP BW.
  - Permissões à atividade "Executar" do objeto de autorização **S_SDSAUTH.**

- Um tempo de execução de ** [integração auto-hospedado (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) com conector SAP .NET 3.0**. Siga estes passos de configuração:

  1. Instale e registe o tempo de execução de integração auto-hospedado, versão 3.13 ou posterior. (Este processo é descrito mais tarde neste artigo.)

  2. Descarregue o [Conector SAP de 64 bits para microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) a partir do site da SAP e instale-o no mesmo computador que o IR auto-hospedado. Durante a instalação, certifique-se de que seleciona Conjuntos de **Instalação para GAC** na caixa de diálogo **de passos de configuração opcional,** como mostra a seguinte imagem:

     ![Configurar caixa de diálogo do conector SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Faça uma cópia completa do SAP BW Open Hub

No portal do Azure, aceda à sua fábrica de dados. Selecione **Author & Monitor** para abrir a UI da Fábrica de Dados num separador.

1. Na página **Let's start,** selecione **Copy Data** para abrir a ferramenta Copy Data.

2. Na página **Propriedades,** especifique um **nome de tarefa**, e, em seguida, selecione **Seguinte**.

3. Na página da loja de **dados Source,** selecione **+Criar uma nova ligação**. Selecione **SAP BW Open Hub** da galeria do conector e, em seguida, selecione **Continuar**. Para filtrar os conectores, pode escrever **SAP** na caixa de pesquisa.

4. Na página de **ligação 'Especte' SAP BW Open Hub,** siga estes passos para criar uma nova ligação.

   ![Crie página de serviço ligada ao SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. A partir do Connect através da lista de tempo de execução de **integração,** selecione um IR auto-hospedado existente. Ou escolha criar um se ainda não tiver um.

      Para criar um novo IR auto-hospedado, **selecione +Novo,** e, em seguida, selecione **Self-hosted**. Introduza um **Nome**e, em seguida, selecione **Seguinte**. **Selecione configuração Express** para instalar no computador atual ou siga os passos de **configuração manual** que são fornecidos.

      Como mencionado nos [Pré-requisitos,](#prerequisites)certifique-se de que tem o Conector SAP para microsoft .NET 3.0 instalado no mesmo computador onde o IR auto-hospedado está em execução.

   2. Preencha o **nome**do Servidor SAP BW, **número de sistema,** **ID do cliente,** **Idioma** (se **não EN),** nome do **utilizador,** e **palavra-passe**.

   3. Selecione a **ligação de teste** para validar as definições e, em seguida, selecione **Terminar**.

   4. Uma nova ligação é criada. Selecione **Next**.

5. Na página **Select Open Hub Destinations,** navegue nos Destinos Open Hub que estão disponíveis no seu SAP BW. Selecione o OHD para copiar dados e, em seguida, selecione **Next**.

   ![Selecione tabela de destino sap BW Open Hub](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se precisar de um. Se o seu OHD apenas contiver dados de um único processo de transferência de dados (DTP) com um único ID de pedido, ou se tiver a certeza de que o seu DTP está terminado e pretender copiar os dados, limpe a caixa de verificação **De Exclusão última solicitação.**

   Saiba mais sobre estas configurações na secção de configurações do [SAP BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) deste artigo. Selecione **Validar** para verificar duas vezes quais os dados que serão devolvidos. Em seguida, selecione **Seguinte**.

   ![Configure filtro Open Hub SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na página da loja de **dados Destination,** selecione **+Create new connection** > **Azure Data Lake Storage Gen2** > **Continue**.

8. Na página de ligação de armazenamento de dados **do Lago De dados Do Especte,** siga estes passos para criar uma ligação.

   ![Criar uma página de serviço ligada a ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione a sua conta de Armazenamento gen2 do Data Lake na lista de lançamentos de **nome.**
   2. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.

9. Na página Escolha o ficheiro de saída ou a página **de pasta,** introduza **o copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Seguinte**.

   ![Escolha a página da pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Na página de definição do **formato 'Ficheiro',** selecione **Next** para utilizar as definições predefinidas.

    ![Especificar página de formato de pia](media/load-sap-bw-data/specify-sink-format.png)

11. Na página **Definições,** expanda **as definições de Desempenho**. Introduza um valor para **o grau de paralelismo de cópia,** como 5 para carregar a partir de SAP BW em paralelo. Em seguida, selecione **Seguinte**.

    ![Configurar as definições de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. Na página **Resumo,** reveja as definições. Em seguida, selecione **Seguinte**.

13. Na página **de Implantação,** selecione **Monitor** para monitorizar o gasoduto.

    ![Página de implementação](media/load-sap-bw-data/deployment.png)

14. Note que o separador **Monitor** no lado esquerdo da página é automaticamente selecionado. A coluna **Ações** inclui links para visualizar detalhes de execução de atividade e para reexecutar o gasoduto.

    ![Vista de monitorização do gasoduto](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para visualizar as operações associadas ao pipeline run, selecione **View Activity Runs** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista de gasodutos, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Tela de monitorização de atividade](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Details,** que é um ícone de óculos abaixo **de Ações** na vista de monitorização da atividade. Os detalhes disponíveis incluem o volume de dados copiado da fonte para o lavatório, a entrada de dados, passos de execução e duração, e configurações utilizadas.

    ![Detalhes de monitorização da atividade](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para ver o **ID de pedido máximo,** volte à vista de monitorização da atividade e selecione **Output** em **Ações**.

    ![Ecrã de saída de atividade](media/load-sap-bw-data/activity-output.png)

    ![Visão de detalhes de saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental do SAP BW Open Hub

> [!TIP]
> Consulte o fluxo de extração delta do [conector SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o conector SAP BW Open Hub em Data Factory copia dados incrementais da SAP BW. Este artigo também pode ajudá-lo a entender a configuração básica do conector.

Agora, vamos continuar a configurar cópia incremental do SAP BW Open Hub.

A cópia incremental utiliza um mecanismo de "marca de água elevada" baseado no ID do **pedido**. Este ID é automaticamente gerado no SAP BW Open Hub Destination pelo DTP. O diagrama seguinte mostra este fluxo de trabalho:

![Gráfico incremental de fluxo de fluxo de fluxo de cópia](media/load-sap-bw-data/incremental-copy-workflow.png)

Na fábrica de dados **Vamos começar** a página, selecione criar o pipeline **do modelo** para usar o modelo incorporado.

1. Procure **sap BW** para encontrar e selecione a cópia Incremental de SAP BW para o modelo De armazenamento de **lagos De dados Azure Gen2.** Este modelo copia dados em Azure Data Lake Storage Gen2. Pode utilizar um fluxo de trabalho semelhante para copiar para outros tipos de pia.

2. Na página principal do modelo, selecione ou crie as seguintes três ligações e, em seguida, selecione **Use este modelo** no canto inferior direito da janela.

   - **Armazenamento Azure Blob**: Neste passeio, utilizamos o armazenamento Azure Blob para armazenar a marca de água elevada, que é o ID de *pedido copiado maxcopiado*.
   - **SAP BW Open Hub**: Esta é a fonte para copiar dados de. Consulte o walkthrough de cópia completa anterior para uma configuração detalhada.
   - **Azure Data Lake Storage Gen2**: Este é o lavatório para copiar dados para. Consulte o walkthrough de cópia completa anterior para uma configuração detalhada.

   ![Cópia incremental do modelo SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Este modelo gera um pipeline com as seguintes três atividades e torna-as acorrentadas no sucesso: *Lookup,* *Copy Data*, e *Web*.

   Vá ao separador **de parâmetros** do oleoduto. Você vê todas as configurações que precisa fornecer.

   ![Cópia incremental da configuração SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique o nome da tabela Open Hub para copiar dados.

   - **Data_Destination_Container:** Especifique o destino do contentor Azure Data Lake Storage Gen2 para copiar dados. Se o recipiente não existir, a atividade de cópia da Fábrica de Dados cria uma durante a execução.
  
   - **Data_Destination_Directory:** Especifique a trajetória da pasta sob o recipiente De armazenamento de lagos Azure Para copiar dados. Se o caminho não existir, a atividade de cópia da Fábrica de Dados cria um caminho durante a execução.
  
   - **Recipiente HighWatermarkBlobRecipiente**: Especifique o recipiente para armazenar o valor de marca de água.

   - **HighWatermarkBlobDirectory**: Especifique o caminho da pasta sob o recipiente para armazenar o valor de marca de água elevada.

   - **Nome HighWatermarkBlobName**: Especifique o nome blob `requestIdCache.txt`para armazenar o alto valor da marca de água, tais como . No armazenamento blob, vá para o caminho correspondente de HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, como *recipiente/caminho/requestIdCache.txt*. Crie uma bolha com conteúdo 0.

      ![Blob content](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Neste modelo, usamos WebActivity para ligar para as Aplicações Lógicas Azure para definir o valor de alta marca de água no armazenamento blob. Ou pode usar a Base de Dados Azure SQL para armazená-la. Utilize uma atividade de procedimento armazenada para atualizar o valor.

      Primeiro é preciso criar uma aplicação lógica, como mostra a imagem seguinte. Em seguida, colar no **URL HTTP POST**.

      ![Configuração de Aplicativo lógico](media/load-sap-bw-data/logic-app-config.png)

      1. Aceda ao portal do Azure. Selecione um novo serviço **de Aplicações Lógicas.** Selecione **+Blank Logic App** para ir ao **Logic Apps Designer**.

      2. Crie um gatilho de **Quando um pedido HTTP é recebido**. Especifique o organismo de pedido http da seguinte forma:

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

      3. Adicione uma ação **de blob Criar.** Para o **caminho da pasta** e o nome **Blob,** utilize os mesmos valores que configurado anteriormente em *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* e *HighWatermarkBlobName*.

      4. Selecione **Guardar**. Em seguida, copie o valor do **URL HTTP POST** para utilizar no pipeline Data Factory.

4. Depois de fornecer os parâmetros do gasoduto Data Factory, selecione **Debug** > **Finish** para invocar uma corrida para validar a configuração. Ou, selecione **Publicar** para publicar todas as alterações e, em seguida, selecione **Adicionar gatilho** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de Destino SAP BW Open Hub

Esta secção introduz a configuração do lado SAP BW para utilizar o conector SAP BW Open Hub na Data Factory para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configure a extração delta em SAP BW

Se precisar tanto de cópia histórica como de cópia incremental ou apenas de cópia incremental, configure a extração delta em SAP BW.

1. Crie o Destino Open Hub. Pode criar o OHD em SAP Transaction RSA1, que cria automaticamente o processo de transformação e transferência de dados necessário. Utilize as seguintes definições:

   - **Tipo de objeto**: Pode utilizar qualquer tipo de objeto. Aqui, usamos o **InfoCube** como exemplo.
   - **Tipo de destino**: Selecionar **Tabela de Bases de Dados**.
   - **Chave da tabela**: Selecione **Chave Técnica**.
   - **Extração**: Selecione **manter os dados e inserir registos na tabela**.

   ![Crie a caixa de diálogo de extração delta SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Crie a caixa de diálogo de extração SAP BW OHD delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Pode aumentar o número de processos de trabalho paralelos em execução SAP para o DTP:

   ![criar-seiva-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende o DTP em cadeias de processos.

   Um DTP delta para um cubo só funciona se as linhas necessárias não forem comprimidos. Certifique-se de que a compressão do cubo BW não está a funcionar antes do DTP para a mesa Open Hub. A forma mais fácil de o fazer é integrar o DTP nas suas cadeias de processos existentes. No exemplo seguinte, o DTP (para o OHD) é inserido na cadeia de processos entre os passos *Ajustar* (rollup agregado) e *Collapse* (compressão do cubo).

   ![Criar gráfico de fluxo de cadeia de processos SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configure a extração completa em SAP BW

Além da extração delta, pode querer uma extração completa do mesmo SAP BW InfoProvider. Isto normalmente aplica-se se quiser fazer cópia completa, mas não incremental, ou se pretende [resincronizar](#resync-delta-extraction)a extração delta .

Não pode saqueado mais do que um DTP para o mesmo OHD. Então, você deve criar um OHD adicional antes da extração delta.

![Criar SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para um OHD de carga completa, escolha diferentes opções que para a extração delta:

- Em OHD: Desdefinir a opção **de extração** para **eliminar dados e inserir registos**. Caso contrário, os dados serão extraídos muitas vezes quando repetir o DTP numa cadeia de processos BW.

- No DTP: Ajuste o **modo de extração** para **o modo completo**. Tem de alterar o DTP criado automaticamente de **Delta** para **Full** imediatamente após a criação do OHD, como esta imagem mostra:

   ![Crie caixa de diálogo SAP BW OHD configurada para extração "completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector BW Open Hub da Fábrica de Dados: Desligue **excluir o último pedido**. Caso contrário, nada será extraído.

Normalmente executa o DTP completo manualmente. Ou pode criar uma cadeia de processos para todo o DTP. É tipicamente uma cadeia separada que é independente das suas cadeias de processos existentes. Em qualquer dos casos, *certifique-se de que o DTP está terminado antes*de iniciar a extração utilizando cópia da Fábrica de Dados . Caso contrário, apenas serão copiados dados parciais.

### <a name="run-delta-extraction-the-first-time"></a>Executar extração delta pela primeira vez

A primeira extração delta é tecnicamente uma *extração completa.* Por predefinição, o conector SAP BW Open Hub exclui o último pedido quando copia dados. Para a primeira extração delta, nenhum dado é extraído pela atividade de cópia da Data Factory até que um DTP subsequente gere dados delta na tabela com um ID de pedido separado. Há duas maneiras de evitar este cenário:

- Desligue a última opção de **pedido de exclusão** para a primeira extração delta. Certifique-se de que o primeiro DTP delta está terminado antes de iniciar a extração delta da primeira vez.
-  Utilize o procedimento para ressincronizar a extração delta, conforme descrito na secção seguinte.

### <a name="resync-delta-extraction"></a>Resincronização da extração de delta

Os seguintes cenários alteram os dados em cubos SAP BW, mas não são considerados pelo DTP delta:

- SAP BW supressão seletiva (de linhas utilizando qualquer condição de filtro)
- SAP BW solicitam supressão (de pedidos defeituosos)

Um Destino SAP Open Hub não é um alvo de dados controlado por data-mart (em todos os pacotes de suporte SAP BW desde 2015). Assim, pode eliminar dados de um cubo sem alterar os dados no OHD. Deve então resincronizar os dados do cubo com data factory:

1. Faça uma extração completa na Fábrica de Dados (utilizando um DTP completo em SAP).
2. Elimine todas as linhas na mesa Open Hub para o DTP delta.
3. Desloque o estado do DTP delta para **Fetched**.

Depois disso, todos os DTPs delta subsequentes e extrações delta da Fábrica de Dados funcionam como esperado.

Para definir o estado do DTP delta para **Fetched,** pode utilizar a seguinte opção para executar manualmente o DTP delta:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o suporte do conector SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Conector Open Hub do Armazém De Negócios SAP](connector-sap-business-warehouse-open-hub.md)
