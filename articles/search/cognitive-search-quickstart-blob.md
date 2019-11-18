---
title: 'Início rápido: criar um conconhecimento no portal do Azure'
titleSuffix: Azure Cognitive Search
description: Use o assistente para importar dados para adicionar habilidades cognitivas a um pipeline de indexação. As habilidades cognitivas incluem o reconhecimento de caracteres ópticos (OCR) e o processamento de linguagem natural.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 2280b718fe949384bb67b1b606ab143ddca8e077
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113364"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Início rápido: criar um Pesquisa Cognitiva de conhecimento cognitiva do Azure no portal do Azure

Um qualificable é um recurso de ia que extrai informações e estrutura de grandes arquivos de texto ou de imagem não diferenciados e torna indexável e pesquisável para consultas de pesquisa de texto completo no Azure Pesquisa Cognitiva. 

Neste guia de início rápido, você combinará serviços e dados na nuvem do Azure para criar o configurador de habilidades. Depois que tudo estiver em vigor, você executará o assistente de **importação de dados** no portal para reunir tudo isso. O resultado final é um índice pesquisável preenchido com dados criados pelo processamento de ia que você pode consultar no portal ([Search Explorer](search-explorer.md)).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este guia de início rápido usa o Azure Pesquisa Cognitiva, o armazenamento de BLOBs do Azure e os [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/) para o ia. 

Como a carga de trabalho é tão pequena, os serviços cognitivas são tocados nos bastidores para fornecer processamento gratuito para até 20 transações diariamente quando invocado do Azure Pesquisa Cognitiva. Desde que você use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso de serviços cognitivas.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. Descompacte os arquivos.

1. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [localize uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) em sua assinatura atual. 

   Escolha a mesma região que o Pesquisa Cognitiva do Azure. Escolha o tipo de conta StorageV2 (uso geral v2) se quiser experimentar o recurso de loja de conhecimento mais tarde, em outro passo a passos. Caso contrário, escolha qualquer tipo.

1. Abra as páginas serviços de BLOB e crie um contêiner. Você pode usar o nível de acesso público padrão. 

1. Em contêiner, clique em **carregar** para carregar os arquivos de exemplo que você baixou na primeira etapa. Observe que você tem uma ampla variedade de tipos de conteúdo, incluindo imagens e arquivos de aplicativos que não são pesquisáveis com texto completo em seus formatos nativos.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na mesma assinatura. Você pode usar um serviço gratuito para este guia de início rápido.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Agora você está pronto para mover o assistente de importação de dados.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de importação de dados

Na página Visão geral do serviço de pesquisa, clique em **importar dados** na barra de comandos para configurar o enriquecimento avançado em quatro etapas.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

1. Em **conectar-se aos seus dados**, escolha **armazenamento de BLOBs do Azure**, selecione a conta de armazenamento e o contêiner que você criou. Dê um nome à origem de dados e utilize os valores predefinidos para o resto. 

   ![Configuração de blobs do Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Vá para a próxima página.

### <a name="step-2-add-cognitive-skills"></a>Passo 2: adicionar capacidades cognitivas

Em seguida, adicione habilidades cognitivas para invocar o processamento de idioma natural. Os dados de exemplo consistem em 12 arquivos, portanto, a alocação gratuita de 20 transações em serviços cognitivas é suficiente para este guia de início rápido. Como não estamos usando o OCR, somente os arquivos que não são de imagem serão contados, violados e usados nesse processo.

1. Para este guia de início rápido, estamos usando o recurso de serviços cognitivas **gratuito** .

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expanda **Adicionar habilidades** e selecione as habilidades que executam o processamento de idioma natural. Para este início rápido, escolha o reconhecimento de entidades de pessoas, organizações e localizações.

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/skillset.png)

1. Aceite o campo de origem padrão: `content`. Isso pode parecer um pequeno destino, mas, para os BLOBs do Azure, o campo `content` contém a maior parte do documento de BLOB (por exemplo, uma palavra doc ou um deck do PowerPoint), o que o torna um bom candidato.

