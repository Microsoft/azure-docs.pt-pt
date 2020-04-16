---
title: Carregue os dados do Office 365 utilizando a Fábrica de Dados Azure
description: Utilizar a Fábrica de Dados Azure para copiar dados do Office 365
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
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415805"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregue os dados do Office 365 utilizando a Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo mostra-lhe como utilizar os dados de carga da Fábrica de Dados _do Office 365 para o armazenamento de Blob Azure_. Pode seguir passos semelhantes para copiar dados para O Lago de Dados Azure Gen1 ou Gen2. Consulte o artigo do [conector do Office 365](connector-office-365.md) sobre a cópia dos dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory:** 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página da nova fábrica de **dados,** forneça valores para os campos que são mostrados na seguinte imagem:
      
   ![Página Nova fábrica de dados](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "O nome de fábrica de dados *LoadFromOffice365Demo* não está disponível", introduza um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**de**_**loadFromOffice365Demo**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure para criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Vê a página inicial da **Data Factory** como mostra a seguinte imagem:
   
   ![Home page da fábrica de dados](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Vamos começar", selecione **o pipeline Create**.
 
    ![Criar pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. No **separador Geral** do gasoduto, introduza "CopyPipeline" para **o nome** do gasoduto.

3. Na caixa de ferramentas De > Mover & Transformar a categoria > arrastar e largar a **atividade Copy** da caixa de ferramentas para a superfície do designer de gasodutos. Especifique "CopyFromOffice365ToBlob" como nome de atividade.

### <a name="configure-source"></a>Configurar origem

1. Vá ao separador > **Fonte,** clique **+ Novo** para criar um conjunto de dados de origem. 

2. Na janela New Dataset, selecione **Office 365**, e, em seguida, selecione **Continuar**.
 
3. Está agora no separador de configuração de atividade de cópia. Clique no botão **Editar** ao lado do conjunto de dados do Office 365 para continuar a configuração de dados.

    ![Config Office 365 dataset geral](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Vê um novo separador aberto para o Conjunto de dados do Office 365. No **separador Geral** na parte inferior da janela Propriedades, introduza "SourceOffice365Dataset" para Nome.
 
5. Vá ao **separador de ligação** da janela Propriedades. Ao lado da caixa de texto de serviço Linked, clique **+ Novo**.

6. Na janela new Linked Service, introduza "Office365LinkedService" como nome, introduza a chave principal de identificação do serviço e do serviço principal, depois teste a ligação e selecione **Criar** para implementar o serviço ligado.

    ![Novo Serviço 365 ligado](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Depois de criar o serviço ligado, volta às definições do conjunto de dados. Ao lado da **Tabela,** escolha a seta para baixo para expandir a lista de conjuntos de dados disponíveis do Office 365, e escolha "BasicDataSet_v0. Message_v0" da lista de entrega:

    ![Config Office 365 dataset tabela](./media/load-office-365-data/edit-dataset.png)

8. Agora volte ao **pipeline** > **Source tab** para continuar a configurar propriedades adicionais para a extração de dados do Office 365.  O alcance do utilizador e o filtro de alcance do utilizador são predicados opcionais que pode definir para restringir os dados que pretende extrair do Office 365. Consulte a secção de propriedades do conjunto de [dados office 365](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) para saber como configura estas definições.

9. É-lhe exigido que escolha um dos filtros de data e forneça os valores de hora de início e fim.

10. Clique no separador **Import Schema** para importar o esquema para conjunto de dados de mensagem.

    ![Config Office 365 dataset schema](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurar sink

1. Vá ao **separador**> Sink e selecione **+ Novo** para criar um conjunto de dados de sumidouro.
 
2. Na janela New Dataset, note que apenas os destinos suportados são selecionados ao copiar do Office 365. Selecione **Armazenamento Blob Azure,** selecione formato Binário e, em seguida, selecione **Continuar**.  Neste tutorial, você copia os dados do Office 365 num Armazém Azure Blob.

3. Clique no botão **Editar** ao lado do conjunto de dados de armazenamento de Blob Azure para continuar a configuração de dados.

4. No **separador Geral** da janela Propriedades, em Nome, introduza "OutputBlobDataset".

5. Vá ao **separador de ligação** da janela Propriedades. Junto à caixa de texto Serviço ligado, selecione **+ Novo**.

6. Na janela new Linked Service, introduza "AzureStorageLinkedService" como nome, selecione "Service Principal" da lista de métodos de autenticação, preencha o Ponto final do serviço, o Inquilino, o Principal ID do Serviço e a chave principal do serviço, e selecione Save para implementar o serviço ligado.  Consulte [aqui](connector-azure-blob-storage.md#service-principal-authentication) como configurar a autenticação principal do serviço para o Armazenamento De Blob Azure.

    ![Novo serviço ligado à Blob](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Validar o pipeline

Para validar o pipeline, selecione **Validar** na barra de ferramentas.

Também pode ver o código JSON associado ao pipeline clicando no Código na parte superior direita.

## <a name="publish-the-pipeline"></a>Publicar o oleoduto

Na barra de ferramentas superior, **selecione Publicar Tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente

**Selecione Adicionar gatilho** na barra de ferramentas e, em seguida, selecione **'Gatilho' agora**. Na página Executar Pipeline, selecione **Concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Vá para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar links na coluna **Ações** para visualizar detalhes de atividade e reexecutar o gasoduto.

![Monitorizar o pipeline](./media/load-office-365-data/pipeline-status.png) 

Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna Ações. Neste exemplo, há apenas uma atividade, pelo que só vai ver uma entrada na lista. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna Ações.

![Monitorizar a atividade](./media/load-office-365-data/activity-status.png) 

Se esta for a primeira vez que solicita dados para este contexto (uma combinação da qual a tabela de dados está a ser acedida, qual a conta de destino que está a ser carregada e em que identidade de utilizador está a fazer o pedido de acesso de dados), verá o estado da atividade da cópia como **Em Curso**, e só quando clicar no link "Detalhes" em Ações verá o estado como **RequesetingConsent**.  Um membro do grupo de aprovadores de acesso a dados precisa de aprovar o pedido na Gestão de Acesso Privilegiado antes que a extração de dados possa prosseguir.

_Estado como consentimento solicitado:_
![Detalhes de execução da atividade - consentimento do pedido](./media/load-office-365-data/activity-details-request-consent.png) 

_Estado como dados de extração:_

![Detalhes de execução de atividade - extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Uma vez que o consentimento seja fornecido, a extração de dados continuará e, após algum tempo, a execução do gasoduto mostrar-se-á como bem sucedida.

![Monitor pipeline - conseguiu](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora vá ao destino Azure Blob Storage e verifique se os dados do Office 365 foram extraídos em formato Binário.

## <a name="next-steps"></a>Passos seguintes

Avançar para o seguinte artigo para saber sobre o suporte do Armazém de Dados Azure SQL: 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)