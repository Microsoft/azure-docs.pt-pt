---
title: Carregar dados do Office 365 utilizando a Azure Data Factory
description: Utilize a Fábrica de Dados Azure para copiar dados do Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 170716804a5bc3133e070ee67f2aac71acad7b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435567"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregar dados do Office 365 utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo mostra-lhe como utilizar os dados de carga da Data Factory _do Office 365 para o armazenamento Azure Blob_. Pode seguir passos semelhantes para copiar dados para Azure Data Lake Gen1 ou Gen2. Consulte o [artigo do conector do Office 365](connector-office-365.md) sobre a cópia dos dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, **selecione Criar uma**Fábrica de  >  Dados de**Análise**  >  **de**Recursos: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na nova página de **fábrica de dados,** forneça valores para os campos que são mostrados na seguinte imagem:
      
   ![Página Nova fábrica de dados](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "Data factory name *LoadFromOffice365Demo* não está disponível", insira um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**do seu nome**_**LoadFromOffice365Demo**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure na qual criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **V2**.
    * **Localização**: Selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Veja a página inicial da **Data Factory** como mostrado na seguinte imagem:
   
   ![Home page da fábrica de dados](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Vamos começar", selecione **Criar o pipeline**.
 
    ![Criar pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. No **separador Geral** do gasoduto, introduza "CopyPipeline" para **o nome** do gasoduto.

3. Na caixa de ferramentas Atividades > a categoria Move & Transform > arrastar e largar a **atividade Copy** da caixa de ferramentas para a superfície do designer de gasodutos. Especifique "CopyFromOffice365ToBlob" como nome de atividade.

### <a name="configure-source"></a>Configurar origem

1. Vá ao pipeline > **Separador Fonte,** clique **+ Novo** para criar um conjunto de dados de origem. 

2. Na janela New Dataset, selecione **Office 365**e, em seguida, selecione **Continue**.
 
3. Está agora no separador de configuração da atividade da cópia. Clique no botão **Editar** ao lado do conjunto de dados do Office 365 para continuar a configuração de dados.

    ![Config Office 365 dataset geral](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Vê um novo separador aberto para o conjunto de dados do Office 365. No **separador Geral** na parte inferior da janela Propriedades, introduza "SourceOffice365Dataset" para nome.
 
5. Aceda ao **separador 'Ligação'** da janela Propriedades. Junto à caixa de texto de serviço Linked, clique **+ Novo**.

6. Na janela New Linked Service, insira "Office365LinkedService" como nome, insira o ID principal de serviço e a chave principal do serviço, em seguida, teste a ligação e selecione **Criar** para implementar o serviço ligado.

    ![Serviço ligado ao Novo Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Depois de criar o serviço ligado, volta às definições do conjunto de dados. Ao lado **do Quadro**, escolha a seta para baixo para expandir a lista de conjuntos de dados disponíveis do Office 365 e escolha "BasicDataSet_v0. Message_v0" da lista de desistências:

    ![Tabela de conjuntos de dados config Office 365](./media/load-office-365-data/edit-dataset.png)

8. Volte agora ao **separador de origem**do gasoduto  >  **Source tab** para continuar a configurar propriedades adicionais para a extração de dados do Office 365.  O âmbito do utilizador e o filtro de âmbito do utilizador são predicados opcionais que pode definir para restringir os dados que pretende extrair do Office 365. Consulte a secção [de propriedades do conjunto de dados do Office 365](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) para saber como configura estas definições.

9. É-lhe exigido que escolha um dos filtros de data e forneça os valores de início e de tempo de fim.

10. Clique no **separador 'Importação' para** importar o esquema para conjunto de dados de mensagens.

    ![Config Office 365 dataset schema](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurar sink

1. Vá ao pipeline > **aba de afundar**, e selecione **+ Novo** para criar um conjunto de dados de pia.
 
2. Na janela New Dataset, note que apenas os destinos suportados são selecionados ao copiar do Office 365. Selecione **Azure Blob Storage**, selecione o formato binário e, em seguida, selecione **Continue**.  Neste tutorial, você copia os dados do Office 365 num Azure Blob Storage.

3. Clique no botão **Editar** ao lado do conjunto de dados de armazenamento Azure Blob para continuar a configuração de dados.

4. No **separador Geral** da janela Propriedades, em Nome, introduza "OutputBlobDataset".

5. Aceda ao **separador 'Ligação'** da janela Propriedades. Junto à caixa de texto Serviço ligado, selecione **+ Novo**.

6. Na janela New Linked Service, insira "AzureStorageLinkedService" como nome, selecione "Service Principal" da lista de métodos de autenticação, preencha o Ponto de Serviço Endpoint, Inquilino, ID principal de serviço e chave principal de serviço, em seguida, selecione Save para implementar o serviço ligado.  Consulte [aqui](connector-azure-blob-storage.md#service-principal-authentication) como configurar a autenticação principal do serviço para o Armazenamento Azure Blob.

    ![Novo serviço ligado blob](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Validar o pipeline

Para validar o pipeline, selecione **Validar** na barra de ferramentas.

Também pode ver o código JSON associado ao pipeline clicando em Código no canto superior direito.

## <a name="publish-the-pipeline"></a>Publicar o oleoduto

Na barra de ferramentas superior, **selecione Publicar Tudo.** Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente

**Selecione Adicionar Gatilho** na barra de ferramentas e, em seguida, selecione Trigger **Now**. Na página Executar Pipeline, selecione **Concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Vá para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar links na coluna **Ações** para visualizar detalhes da atividade e para refazer o pipeline.

![Monitorizar o pipeline](./media/load-office-365-data/pipeline-status.png) 

Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna Ações. Neste exemplo, há apenas uma atividade, pelo que só vai ver uma entrada na lista. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna Ações.

![Monitorizar a atividade](./media/load-office-365-data/activity-status.png) 

Se esta for a primeira vez que solicita dados para este contexto (uma combinação a que a tabela de dados está a ser acedida, qual a conta de destino em que são os dados que estão a ser carregados, e qual a identidade do utilizador que está a fazer o pedido de acesso aos dados), verá o estado da atividade de cópia como **Em Andamento**, e só quando clicar no link "Detalhes" em Ações verá o estado como **RequesetingConsent**.  Um membro do grupo de aprovadores de acesso a dados tem de aprovar o pedido na Gestão de Acesso Privilegiado antes que a extração de dados possa prosseguir.

_Estado como consentimento solicitado:_ 
 ![ Detalhes da execução da atividade - consentimento do pedido](./media/load-office-365-data/activity-details-request-consent.png) 

_Estado como dados de extração:_

![Detalhes da execução da atividade - extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Uma vez que o consentimento é fornecido, a extração de dados continuará e, após algum tempo, o gasoduto será apresentado como bem sucedido.

![Monitor pipeline - conseguiu](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora vá ao destino Azure Blob Storage e verifique se os dados do Office 365 foram extraídos em formato Binário.

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber sobre o suporte da Azure Synapse Analytics (anteriormente SQL Data Warehouse): 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)