1. Vá para a próxima página.

> [!NOTE]
> As competências de processamento de linguagem natural funcionam através de conteúdo de texto no conjunto de dados de exemplo. Como não selecionamos a opção OCR, os arquivos JPEG e PNG encontrados no conjunto de dados de exemplo não serão processados neste guia de início rápido. 

### <a name="step-3-configure-the-index"></a>Passo 3: configurar o índice

No Azure Pesquisa Cognitiva, um índice contém seu conteúdo pesquisável e o assistente de **importação de dados** geralmente pode criar o esquema para você por meio da amostragem da fonte de dados. Nesta etapa, examine o esquema gerado e, potencialmente, revise as configurações. Abaixo está o esquema padrão criado para o conjunto de dados de blob de demonstração.

Para este início rápido, o assistente é muito útil, pois define predefinições razoáveis: 

+ O nome padrão é *azureblob* com base no tipo de fonte de dados. 

+ Os campos padrão são baseados no campo de dados de origem original (`content`), além dos campos de saída (`people`, `organizations`e `locations`) criados pelas habilidades cognitivas. Os tipos de dados padrão são inferidos de metadados e amostragem de dados.

+ A chave do documento padrão está *metadata_storage_path* (selecionada porque o campo contém valores exclusivos).

+ Os atributos padrão são **recuperáveis** e **pesquisáveis** para esses campos. **Pesquisável** indica que um campo pode ser pesquisado. **Recuperável** significa que ele pode ser retornado nos resultados. O assistente pressupõe que você deseja que esses campos sejam recuperáveis e pesquisáveis porque você os criou por meio de um contratador de habilidades.

  ![Campos de índice](media/cognitive-search-quickstart-blob/index-fields.png)

Observe o tachado e o ponto de interrogação no atributo **recuperável** pelo campo `content`. Para documentos de blob com texto pesado, o campo `content` contém a massa do arquivo, potencialmente em milhares de linhas. Se você precisar passar o conteúdo do arquivo para o código do cliente, certifique-se de que **recuperável** permaneça selecionado. Caso contrário, considere limpar esse atributo em `content` se os elementos extraídos (`people`, `organizations`e `locations`) forem suficientes para suas finalidades.

Marcar um campo como **recuperável** não significa que o campo *deve* estar presente nos resultados da pesquisa. Você pode controlar precisamente a composição dos resultados da pesquisa usando o parâmetro de consulta **$Select** para especificar quais campos incluir. Para campos com texto pesado como `content`, o parâmetro **$Select** é sua solução para fornecer resultados de pesquisa gerenciáveis para os usuários humanos de seu aplicativo, ao mesmo tempo em que garante que o código do cliente tenha acesso a todas as informações necessárias por meio do atributo **recuperável** .
  
Vá para a próxima página.

### <a name="step-4-configure-the-indexer"></a>Passo 4: configurar o indexador

O indexador é um recurso de alto nível que impulsiona o processo de indexação. Especifica o nome da fonte de dados, um índice de destino e a frequência de execução. O assistente de **importação de dados** cria vários objetos, e eles são sempre um indexador que pode ser executado repetidamente.

