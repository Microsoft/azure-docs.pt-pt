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
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 607c3ff128c82c7baa268cf8f068232428ec5331
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733325"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Carregar dados do SAP Business Warehouse (BW) através do Azure Data Factory

Este artigo mostra-lhe um passo a passo sobre como utilizar o Data Factory _carregar dados do SAP Business Warehouse (BW) através de Hub aberto para o ger2 de armazenamento do Azure Data Lake_. Pode seguir passos semelhantes para copiar dados para si [arquivos de dados de sink suportados](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Consulte a [artigo de conector de Hub aberto do SAP BW](connector-sap-business-warehouse-open-hub.md) em copiar dados do SAP BW em geral, incluindo introdução sobre o fluxo de extração de integração e delta do Hub aberto do SAP BW.

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory (ADF):** Se não tiver uma fábrica de dados, siga a "[criar uma fábrica de dados](quickstart-create-data-factory-portal.md#create-a-data-factory)" secção para criar um. 

- **SAP BW aberto Hub destino (OHD) com o tipo de destino como "Tabela de base de dados".** Siga [configurações de SAP BW aberto Hub destino](#sap-bw-open-hub-destination-configurations) secção para criar um ou para confirmar se o seu OHD existente está corretamente configurado para ser integrado com o ADF.

- **Utilizador de SAP BW que está a ser utilizado tem de ter as seguintes permissões:**

  - Autorização para RFC e SAP BW.
  - Permissões para a "**executar**"Atividade de autorização de objeto"**S_SDSAUTH**".

- **[Hospedagem do Runtime de integração](concepts-integration-runtime.md#self-hosted-integration-runtime) com o conector de SAP .NET 3.0 são necessárias**. Abaixo estão os preparativos detalhados que precisam ser feito:

  1. Instalar e registar o IR autoalojado com a versão > = 3.13 (abrangido a instrução a seguir). 

  2. Transfira o [SAP de 64 bits .NET 3.0 de conector](https://support.sap.com/en/product/connectors/msnet.html) partir do site do SAP e instalá-lo na máquina do Runtime de integração autoalojado.  Quando instalar, na janela "passos de configuração opcionais", certifique-se de que seleciona a "**instalar Assemblies ao GAC**" opção conforme mostrado na imagem seguinte.

     ![Configurar o conector do SAP .NET](media\connector-sap-business-warehouse-open-hub\install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Cópia completa do Hub aberto do SAP BW

No portal do Azure, vá para a fábrica de dados -> selecione **criar e monitorizar** para iniciar a IU do ADF num separador à parte. 

1. Na página **Vamos começar**, selecione a página **Copiar Dados** para iniciar a ferramenta Copiar Dados. 

2. Sobre o **propriedades** página, especifique um nome para o **nome da tarefa** campo e selecione **seguinte**.

3. Sobre o **o arquivo de dados de origem** página, clique em **+ criar nova ligação** -> selecione **Hub aberto do SAP BW** da galeria do conector -> selecione **continuar**. Pode digitar "SAP" na caixa de pesquisa para filtrar os conectores.

4. Sobre o **ligação do Hub aberto BW SAP especificar** página, 

   ![Criar serviço de SAP BW aberto Hub ligado](media\load-sap-bw-data\create-sap-bw-open-hub-linked-service.png)

   1. Escolha o **ligar através do Runtime de integração**: clique na lista pendente para selecionar um runtime de integração autoalojado existente ou criar um se não tiver o ir Autoalojado configurar ainda. 

      Para criar um novo, clique em **+ novo** na lista pendente-> tipo select **autoalojado** -> especifique uma **nome** e clique em **seguinte** -> Escolha **Configuração rápida** para instalar no computador atual ou siga a **configuração Manual** passos lá.

      Conforme mencionado na [pré-requisitos](#prerequisites), certifique-se de tem também a **conector do SAP .NET 3.0** instalado na mesma máquina onde ir Autoalojado está em execução.

   2. Especifique o SAP BW **nome do servidor**, **número de sistema**, **ID de cliente** **linguagem** (se diferente de EN), **denomedeutilizador**, e **palavra-passe**.

   3. Clique em **Testar ligação** para validar as definições, em seguida, selecione **concluir**.

   4. Verá, que é criada uma nova ligação. Selecione **Seguinte**.

5. Sobre o **destinos de selecionar Hub aberto** página, procure os destinos de Hub aberto disponível no seu SAP BW e selecionar aquele que pretende copiar dados de, em seguida, clique em **seguinte**.

   ![Selecionar tabela de Hub aberto do SAP BW](media\load-sap-bw-data\select-sap-bw-open-hub-table.png)

6. Especifica o filtro se for necessário. Se o destino de Hub aberto contém apenas dados de execução individual do processo de transferência de dados (DTP) com o ID de pedido único, ou tiver a certeza de sua DTP foi concluída e desejar copiar todos os dados, desmarque a **excluir o último pedido**. Pode saber mais sobre como estas definições estão relacionados à configuração no SAP BW [configurações de SAP BW aberto Hub destino](#sap-bw-open-hub-destination-configurations) secção. Clique em **Validate** volte a verificar os dados devolvidos, em seguida, selecione **próxima**.

   ![Configurar o filtro de Hub aberto do SAP BW](media\load-sap-bw-data\configure-sap-bw-open-hub-filter.png)

7. Na **o arquivo de dados de destino** página, clique em **+ criar nova ligação**e, em seguida, selecione **Gen2 de armazenamento do Azure Data Lake**e selecione **continuar**.

8. Na **ligação de especificar o Azure Data Lake armazenamento** página, 

   ![Criar serviço de geração 2 do ADLS ligado](media\load-sap-bw-data\create-adls-gen2-linked-service.png)

   1. Selecione seu Gen2 de armazenamento do Data Lake conta com capacidade do "nome de conta de armazenamento" na lista pendente.
   2. Selecione **concluir** para criar a ligação. Em seguida, selecione **Seguinte**.

9. Na **escolher o ficheiro de saída ou a pasta** página, introduza "copyfromopenhub" como o nome da pasta de saída e selecione **próxima**.

   ![Escolha a pasta de saída](media\load-sap-bw-data\choose-output-folder.png)

10. Na **formato de ficheiro de definição** página, selecione **próxima** para utilizar as predefinições.

   ![Especificar o formato de sink](media\load-sap-bw-data\specify-sink-format.png)

11. Na **configurações** página, expanda o **as definições de desempenho**e defina **grau de paralelismo de cópia** como 5 para carregar a partir do SAP BW em paralelo. Clique em **Seguinte**.

    ![Configurar as definições de cópia](media\load-sap-bw-data\configure-copy-settings.png)

12. Na **resumo** página, reveja as definições e selecione **próxima**.

13. Na **implantação** página, selecione **Monitor** para monitorizar o pipeline.

    ![Página de implementação](media\load-sap-bw-data\deployment.png)

14. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline:

    ![Monitorização do pipeline](media\load-sap-bw-data\pipeline-monitoring.png)

15. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorização da atividade](media\load-sap-bw-data\activity-monitoring.png)

16. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação (imagem de óculos), em **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado configurações:

    ![Monitorizar os detalhes de atividade](media\load-sap-bw-data\activity-monitoring-details.png)

17. Reveja os **máximo ID do pedido** que é copiado. Voltar para a atividade de vista de monitorização, clique nas **saída** sob **ações**.

    ![Saída da atividade](media\load-sap-bw-data\activity-output.png)

    ![Detalhes de saída da atividade](media\load-sap-bw-data\activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental do Hub aberto do SAP BW

> [!TIP]

> Consulte a [fluxo de extração de delta de conector de Hub aberto do SAP BW](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para obter mais informações sobre como funciona a atividade de cópia do ADF para copiar dados incrementais de SAP BW.

Agora, vamos continuar a configurar a cópia incremental do Hub aberto do SAP BW. 

A cópia incremental está a utilizar o mecanismo de limite superior de tamanho com base no pedido de ID gerado automaticamente no destino de Hub do SAP BW aberto ao DTP. O fluxo de trabalho para esta abordagem é descrito no diagrama seguinte:

![Fluxo de trabalho de cópia incremental](media\load-sap-bw-data\incremental-copy-workflow.png)

Na IU do ADF **Vamos começar** página, selecione **criar pipeline**. 

1. Arraste três atividades - **Lookup, dados de cópia e Web** - na tela e marca-los em cadeia no sucesso. Nestas instruções, utilizamos BLOBs do Azure para armazenar o limite superior de tamanho - ID do pedido copiado máximo. Também pode utilizar a base de dados SQL para armazená-lo e utilizar a atividade de procedimento armazenado em vez de atividade Web para atualizá-lo.

   ![Pipeline da cópia incremental](media\load-sap-bw-data\incremental-copy-pipeline.png)

2. Configure a atividade de pesquisa:

   1. Na atividade de pesquisa **configurações** separador, verificação **primeira linha apenas** opção.

      ![Definições de pesquisa](media\load-sap-bw-data\lookup-settings.png)

   2. Configurar **conjunto de dados de origem** como um conjunto de dados do Blob no **caminho do ficheiro**, aponte para o blob onde pretende armazenar o ID do pedido copiado máximo como limite superior de tamanho e a manter o formato que o formato de texto.

      ![Definições do conjunto de dados de BLOBs](media\load-sap-bw-data\blob-dataset.png)

   3. O caminho correspondente do blob, crie um blob com conteúdo 0.

      ![Conteúdo do blob](media\load-sap-bw-data\blob.png)

3. Configure a atividade de cópia: 

   1. Na **origem** separador, configure **conjunto de dados de origem** como um conjunto de dados de Hub aberto do SAP BW. 

   2. Editar a **conjunto de dados de Hub aberto do SAP BW**:

      1. Na **parâmetros** separador, definir um parâmetro com o nome "requestId"
      2. Na **conexão** separador, especifique um nome de tabela de Hub aberto, manter "Excluir última ID do pedido" selecionado e defina o ID do pedido de Base para utilizar a expressão (adicionar conteúdo dinâmico) `@dataset().requestId`.

   3. Volte à atividade de cópia **origem** separador, configure o valor de "requestId" para utilizar a expressão (adicionar conteúdo dinâmico) `@{activity('<look up activity name>').output.firstRow.Prop_0}`. Altere a "procura de nome da atividade" nesta expressão em conformidade.

      ![Definições de origem de Copy](media\load-sap-bw-data\copy-source.png)

4. Configurar a atividade Web: esta atividade Web chamará aplicação lógica para armazenar o ID do pedido copiado máximo no blob.

   1. Aceda ao portal do Azure -> novo uma **aplicação lógica** com um **pedido HTTP** e um **Create blob** da seguinte forma. Utilize o mesmo arquivo de blob configurado na origem de atividade de pesquisa acima. E copie os **URL do HTTP POST** que será utilizado na atividade web.

      ![Configuração da aplicação lógica](media\load-sap-bw-data\logic-app-config.png)

   2. Volte para editar o ADF **atividade da Web** definições conforme mostrado a seguir. Configurar o corpo como expressão (adicionar conteúdo dinâmico) `{"sapOpenHubMaxRequestId":"@{activity('CopyFromSap').output.sapOpenHubMaxRequestId}"}`.

      ![Definições de atividade Web](media\load-sap-bw-data\web-activity-settings.png)

5. Em seguida, pode clicar em **depurar** para validar a configuração ou selecione **publicar tudo** publicar todas as alterações, em seguida, clique em **acionador** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino de Hub aberto do SAP BW

Esta secção apresenta a configuração necessária no lado do SAP BW, para poder utilizar o conector de Hub aberto do SAP BW no ADF para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurar a extração de delta no SAP BW

Se precisar de cópia histórica e cópia incremental ou apenas cópia incremental, configure a extração de delta no SAP BW.

1. Criar o destino de Hub aberto (OHD)

   Pode criar o OHD no RSA1 de transação do SAP. Isso cria automaticamente a transformação necessária e processo de transferência de dados (DTP). Utilize as seguintes definições:

   - Tipo de objeto pode ser qualquer um. Aqui usamos consulta do InfoCube como exemplo.
   - **Tipo de destino:** *Tabela de base de dados*
   - **Chave da tabela:** *Chave técnico*
   - **Extração:** *Manter os dados e registos de Insert em tabela*

   ![Criar a extração do SAP BW OHD delta](media\load-sap-bw-data\create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media\load-sap-bw-data\create-sap-bw-ohd-delta2.png)

   Pode aumentar o número de paralelo executar processos de trabalho do SAP para o DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data\create-sap-bw-ohd-delta3.png)

2. Agendar DTP em cadeias de processo

   Um DTP Delta para um cubo só funciona quando o necessitam linhas não comprimidas ainda. Por conseguinte, deve certificar-se de que BW cubo compressão não está em execução antes do DTP para a tabela de Hub aberto. A maneira mais fácil para que isso está a integrar esta DTP em suas cadeias de processo existente. No exemplo abaixo, DTP (para OHD) é inserido na cadeia de processo entre a etapa ajustar (Rollup de agregação) e fechar (compressão de cubo).

   ![create-sap-bw-process-chain](media\load-sap-bw-data\create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurar a extração completa no SAP BW

Além da extração de delta, pode querer ter uma extração completa da mesma InfoProvider. Isto aplica-se normalmente se desejar a para completa, cópia sem necessidade de incremental ou pretende [volte a sincronizar a extração de delta](#re-sync-delta-extraction).

Não tem de ter mais do que um DTP para o mesmo OHD. Portanto, precisa criar um OHD adicional de extração de delta.

![create-sap-bw-ohd-full](media\load-sap-bw-data\create-sap-bw-ohd-full.png)

Para uma carga completa OHD, escolha opções diferentes de extração de delta:

- Em OHD: definir a opção de "Extração" como "*eliminar dados e registos de inserir*". Caso contrário, os dados seriam extraídos muitas vezes repeti-la quando DTP numa cadeia de processo BW.

- Em DTP: definir o "Modo de extração" como "*completo*". Tem de alterar o DTP criada automaticamente de Delta para completo depois do OHD foi criado:

   ![create-sap-bw-ohd-full2](media\load-sap-bw-data\create-sap-bw-ohd-full2.png)

- No conector do ADF SAP BW aberto Hub: desativar a opção "*excluir o último pedido*". Caso contrário, nada seria extraído. 

Normalmente, executa o DTP completa manualmente. Ou pode também criar uma cadeia de processo para o DTP completa - normalmente, isto poderá ser uma cadeia de processo separado independentemente de suas cadeias de processo existente. Em ambos os casos, deve **certificar-se de que o DTP foi concluída antes de iniciar a extração utilizando a cópia do ADF**, caso contrário, serão copiados dados parciais.

### <a name="run-delta-extraction-the-first-time"></a>Executar a extração de delta pela primeira vez

A primeira extração de Delta é tecnicamente uma **extração completa**. Nota pelo conector do padrão Open Hub do ADF SAP BW exclui o último pedido ao copiar os dados. No caso de extração de delta pela primeira vez, no ADF cópia activtiy, não existem dados serão extraídos até que haja subsequentes DTP gera dados delta da tabela com o ID do pedido separada. Embora, existem duas maneiras possíveis para evitar este cenário:

1. Desativar a opção "Excluir último pedido" para o primeiro Delta extração In neste caso, que terá de certificar-se de que o primeiro DTP Delta foi concluída antes de iniciar a extração de Delta pela primeira vez
2. Utilize o procedimento para voltar a sincronizar a extração de Delta, conforme descrito abaixo.

### <a name="re-sync-delta-extraction"></a>Volte a sincronizar a extração de delta

Existem alguns cenários que altera os dados nos cubos do SAP BW, mas não são considerados pelo DTP Delta:

- Eliminação seletiva do SAP BW (de linhas utilizando qualquer condição de filtro)
- Eliminação de pedido do SAP BW (de pedido com falhas)

Um destino de Hub aberto do SAP não é um destino de dados de data mart controlado (em todos os SAP BW pacotes de suporte desde o ano de 2015). Por conseguinte, é possível eliminar dados de um cubo sem alterar os dados no OHD. Neste caso, deve sincronizar novamente os dados do cubo com os dados no ADF, efetuando os seguintes passos:

1. Executar uma extração completa no ADF (ao utilizar um DTP completo no SAP)
2. Eliminar todas as linhas da tabela de Hub aberto para o DTP Delta
3. Definir o estado de DTP o Delta como obtidas

Depois disso, todos os subsequentes Delta DTPs e Extrações de Delta do ADF funcionam conforme esperado.

Pode definir o estado de DTP o Delta para obtidas ao executar o DTP Delta manualmente usando a seguinte opção: "*Nenhuma transferência de dados; Estado de delta na origem: Obter*".

## <a name="next-steps"></a>Passos Seguintes

Avance para o seguinte artigo para saber mais sobre o suporte do conector do Hub aberto do SAP BW: 

> [!div class="nextstepaction"]
>[Conector do Hub de Open Business Warehouse SAP](connector-sap-business-warehouse-open-hub.md)
