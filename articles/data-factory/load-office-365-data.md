---
title: Carregar dados do Office 365 usando Azure Data Factory
description: Usar Azure Data Factory para copiar dados do Office 365
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
ms.openlocfilehash: 0c938caef81063409d3c8d6632cd7e8df99f1ea1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672625"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregar dados do Office 365 usando Azure Data Factory

Este artigo mostra como usar o Data Factory _carregar dados do Office 365 para o armazenamento de BLOBs do Azure_. Você pode seguir etapas semelhantes para copiar dados para Azure Data Lake Gen1 ou Gen2. Consulte o [artigo conector do office 365](connector-office-365.md) sobre como copiar dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **Analytics** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os campos mostrados na imagem a seguir:
      
   ![Página Nova fábrica de dados](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para sua data Factory do Azure. Se você receber o erro "o nome do data Factory *LoadFromOffice365Demo* não está disponível", insira um nome diferente para o data Factory. Por exemplo, você pode usar o nome _**Your**_ name**LoadFromOffice365Demo**. Tente criar o data factory novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione sua assinatura do Azure na qual criar o data Factory. 
    * **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione a opção **criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **v2**.
    * **Local**: selecione o local para o data Factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados usados pelo data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, armazenamento do Azure, banco de dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o data factory. Você verá o home page de **Data Factory** conforme mostrado na imagem a seguir:
   
   ![Home page da fábrica de dados](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o bloco **criar & monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Vamos começar", selecione **criar pipeline**.
 
    ![Criar pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **guia Geral** do pipeline, insira "CopyPipeline" para o **nome** do pipeline.

3. Na caixa de ferramentas atividades > mover & categoria transformar > arraste e solte a **atividade de cópia** da caixa de ferramentas para a superfície do designer de pipeline. Especifique "CopyFromOffice365ToBlob" como o nome da atividade.

### <a name="configure-source"></a>Configurar origem

1. Vá para a **guia origem**do > de pipeline, clique em **+ novo** para criar um conjunto de um DataSet de origem. 

2. Na janela novo conjunto de novos DataSet, selecione **Office 365**e, em seguida, selecione **continuar**.
 
3. Agora você está na guia de configuração da atividade de cópia. Clique no botão **Editar** ao lado do conjunto de dados do Office 365 para continuar a configuração do dado.

    ![Configurar o Office 365 DataSet geral](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Você verá uma nova guia aberta para o Office 365 DataSet. Na **guia Geral** na parte inferior da janela Propriedades, digite "SourceOffice365Dataset" para nome.
 
5. Vá para a **guia conexão** da janela Propriedades. Ao lado da caixa de texto serviço vinculado, clique em **+ novo**.

6. Na janela novo serviço vinculado, digite "Office365LinkedService" como nome, insira a ID da entidade de serviço e a chave da entidade de serviço, teste a conexão e selecione **criar** para implantar o serviço vinculado.

    ![Novo serviço vinculado do Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Depois de criar o serviço ligado, volta às definições do conjunto de dados. Ao lado de **tabela**, escolha a seta para baixo para expandir a lista de conjuntos de datadisponíveis do Office 365 e escolha "BasicDataSet_v0. Message_v0 "na lista suspensa:

    ![Configurar a tabela de conjunto de configurações do Office 365](./media/load-office-365-data/edit-dataset.png)

8. Agora, volte para a **guia origem** do **pipeline** > para continuar Configurando propriedades adicionais para extração de dados do Office 365.  O escopo do usuário e o filtro de escopo do usuário são predicados opcionais que você pode definir para restringir os dados que deseja extrair do Office 365. Consulte a seção [Propriedades do conjunto de banco de 365 do Office](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) para saber como definir essas configurações.

9. É necessário escolher um dos filtros de data e fornecer os valores de hora de início e hora de término.

10. Clique na guia **importar esquema** para importar o esquema para o conjunto de mensagens de mensagem.

    ![Configurar o esquema de conjunto de esquemas do Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurar sink

1. Vá para a **guia coletor**de > do pipeline e selecione **+ novo** para criar um conjunto de coletas.
 
2. Na janela novo conjunto de notícias, observe que somente os destinos com suporte são selecionados ao copiar do Office 365. Selecione **armazenamento de BLOBs do Azure**, selecione formato binário e, em seguida, selecione **continuar**.  Neste tutorial, você copia dados do Office 365 em um armazenamento de BLOBs do Azure.

3. Clique no botão **Editar** ao lado do conjunto de dados do armazenamento de BLOBs do Azure para continuar a configuração do dado.

4. Na **guia Geral** do janela Propriedades, em nome, insira "OutputBlobDataset".

5. Vá para a **guia conexão** da janela Propriedades. Ao lado da caixa de texto serviço vinculado, selecione **+ novo**.

6. Na janela novo serviço vinculado, digite "AzureStorageLinkedService" como nome, selecione "entidade de serviço" na lista suspensa de métodos de autenticação, preencha o ponto de extremidade de serviço, locatário, ID da entidade de serviço e chave da entidade de serviço e, em seguida, selecione salvar em implante o serviço vinculado.  Consulte [aqui](connector-azure-blob-storage.md#service-principal-authentication) para saber como configurar a autenticação de entidade de serviço para o armazenamento de BLOBs do Azure.

    ![Novo serviço vinculado de BLOB](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Validar o pipeline

Para validar o pipeline, selecione **Validar** na barra de ferramentas.

Você também pode ver o código JSON associado ao pipeline clicando em código no canto superior direito.

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Na barra de ferramentas superior, selecione **publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente

Selecione **Adicionar gatilho** na barra de ferramentas e, em seguida, selecione **disparar agora**. Na página execução do pipeline, selecione **concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Vá para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar as ligações na coluna **Ações** para ver os detalhes das atividades e para voltar a executar o pipeline.

![Monitorizar o pipeline](./media/load-office-365-data/pipeline-status.png) 

Para ver as execuções de atividade associadas à execução do pipeline, selecione o link **Exibir execuções de atividade** na coluna ações. Neste exemplo, há apenas uma atividade, pelo que só vai ver uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **detalhes** (ícone de óculos) na coluna ações.

![Monitorar atividade](./media/load-office-365-data/activity-status.png) 

Se esta for a primeira vez que você está solicitando dados para esse contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá a atividade de cópia status como **em andamento**, e somente quando você clicar no link "detalhes" em ações, verá o status como **RequesetingConsent**.  Um membro do grupo Aprovador de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que a extração de dados possa continuar.

_Status como solicitando consentimento:_ detalhes de execução de atividade de
![-solicitar consentimento](./media/load-office-365-data/activity-details-request-consent.png) 

_Status como Extraindo dados:_

![Detalhes da execução da atividade – extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Depois que o consentimento for fornecido, a extração de dados continuará e, após algum tempo, a execução do pipeline será mostrada como bem-sucedida.

![Pipeline de monitor-bem-sucedido](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora, acesse o armazenamento de BLOBs do Azure de destino e verifique se os dados do Office 365 foram extraídos em formato binário.

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber mais sobre o suporte do Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)