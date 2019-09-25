---
title: Criar um repositório de conhecimento no Azure Search de portal do Azure
description: Crie um repositório de conhecimento Azure Search para persistir aprimoramentos do pipeline de pesquisa cognitiva, usando o assistente de importação de dados na portal do Azure.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265203"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Criar um repositório de conhecimento Azure Search no portal do Azure

> [!Note]
> A loja de conhecimento está em versão prévia e não deve ser usada na produção. A [API REST do Azure Search versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

O repositório de conhecimento é um recurso do Azure Search que persiste a saída de um pipeline de enriquecimento de ia para análise posterior ou outro processamento downstream. Um pipeline aprimorado de ia aceita arquivos de imagem ou arquivos de texto não estruturados, indexa-os usando Azure Search, aplica aprimoramentos de ia de serviços cognitivas (como análise de imagem e processamento de linguagem natural) e, em seguida, salva os resultados em uma loja de conhecimento no Azure repositório. Você pode usar ferramentas como Power BI ou Gerenciador de Armazenamento para explorar a loja de conhecimento.

Neste artigo, você usará o assistente de importação de dados no portal do Azure para ingerir, indexar e aplicar os aprimoramentos de AI a um conjunto de revisões de Hotel. As revisões de Hotel são importadas para o armazenamento de blog do Azure e os resultados são salvos como uma loja de conhecimento no armazenamento de tabelas do Azure.

Depois de criar a loja de conhecimento, você pode aprender a acessar essa loja de conhecimento usando Gerenciador de Armazenamento ou Power BI.

## <a name="prerequisites"></a>Pré-requisitos

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

+ [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo e a loja de conhecimento. Sua conta de armazenamento deve usar o mesmo local (como US-WEas seu serviço de Azure Search e o *tipo de conta* deve ser *StorageV2 (uso geral v2)* (padrão) ou *armazenamento (uso geral v1)* .

## <a name="load-the-data"></a>Carregar os dados

Carregue o arquivo CSV de revisões de Hotel no armazenamento de BLOBs do Azure para que ele possa ser acessado por um indexador Azure Search e alimentado por meio do pipeline de enriquecimento de ia.

### <a name="create-an-azure-blob-container-with-the-data"></a>Criar um contêiner de blob do Azure com os dados

1. [Baixe os dados de revisão do Hotel salvos em um arquivo CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Esses dados são provenientes de Kaggle.com e contêm comentários de clientes sobre hotéis.
1. [Entre no portal do Azure](https://portal.azure.com)e navegue até sua conta de armazenamento do Azure.
1. [Criar um contêiner de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Para fazer isso, na barra de navegação à esquerda para sua conta de armazenamento, clique em **BLOBs**e, em seguida, clique em **+ contêiner** na barra de comandos.
1. Para o novo **nome**do contêiner, `hotel-reviews`digite.
1. Selecione qualquer **nível de acesso público**. Usamos o padrão.
1. Clique em **OK** para criar o contêiner de blob do Azure.
1. Abra o novo `hotels-review` contêiner, clique em **carregar**e selecione o arquivo **HotelReviews-Free. csv** que você baixou na primeira etapa.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as revisões do Hotel")

1. Clique em **carregar** para importar o arquivo CSV para o armazenamento de BLOBs do Azure. O novo contêiner será exibido.

    ![Criar o contêiner de blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de blob do Azure")

### <a name="get-the-azure-storage-account-connection-string"></a>Obter a cadeia de conexão da conta de armazenamento do Azure

1. No portal, navegue até sua conta de armazenamento do Azure.
1. No painel de navegação à esquerda do serviço, clique em **chaves de acesso**.
1. Em **chave 1**, copie e salve a *cadeia de conexão*. A cadeia de caracteres `DefaultEndpointsProtocol=https`começa com. O nome e a chave da conta de armazenamento são inseridos na cadeia de caracteres. Mantenha essa cadeia de caracteres à mão. Você precisará dela em etapas futuras.

## <a name="create-and-run-ai-enrichments"></a>Criar e executar aprimoramentos de ia

Use o assistente para importar dados para criar a loja de conhecimento. Você criará uma fonte de dados, escolherá os aprimoramentos, configurará uma loja de conhecimento e um índice e, em seguida, executará.

### <a name="start-the-import-data-wizard"></a>Iniciar o assistente de importação de dados

1. Na portal do Azure, [localize o serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na barra de comandos, clique em **importar dados** para iniciar o assistente de importação.

### <a name="connect-to-your-data-import-data-wizard"></a>Conectar-se aos seus dados (Assistente de importação de dados)

Nesta etapa do assistente, você criará uma fonte de dados do blob do Azure com seus dados de hotéis.

1. Na lista **fonte de dados** , selecione **armazenamento de BLOBs do Azure**.
1. Para o **nome**, insira `hotel-reviews-ds`.
1. Para o **modo de análise**, selecione **texto delimitado**e, em seguida, selecione a caixa de seleção a **primeira linha contém cabeçalho** . Verifique se o **caractere delimitador** é uma vírgula (,).
1. Insira a cadeia de **conexão** do serviço de armazenamento que você salvou em uma etapa anterior.
1. Para **nome do contêiner**, `hotel-reviews`insira.
1. Clique em **Seguinte: Adicionar pesquisa cognitiva (opcional)** .

      ![Criar um objeto de fonte de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de fonte de dados")

## <a name="add-cognitive-search-import-data-wizard"></a>Adicionar pesquisa cognitiva (Assistente de importação de dados)

Nesta etapa do assistente, você criará um configurador de habilidades com aprimoramentos de habilidades cognitivas. As habilidades que usamos neste exemplo extrairão frases-chave e detectarão o idioma e as opiniões. Esses aprimoramentos serão "projetados" em uma loja de conhecimento como tabelas do Azure.

1. Expanda **anexar serviços cognitivas**. **Gratuito (aprimoramentos limitados)** é selecionado por padrão. Você pode usar esse recurso porque o número de registros em HotelReviews-Free. csv é 19 e esse recurso gratuito permite até 20 transações por dia.
1. Expanda **Adicionar aprimoramentos**.
1. Para **nome**do condigitset `hotel-reviews-ss`, digite.
1. Em **campo de dados de origem**, selecione **reviews_text*.
1. Para **nível de granularidade de enriquecimento**, selecione **páginas (partes de 5000 caracteres)**
1. Selecione essas habilidades cognitivas:
    + **Extrair expressões-chave**
    + **Detectar idioma**
    + **Detectar sentimentos**

      ![Criar um habilidades](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um habilidades")

1. Expanda **salvar aprimoramentos na loja de conhecimento**.
1. Insira a **cadeia de conexão da conta de armazenamento** que você salvou em uma etapa anterior.
1. Selecione estas **projeções de tabela do Azure**:
    + **Documento**
    + **Páginas**
    + **Frases-chave**

    ![Configurar o repositório de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurar o repositório de conhecimento")

1. Clique em **Seguinte: Personalizar índice**de destino.

### <a name="import-data-import-data-wizard"></a>Importar dados (Assistente de importação de dados)

Nesta etapa do assistente, você configurará um índice para consultas de pesquisa de texto completo opcionais. O assistente criará uma amostra da fonte de dados para inferir campos e tipos de dados. Você só precisa selecionar os atributos para o comportamento desejado. Por exemplo, o atributo **recuperável** permitirá que o serviço de pesquisa retorne um valor de campo enquanto o **pesquisável** habilitará a pesquisa de texto completo no campo.

1. Para **nome do índice**, `hotel-reviews-idx`insira.
1. Para atributos, faça estas seleções:
    + Selecione **recuperável** para todos os campos.
    + Selecione **filtrável** e **facetable** para esses campos: *Sentimentos*, *idioma*, *prefrases*
    + Selecione **pesquisável** para estes campos: *cidade*, *nome*, *reviews_text*, *idioma*, *prefrases*

    O índice deve ser semelhante à imagem a seguir. Como a lista é longa, nem todos os campos são visíveis na imagem.

    ![Configurar um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurar um índice")

1. Clique em **Seguinte: Criar um indexador**.

### <a name="create-an-indexer"></a>Criar um indexador

Nesta etapa do assistente, você configurará um indexador que reunirá a fonte de dados, o conjunto de qualificações e o índice que você definiu nas etapas anteriores do assistente.

1. Para **nome**, digite `hotel-reviews-idxr`.
1. Para **agenda**, mantenha o padrão **uma vez**.
1. Clique em **Enviar** para executar o indexador. A extração de dados, a indexação e a aplicação de habilidades cognitivas acontecem nesta etapa.

### <a name="monitor-the-notifications-queue-for-status"></a>Monitorar a fila de notificações para o status

1. No portal do Azure, monitore o log de atividades de notificações para obter um link de status de **notificação Azure Search** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

Agora que você enriqueceu seus dados usando serviços cognitivas e projetou os resultados em uma loja de conhecimento, você pode usar Gerenciador de Armazenamento ou Power BI para explorar seu conjunto de dados aprimorado.

Para saber como explorar essa loja de conhecimento usando Gerenciador de Armazenamento, consulte o passo a passos a seguir.

> [!div class="nextstepaction"]
> [Exibir com Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como conectar essa loja de conhecimento ao Power BI, consulte o passo a passos a seguir.

> [!div class="nextstepaction"]
> [Ligar com o Power BI](knowledge-store-connect-power-bi.md)

Se você quiser repetir este exercício ou tentar um passo a passos de enriquecimento de ia diferente, exclua o indexador *Hotel-Reviews-idxr* . A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero.
