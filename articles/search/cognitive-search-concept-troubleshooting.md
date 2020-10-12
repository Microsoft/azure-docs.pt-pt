---
title: Dicas para design de enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Dicas e resolução de problemas para a criação de gasodutos de enriquecimento de IA na Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 1ab366cddbabf7e6d574189892e779ab49f6fad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403386"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Dicas para enriquecimento de IA em Pesquisa Cognitiva Azure

Este artigo contém uma lista de dicas e truques para mantê-lo em movimento à medida que começa com capacidades de enriquecimento de IA na Pesquisa Cognitiva Azure. 

Se ainda não o fez, passe pelo [Tutorial: Aprenda a chamar APIs de enriquecimento](cognitive-search-quickstart-blob.md) de IA para prática na aplicação de enriquecimentos de IA a uma fonte de dados blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Dica 1: Comece com um pequeno conjunto de dados
A melhor maneira de encontrar problemas rapidamente é aumentar a velocidade a que pode corrigir problemas. A melhor forma de reduzir o tempo de indexação é reduzindo o número de documentos a indexar. 

Comece por criar uma fonte de dados com apenas um punhado de documentos/registos. A sua amostra de documento deve ser uma boa representação da variedade de documentos que serão indexados. 

Passe a sua amostra de documento através do oleoduto de ponta a ponta e verifique se os resultados satisfazem as suas necessidades. Uma vez satisfeito com os resultados, pode adicionar mais ficheiros à sua fonte de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Dica 2: Certifique-se de que as suas credenciais de origem de dados estão corretas
A ligação de fonte de dados não é validada até definir um indexante que o utilize. Se vir algum erro que mencione que o indexante não pode chegar aos dados, certifique-se de que:
- A sua ligação está correta. Especialmente quando estiver a criar fichas SAS, certifique-se de utilizar o formato esperado pela Azure Cognitive Search. Veja [como especificar a secção de credenciais](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para saber sobre os diferentes formatos suportados.
- O nome do seu recipiente no indexante está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Dica 3: Veja o que funciona mesmo que existam algumas falhas
Às vezes, uma pequena falha para um indexante nas suas pegadas. Tudo bem se planeia resolver problemas um a um. No entanto, é possível que queira ignorar um determinado tipo de erro, permitindo que o indexante continue para que possa ver quais os fluxos que estão realmente funcionando.

Nesse caso, é melhor dizer ao indexante para ignorar os erros. Faça isso definindo *maxFailedItems* e *maxFailedItemsPerBatch* como -1 como parte da definição do indexante.

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
> [!NOTE]
> Como boas práticas, definir os maxFailedItems, maxFailedItemsPerBatch para 0 para cargas de trabalho de produção

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>Dica 4: Use sessões de Debug para identificar e resolver problemas com o seu skillset 

A Debug Sessions é uma editora visual que trabalha com uma habilidade existente no portal Azure. Numa sessão de depuração pode identificar e resolver erros, validar alterações e comprometer alterações a uma habilidade de produção no pipeline de enriquecimento de IA. Esta é uma funcionalidade de pré-visualização [leia a documentação.](./cognitive-search-debug-session.md) Para obter mais informações sobre conceitos e começar, consulte as [sessões de Debug.](./cognitive-search-tutorial-debug-sessions.md)

As sessões de depuração funcionam num único documento são uma ótima maneira de construir iterativamente oleodutos de enriquecimento mais complexos.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>Dica 5: Olhar para documentos enriquecidos sob o capot 
Os documentos enriquecidos são estruturas temporárias criadas durante o enriquecimento e, em seguida, eliminadas quando o processamento está completo.

Para capturar um instantâneo do documento melhorado criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador captura automaticamente para o campo uma representação da cadeia de todos os melhoramentos desse documento.

O campo ```enriched``` vai conter uma cadeia que é uma representação lógica do documento melhorado na memória no JSON.  Contudo, o valor do campo é um documento JSON válido. As aspas são carateres de escape, por isso, terá de substituir `\"` por `"` para ver o documento como JSON formatado. 

O campo enriquecido destina-se apenas a depurar propósitos, para ajudá-lo a compreender a forma lógica do conteúdo contra o qual as expressões estão a ser avaliadas. Não deve depender deste campo para fins de indexação.

Adicione um ```enriched``` campo como parte da sua definição de índice para fins de depuração:

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

## <a name="tip-6-expected-content-fails-to-appear"></a>Dica 6: O conteúdo esperado não aparece

O conteúdo em falta pode ser o resultado de documentos que foram retirados durante a indexação. Os níveis gratuitos e básicos têm limites baixos no tamanho do documento. Qualquer ficheiro que exceda o limite é retirado durante a indexação. Pode verificar se há documentos deixados no portal Azure. No painel de instrumentos de pesquisa, clique duas vezes no azulejo indexantes. Reveja o rácio de documentos bem sucedidos indexados. Se não for 100%, pode clicar na razão para obter mais detalhes. 

Se o problema estiver relacionado com o tamanho do ficheiro, poderá ver um erro como este: "A \<file-name> bolha" tem o tamanho dos \<file-size> bytes, que excede o tamanho máximo para a extração de documentos para o seu nível de serviço atual." Para obter mais informações sobre os limites do indexante, consulte [os limites de Serviço](search-limits-quotas-capacity.md).

Uma segunda razão para o facto não aparecer pode estar relacionada com erros de mapeamento de entrada/saída. Por exemplo, um nome-alvo de saída é "People", mas o nome do campo de índice é "pessoas" minúsculas. O sistema poderia devolver 201 mensagens de sucesso para todo o oleoduto, por isso pensa que a indexação foi bem sucedida, quando na verdade um campo está vazio. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Dica 7: Prolongar o processamento para além do tempo máximo de funcionaamento (janela de 24 horas)

A análise de imagem é computacionalmente intensiva para casos mesmo simples, por isso quando as imagens são especialmente grandes ou complexas, os tempos de processamento podem exceder o tempo máximo permitido. 

O tempo máximo de funcionamento varia por nível: vários minutos no nível Livre, indexação de 24 horas em níveis faturantes. Se o processamento não estiver concluído dentro de um período de 24 horas para o processamento a pedido, mude para um horário para que o indexante retome o processamento onde este deixou de ser colocado. 

Para os indexantes programados, a indexação retoma o calendário no último bom documento conhecido. Ao utilizar um horário recorrente, o indexante pode trabalhar o seu caminho através do atraso de imagem ao longo de uma série de horas ou dias, até que todas as imagens não processadas sejam processadas. Para obter mais informações sobre a sintaxe do horário, consulte [o passo 3: Criar um indexador](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) ou ver [Como agendar indexadores para a Pesquisa Cognitiva Azure](search-howto-schedule-indexers.md).

> [!NOTE]
> Se um indexante for definido para um determinado horário, mas falhar repetidamente no mesmo documento repetidamente cada vez mais de cada vez que for executado, o indexante começará a funcionar num intervalo menos frequente (até ao máximo de pelo menos uma vez a cada 24 horas) até que progrida com sucesso novamente.  Se acredita que corrigiu qualquer que fosse o problema que estava a causar a fixação do indexante num determinado ponto, pode realizar uma execução a pedido do indexante, e se isso progredir com sucesso, o indexante voltará a ter o seu intervalo de horário definido.

Para a indexação baseada no portal (como descrito no arranque rápido), escolher a opção indexante "run once" limita o processamento para 1 hora ( `"maxRunTime": "PT1H"` ). É melhor estender a janela de processamento para algo mais longo.

## <a name="tip-8-increase-indexing-throughput"></a>Dica 8: Aumentar a produção de indexação

Para [uma indexação paralela,](search-howto-large-index.md)coloque os seus dados em vários recipientes ou várias pastas virtuais dentro do mesmo recipiente. Em seguida, crie vários pares de recursos de dados e indexantes. Todos os indexantes podem usar o mesmo skillset e escrever no mesmo índice de pesquisa alvo, por isso a sua aplicação de pesquisa não precisa de estar ciente desta partição.

## <a name="see-also"></a>Consulte também

+ [Quickstart: Criar um oleoduto de enriquecimento de IA no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Aprenda AI enriquecimento REST APIs](cognitive-search-tutorial-blob.md)
+ [Especificar credenciais de origem de dados](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Como mapear campos enriquecidos para um índice](cognitive-search-output-field-mapping.md)