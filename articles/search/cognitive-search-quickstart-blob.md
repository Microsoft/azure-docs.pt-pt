---
title: Criar um skillset no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste portal quickstart, aprenda a usar o assistente de dados importe adicionar habilidades cognitivas a um pipeline de indexação em Azure Cognitive Search. As competências incluem reconhecimento de caracteres óticos (OCR) e processamento de linguagem natural.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: d07b52d8abeab34d565ebde4bac58eec66780dce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179270"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Quickstart: Criar uma pesquisa cognitiva Azure no portal Azure

Um skillset é uma funcionalidade baseada em IA que usa modelos de aprendizagem profunda para extrair informação e estrutura de grandes ficheiros de texto ou imagem indiferenciados, tornando o conteúdo indexável e pes pescável em Azure Cognitive Search. 

Neste arranque rápido, você combinará serviços e dados na nuvem Azure para criar o skillset. Uma vez que tudo esteja no lugar, você executará o assistente **de dados Import** no portal Azure para juntar tudo. O resultado final é um índice pesmável preenchido com dados criados pelo processamento de IA que pode consultar no portal[(Explorador de pesquisa).](search-explorer.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha os seguintes pré-requisitos no lugar:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ Uma conta de armazenamento Azure com [armazenamento Blob](../storage/blobs/index.yml).

> [!NOTE]
> Este quickstart também usa [serviços cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para processamento gratuito até 20 transações. Isto significa que você pode completar este exercício sem ter que criar um recurso adicional de Serviços Cognitivos.

## <a name="set-up-your-data"></a>Configurar os seus dados

Nos passos seguintes, instale um recipiente blob no Azure Storage para armazenar ficheiros de conteúdo heterogéneo.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. Desaperte os ficheiros.

1. [Crie uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Escolha a mesma região que a Azure Cognitive Search para evitar cargas de largura de banda. 

   + Escolha o tipo de conta StorageV2 (propósito geral V2) se quiser experimentar a função de loja de conhecimento mais tarde, em outra passagem. Caso contrário, escolha qualquer tipo.

1. Abra as páginas de serviços Blob e crie um recipiente. Pode utilizar o nível de acesso público predefinido. 

1. No recipiente, clique em **Upload** para carregar os ficheiros de amostra que descarregou no primeiro passo. Note que tem uma vasta gama de tipos de conteúdo, incluindo imagens e ficheiros de aplicações que não são de texto completo pescável nos seus formatos nativos.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Ficheiros de origem no armazenamento de blobs do Azure" border="false":::

Está agora pronto para avançar com o assistente de dados de Importação.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de dados de importação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

1. [Encontre o seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e na página 'Vista Geral', clique em Importar **dados** na barra de comando para configurar o enriquecimento cognitivo em quatro etapas.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Comando de importação de dados" border="false":::

### <a name="step-1---create-a-data-source"></a>Passo 1 - Criar uma fonte de dados

1. Em **Ligar aos seus dados,** escolha o armazenamento **Azure Blob,** selecione a conta de Armazenamento e o recipiente que criou. Dê um nome à origem de dados e utilize os valores predefinidos para o resto. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Configuração de blobs do Azure" border="false":::

    Continue para a próxima página.

### <a name="step-2---add-cognitive-skills"></a>Passo 2 - Adicionar habilidades cognitivas

Em seguida, configurar o enriquecimento de IA para invocar OCR, análise de imagem e processamento de linguagem natural. 

1. Para este arranque rápido, estamos a usar o recurso **Serviços** Cognitivos Gratuitos. Os dados da amostra consistem em 14 ficheiros, pelo que o loteamento gratuito de 20 transações nos Serviços Cognitivos é suficiente para este arranque rápido. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Anexar Serviços Cognitivos anexar serviço de base" border="false":::

1. Expandir **Adicione enriquecimentos** e faça quatro seleções. 

   Ative o OCR para adicionar habilidades de análise de imagem à página de assistente.

   Deslovide a granularidade às páginas para dividir o texto em pedaços menores. Várias habilidades de texto estão limitadas a entradas de 5 KB.

   Escolha competências de reconhecimento de entidades (pessoas, organizações, locais) e competências de análise de imagem.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Anexar serviços de seleção de Serviços Cognitivos para skillset" border="false":::

   Continue para a próxima página.

### <a name="step-3---configure-the-index"></a>Passo 3 - Configurar o índice

Um índice contém o seu conteúdo pes **pes pes procurado** e o assistente de dados de Importação pode normalmente criar o esquema para si através da amostragem da fonte de dados. Neste passo, reveja o esquema gerado e reveja potencialmente quaisquer definições. Abaixo está o esquema padrão criado para o conjunto de dados de demo Blob.

Para este início rápido, o assistente é muito útil, pois define predefinições razoáveis:  

+ Os campos predefinidos baseiam-se em propriedades para bolhas existentes e novos campos para conter a saída de enriquecimento (por exemplo, `people` `organizations` , `locations` . Os tipos de dados são inferidos a partir de metadados e por amostragem de dados.

+ A chave de documento predefinido é *metadata_storage_path* (selecionada porque o campo contém valores únicos).

+ Os atributos predefinidos são **recuperáveis** e **pescaveis.** **O googleable** permite pesquisar por texto completo. **Recuperável** significa que os valores de campo podem ser devolvidos em resultados. O feiticeiro assume que quer que estes campos sejam recuperáveis e pesjáveis porque os criou através de um skillset.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Campos de índice" border="false":::

Note o ponto de greve e ponto de interrogação no atributo **Recuperável** pelo `content` campo. Para documentos blob pesados de texto, o `content` campo contém a maior parte do ficheiro, potencialmente escavendo milhares de linhas. Um campo como este é inflexível nos resultados de pesquisa e você deve excluí-lo para esta demonstração. 

No entanto, se precisar de passar o conteúdo do ficheiro para o código do cliente, certifique-se de que **o Retrievable** permanece selecionado. Caso contrário, considere limpar este atributo `content` se os elementos extraídos (tais `people` `organizations` como, , e assim por `locations` diante) forem suficientes.

Marcar um campo como **Recuperável** não significa que o campo *deve* estar presente nos resultados da pesquisa. Pode controlar com precisão a composição dos resultados da pesquisa utilizando o parâmetro de consulta **$select** para especificar quais os campos a incluir. Para campos pesados de texto `content` como , o parâmetro **$select** é a sua solução para fornecer resultados de pesquisa geríveis aos utilizadores humanos da sua aplicação, garantindo ao mesmo tempo que o código do cliente tem acesso a toda a informação de que necessita através do atributo **Retrievable.**
  
Continue para a próxima página.

### <a name="step-4---configure-the-indexer"></a>Passo 4 - Configurar o indexante

O indexador é um recurso de alto nível que impulsiona o processo de indexação. Especifica o nome da fonte de dados, um índice-alvo e a frequência da execução. O assistente **de dados De importação** cria vários objetos, e deles é sempre um indexante que pode ser executado repetidamente.

1. Na página **Indexer,** pode aceitar o nome predefinido e clicar na opção de agendação **Once** para executá-lo imediatamente. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Definição do indexador" border="false":::

1. Clique **em Submeter** para criar e executar simultaneamente o indexante.

## <a name="monitor-status"></a>Estado do monitor

A indexação das competências cognitivas demora mais tempo a completar do que a indexação típica baseada em texto, especialmente o OCR e a análise de imagem. Para monitorizar o progresso, vá à página 'Visão Geral' e clique em **Indexers** no meio da página.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Notificação de Pesquisa Cognitiva Azure" border="false":::

As advertências são normais dada a ampla gama de tipos de conteúdo. Alguns tipos de conteúdo não são válidos para certas habilidades e em níveis inferiores é comum encontrar [limites indexantes](search-limits-quotas-capacity.md#indexer-limits). Por exemplo, as notificações de truncação de 32.000 caracteres são um limite de indexante no nível Livre. Se fizesses esta demonstração num nível mais alto, muitos avisos de truncação desapareceriam.

Para verificar avisos ou erros, clique no estado de aviso na lista de Indexadores para abrir a página 'Histórico de Execução'.

Nessa página, clique novamente no estado de aviso para visualizar a lista de avisos semelhantes aos apresentados abaixo. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Lista de avisos indexantes" border="false":::

Os detalhes aparecem quando clica numa linha de estado específica. Este aviso diz que a fusão parou depois de atingir um limiar máximo (este PDF em particular é grande).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Detalhes de aviso" border="false":::

## <a name="query-in-search-explorer"></a>Consultar no Explorador de procura

Depois de criado um índice, pode executar consultas para obter resultados. No portal, utilize o **Explorador de Pesquisa** para esta tarefa. 

1. Na página do painel de instrumentos do serviço de pesquisa, clique em **Procurar explorador** na barra de comando.

1. Selecione **Alterar Índice**, na parte superior, para selecionar o índice que criou.

1. Introduza uma cadeia de pesquisa para consultar o índice, tal como `search=Microsoft&$select=people,organizations,locations,imageTags` .

Os resultados são devolvidos como JSON, que pode ser verboso e difícil de ler, especialmente em grandes documentos originários de bolhas Azure. Algumas dicas para pesquisar nesta ferramenta incluem as seguintes técnicas:

+ Apêndice `$select` para especificar quais os campos a incluir nos resultados. 
+ Utilize o CTRL-F para procurar dentro do JSON propriedades ou termos específicos.

As cadeias de consulta são sensíveis ao caso, por isso, se receber uma mensagem de "campo desconhecido", verifique **Fields** ou **Index Definition (JSON)** para verificar o nome e a caixa. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Exemplo do explorador de procura" border="false":::

## <a name="takeaways"></a>Conclusões

Criou agora o seu primeiro skillset e aprendeu conceitos importantes úteis para prototipar uma solução de pesquisa enriquecida usando os seus próprios dados.

Alguns dos conceitos-chave que esperamos que tenha assimilado incluem a dependência das origens de dados do Azure. Um skillset é ligado a um indexante, e os indexantes são Azure e específicos de origem. Embora este início rápida utilize o armazenamento de Blobs do Azure, pode utilizar outras origens de dados do Azure. Para obter mais informações, consulte [Indexers in Azure Cognitive Search](search-indexer-overview.md). 

Outro conceito importante é que as competências operam sobre os tipos de conteúdo, e ao trabalhar com conteúdo heterogéneo, algumas entradas serão ignoradas. Além disso, ficheiros ou campos grandes podem exceder os limites do indexante do seu nível de serviço. É normal ver avisos quando estes eventos ocorrem. 

A produção é direcionada para um índice de pesquisa, e há um mapeamento entre pares de valor-nome criados durante a indexação e campos individuais no seu índice. Internamente, o portal configura as [anotações](cognitive-search-concept-annotations-syntax.md) e define um [conjunto de competências](cognitive-search-defining-skillset.md) ao estabelecer a ordem das operações e o fluxo geral. Estes passos estão ocultos no portal, mas quando começar a escrever código, estes conceitos tornam-se importantes.

Finalmente, aprendeu que pode verificar o conteúdo consultando o índice. No final, o que a Azure Cognitive Search fornece é um índice pesmável, que pode consultar usando a [sintaxe de consulta](/rest/api/searchservice/lucene-query-syntax-in-azure-search) [simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) ou totalmente alargada. Um índice com campos melhorados é igual a qualquer outro. Se pretender incorporar [analisadores](search-analyzers.md)standard ou [personalizados, perfis de pontuação,](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [sinónimos, filtros](search-synonyms.md) [facetados,](search-filters-facets.md)geo-pesquisa ou qualquer outra funcionalidade de Pesquisa Cognitiva Azure, certamente poderá fazê-lo.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Pode criar habilidades utilizando o portal,NET SDK ou REST API. Para promover o seu conhecimento, experimente a API REST utilizando o Carteiro e mais dados de amostra.

> [!div class="nextstepaction"]
> [Tutorial: Extrair texto e estrutura de bolhas JSON usando APIs REST ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Se quiser repetir este exercício ou experimentar uma passagem diferente de enriquecimento de IA, elimine o indexante no portal. A eliminação do indexante repõe o contador de transações diários gratuito de volta a zero para o processamento de Serviços Cognitivos.