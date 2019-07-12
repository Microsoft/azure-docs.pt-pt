---
title: 'Início rápido: Criar um índice sofisticados de IA no portal do Azure - Azure Search'
description: Extração de dados, a linguagem natural e habilidades num portal de indexação de pesquisa do Azure, de processamento de imagens no portal do Azure e dados de exemplo.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8f3a1dadaddb423a83f4c3691a4b5747a5196d2a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795338"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-in-azure-search"></a>Início rápido: Criar um pipeline de indexação de IA com competências cognitivas no Azure Search

Integra-o Azure Search [dos serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/), adição de extração de conteúdo, o processamento de linguagem natural (NLP) e as competências de processamento de imagem para um pipeline de indexação de pesquisa do Azure, tornando o conteúdo não estruturado ou unsearchable mais pesquisável. 

Muitos recursos de serviços cognitivos - por exemplo, [OCR](cognitive-search-skill-ocr.md), [deteção de idioma](cognitive-search-skill-language-detection.md), [reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para mencionar alguns – podem ser anexados a um processo de indexação. Os algoritmos de IA dos serviços cognitivos são usados para encontrar padrões, funcionalidades e características na origem de dados, retornando estruturas e conteúdo textual que pode ser utilizado em soluções de pesquisa em texto completo com base no Azure Search.

Neste início rápido, crie seu primeiro pipeline de melhoria na [portal do Azure](https://portal.azure.com) antes de escrever uma única linha de código:

> [!div class="checklist"]
> * Comece com dados de exemplo no armazenamento de Blobs do Azure
> * Configurar o [ **importar dados** ](search-import-data-portal.md) Assistente para a indexação cognitivos e enriquecimento 
> * Execute o assistente (uma capacidade de entidades deteta as pessoas, a localização e as organizações)
> * Uso [ **Explorador de pesquisa** ](search-explorer.md) para consultar os dados plena

Este início rápido executa o serviço gratuito, mas o número de transações gratuitos está limitado a 20 documentos por dia. Se pretender executar este início rápido mais do que uma vez por dia, utilizar um ficheiro mais pequeno, definir, para que caibam em mais execuções.

> [!NOTE]
> Como expandir âmbito ao aumento da frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, precisará [anexar um recurso dos serviços cognitivos cobrar](cognitive-search-attach-cognitive-services.md). Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/). Preços de extração de imagem está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido.

[Os serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/) fornece a IA. Este guia de introdução inclui passos para adicionar esses recursos em linha, quando especificar o pipeline. Não é necessário configurar contas com antecedência.

Serviços do Azure são necessários para fornecer as entradas para o pipeline de indexação. Pode utilizar qualquer origem de dados suportada pelo [indexadores do Azure Search](search-indexer-overview.md) , exceto para o armazenamento de tabelas do Azure, que não é suportado para a indexação de IA. Este início rápido utiliza [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) como um contêiner para ficheiros de origem de dados. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurar o serviço Blob do Azure e carregar dados de exemplo

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. 

1. [Inscreva-se para o armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), criar uma conta de armazenamento, abra as páginas de serviços de Blob e criar um contentor.  Crie a conta de armazenamento na mesma região que o Azure Search.

1. O contentor que criou, clique em **carregar** para carregar os ficheiros de exemplo que transferiu no passo anterior.

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Criar o pipeline de melhoramento

Voltar à página de dashboard de serviço de Azure Search e clique em **importar dados** na barra de comandos para configurar a melhoria cognitiva em quatro passos.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados

Na **ligar aos seus dados**, escolha **armazenamento de Blobs do Azure**, selecione a conta e contentor que criou. Dê um nome à origem de dados e utilize os valores predefinidos para o resto. 

  ![Configuração de blobs do Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Continue para a página seguinte.

  ![Botão seguinte da página de pesquisa cognitiva](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>Passo 2: Adicionar capacidades cognitivas

Em seguida, adicione passos de melhoramento ao pipeline de indexação. Se não tiver um recurso dos serviços cognitivos, pode inscrever-se para uma versão gratuita, que lhe dá 20 transações diariamente. Os dados de exemplo consiste em 14 de arquivos, para que sua alocação diária será principalmente utilizada cópia de segurança depois de executar este assistente.

1. Expanda **anexar serviços cognitivos** para ver as opções para resourcing as APIs serviços cognitivos. Para efeitos deste tutorial, pode utilizar o **gratuito** recursos.

   ![Anexar serviços cognitivos](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Expanda **possível adicionar** e selecione as competências que realizam o processamento de linguagem natural. Para este início rápido, escolha o reconhecimento de entidades de pessoas, organizações e localizações.

   ![Anexar serviços cognitivos](media/cognitive-search-quickstart-blob/skillset.png)

   O portal oferece capacidades incorporadas para análise de texto e de processamento de OCR. No portal, um conjunto de competências funciona através de um campo de origem exclusivo. Tal poderá parecer como um destino pequeno, mas, para os blobs do Azure, o campo `content` contém a maior parte do documento de blobs (por exemplo, um documento Word ou uma apresentação do PowerPoint). Como tal, este campo é uma entrada ideal uma vez que contém todo o conteúdo de um blob.

3. Continue para a página seguinte.

   ![Próxima página Personalizar o índice](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> As competências de processamento de linguagem natural funcionam através de conteúdo de texto no conjunto de dados de exemplo. Uma vez que não selecionarmos a opção de OCR, os arquivos JPEG e PNG encontrados no conjunto de dados de exemplo não processados neste início rápido. 

### <a name="step-3-configure-the-index"></a>Passo 3: Configurar o índice

O assistente, normalmente, pode inferir um índice predefinido. Neste passo, pode ver o esquema de índice gerado e potencialmente revisar quaisquer definições. Abaixo é o índice predefinido criado para o conjunto de dados de Blob de demonstração.

Para este início rápido, o assistente é muito útil, pois define predefinições razoáveis: 

+ Nome predefinido é *azureblob-índice* com base no tipo de origem de dados. 

+ Campos predefinidos baseiam-se o campo de dados de origem original (`content`), além dos campos de saída (`people`, `organizations`, e `locations`) criado pelo pipeline cognitivo. Tipos de dados predefinidos são inferidos do metadados e amostragem de dados.

+ É a chave predefinida *metadata_storage_path* (Este campo contém valores exclusivos).

+ Atributos predefinidos são **recuperável** e **pesquisável** para estes campos. **Pesquisável** indica um campo pode ser pesquisado. **Recuperável** significa que pode ser devolvido nos resultados. O Assistente de parte do princípio de que pretende que estes campos para ser recuperáveis e pesquisáveis, uma vez que os criou através de um conjunto de capacidades.

  ![Campos de índice](media/cognitive-search-quickstart-blob/index-fields.png)

Repare que o ponto de interrogação e rasurado no **recuperável** atributo pelo `content` campo. Para documentos de blob de muito texto, o `content` campo contém a maior parte do arquivo, potencialmente em execução em milhares de linhas. Se tiver de passar o conteúdo do ficheiro para o código de cliente, certifique-se de que **recuperável** permanece selecionado. Caso contrário, considere limpar este atributo na `content` se os elementos extraídos (`people`, `organizations`, e `locations`) são suficientes para os fins pretendidos.

Marcar um campo como **recuperável** não significa que o campo *tem* estar presente nos resultados da pesquisa. Pode controlar com precisão composição de resultados de pesquisa, utilizando o **$select** consultar o parâmetro para especificar quais campos serão incluídos. Como campos de texto forte `content`, o **$select** parâmetro for a sua solução para fornecer os resultados da pesquisa gerenciável para os usuários humanos da sua aplicação, garantindo o código de cliente tem acesso a todas as informações necessárias através da **recuperável** atributo.
  
Continue para a página seguinte.

  ![Próxima página Criar indexador](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>Passo 4: Configurar o indexador

O indexador é um recurso de alto nível que impulsiona o processo de indexação. Especifica o nome da origem de dados, um índice de destino e a frequência de execução. O resultado final do assistente para **Importar dados** é sempre um indexador que pode executar repetidamente.

Na **indexador** página, pode aceitar o nome predefinido e utilizar o **executada uma vez** agendar a opção para executá-lo imediatamente. 

  ![Definição do indexador](media/cognitive-search-quickstart-blob/indexer-def.png)

Clique em **submeter** para criar e executar simultaneamente o indexador.

## <a name="monitor-indexing"></a>Monitor de indexação

Passos de enriquecimento demoram mais tempo a concluir do que típico baseado em texto de indexação. O assistente deve abrir a lista de indexador na página de descrição geral, para que pode controlar o progresso. Para uma navegação automática, vá para a descrição geral da página e clique em **indexadores**.

O aviso ocorre porque os arquivos JPG e PNG são arquivos de imagem e omitíssemos a habilidade de OCR partir este pipeline. Também encontrará notificações de truncamento. O Azure Search limita a extração de 32.000 caracteres no escalão gratuito.

  ![Notificação do Azure Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

A indexação e o melhoramento podem demorar algum tempo, motivo pelo qual os conjuntos de dados mais pequenos são recomendados para uma exploração antecipada. 

## <a name="query-in-search-explorer"></a>Consultar no Explorador de procura

Depois de criar um índice, pode submeter consultas para devolver documentos do índice. No portal, utilize **Explorador de procura** para executar consultas e ver resultados. 

1. Na página do dashboard do serviço de procura, clique em **Explorador de procura** na barra de comandos.

1. Selecione **Alterar Índice**, na parte superior, para selecionar o índice que criou.

1. Introduza uma cadeia de procura para consultar o índice, tais como `search=Microsoft&searchFields=organizations`.

Os resultados são devolvidos em JSON, que podem ser verbosos e difíceis de ler, especialmente em documentos grandes provenientes de blobs do Azure. Se não conseguir analisar os resultados facilmente, utilize CTRL-F para procurar nos documentos. Para esta consulta, pode pesquisar dentro de JSON para termos específicos. 

CTRL-F pode igualmente ajudá-lo a determinar o número de documentos num determinado conjunto de resultados. Para os blobs do Azure, o portal escolhe “metadata_storage_path” como a chave, porque cada valor é exclusivo para o documento. Com CTRL-F, pesquise “metadata_storage_path” para obter uma contagem de documentos. 

  ![Exemplo do explorador de procura](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Conclusões

Agora que concluiu seu exercício de indexação enriquecida cognitivos primeiro. Este início rápido tinha como objetivo introduzir conceitos importantes e orientá-lo através do assistente para que possa criar rapidamente um protótipo de uma solução de pesquisa cognitiva com os seus próprios dados.

Alguns dos conceitos-chave que esperamos que tenha assimilado incluem a dependência das origens de dados do Azure. O melhoramento da pesquisa cognitiva está vinculado aos indexadores e, por sua vez, os indexadores são específicos do Azure e da origem. Embora este início rápida utilize o armazenamento de Blobs do Azure, pode utilizar outras origens de dados do Azure. Para obter mais informações, veja [Indexadores no Azure Search](search-indexer-overview.md).

Outro conceito importante é que as competências funcionam através de campos de texto. No portal, tem de escolher um campo de origem exclusivo para todas as competências. No código, as entradas podem ser outros campos ou o resultado de uma competência de origem.

 As entradas para uma competência estão mapeadas num campo de saída num índice. Internamente, o portal configura as [anotações](cognitive-search-concept-annotations-syntax.md) e define um [conjunto de competências](cognitive-search-defining-skillset.md) ao estabelecer a ordem das operações e o fluxo geral. Estes passos estão ocultos no portal, mas quando começar a escrever código, estes conceitos tornam-se importantes.

Por fim, aprendeu que pode visualizar resultados através da consulta do índice. No final, o Azure Search fornece um índice pesquisável, que pode consultar com a sintaxe de consulta [simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou [completamente expandida](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Um índice com campos melhorados é igual a qualquer outro. Se pretender incorporar [analisadores personalizados](search-analyzers.md) ou standard, [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [sinónimos](search-synonyms.md), [filtros por facetas](search-filters-facets.md), pesquisa geográfica ou qualquer outra funcionalidade do Azure Search, pode certamente fazê-lo.

## <a name="clean-up"></a>Limpeza

Quando está trabalhando na sua própria subscrição, é uma boa idéia no final de um projeto para identificar se ainda precisa que os recursos que criou. Pode executar esquerda de recursos custa dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode localizar e gerir recursos no portal, utilizando o **todos os recursos** ou **grupos de recursos** ligação no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e origens de dados. Pode eliminar os itens individuais no portal para se manter sob o limite. 

## <a name="next-steps"></a>Passos Seguintes

Dependendo de como tiver aprovisionado o recurso dos serviços cognitivos, pode experimentar com indexação e enriquecimento executando novamente o assistente com diferentes habilidades e campos de dados de origem. Para repetir os passos, elimine o índice e o indexador e, em seguida, recrie o indexador com uma nova combinação de seleções.

+ Em **Descrição geral** > **Índices**, selecione o índice criado e clique em **Eliminar**.

+ Na **Descrição geral**, faça duplo clique no mosaico **Indexadores**. Localize o indexador que criou e elimine-o.

Alternativamente, reutilize os dados de exemplo e os serviços que criou e saiba como realizar as mesmas tarefas programaticamente no próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Saiba que o cognitivos APIs de REST de pesquisa](cognitive-search-tutorial-blob.md)
