---
title: 'Início rápido: criar um conconhecimento no portal do Azure'
titleSuffix: Azure Cognitive Search
description: Neste guia de início rápido do portal, saiba como usar o assistente de importação de dados para adicionar habilidades cognitivas a um pipeline de indexação no Azure Pesquisa Cognitiva. As habilidades incluem o reconhecimento de caracteres ópticos (OCR) e o processamento de linguagem natural.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: a994a72ae57b39dba8025e7636e0f822f483bc8c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720678"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Início rápido: criar um Pesquisa Cognitiva de conhecimento cognitiva do Azure no portal do Azure

Um qualificable é um recurso de ia que extrai informações e estrutura de grandes arquivos de texto ou de imagem não diferenciados e torna indexável e pesquisável para consultas de pesquisa de texto completo no Azure Pesquisa Cognitiva. 

Neste guia de início rápido, você combinará serviços e dados na nuvem do Azure para criar o configurador de habilidades. Uma vez que tudo esteja no lugar, você executará o assistente de **dados de importação** no portal para juntar tudo. O resultado final é um índice pesquisável povoado com dados criados pelo processamento de IA que pode consultar no portal ( Explorador de[Pesquisa).](search-explorer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este quickstart utiliza pesquisa cognitiva Azure, [armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/)e [Serviços Cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. 

Como a carga de trabalho é tão pequena, os serviços cognitivas são tocados nos bastidores para fornecer processamento gratuito para até 20 transações diariamente por indexador quando invocado do Azure Pesquisa Cognitiva. Desde que você use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso de serviços cognitivas.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. Descompacte os arquivos.

1. [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) de armazenamento Azure ou [encontre uma conta existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 

   Escolha a mesma região que o Azure Pesquisa Cognitiva para evitar encargos de largura de banda. 
   
   Escolha o tipo de conta StorageV2 (uso geral v2) se quiser experimentar o recurso de loja de conhecimento mais tarde, em outro passo a passos. Caso contrário, escolha qualquer tipo.

1. Abra as páginas serviços de BLOB e crie um contêiner. Você pode usar o nível de acesso público padrão. 

1. No recipiente, clique em **Carregar** para carregar os ficheiros de amostra que descarregou no primeiro passo. Observe que você tem uma ampla variedade de tipos de conteúdo, incluindo imagens e arquivos de aplicativos que não são pesquisáveis com texto completo em seus formatos nativos.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Crie um serviço](search-create-service-portal.md) de Pesquisa Cognitiva Azure ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Você pode usar um serviço gratuito para este guia de início rápido.

Agora você está pronto para mover o assistente de importação de dados.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de importação de dados

Na página de visão geral do serviço de pesquisa, clique em **dados de importação** na barra de comando para configurar o enriquecimento cognitivo em quatro passos.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Etapa 1-criar uma fonte de dados

1. Em **Connect aos seus dados,** escolha o armazenamento **Azure Blob,** selecione a conta de Armazenamento e o recipiente que criou. Dê um nome à origem de dados e utilize os valores predefinidos para o resto. 

   ![Configuração de blobs do Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Vá para a próxima página.

### <a name="step-2---add-cognitive-skills"></a>Etapa 2 – Adicionar habilidades cognitivas

Em seguida, configure o enriquecimento de ia para invocar o OCR, a análise de imagem e o processamento de idioma natural. 

1. Para este arranque rápido, estamos a usar o recurso **Free** Cognitive Services. Os dados de exemplo consistem em 14 arquivos, portanto, a alocação gratuita de 20 transações em serviços cognitivas é suficiente para este guia de início rápido. 

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expandir **Adicionar enriquecimentos** e fazer quatro seleções. 

   Habilitar o OCR para adicionar habilidades de análise de imagem à página do assistente.

   Defina a granularidade para páginas para dividir o texto em partes menores. Várias habilidades de texto são limitadas a entradas de 5 KB.

   Escolha o reconhecimento de entidades (pessoas, organizações, locais) e habilidades de análise de imagem.

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/skillset.png)

   Vá para a próxima página.

### <a name="step-3---configure-the-index"></a>Etapa 3 – configurar o índice

Um índice contém o seu conteúdo pesquisável e o assistente de **dados da Importação** pode normalmente criar o esquema para si, através da amostragem da fonte de dados. Nesta etapa, examine o esquema gerado e, potencialmente, revise as configurações. Abaixo está o esquema padrão criado para o conjunto de dados de blob de demonstração.

Para este início rápido, o assistente é muito útil, pois define predefinições razoáveis:  

+ Os campos predefinidos baseiam-se em propriedades para bolhas existentes mais novos campos para conter a saída de enriquecimento (por exemplo, `people`, `organizations`, `locations`). Os tipos de dados são inferidos de metadados e por amostragem de dados.

+ A chave *de* documento predefinida é metadata_storage_path (selecionada porque o campo contém valores únicos).

+ Os atributos predefinidos são **recuperáveis** e **pesquisáveis.** **O searchável** permite-lhe pesquisar por palavras ou sem por isso. **Recuperar** significa que os valores de campo podem ser devolvidos em resultados. O assistente pressupõe que você deseja que esses campos sejam recuperáveis e pesquisáveis porque você os criou por meio de um contratador de habilidades.

  ![Campos de índice](media/cognitive-search-quickstart-blob/index-fields.png)

Note o strikethrough e o ponto de interrogação no atributo **recuperável** pelo campo `content`. Para documentos de blob pesados de texto, o campo `content` contém a maior parte do ficheiro, potencialmente em milhares de linhas. Um campo como esse é complicado nos resultados da pesquisa e você deve excluí-lo para esta demonstração. 

No entanto, se precisar de passar o conteúdo do ficheiro para o código do cliente, certifique-se de que **o Recuperável** permanece selecionado. Caso contrário, considere limpar este atributo em `content` se os elementos extraídos (tais como `people`, `organizations`, `locations`, e assim por diante) forem suficientes.

Marcar um campo como **Recuperável** não significa que o campo *deve* estar presente nos resultados da pesquisa. Pode controlar com precisão a composição dos resultados da pesquisa utilizando o parâmetro de consulta **$select** para especificar quais os campos a incluir. Para campos pesados de texto como `content`, o parâmetro **$select** é a sua solução para fornecer resultados de pesquisa manejáveis aos utilizadores humanos da sua aplicação, garantindo ao mesmo tempo que o código do cliente tem acesso a todas as informações de que necessita através do atributo **Recuperável.**
  
Vá para a próxima página.

### <a name="step-4---configure-the-indexer"></a>Etapa 4 – configurar o indexador

O indexador é um recurso de alto nível que impulsiona o processo de indexação. Especifica o nome da fonte de dados, um índice de destino e a frequência de execução. O assistente de **dados da Importação** cria vários objetos, e deles é sempre um indexante que pode executar repetidamente.

1. Na página **Indexer,** pode aceitar o nome predefinido e clicar na opção de agenda **Uma vez** executada imediatamente. 

   ![Definição do indexador](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Clique em **Submeter** para criar e, simultaneamente, executar o indexador.

## <a name="monitor-status"></a>Status do monitor

A indexação de habilidades cognitiva leva mais tempo para ser concluída do que a indexação típica baseada em texto, especialmente o OCR e a análise de imagem. Para monitorizar o progresso, vá à página de Visão Geral e clique em **Indexers** no meio da página.

  ![Notificação de Pesquisa Cognitiva do Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Os avisos são normais, considerando a ampla variedade de tipos de conteúdo. Alguns tipos de conteúdo não são válidos para certas habilidades e em níveis inferiores o seu comum para encontrar [limites indexantes](search-limits-quotas-capacity.md#indexer-limits). Por exemplo, as notificações de truncamento de 32.000 caracteres são um limite de indexador na camada gratuita. Se você executou esta demonstração em uma camada superior, muitos avisos de truncamento desaparecerão.

Para verificar avisos ou erros, clique no status de aviso na lista indexadores para abrir a página Histórico de execução.

Nessa página, clique em status de aviso novamente para exibir a lista de avisos semelhante à mostrada abaixo. 

  ![Lista de avisos do indexador](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Os detalhes são exibidos quando você clica em uma linha de status específica. Esse aviso diz que a mesclagem parou depois de atingir um limite máximo (esse PDF específico é grande).

  ![Detalhes do aviso](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Consultar no Explorador de procura

Depois que um índice é criado, você pode executar consultas para retornar resultados. No portal, utilize o **explorador de pesquisa** para esta tarefa. 

1. Na página do dashboard do serviço de procura, clique em **Explorador de procura** na barra de comandos.

1. Selecione **Alterar Índice**, na parte superior, para selecionar o índice que criou.

1. Introduza uma cadeia de pesquisa para consultar o índice, como `search=Microsoft&$select=people,organizations,locations,imageTags`.

Os resultados são retornados como JSON, que podem ser detalhados e difíceis de ler, especialmente em documentos grandes provenientes de BLOBs do Azure. Algumas dicas para pesquisar nessa ferramenta incluem as seguintes técnicas:

+ Apêndice `$select` especificar quais os campos a incluir nos resultados. 
+ Use CTRL-F para pesquisar dentro do JSON para propriedades ou termos específicos.

As cordas de consulta são sensíveis aos casos, por isso, se receber uma mensagem de "campo desconhecido", verifique **Fields** ou **Index Definition (JSON)** para verificar o nome e o caso. 

  ![Exemplo do explorador de procura](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Conclusões

Agora você criou seu primeiro qualificable e conhece conceitos importantes úteis para criar um protótipo de uma solução de pesquisa aprimorada usando seus próprios dados.

Alguns dos conceitos-chave que esperamos que tenha assimilado incluem a dependência das origens de dados do Azure. Um configurador de qualificações é associado a um indexador, e os indexadores são específicos do Azure e de origem. Embora este início rápida utilize o armazenamento de Blobs do Azure, pode utilizar outras origens de dados do Azure. Para mais informações, consulte [Indexers em Pesquisa Cognitiva Azure](search-indexer-overview.md). 

Outro conceito importante é que as competências operam sobre tipos de conteúdo, e quando se trabalha com conteúdo heterogéneo, algumas inputs serão ignoradas. Além disso, arquivos ou campos grandes podem exceder os limites do indexador da sua camada de serviço. É normal ver avisos quando esses eventos ocorrem. 

A saída é direcionada para um índice de pesquisa e há um mapeamento entre pares de nome-valor criados durante a indexação e campos individuais no índice. Internamente, o portal configura as [anotações](cognitive-search-concept-annotations-syntax.md) e define um [conjunto de competências](cognitive-search-defining-skillset.md) ao estabelecer a ordem das operações e o fluxo geral. Estes passos estão ocultos no portal, mas quando começar a escrever código, estes conceitos tornam-se importantes.

Por fim, você aprendeu que o pode verificar o conteúdo consultando o índice. No final, o que a Pesquisa Cognitiva Azure fornece é um índice pesquisável, que pode consultar usando a [simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou [totalmente estendida sintaxe](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)de consulta . Um índice com campos melhorados é igual a qualquer outro. Se quiser incorporar [analisadores](search-analyzers.md)padrão ou [personalizados, perfis de pontuação,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [sinónimos,](search-synonyms.md) [filtros faciais,](search-filters-facets.md)geo-pesquisa ou qualquer outra funcionalidade de Pesquisa Cognitiva Azure, certamente poderá fazê-lo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando está a trabalhar na sua própria subscrição, é uma boa ideia no final de um projeto identificar se ainda precisa dos recursos que criou. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

> [!Tip]
> Se você quiser repetir este exercício ou tentar um passo diferente de aprimoramento de ia, exclua o indexador no Portal. A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero para o processamento de serviços cognitivas.

## <a name="next-steps"></a>Passos Seguintes

Você pode criar habilidades usando o portal, o SDK do .NET ou a API REST. Para saber mais sobre seu conhecimento, experimente a API REST usando o postmaster e mais dados de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: Extrair texto e estrutura de bolhas JSON usando APIs REST](cognitive-search-tutorial-blob.md)