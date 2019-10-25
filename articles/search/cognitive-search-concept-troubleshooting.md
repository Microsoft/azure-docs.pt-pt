---
title: Dicas de solução de problemas para o enriquecimento de ia
titleSuffix: Azure Cognitive Search
description: Dicas e solução de problemas para configurar pipelines de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 485dd47e035f03a8e20ded4c8a424f1658f5246a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787706"
---
# <a name="troubleshooting-tips-for-ai-enrichment-in-azure-cognitive-search"></a>Dicas de solução de problemas para o enriquecimento de ia no Azure Pesquisa Cognitiva

Este artigo contém uma lista de dicas e truques para manter você mudando à medida que começar a usar os recursos de enriquecimento de ia no Azure Pesquisa Cognitiva. 

Se você ainda não fez isso, percorra o [tutorial: saiba como chamar APIs de enriquecimento de ai](cognitive-search-quickstart-blob.md) para praticar na aplicação de aprimoramentos de ai a uma fonte de dados de BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Dica 1: comece com um conjunto de pequenos conjuntos de um
A melhor maneira de encontrar problemas rapidamente é aumentar a velocidade na qual você pode corrigir problemas. A melhor maneira de reduzir o tempo de indexação é reduzindo o número de documentos a serem indexados. 

Comece criando uma fonte de dados com apenas alguns documentos/registros. O exemplo de documento deve ser uma boa representação da variedade de documentos que serão indexados. 

