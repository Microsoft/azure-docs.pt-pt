---
title: 'Quickstart: Criar uma habilidade no portal Azure'
titleSuffix: Azure Cognitive Search
description: Neste arranque rápido do portal, aprenda a usar o assistente de dados da Importação para adicionar habilidades cognitivas a um pipeline indexante em Pesquisa Cognitiva Azure. As competências incluem reconhecimento ótico de caracteres (OCR) e processamento de linguagem natural.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472422"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Quickstart: Criar uma habilidade cognitiva de pesquisa cognitiva Azure no portal Azure

Um skillset é uma funcionalidade de IA que extrai informações e estrutura de grandes ficheiros de texto ou imagem indiferenciados, e torna-o indexável e pesquisável para consultas completas de pesquisa de texto em Pesquisa Cognitiva Azure. 

Neste arranque rápido, irá combinar serviços e dados na nuvem Azure para criar o skillset. Uma vez que tudo esteja no lugar, você executará o assistente de **dados de importação** no portal para juntar tudo. O resultado final é um índice pesquisável povoado com dados criados pelo processamento de IA que pode consultar no portal ( Explorador de[Pesquisa).](search-explorer.md)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e dados de carga

Este quickstart utiliza pesquisa cognitiva Azure, [armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/)e [Serviços Cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. 

Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para fornecer processamento gratuito até 20 transações. Para um conjunto de dados tão pequeno, pode ignorar a criação ou a anexação de um recurso dos Serviços Cognitivos.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. Desaperte os ficheiros.

1. [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) de armazenamento Azure ou [encontre uma conta existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 

   Escolha a mesma região que a Pesquisa Cognitiva Azure para evitar cargas de largura de banda. 
   
   Escolha o tipo de conta StorageV2 (finalidade geral V2) se quiser experimentar a funcionalidade da loja de conhecimento mais tarde, em outra passagem. Caso contrário, escolha qualquer tipo.

1. Abra as páginas dos serviços blob e crie um recipiente. Pode utilizar o nível de acesso público predefinido. 

1. No recipiente, clique em **Carregar** para carregar os ficheiros de amostra que descarregou no primeiro passo. Note que tem uma vasta gama de tipos de conteúdo, incluindo imagens e ficheiros de aplicações que não são pesquisáveis em texto completo nos seus formatos nativos.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Crie um serviço](search-create-service-portal.md) de Pesquisa Cognitiva Azure ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido.

Está agora pronto para avançar com o assistente de dados da Importação.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de dados da Importação

Na página de visão geral do serviço de pesquisa, clique em **dados de importação** na barra de comando para configurar o enriquecimento cognitivo em quatro passos.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Passo 1 - Criar uma fonte de dados

1. Em **Connect aos seus dados,** escolha o armazenamento **Azure Blob,** selecione a conta de Armazenamento e o recipiente que criou. Dê um nome à origem de dados e utilize os valores predefinidos para o resto. 

   ![Configuração de blobs do Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Continue para a página seguinte.

### <a name="step-2---add-cognitive-skills"></a>Passo 2 - Adicionar habilidades cognitivas

Em seguida, configurar o enriquecimento de IA para invocar OCR, análise de imagem e processamento de linguagem natural. 

1. Para este arranque rápido, estamos a usar o recurso **Free** Cognitive Services. Os dados da amostra são compostos por 14 ficheiros, pelo que o loteamento gratuito de 20 transações em Serviços Cognitivos é suficiente para este arranque rápido. 

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expandir **Adicionar enriquecimentos** e fazer quatro seleções. 

   Ative o OCR para adicionar habilidades de análise de imagem à página do assistente.

   Defino a granularidade para páginas para dividir o texto em pedaços menores. Várias habilidades de texto estão limitadas a inputs de 5 KB.

   Escolha o reconhecimento de entidades (pessoas, organizações, locais) e competências de análise de imagem.

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/skillset.png)

   Continue para a página seguinte.

### <a name="step-3---configure-the-index"></a>Passo 3 - Configurar o índice

Um índice contém o seu conteúdo pesquisável e o assistente de **dados da Importação** pode normalmente criar o esquema para si, através da amostragem da fonte de dados. Neste passo, reveja o esquema gerado e potencialmente reveja quaisquer configurações. Abaixo está o esquema predefinido criado para o conjunto de dados de demonstração Blob.

Para este início rápido, o assistente é muito útil, pois define predefinições razoáveis:  

+ Os campos predefinidos baseiam-se em propriedades para bolhas existentes `people` `organizations`mais `locations`novos campos para conter a saída de enriquecimento (por exemplo, , , ). Os tipos de dados são inferidos a partir de metadados e por amostragem de dados.

+ A chave *de* documento predefinida é metadata_storage_path (selecionada porque o campo contém valores únicos).

+ Os atributos predefinidos são **recuperáveis** e **pesquisáveis.** **O searchável** permite-lhe pesquisar por palavras ou sem por isso. **Recuperar** significa que os valores de campo podem ser devolvidos em resultados. O feiticeiro assume que quer que estes campos sejam recuperáveis e pesquisáveis porque os criou através de uma habilidade.

  ![Campos de índice](media/cognitive-search-quickstart-blob/index-fields.png)

Repare no strikethrough e no ponto de `content` interrogação no atributo **recuperável** pelo campo. Para documentos de blob `content` pesados de texto, o campo contém a maior parte do ficheiro, potencialmente em milhares de linhas. Um campo como este é inflexível nos resultados da pesquisa e você deve excluí-lo para esta demonstração. 

No entanto, se precisar de passar o conteúdo do ficheiro para o código do cliente, certifique-se de que **o Recuperável** permanece selecionado. Caso contrário, considere limpar `content` este atributo se os `people`elementos `locations`extraídos (tais como, por exemplo, `organizations`e assim por diante) forem suficientes.

Marcar um campo como **Recuperável** não significa que o campo *deve* estar presente nos resultados da pesquisa. Pode controlar com precisão a composição dos resultados da pesquisa utilizando o parâmetro de consulta **$select** para especificar quais os campos a incluir. Para campos pesados `content`de texto como , o **parâmetro $select** é a sua solução para fornecer resultados de pesquisa controláveis aos utilizadores humanos da sua aplicação, garantindo ao mesmo tempo que o código do cliente tem acesso a toda a informação de que necessita através do atributo **Recuperável.**
  
Continue para a página seguinte.

### <a name="step-4---configure-the-indexer"></a>Passo 4 - Configure o indexador

O indexador é um recurso de alto nível que impulsiona o processo de indexação. Especifica o nome da fonte de dados, um índice-alvo e a frequência de execução. O assistente de **dados da Importação** cria vários objetos, e deles é sempre um indexante que pode executar repetidamente.

1. Na página **Indexer,** pode aceitar o nome predefinido e clicar na opção de agenda **Uma vez** executada imediatamente. 

   ![Definição do indexador](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Clique em **Submeter** para criar e, simultaneamente, executar o indexador.

## <a name="monitor-status"></a>Estado do monitor

A indexação de competências cognitivas demora mais tempo a completar do que a indexação típica baseada em texto, especialmente o OCR e a análise de imagem. Para monitorizar o progresso, vá à página de Visão Geral e clique em **Indexers** no meio da página.

  ![Notificação de Pesquisa Cognitiva Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Os avisos são normais dada a vasta gama de tipos de conteúdo. Alguns tipos de conteúdo não são válidos para certas habilidades e em níveis inferiores o seu comum para encontrar [limites indexantes](search-limits-quotas-capacity.md#indexer-limits). Por exemplo, as notificações de truncation de 32.000 caracteres são um limite de indexante no nível Livre. Se fizesses esta demonstração num nível mais alto, muitos avisos de truncation desapareceriam.

Para verificar avisos ou erros, clique no estado de Aviso na lista de Indexers para abrir a página História da Execução.

Nessa página, clique novamente no estado de aviso para ver a lista de avisos semelhantes ao indicado abaixo. 

  ![Lista de aviso sacarilista](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Os detalhes aparecem quando clica numa linha de estado específica. Este aviso diz que a fusão parou depois de atingir um limiar máximo (este PDF em particular é grande).

  ![Detalhes de aviso](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Consultar no Explorador de procura

Depois de criado um índice, pode executar consultas para devolver resultados. No portal, utilize o **explorador de pesquisa** para esta tarefa. 

1. Na página do painel de instrumentos do serviço de pesquisa, clique em **Pesquisar explorador** na barra de comando.

1. Selecione **Alterar Índice**, na parte superior, para selecionar o índice que criou.

1. Introduza uma cadeia de pesquisa para `search=Microsoft&$select=people,organizations,locations,imageTags`consultar o índice, como .

Os resultados são devolvidos como JSON, que pode ser verboso e difícil de ler, especialmente em grandes documentos originários de bolhas Azure. Algumas dicas para pesquisar nesta ferramenta incluem as seguintes técnicas:

+ Anexar `$select` para especificar quais os campos a incluir nos resultados. 
+ Utilize CTRL-F para pesquisar dentro do JSON propriedades ou termos específicos.

As cordas de consulta são sensíveis aos casos, por isso, se receber uma mensagem de "campo desconhecido", verifique **Fields** ou **Index Definition (JSON)** para verificar o nome e o caso. 

  ![Exemplo do explorador de procura](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Conclusões

Criou agora a sua primeira habilidade e aprendeu conceitos importantes úteis para protótipo de uma solução de pesquisa enriquecida utilizando os seus próprios dados.

Alguns dos conceitos-chave que esperamos que tenha assimilado incluem a dependência das origens de dados do Azure. Um skillset está ligado a um indexador, e os indexadores são Azure e específicos da fonte. Embora este início rápida utilize o armazenamento de Blobs do Azure, pode utilizar outras origens de dados do Azure. Para mais informações, consulte [Indexers em Pesquisa Cognitiva Azure](search-indexer-overview.md). 

Outro conceito importante é que as competências operam sobre tipos de conteúdo, e quando se trabalha com conteúdo heterogéneo, algumas inputs serão ignoradas. Além disso, ficheiros ou campos grandes podem exceder os limites do indexante do seu nível de serviço. É normal ver avisos quando estes eventos ocorrem. 

A saída é direcionada para um índice de pesquisa, e há um mapeamento entre pares de valor de nome criados durante a indexação e os campos individuais no seu índice. Internamente, o portal configura as [anotações](cognitive-search-concept-annotations-syntax.md) e define um [conjunto de competências](cognitive-search-defining-skillset.md) ao estabelecer a ordem das operações e o fluxo geral. Estes passos estão ocultos no portal, mas quando começar a escrever código, estes conceitos tornam-se importantes.

Finalmente, aprendeu que pode verificar o conteúdo consultando o índice. No final, o que a Pesquisa Cognitiva Azure fornece é um índice pesquisável, que pode consultar usando a [simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou [totalmente estendida sintaxe](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)de consulta . Um índice com campos melhorados é igual a qualquer outro. Se quiser incorporar [analisadores](search-analyzers.md)padrão ou [personalizados, perfis de pontuação,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [sinónimos,](search-synonyms.md) [filtros faciais,](search-filters-facets.md)geo-pesquisa ou qualquer outra funcionalidade de Pesquisa Cognitiva Azure, certamente poderá fazê-lo.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Pode criar habilidades utilizando o portal, .NET SDK ou REST API. Para aprofundar o seu conhecimento, experimente a API REST utilizando o Carteiro e mais dados de amostra.

> [!div class="nextstepaction"]
> [Tutorial: Extrair texto e estrutura de bolhas JSON usando APIs REST](cognitive-search-tutorial-blob.md)

> [!Tip]
> Se quiser repetir este exercício ou experimentar uma passagem diferente de enriquecimento de IA, elimine o indexador no portal. A desapagamento do indexante repõe o contador de transações diário gratuito de volta a zero para o processamento de Serviços Cognitivos.