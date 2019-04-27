---
title: Carregar dados do Office 365 com o Azure Data Factory | Documentos da Microsoft
description: Utilizar o Azure Data Factory para copiar dados do Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60548294"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregar dados do Office 365 com o Azure Data Factory

Este artigo mostra-lhe como utilizar o Data Factory _carregar dados do Office 365 para o armazenamento de Blobs do Azure_. Pode seguir passos semelhantes para copiar dados para o Azure Data Lake Gen1 ou de geração 2. Consulte a [artigo de conector do Office 365](connector-office-365.md) sobre a cópia de dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte:
      
   ![Página Nova fábrica de dados](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadFromOffice365Demo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, poderia usar o nome  _**yourname**_**LoadFromOffice365Demo**. Tente criar a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar noutras localizações e regiões. Esses arquivos de dados incluem o Azure Data Lake Store, armazenamento do Azure, base de dados do Azure SQL e assim por diante.

3. Selecione **Criar**.
4. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte:
   
   ![Home page da fábrica de dados](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Vamos começar", selecione **criar pipeline**.
 
    ![Criar pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **guia General** do pipeline, introduza "CopyPipeline" para **nome** do pipeline.

3. Na caixa de ferramenta de atividades > mover e transformar categoria > arrastar e soltar os **atividade de cópia** da caixa de ferramenta para a superfície de desenho do pipeline. Especifica "CopyFromOffice365ToBlob" como nome da atividade.

### <a name="configure-source"></a>Configurar origem

1. Vá para o pipeline > **separador origem**, clique em **+ novo** para criar um conjunto de dados de origem. 

2. Na janela novo conjunto de dados, selecione **Office 365**e, em seguida, selecione **concluir**.

    ![Novo conjunto de dados do Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Verá um novo separador aberto para o conjunto de dados do Office 365. Sobre o **separador Geral** na parte inferior da janela Propriedades, introduza "SourceOffice365Dataset" para o nome.

    ![Conjunto de dados de configuração Office 365 geral](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Vá para o **separador ligação** da janela Propriedades. Junto à caixa de texto do serviço de ligado, clique em **+ novo**.
 
    ![Ligação do conjunto de dados de configuração Office 365](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Na janela novo serviço ligado, introduza "Office365LinkedService" como nome, introduza o ID de principal de serviço e a chave principal do serviço, em seguida, selecione guardar para implementar o serviço ligado.

    ![Novo serviço ligado do Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Depois de criar o serviço ligado, volta às definições do conjunto de dados. Ao lado de "Tabela", selecione a seta para baixo para expandir a lista de conjuntos de dados do Office 365 disponíveis e escolha "BasicDataSet_v0. Contact_v0 "na lista pendente:

    ![Tabela de conjunto de dados de configuração Office 365](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Vá para o **separador de esquema** da janela de propriedades e selecione **importar esquema**.  Tenha em atenção que os valores de exemplo e esquema para o conjunto de dados de contacto é apresentado.

    ![Esquema de conjunto de dados de configuração Office 365](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Agora, voltar para o pipeline > separador de origem, confirme que SourceBlobDataset está selecionado.
 
### <a name="configure-sink"></a>Configurar sink

1. Vá para o pipeline > **separador Sink**e selecione **+ novo** para criar um conjunto de dados de sink.
 
2. Na janela do novo conjunto de dados, tenha em atenção que apenas o destino suportado estão selecionadas ao copiar a partir do Office 365. Selecione **armazenamento de Blobs do Azure**e, em seguida, selecione **concluir**.  Neste tutorial, deverá copiar dados do Office 365 para um armazenamento de Blobs do Azure.

    ![Novo conjunto de dados do Blob](./media/load-office-365-data/new-blob-dataset.png)

4. Sobre o **separador Geral** da janela de propriedades, no nome, introduza "OutputBlobDataset".

5. Vá para o **separador ligação** da janela Propriedades. Junto à caixa de texto do serviço de ligado, selecione **+ novo**.

    ![Ligação do conjunto de dados de Blob de configuração](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Na janela novo serviço ligado, introduza "AzureStorageLinkedService" como nome, selecione "Principal de serviço" na lista pendente de métodos de autenticação, preencha o ponto de extremidade do serviço, ID de principal de serviço de inquilino e chave de principal de serviço, em seguida, selecione guardar Implemente o serviço ligado.  Consultar [aqui](connector-azure-blob-storage.md#service-principal-authentication) para saber como configurar a autenticação do principal de serviço para o armazenamento de Blobs do Azure.

    ![Novo serviço ligado do Blob](./media/load-office-365-data/new-blob-linked-service.png)

7. Depois de criar o serviço ligado, volta às definições do conjunto de dados. Junto ao caminho do ficheiro, selecione **procurar** para escolher onde os dados do Office 365 serão extraídos para a pasta de saída.  Em "Ficheiro de definições do formato", junto ao formato de ficheiro, selecione "**formato JSON**" e ao lado de padrão de ficheiro, escolha "**conjunto de objetos**".

    ![Caminho do Blob de configuração de conjunto de dados e de formato](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Volte ao pipeline > separador Sink, confirme que OutputBlobDataset está selecionado.

## <a name="validate-the-pipeline"></a>Validar o pipeline

Para validar o pipeline, selecione **Validar** na barra de ferramentas.

Também pode ver o código JSON associado com o pipeline ao clicar em código no canto superior direito.

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Na barra de ferramentas superior, selecione **publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente

Selecione **Acionar** na barra de ferramentas e, em seguida, selecione **Acionar Agora**. Na página de execução de Pipeline, selecione **concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Vá para o **separador Monitor** à esquerda. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar as ligações na **coluna ações** para ver os detalhes de atividade e voltar a executar o pipeline.

![Monitorizar o pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Para ver as execuções de atividade associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na coluna ações. Neste exemplo, há apenas uma atividade, pelo que só vai ver uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o **ligação de detalhes (ícone de óculos)** na coluna ações.

![Monitorizar a atividade](./media/load-office-365-data/activity-monitoring.png) 

Se esta for a primeira vez que está a solicitar dados para este contexto (uma combinação de quais dados tabela está a ser acesso, o destino conta é os dados a ser carregados para, e que identidade de utilizador está a tornar os dados de pedido de acesso), verá que a atividade de cópia Estado como "**em curso**", e apenas quando clicar na ligação de "Detalhes" em ações irão ver o estado como "**RequesetingConsent**".  Um membro do grupo de aprovador de acesso de dados tem de aprovar o pedido de Privileged Access Management antes de pode continuar a extração de dados.

_Estado como pedir consentimento:_
![detalhes de execução de atividade - pedido de consentimento](./media/load-office-365-data/activity-details-request-consent.png) 

_Estado como a extração de dados:_

![Detalhes da execução de atividade - extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Depois do consentimento for fornecido, extração de dados irá continuar e, após algum tempo, a execução do pipeline mostrará como concluído.

![Monitorizar o pipeline - foi concluída com êxito](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora, vá para o armazenamento de Blobs do Azure de destino e certifique-se de que foi extraídos dados do Office 365 no formato JSON.

## <a name="next-steps"></a>Passos Seguintes

Avance para o seguinte artigo para saber mais sobre o suporte do Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)