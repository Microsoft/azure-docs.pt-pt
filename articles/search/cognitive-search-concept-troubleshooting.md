---
title: Sugestões de resolução de problemas para pesquisa cognitiva - Azure Search
description: Sugestões e resolução de problemas para a configuração cognitivos pesquisar pipelines do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5f21fe3c65e37d3fee4043526762a7fafdea5cc4
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593969"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Sugestões de resolução de problemas para pesquisa cognitiva

Este artigo contém uma lista de dicas e truques para mantê-lo a mover de como começar a trabalhar com capacidades de pesquisa cognitiva no Azure Search. 

Se ainda não o tiver o feito, siga os passos a [Tutorial: Saiba como chamar a APIs de pesquisa cognitiva](cognitive-search-quickstart-blob.md) para prática na aplicação possível de pesquisa cognitiva a uma origem de dados de Blobs.

## <a name="tip-1-start-with-a-small-dataset"></a>Sugestão 1: Começar com um pequeno conjunto de dados
É a melhor maneira de encontrar problemas rapidamente aumentar a velocidade a que pode corrigir os problemas. É a melhor maneira de reduzir o tempo de indexação, reduzindo o número de documentos ser indexados. 

Comece por criar uma origem de dados com apenas alguns documentos/registos. Seu exemplo de documento deve ser uma boa representação a variedade de documentos que serão indexados. 

Execute o seu exemplo de documento através do pipeline de ponta a ponta e verificar que os resultados atender às suas necessidades. Quando estiver satisfeito com os resultados, pode adicionar mais ficheiros para a sua origem de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Sugestão 2: Certifique-se de que as credenciais da origem de dados estão corretas
A ligação de origem de dados não é validada até que define um indexador que o utiliza. Se vir algum erro mencionar que o indexador não é possível obter os dados, certifique-se de que:
- A cadeia de ligação está correta. Especialmente quando estiver a criar tokens SAS, certifique-se utilizar o formato esperado pelo Azure Search. Ver [como especificar credenciais secção](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para saber mais sobre os diferentes formatos com suporte.
- O nome do contentor no indexador está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Sugestão 3: Ver o que funciona mesmo se existirem algumas falhas
Por vezes, uma pequena falha deixa de um indexador nos rastos. Não há problema se planeja corrigir problemas de individualmente. No entanto, pode querer ignorar um determinado tipo de erro, permitindo que o indexador continuar, para que pode ver que fluxos são, na verdade, a trabalhar.

Nesse caso, pode querer saber o indexador para ignorar os erros. Faço isso configurando *maxFailedItems* e *maxFailedItemsPerBatch* como -1 como parte da definição de indexador.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Sugestão 4: Observando a plena documentos nos bastidores 
Documentos plena são estruturas temporárias criados durante a melhoria e, em seguida, eliminados quando o processamento estar concluído.

Para capturar um instantâneo do documento melhorado criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador captura automaticamente para o campo uma representação da cadeia de todos os melhoramentos desse documento.

O campo ```enriched``` vai conter uma cadeia que é uma representação lógica do documento melhorado na memória no JSON.  Contudo, o valor do campo é um documento JSON válido. As aspas são carateres de escape, por isso, terá de substituir `\"` por `"` para ver o documento como JSON formatado. 

O campo plena destina-se para fins de depuração só, para ajudar a compreender a forma de lógica do conteúdo que expressões estão a ser avaliadas em comparação com. Não deve depender este campo para fins de indexação.

Adicionar um ```enriched``` campo como parte da sua definição de índice para fins de depuração:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Sugestão 5: Era esperado conteúda falha apareça

Conteúdo em falta pode ser o resultado de documentos deixados durante a indexação. Os escalões gratuito e básicos têm limites baixas no tamanho do documento. Qualquer ficheiro que exceda o limite é removido durante a indexação. Pode verificar ignorados documentos no portal do Azure. No dashboard do serviço de pesquisa, faça duplo clique no mosaico de indexadores. Reveja a taxa de êxito documentos indexados. Se não for 100%, pode clicar a proporção para obter mais detalhes. 

Se o problema está relacionado ao tamanho do ficheiro, poderá ver um erro como este: "O blob < nome de ficheiro >" tem o tamanho de bytes de < tamanho de ficheiro >, que excede o tamanho máximo para extração de documentos do escalão de serviço atual." Para obter mais informações sobre os limites de indexador, consulte [limites de serviço](search-limits-quotas-capacity.md).

Uma segunda razão para o conteúdo que não aparecem poderá ser erros de mapeamento de entrada/saída relacionados. Por exemplo, um nome de destino de saída é "Pessoas", mas o nome do campo de índice é minúsculas "pessoas". O sistema poderia retornar 201 mensagens de sucesso para todo o pipeline, para que acha que a indexação foi concluída com êxito, quando na verdade um campo estiver vazio. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Sugestão 6: Expandir o processamento além do tempo (janela de 24 horas) de execução máximo

A análise de imagem é computacionalmente intensiva para casos simples, portanto, quando as imagens são especialmente grandes ou complexos, tempos de processamento podem exceder o tempo máximo permitido. 

Tempo de execução máximo varia consoante o escalão: escalão de vários minutos no gratuito, indexação nos escalões de cobrar 24 horas. Se o processamento de mensagens em fila não conseguir concluir dentro de um período de 24 horas para processamento sob demanda, mude para uma agenda para fazer com que este pegar processamento onde parou. 

Para indexadores agendadas, indexação CVS numa agenda em último documento bom conhecido. Ao utilizar uma agenda periódica, o indexador pode trabalhar ao percorrer a lista de pendências de imagem através de uma série de horas ou dias, até que todas as imagens não processadas são processadas. Para obter mais informações sobre a sintaxe de agenda, consulte [passo 3: Criar um indexador](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Para baseado no portal de indexação (conforme descrito no guia de introdução), escolhendo o indexador "run once" opção limites de processamento para 1 hora (`"maxRunTime": "PT1H"`). Talvez queira estender a janela de processamento para algo mais.

## <a name="tip-7-increase-indexing-throughput"></a>Sugestão 7: Aumentar o débito de indexação

Para [indexação paralela](search-howto-large-index.md), colocar os dados em vários contentores ou várias pastas virtuais dentro do mesmo contêiner. Em seguida, crie vários pares de origem de dados e o indexador. Todos os indexadores podem utilizar o mesmo conjunto de capacidades e escrever para o mesmo índice de pesquisa de destino, para que seu aplicativo de pesquisa não precisa de conhecer esta criação de partições.
Para obter mais informações, consulte [indexação de grandes conjuntos de dados](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Consulte também
+ [Quickstart: Criar um pipeline de pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Saiba as APIs REST de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Especificação de credenciais de origem de dados](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexação de grandes conjuntos de dados](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Como mapear campos plena para um índice](cognitive-search-output-field-mapping.md)