---
title: Copiar dados da Azure Data Factory para o Azure Data Explorer
description: Neste artigo, aprende-se a ingerir (carregar) dados no Azure Data Explorer utilizando a ferramenta de cópia da Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422905"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados para o Azure Data Explorer utilizando a Azure Data Factory 

O Azure Data Explorer é um serviço rápido, totalmente gerido, de análise de dados. Oferece análiseem em tempo real sobre grandes volumes de dados que fluem de muitas fontes, tais como aplicações, websites e dispositivos IoT. Com o Azure Data Explorer, pode explorar iterativamente dados e identificar padrões e anomalias para melhorar os produtos, melhorar as experiências do cliente, monitorizar dispositivos e impulsionar as operações. Ajuda-o a explorar novas perguntas e a obter respostas em minutos. 

A Azure Data Factory é um serviço totalmente gerido, baseado na nuvem, de integração de dados. Pode usá-lo para povoar a sua base de dados do Azure Data Explorer com dados do seu sistema existente. Pode ajudá-lo a economizar tempo quando está a construir soluções de análise.

Quando carrega dados no Azure Data Explorer, a Data Factory fornece os seguintes benefícios:

* **Configuração fácil**: Obtenha um assistente intuitivo e de cinco passos sem necessidade de scripts.
* **Suporte rico em lojas**de dados : Obtenha suporte incorporado para um conjunto rico de lojas de dados no local e baseadas na nuvem. Para obter uma lista detalhada, consulte a tabela de lojas de [dados suportadas](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Seguro e conforme:** Os dados são transferidos para HTTPS ou Azure ExpressRoute. A presença global de serviço garante que os seus dados nunca saem da fronteira geográfica.
* **Alto desempenho**: A velocidade de carregamento de dados é de até 1 gigabyte por segundo (GBps) no Azure Data Explorer. Para mais informações, consulte o [desempenho da atividade do Copy.](/azure/data-factory/copy-activity-performance)

Neste artigo, utiliza a ferramenta Data Copy Data da Data para carregar dados do Serviço de Armazenamento Simples (S3) da Amazon para o Azure Data Explorer. Pode seguir um processo semelhante para copiar dados de outras lojas de dados, tais como:
* [Armazenamento de Blobs do Azure](/azure/data-factory/connector-azure-blob-storage)
* [Base de Dados SQL do Azure](/azure/data-factory/connector-azure-sql-database)
* [Armazém de dados Azure SQL](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Sistema de ficheiros](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster azure Data Explorer e base de dados.](create-cluster-database-portal.md)
* Uma fonte de dados.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [Portal do Azure](https://ms.portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory**.

   ![Criar uma fábrica de dados no portal Azure](media/data-factory-load-data/create-adf.png)

1. No painel da nova fábrica de **dados,** forneça valores para os campos no quadro seguinte:

   ![O painel "Nova fábrica de dados"](media/data-factory-load-data/my-new-data-factory.png)  

   | Definição  | Valor a entrar  |
   |---|---|
   | **Nome** | Na caixa, introduza um nome globalmente único para a sua fábrica de dados. Se receber um erro, o *nome \"de\" fábrica de dados LoadADXDemo não está disponível*, insira um nome diferente para a fábrica de dados. Para regras sobre nomeação de artefactos da Fábrica de Dados, consulte regras de [nomeação da Data Factory](/azure/data-factory/naming-rules).|
   | **Subscrição** | Na lista de drop-down, selecione a subscrição Azure para criar a fábrica de dados. |
   | **Grupo de Recursos** | Selecione **Criar novo**e, em seguida, insira o nome de um novo grupo de recursos. Se já tem um grupo de recursos, selecione **Use existente**. |
   | **Versão** | Na lista de lançamentos, selecione **V2**. |    
   | **Localização** | Na lista de entrega, selecione a localização para a fábrica de dados. Apenas os locais suportados são apresentados na lista. As lojas de dados que são utilizadas pela fábrica de dados podem existir noutros locais ou regiões. |

1. Selecione **Criar**.

1. Para monitorizar o processo de criação, selecione **Notificações** na barra de ferramentas. Depois de criar a fábrica de dados, selecione-a.
   
   O painel **da Fábrica** de Dados abre.

   ![O painel da Fábrica de Dados](media/data-factory-load-data/data-factory-home-page.png)

1. Para abrir a aplicação num painel separado, selecione o azulejo **do Autor & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Carregue dados no Azure Data Explorer

Pode carregar dados de vários tipos de lojas de [dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) para o Azure Data Explorer. Este artigo discute como carregar dados da Amazon S3.

Pode carregar os seus dados de qualquer uma das seguintes formas:

* Na interface de utilizador da Azure Data Factory, no painel esquerdo, selecione o ícone **Autor.** Isto é mostrado na secção "Criar uma fábrica de dados" da [Create a data factory utilizando a Azure Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* Na ferramenta Dados de Cópia de Cópia de Cópia de Dados da Fábrica de Dados Azure, tal como mostrado na [ferramenta Utilizar a ferramenta Copy Data para copiar dados](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados da Amazon S3 (fonte)

1. No **Let's start pane,** abra a ferramenta Copy Data selecionando **Dados de Cópia**.

   ![O botão de ferramenta copy Data](media/data-factory-load-data/copy-data-tool-tile.png)

1. No painel **'Propriedades',** na caixa de **nomes de tarefas,** introduza um nome e, em seguida, selecione **Seguinte**.

    ![O painel de propriedades de dados de cópia](media/data-factory-load-data/copy-from-source.png)

1. No painel source **data store,** selecione **Criar uma nova ligação**.

    ![O painel de dados de cópia "Source data store"](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**, e, em seguida, selecione **Continuar**.

    ![O novo painel do Serviço Ligado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. No painel **new linked service (Amazon S3),** faça o seguinte:

    ![Especificar serviço ligado à Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Na caixa **Nome,** insira o nome do seu novo serviço ligado.

    b. No Connect através da lista de drop-down de tempo de **integração,** selecione o valor.

    c. Na caixa de ID da chave de **acesso,** introduza o valor.
    
    > [!NOTE]
    > No Amazon S3, para localizar a sua chave de acesso, selecione o seu nome de utilizador Amazon na barra de navegação e, em seguida, selecione **As minhas credenciais**de segurança .
    
    d. Na caixa chave de **acesso secreto,** introduza um valor.

    e. Para testar a ligação de serviço ligada que criou, selecione **Test Connection**.

    f. Selecione **Concluir**.
    
      O painel da loja de **dados Source** exibe a sua nova ligação AmazonS31. 

1. Selecione **Next**.

   ![Fonte data store criou conexão](media/data-factory-load-data/source-data-store-created-connection.png)

1. No ficheiro de entrada ou painel de **pastas,** faça os seguintes passos:

    a. Navegue no ficheiro ou na pasta que pretende copiar e, em seguida, selecione-o.

    b. Selecione o comportamento da cópia que deseja. Certifique-se de que a caixa de verificação de **cópias Binárias** está limpa.

    c. Selecione **Next**.

    ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. No painel de definições do **formato Ficheiro,** selecione as definições relevantes para o seu ficheiro. e, em seguida, selecione **Next**.

   ![O painel "Definições de formato de ficheiro"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados para o Azure Data Explorer (destino)

O novo serviço ligado ao Azure Data Explorer é criado para copiar os dados na tabela de destino do Azure Data Explorer (pia) especificada nesta secção.

> [!NOTE]
> Utilize a atividade de comando da [Azure Data Factory para executar comandos](data-factory-command-activity.md) de controlo do `.set-or-replace`Explorador de Dados Azure e utilizar qualquer um dos [ingeridos a partir de comandos de consulta](/azure/kusto/management/data-ingestion/ingest-from-query), tais como .

#### <a name="create-the-azure-data-explorer-linked-service"></a>Criar o serviço ligado ao Explorador de Dados Azure

Para criar o serviço ligado ao Azure Data Explorer, faça os seguintes passos:

1. Para utilizar uma ligação de armazenamento de dados existente ou especificar uma nova loja de dados, no painel de loja de **dados Destination,** selecione **Criar uma nova ligação**.

    ![Painel de loja de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. No painel **new Linked Service,** selecione **Azure Data Explorer**, e, em seguida, selecione **Continue**.

    ![O novo painel de serviço ligado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. No painel **new linked service (Azure Data Explorer),** faça os seguintes passos:

    ![O painel de serviço novo explorador de dados azure](media/data-factory-load-data/adx-new-linked-service.png)

   a. Na caixa **Nome,** introduza um nome para o serviço ligado ao Explorador de Dados Azure.

   b. No método de **seleção da Conta,** escolha uma das seguintes opções: 

    * Selecione **A partir da subscrição do Azure** e, em seguida, nas listas de drop-down, selecione a sua **subscrição Azure** e o seu **Cluster**. 

        > [!NOTE]
        > O controlo de drop-down **do Cluster** lista apenas os clusters associados à sua subscrição.

    * Selecione **Introduzir manualmente**e, em seguida, introduza o seu **ponto final**.

   c. Na caixa do **inquilino,** insira o nome do inquilino.

   d. Na caixa de **identificação principal** do serviço, introduza o ID principal do serviço.

   e. Selecione **a chave principal do serviço** e, em seguida, na caixa de chaves principal do **Serviço,** introduza o valor para a chave.

   f. Na lista de desatentes da Base de **Dados,** selecione o nome da sua base de dados. Em alternativa, selecione a caixa de verificação **Editar** e, em seguida, introduza o nome da base de dados.

   g. Para testar a ligação de serviço ligada que criou, selecione **Test Connection**. Se conseguir ligar-se ao seu serviço ligado, o painel apresenta uma marca de verificação verde e uma mensagem **de sucesso da Ligação.**

   h. Selecione **Terminar** para completar a criação de serviço ligado.

    > [!NOTE]
    > O diretor de serviço é utilizado pela Azure Data Factory para aceder ao serviço Azure Data Explorer. Para criar um diretor de serviço, vá criar um diretor de [serviço azure Ative Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Não utilize o método Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configure a ligação de dados do Explorador de Dados Do Azure

Depois de ter criado a ligação de serviço ligada, abre-se o painel da loja de **dados Destination** e a ligação que criou está disponível para utilização. Para configurar a ligação, faça os seguintes passos:

1. Selecione **Next**.

    ![O painel azure Data Explorer "Destination Data Store"](media/data-factory-load-data/destination-data-store.png)

1. No painel de mapeamento da **tabela,** desloque o nome da tabela de destino e, em seguida, selecione **Next**.

    ![O painel de dados de destino "Mapeamento de mesa"](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. No painel de mapeamento da **Coluna,** realizam-se os seguintes mapeamentos:

    a. O primeiro mapeamento é realizado pela Azure Data Factory de acordo com o [mapeamento de esquemas da Azure Data Factory.](/azure/data-factory/copy-activity-schema-and-type-mapping) Faça o seguinte:

    * Detete os **mapeamentos** da Coluna para a tabela de destino da Fábrica de Dados Azure. O mapeamento predefinido é apresentado de origem para a tabela de destino da Fábrica de Dados Azure.

    * Cancele a seleção das colunas que não precisa para definir o mapeamento da coluna.

    b. O segundo mapeamento ocorre quando estes dados tabulares são ingeridos no Azure Data Explorer. O mapeamento é realizado de acordo com as regras de [mapeamento csv](/azure/kusto/management/mappings#csv-mapping). Mesmo que os dados de origem não estejam no formato CSV, a Azure Data Factory converte os dados num formato tabular. Portanto, o mapeamento cSV é o único mapeamento relevante nesta fase. Faça o seguinte:

    * (Opcional) Sob propriedades de **sink Explorer Azure Data (Kusto),** adicione o nome de mapeamento de **ingestion** relevante para que o mapeamento da coluna possa ser usado.

    * Se o nome de mapeamento da **ingestion** não for especificado, será utilizada a ordem de mapeamento por *nome* a que está definida na secção de **mapeamento da Coluna.** Se o mapeamento *por nome* falhar, o Azure Data Explorer tenta ingerir os dados numa ordem de posição *por coluna* (isto é, mapeia por posição como padrão).

    * Selecione **Next**.

    ![O painel de dados de destino "Mapeamento de colunas"](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. No painel **Definições,** faça os seguintes passos:

    a. Em definições de tolerância à **falha,** introduza as definições relevantes.

    b. Sob **as definições**de Desempenho , **a encenação ativa** não se aplica e as **definições avançadas** incluem considerações de custos. Se não tiver requisitos específicos, deixe estas definições como estão.

    c. Selecione **Next**.

    ![O painel de dados de cópia "Definições"](media/data-factory-load-data/copy-data-settings.png)

1. No painel **resumo,** reveja as definições e, em seguida, selecione **Seguinte**.

    ![O painel de dados de cópia "Resumo"](media/data-factory-load-data/copy-data-summary.png)

1. No painel completo de **implantação,** faça o seguinte:

    a. Para mudar para o separador **Monitor** e ver o estado do gasoduto (isto é, progresso, erros e fluxo de dados), selecione **Monitor**.

    b. Para editar serviços, conjuntos de dados e oleodutos ligados, selecione **Editar Pipeline**.

    c. Selecione **Terminar** para completar a tarefa de dados de cópia.

    ![O painel "Implantação completa"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passos seguintes

* Conheça o [conector Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) na Azure Data Factory.
* Saiba mais sobre a edição de serviços ligados, conjuntos de dados e oleodutos na [UI da Fábrica](/azure/data-factory/quickstart-create-data-factory-portal)de Dados.
* Saiba mais sobre as consultas do [Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.
