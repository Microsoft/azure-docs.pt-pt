---
title: Dicas para design de enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Dicas e resolução de problemas para a criação de oleodutos de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245489"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Dicas para enriquecimento de IA em Pesquisa Cognitiva Azure

Este artigo contém uma lista de dicas e truques para mantê-lo em movimento à medida que começa com capacidades de enriquecimento de IA em Pesquisa Cognitiva Azure. 

Se ainda não o fez, passe pelo [Tutorial: Aprenda a chamar APIs](cognitive-search-quickstart-blob.md) de enriquecimento de IA para prática na aplicação de enriquecimentos de IA a uma fonte de dados blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Dica 1: Comece com um pequeno conjunto de dados
A melhor maneira de encontrar problemas rapidamente é aumentar a velocidade a que pode corrigir problemas. A melhor forma de reduzir o tempo de indexação é reduzindo o número de documentos a indexar. 

Comece por criar uma fonte de dados com apenas um punhado de documentos/registos. A sua amostra de documento deve ser uma boa representação da variedade de documentos que serão indexados. 

Passe a amostra de documento através do gasoduto de ponta a ponta e verifique se os resultados satisfazem as suas necessidades. Uma vez satisfeito com os resultados, pode adicionar mais ficheiros à sua fonte de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Dica 2: Certifique-se de que as suas credenciais de origem de dados estão corretas
A ligação fonte de dados não é validada até definir um indexante que a utiliza. Se vir algum erro que mencione que o indexante não consegue chegar aos dados, certifique-se de que:
- A sua corda de ligação está correta. Especialmente quando estiver a criar tokens SAS, certifique-se de utilizar o formato esperado pela Pesquisa Cognitiva Azure. Consulte [a secção de credenciais para](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) saber sobre os diferentes formatos suportados.
- O nome do seu recipiente no indexante está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Dica 3: Veja o que funciona mesmo que haja algumas falhas
Às vezes, uma pequena falha para um indexante nos seus trilhos. Tudo bem se planeia resolver os problemas um a um. No entanto, é possível que queira ignorar um determinado tipo de erro, permitindo que o indexante continue para que possa ver quais os fluxos que estão realmente a funcionar.

Nesse caso, é possível que queira dizer ao indexador para ignorar os erros. Faça isso definindo *maxFailedItems* e *maxFailedItemsPerBatch* como -1 como parte da definição do indexante.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Dica 4: Olhar para documentos enriquecidos sob o capuz 
Os documentos enriquecidos são estruturas temporárias criadas durante o enriquecimento e, em seguida, eliminadas quando o processamento está completo.

Para capturar um instantâneo do documento melhorado criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador captura automaticamente para o campo uma representação da cadeia de todos os melhoramentos desse documento.

O campo ```enriched``` vai conter uma cadeia que é uma representação lógica do documento melhorado na memória no JSON.  Contudo, o valor do campo é um documento JSON válido. As aspas são carateres de escape, por isso, terá de substituir `\"` por `"` para ver o documento como JSON formatado. 

O campo enriquecido destina-se apenas a fins de depuração, para ajudá-lo a entender a forma lógica do conteúdo contra o qual as expressões estão a ser avaliadas. Não deve depender deste campo para fins de indexação.

Adicione um campo ```enriched``` como parte da sua definição de índice para fins de depuração:

#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Dica 5: Conteúdo esperado não aparece

O conteúdo em falta pode ser o resultado da queda de documentos durante a indexação. Os níveis gratuitos e básicos têm limites baixos no tamanho do documento. Qualquer ficheiro que exceda o limite é retirado durante a indexação. Pode verificar se há documentos no portal Azure. No painel de serviço de pesquisa, clique duas vezes no azulejo indexers. Reveja a razão dos documentos bem sucedidos indexados. Se não for 100%, pode clicar na razão para obter mais detalhes. 

Se o problema estiver relacionado com o tamanho do ficheiro, poderá ver um erro como este: "A bolha \<nome de ficheiro>" tem o tamanho de \<tamanho de ficheiro> bytes, que excede o tamanho máximo para a extração de documentos para o seu nível de serviço atual." Para obter mais informações sobre os limites dos indexantes, consulte [os limites de serviço](search-limits-quotas-capacity.md).

Uma segunda razão para o conteúdo não aparecer pode ser erros de mapeamento de entrada/saída relacionados. Por exemplo, um nome-alvo de saída é "People", mas o nome de campo de índice é "pessoas" minúsculas. O sistema poderia devolver 201 mensagens de sucesso para todo o oleoduto, pelo que se pensa que a indexação foi bem sucedida, quando na verdade um campo está vazio. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Dica 6: Prolongar o processamento para além do tempo máximo de execução (janela de 24 horas)

A análise da imagem é computacionalmente intensiva para casos simples, por isso, quando as imagens são especialmente grandes ou complexas, os tempos de processamento podem exceder o tempo máximo permitido. 

O tempo máximo de execução varia por nível: vários minutos no nível Livre, indexação de 24 horas em níveis de faturação. Se o processamento não estiver concluído num período de 24 horas para o processamento a pedido, mude para um horário para que o indexador retome o processamento onde deixou de ser. 

Para os indexadores programados, a indexação retoma no horário no último bom documento conhecido. Ao utilizar um horário recorrente, o indexante pode fazer o seu caminho através do atraso de imagem ao longo de uma série de horas ou dias, até que todas as imagens não processadas sejam processadas. Para obter mais informações sobre a sintaxe de horário, consulte [passo 3: Criar um indexante](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) ou ver [Como agendar indexadores para pesquisa cognitiva Azure](search-howto-schedule-indexers.md).

> [!NOTE]
> Se um indexante for definido para um determinado horário, mas falha repetidamente no mesmo documento vezes sem conta cada vez que funciona, o indexante começará a funcionar num intervalo menos frequente (até ao máximo de pelo menos uma vez a cada 24 horas) até que faça progressos aga em.  Se acredita ter corrigido qualquer que seja o problema que estava a causar o facto de o indexante estar preso a um determinado ponto, pode realizar uma execução a pedido do indexante, e se isso fizer progressos com sucesso, o indexante voltará ao intervalo de horário definido novamente.

Para indexação baseada em portal (como descrito no quickstart), escolher a opção indexante "executar uma vez" limita o processamento para 1 hora (`"maxRunTime": "PT1H"`). Talvez queira estender a janela de processamento para algo mais longo.

## <a name="tip-7-increase-indexing-throughput"></a>Dica 7: Aumentar a pontuação de indexação

Para [indexação paralela,](search-howto-large-index.md)coloque os seus dados em vários recipientes ou em várias pastas virtuais dentro do mesmo recipiente. Em seguida, crie múltiplos par de fontes de dados e indexantes. Todos os indexadores podem usar o mesmo skillset e escrever no mesmo índice de pesquisa de alvo, por isso a sua aplicação de pesquisa não precisa de estar ciente desta divisão.
Para mais informações, consulte [Indexar grandes conjuntos](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)de dados .

## <a name="see-also"></a>Consulte também
+ [Quickstart: Criar um oleoduto de enriquecimento de IA no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Aprenda a IA enriquecimento REST APIs](cognitive-search-tutorial-blob.md)
+ [Especificação de credenciais de origem de dados](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexação de grandes conjuntos de dados](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Como mapear campos enriquecidos para um índice](cognitive-search-output-field-mapping.md)