Execute o exemplo de documento por meio do pipeline de ponta a ponta e verifique se os resultados atendem às suas necessidades. Quando estiver satisfeito com os resultados, você poderá adicionar mais arquivos à fonte de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Dica 2: Verifique se suas credenciais de fonte de dados estão corretas
A conexão da fonte de dados não é validada até que você defina um indexador que a usa. Se você vir algum erro mencionando que o indexador não pode obter os dados, verifique se:
- A cadeia de conexão está correta. Especialmente quando você estiver criando tokens SAS, certifique-se de usar o formato esperado pelo Azure Pesquisa Cognitiva. Consulte a [seção como especificar credenciais](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para saber mais sobre os diferentes formatos com suporte.
- O nome do contêiner no indexador está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Dica 3: Veja o que funciona mesmo se houver algumas falhas
Às vezes, uma pequena falha interrompe um indexador em suas faixas. Isso é bom se você planeja corrigir problemas um a um. No entanto, talvez você queira ignorar um determinado tipo de erro, permitindo que o indexador continue para que você possa ver quais fluxos estão realmente funcionando.

Nesse caso, talvez você queira instruir o indexador a ignorar erros. Faça isso definindo *maxFailedItems* e *maxFailedItemsPerBatch* como-1 como parte da definição do indexador.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Dica 4: examinando os documentos aprimorados nos bastidores 
Documentos aprimorados são estruturas temporárias criadas durante o enriquecimento e, em seguida, excluídas quando o processamento é concluído.

Para capturar um instantâneo do documento melhorado criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador captura automaticamente para o campo uma representação da cadeia de todos os melhoramentos desse documento.

O campo ```enriched``` vai conter uma cadeia que é uma representação lógica do documento melhorado na memória no JSON.  Contudo, o valor do campo é um documento JSON válido. As aspas são carateres de escape, por isso, terá de substituir `\"` por `"` para ver o documento como JSON formatado. 

O campo enriquecedo destina-se apenas a fins de depuração, para ajudá-lo a entender a forma lógica do conteúdo no qual as expressões estão sendo avaliadas. Você não deve depender desse campo para fins de indexação.

Adicione um campo de ```enriched``` como parte da definição de índice para fins de depuração:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Dica 5: falha na exibição do conteúdo esperado

O conteúdo ausente pode ser o resultado de documentos sendo removidos durante a indexação. As camadas gratuita e básica têm limites baixos de tamanho do documento. Qualquer arquivo que exceda o limite é removido durante a indexação. Você pode verificar se há documentos descartados na portal do Azure. No painel do serviço de pesquisa, clique duas vezes no bloco indexadores. Examine a taxa de documentos bem-sucedidos indexados. Se não for 100%, você poderá clicar na taxa para obter mais detalhes. 

Se o problema estiver relacionado ao tamanho do arquivo, você poderá ver um erro como este: "o blob \<nome-do-arquivo >" tem o tamanho de \<tamanho do arquivo > bytes, o que excede o tamanho máximo para a extração de documentos para a camada de serviço atual. " Para obter mais informações sobre os limites do indexador, consulte [limites de serviço](search-limits-quotas-capacity.md).

Um segundo motivo para a falha na exibição do conteúdo pode estar relacionado a erros de mapeamento de entrada/saída. Por exemplo, um nome de destino de saída é "People", mas o nome do campo de índice é "People" em letras minúsculas. O sistema poderia retornar 201 mensagens de êxito para o pipeline inteiro para que você ache que a indexação foi bem-sucedida, quando, na verdade, um campo está vazio. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Dica 6: estender o processamento além do tempo de execução máximo (janela de 24 horas)

A análise de imagem é computacionalmente intensiva para até mesmo casos simples, portanto, quando as imagens são especialmente grandes ou complexas, tempos de processamento podem exceder o tempo máximo permitido. 

O tempo de execução máximo varia de acordo com a camada: vários minutos na camada gratuita, indexação de 24 horas em camadas faturáveis. Se o processamento não for concluído em um período de 24 horas para processamento sob demanda, mude para um agendamento para que o indexador pegue o processamento de onde parou. 

Para indexadores agendados, a indexação é retomada na agenda no último documento válido conhecido. Usando um agendamento recorrente, o indexador pode trabalhar de maneira correspondente por meio da lista de pendências de imagem em uma série de horas ou dias, até que todas as imagens não processadas sejam processadas. Para obter mais informações sobre a sintaxe de agendamento, consulte [etapa 3: criar-um-indexador](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) ou ver [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

> [!NOTE]
> Se um indexador for definido como um determinado agendamento, mas falhar repetidamente no mesmo documento repetidamente cada vez que for executado, o indexador começará a ser executado em um intervalo menos frequente (até o máximo de pelo menos uma vez a cada 24 horas) até que ele faça com que o progresso seja agado com êxito no.  Se acreditar que você corrigiu qualquer problema que estava fazendo com que o indexador fosse paralisado em um determinado ponto, você poderá executar uma execução sob demanda do indexador e, se isso fizer o progresso com êxito, o indexador retornará ao seu intervalo de agendamento definido novamente.

Para a indexação baseada em Portal (conforme descrito no guia de início rápido), a escolha da opção de indexador "executar uma vez" limita o processamento para uma hora (`"maxRunTime": "PT1H"`). Talvez você queira estender a janela de processamento para algo mais longo.

## <a name="tip-7-increase-indexing-throughput"></a>Dica 7: aumentar a taxa de transferência de indexação

Para a [indexação paralela](search-howto-large-index.md), coloque os dados em vários contêineres ou em várias pastas virtuais dentro do mesmo contêiner. Em seguida, crie vários pares DataSource e indexador. Todos os indexadores podem usar o mesmo habilidades e gravar no mesmo índice de pesquisa de destino, de modo que seu aplicativo de pesquisa não precisa estar ciente desse particionamento.
Para obter mais informações, consulte [indexando grandes conjuntos](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)de dados.

## <a name="see-also"></a>Ver também
+ [Início rápido: criar um pipeline de enriquecimento de ia no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: aprender sobre APIs REST de rericação de AI](cognitive-search-tutorial-blob.md)
+ [Especificando credenciais de fonte de dados](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexando conjuntos de grandes volumes](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como mapear campos aprimorados para um índice](cognitive-search-output-field-mapping.md)