1. Na página **indexador** , você pode aceitar o nome padrão e clicar na opção agendar **uma vez** para executá-lo imediatamente. 

   ![Definição do indexador](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Clique em **Enviar** para criar e executar o indexador simultaneamente.

## <a name="monitor-status"></a>Status do monitor

A indexação de habilidades cognitiva leva mais tempo para ser concluída do que a indexação típica baseada em texto. Para monitorar o progresso, vá para a página Visão geral e clique em **indexadores** no meio da página.

O aviso ocorre porque os arquivos de imagem JPG e PNG estão na fonte de dados e omitimos a habilidade de OCR deste pipeline. Você também encontrará notificações de truncamento. A extração é limitada a 32.000 caracteres na camada gratuita.

  ![Notificação de Pesquisa Cognitiva do Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

A indexação e o melhoramento podem demorar algum tempo, motivo pelo qual os conjuntos de dados mais pequenos são recomendados para uma exploração antecipada. 

No portal do Azure, você também pode monitorar o log de atividades de notificações para obter um link de status de **notificação do Azure pesquisa cognitiva** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="query-in-search-explorer"></a>Consultar no Explorador de procura

Depois de criar um índice, pode submeter consultas para devolver documentos do índice. No portal, utilize **Explorador de procura** para executar consultas e ver resultados. 

1. Na página do dashboard do serviço de procura, clique em **Explorador de procura** na barra de comandos.

1. Selecione **Alterar Índice**, na parte superior, para selecionar o índice que criou.

1. Insira uma cadeia de caracteres de pesquisa para consultar o índice, como `search=Microsoft&searchFields=Organizations`.

Os resultados são devolvidos em JSON, que podem ser verbosos e difíceis de ler, especialmente em documentos grandes provenientes de blobs do Azure. Se não conseguir analisar os resultados facilmente, utilize CTRL-F para procurar nos documentos. Para essa consulta, você pode pesquisar dentro do JSON para termos específicos. 

CTRL-F pode igualmente ajudá-lo a determinar o número de documentos num determinado conjunto de resultados. Para os blobs do Azure, o portal escolhe “metadata_storage_path” como a chave, porque cada valor é exclusivo para o documento. Com CTRL-F, pesquise “metadata_storage_path” para obter uma contagem de documentos. 

  ![Exemplo do explorador de procura](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Conclusões

Agora você criou seu primeiro qualificable e conhece conceitos importantes úteis para criar um protótipo de uma solução de pesquisa aprimorada usando seus próprios dados.

Alguns dos conceitos-chave que esperamos que tenha assimilado incluem a dependência das origens de dados do Azure. Um configurador de qualificações é associado a um indexador, e os indexadores são específicos do Azure e de origem. Embora este início rápida utilize o armazenamento de Blobs do Azure, pode utilizar outras origens de dados do Azure. Para obter mais informações, consulte [indexadores no Azure pesquisa cognitiva](search-indexer-overview.md).

Outro conceito importante é que as competências funcionam através de campos de texto. No portal, tem de escolher um campo de origem exclusivo para todas as competências. No código, as entradas podem ser outros campos ou o resultado de uma competência de origem.

A saída é direcionada para um índice de pesquisa e há um mapeamento entre pares de nome-valor criados durante a indexação e campos individuais no índice. Internamente, o portal configura as [anotações](cognitive-search-concept-annotations-syntax.md) e define um [conjunto de competências](cognitive-search-defining-skillset.md) ao estabelecer a ordem das operações e o fluxo geral. Estes passos estão ocultos no portal, mas quando começar a escrever código, estes conceitos tornam-se importantes.

Por fim, você aprendeu que o pode verificar o conteúdo consultando o índice. No final, o que o Azure Pesquisa Cognitiva fornece é um índice pesquisável, que você pode consultar usando a sintaxe de consulta [simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou [totalmente estendida](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Um índice com campos melhorados é igual a qualquer outro. Se você quiser incorporar [analisadores](search-analyzers.md)padrão ou personalizados, [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [sinônimos](search-synonyms.md), [filtros facetados](search-filters-facets.md), pesquisa geográfica ou qualquer outro recurso de pesquisa cognitiva do Azure, certamente poderá fazer isso.

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Você pode criar habilidades usando o portal, o SDK do .NET ou a API REST. Para saber mais sobre seu conhecimento, experimente a API REST usando o postmaster e mais dados de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: adicionar estrutura ao "conteúdo não estruturado" com o enriquecimento de ia](cognitive-search-tutorial-blob.md)

> [!Tip]
> Se você quiser repetir este exercício ou tentar um passo diferente de aprimoramento de ia, exclua o indexador no Portal. A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero para o processamento de serviços cognitivas.