---
title: Criar um pipeline de pesquisa cognitiva para com tecnologia de IA indexação no portal do Azure - Azure Search
description: Exemplo de extração de dados, linguagem natural e competências de processamento de imagens no portal do Azure com dados de exemplo.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 03/17/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f00df841f81ea5c7aa1fd53309b00487602e5143
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200635"
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>Início rápido: Criar um pipeline de pesquisa cognitiva com habilidades e dados de exemplo

A pesquisa cognitiva (pré-visualização) adiciona a extração de dados, o processamento de linguagem natural (NLP) e as competências de processamento de imagens a um pipeline de indexação do Azure Search, tornando o conteúdo não pesquisável ou não estruturado mais pesquisável. 

Integra-se de um pipeline de pesquisa cognitiva [recursos dos serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/) – tal como [OCR](cognitive-search-skill-ocr.md), [deteção de idioma](cognitive-search-skill-language-detection.md), [reconhecimento de entidades](cognitive-search-skill-entity-recognition.md)– num processo de indexação. Os algoritmos de IA dos serviços cognitivos são usados para encontrar padrões, funcionalidades e características na origem de dados, retornando estruturas e conteúdo textual que pode ser utilizado em soluções de pesquisa em texto completo com base no Azure Search.

Neste início rápido, crie seu primeiro pipeline de melhoria na [portal do Azure](https://portal.azure.com) antes de escrever uma única linha de código:

> [!div class="checklist"]
> * Comece com dados de exemplo no armazenamento de Blobs do Azure
> * Configurar o [ **importar dados** ](search-import-data-portal.md) Assistente para a indexação cognitivos e enriquecimento 
> * Execute o assistente (uma capacidade de entidades deteta as pessoas, a localização e as organizações)
> * Uso [ **Explorador de pesquisa** ](search-explorer.md) para consultar os dados plena

## <a name="supported-regions"></a> Regiões suportadas

IA sofisticados indexação através dos serviços cognitivos está disponível em todas as regiões do Azure Search.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> A partir de 21 de Dezembro de 2018, poderá associar um recurso dos serviços cognitivos com um conjunto de capacidades do Azure Search. Isso permitirá que comecem a cobrança de consumo para a execução do conjunto de capacidades. Esta data, podemos também começar a cobrar extração de imagem como parte da fase de aberturas de documentos. Continuaremos a oferecer a extração de texto de documentos sem custos adicionais.
>
> A execução das habilidades internas será cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/) . Preços de extração de imagem são cobrados ao preço de pré-visualização e está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Pré-requisitos

A página [“O que é a pesquisa cognitiva?”](cognitive-search-concept-intro.md) apresenta os melhoramentos dos componentes e da arquitetura. 

Os serviços do Azure são utilizados exclusivamente neste cenário. A criação dos serviços de que precisa faz parte da preparação.

+ [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) fornece a origem de dados
+ [Os serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/) fornece a IA (pode criar esses recursos em linha, quando especificar o pipeline)
+ [O Azure Search](https://azure.microsoft.com/services/search/) fornece o pipeline de indexação plena e uma experiência de pesquisa de texto avançado livres para utilização em aplicações personalizadas

### <a name="set-up-azure-search"></a>Configurar o Azure Search

Primeiro, inscreva-se no serviço do Azure Search. 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

1. Clique em **Criar um recurso**, procure o Azure Search e clique em **Criar**. Veja [Criar um serviço do Azure Search no portal](search-create-service-portal.md) se estiver a configurar um serviço de pesquisa pela primeira vez e precisar de mais ajuda.

   ![Portal do dashboard](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Criar o serviço do Azure Search no portal")

1. Grupo de recursos, crie um novo grupo de recursos para conter todos os recursos que criou neste início rápido. Tal torna mais fácil limpar os recursos depois de concluir o início rápido.

1. Para a localização, escolha uma da [regiões suportadas](#supported-regions) para pesquisa cognitiva.

1. Para Escalão de preço, pode criar um serviço **Gratuito** para concluir os tutoriais e os inícios rápidos. Para uma investigação mais aprofundada através dos seus próprios dados, crie um [serviço pago](https://azure.microsoft.com/pricing/details/search/), como **Básico** ou **Standard**. 

   O serviço Gratuito está limitado a 3 índices, um tamanho de blob máximo de 16 MB e 2 minutos de indexação, o que é insuficiente para executar todas as funcionalidades da pesquisa cognitiva. Para rever os limites dos diferentes escalões, veja [Limites do Serviço](search-limits-quotas-capacity.md).

   ![Página de definições do serviço no portal](./media/cognitive-search-tutorial-blob/create-search-service2.png "Página de definições do serviço no portal")

   > [!NOTE]
   > A Pesquisa cognitiva está em pré-visualização pública. A execução do conjunto de competências está atualmente disponível em todos os escalões, incluindo o gratuito. Será capaz de executar um número limitado de possível sem associar um recurso dos serviços cognitivos pago. Saiba [mais](cognitive-search-attach-cognitive-services.md).

1. Afixe o serviço no dashboard para ter acesso rápido às informações do serviço.

   ![Página de definições do serviço no portal](./media/cognitive-search-tutorial-blob/create-search-service3.png "Página de definições do serviço no portal")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurar o serviço Blob do Azure e carregar dados de exemplo

O pipeline de melhoramento solicita as origens de dados do Azure suportadas pelos [Indexadores do Azure Search](search-indexer-overview.md). Tenha em atenção que o armazenamento de tabelas do Azure não é suportado para a pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. A [transferência de dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) é constituída por um pequeno conjunto de ficheiros de diferentes tipos. 

1. Inscreva-se para o armazenamento de Blobs do Azure, criar uma conta de armazenamento, abra as páginas de serviços de Blob e criar um contentor. 

1. No contentor, definiu o acesso público ao nível para **contentor (acesso de leitura anónimo para contentores e blobs)**. Para obter mais informações, consulte [secção "Criar um contentor de"](../storage/blobs/storage-unstructured-search.md#create-a-container) no *procurar dados não estruturados* tutorial.

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

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Expanda **possível adicionar** e selecione as competências que realizam o processamento de linguagem natural. Para este início rápido, escolha o reconhecimento de entidades de pessoas, organizações e localizações.

   ![Anexar Serviços Cognitivos](media/cognitive-search-quickstart-blob/skillset.png)

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

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver terminado a exploração, a forma mais rápida de a limpar passará por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure.  

Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este exercício. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Dependendo de como tiver aprovisionado o recurso dos serviços cognitivos, pode experimentar com indexação e enriquecimento executando novamente o assistente com diferentes habilidades e campos de dados de origem. Para repetir os passos, elimine o índice e o indexador e, em seguida, recrie o indexador com uma nova combinação de seleções.

+ Em **Descrição geral** > **Índices**, selecione o índice criado e clique em **Eliminar**.

+ Na **Descrição geral**, faça duplo clique no mosaico **Indexadores**. Localize o indexador que criou e elimine-o.

Alternativamente, reutilize os dados de exemplo e os serviços que criou e saiba como realizar as mesmas tarefas programaticamente no próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Saiba que o cognitivos APIs de REST de pesquisa](cognitive-search-tutorial-blob.md)
