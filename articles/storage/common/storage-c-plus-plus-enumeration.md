---
title: Lista de recursos de armazenamento Azure com biblioteca de clientes C++
description: Saiba como utilizar a listagem de APIs na Microsoft Azure Storage Client Library para C++ para enumerar contentores, bolhas, filas, mesas e entidades.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74269455"
---
# <a name="list-azure-storage-resources-in-c"></a>Listar Recursos do Armazenamento do Microsoft Azure em C++

As operações de listagem são fundamentais para muitos cenários de desenvolvimento com o Armazenamento Azure. Este artigo descreve como enumerar de forma mais eficiente os objetos no Armazenamento Azure utilizando as APIs de listagem fornecidas na Biblioteca de Clientes de Armazenamento Microsoft Azure para C++.

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento Azure para a versão 2.x C++ 2.x, que está disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

A Biblioteca do Cliente de Armazenamento fornece uma variedade de métodos para listar ou consultar objetos no Armazenamento Azure. Este artigo aborda os seguintes cenários:

* Listar contentores numa conta
* Liste bolhas num recipiente ou num diretório de blob virtual
* Lista rindo filas numa conta
* Tabelas de listas numa conta
* Consultas em mesa

Cada um destes métodos é mostrado usando diferentes sobrecargas para diferentes cenários.

## <a name="asynchronous-versus-synchronous"></a>Assíncrono versus sincronia

Como a Biblioteca de Clientes de Armazenamento para C++ é construída em cima da [biblioteca C++ REST,](https://github.com/Microsoft/cpprestsdk)apoiamos inerentemente operações assíncronas utilizando [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

As operações sincronizadas envolvem as operações assíncronas correspondentes:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se estiver a trabalhar com múltiplas aplicações ou serviços de rosca, recomendamos que utilize as APIs asincronizadas diretamente em vez de criar um fio para chamar as APIs sincronizadas, o que impacta significativamente o seu desempenho.

## <a name="segmented-listing"></a>Listagem segmentada

A escala de armazenamento em nuvem requer listagem segmentada. Por exemplo, pode ter mais de um milhão de bolhas num contentor de blob Azure ou mais de mil milhões de entidades numa Mesa Azure. Estes não são números teóricos, mas casos reais de uso do cliente.

Por conseguinte, é impraticável enumerar todos os objetos numa única resposta. Em vez disso, pode listar objetos usando a paging. Cada uma das APIs de listagem tem uma sobrecarga *segmentada.*

A resposta para uma operação de listagem segmentada inclui:

* *_segment*, que contém o conjunto de resultados devolvidos para uma única chamada para a API de listagem.
* *continuation_token*, que é passado para a chamada seguinte para obter a próxima página dos resultados. Quando não há mais resultados para regressar, o sinal de continuação é nulo.

Por exemplo, uma chamada típica para listar todas as bolhas num recipiente pode parecer o seguinte código snippet. O código está disponível nas [nossas amostras:](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Note que o número de resultados devolvidos numa página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se não especificar o parâmetro *max_results,* o valor máximo padrão de até 5000 resultados é devolvido numa única página.

Note também que uma consulta contra o armazenamento da Tabela Azure pode não devolver registos, ou menos registos do que o valor do parâmetro *max_results* que especificou, mesmo que o token de continuação não esteja vazio. Uma das razões pode ser que a consulta não possa ser completada em cinco segundos. Enquanto o sinal de continuação não estiver vazio, a consulta deve continuar, e o seu código não deve assumir o tamanho dos resultados do segmento.

O padrão de codificação recomendado para a maioria dos cenários é a listagem segmentada, que proporciona um progresso explícito de listagem ou consulta, e como o serviço responde a cada pedido. Especialmente para aplicações ou serviços C++, o controlo de nível inferior do progresso da listagem pode ajudar a controlar a memória e o desempenho.

## <a name="greedy-listing"></a>Listagem gananciosa

Versões anteriores da Biblioteca de Clientes de Armazenamento para C++ (versões 0.5.0 Preview e anteriores) incluíam APIs de listagem não segmentadas para tabelas e filas, como no exemplo seguinte:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Estes métodos foram implementados como invólucros de APIs segmentados. Para cada resposta da listagem segmentada, o código anexou os resultados a um vetor e devolveu todos os resultados após a digitalização dos contentores completos.

Esta abordagem pode funcionar quando a conta de armazenamento ou a tabela contenham um pequeno número de objetos. No entanto, com um aumento no número de objetos, a memória necessária poderia aumentar sem limite, porque todos os resultados permaneceram na memória. Uma operação de listagem pode demorar muito tempo, durante a qual o chamador não tinha informações sobre o seu progresso.

Estas APIs de listagem gananciosa no SDK não existem em C#, Java, ou no ambiente JavaScript Node.js. Para evitar os potenciais problemas de utilização destas APIs gananciosas, removemo-las na versão 0.6.0 Preview.

Se o seu código está chamando estas APIs gananciosas:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Em seguida, deve modificar o seu código para utilizar as APIs de listagem segmentadas:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Especificando o parâmetro *max_results* do segmento, pode equilibrar entre os números de pedidos e o uso da memória para atender às considerações de desempenho da sua aplicação.

Além disso, se estiver a usar APIs de listagem segmentada, mas guarde os dados numa coleção local num estilo "ganancioso", recomendamos também que refactoro o seu código para lidar com o armazenamento de dados numa recolha local cuidadosamente em escala.

## <a name="lazy-listing"></a>Listagem preguiçosa

Embora a listagem gananciosa tenha levantado potenciais problemas, é conveniente se não houver muitos objetos no recipiente.

Se também estiver a usar C# ou Oracle Java SDKs, deve estar familiarizado com o modelo de programação Enumerable, que oferece uma listagem de estilo preguiçoso, onde os dados de uma determinada compensação só são recolhidos se fornecessário. Em C++, o modelo baseado no iterator também fornece uma abordagem semelhante.

Uma típica Lista de Anúncios Preguiçosos API, usando **list_blobs** como exemplo, parece assim:

```cpp
list_blob_item_iterator list_blobs() const;
```

Um típico corte de código que usa o padrão de listagem preguiçoso pode parecer assim:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Note que a listagem preguiçosa só está disponível em modo sincronizado.

Em comparação com a listagem gananciosa, a listagem preguiçosa só atinge dados quando necessário. Sob as capas, só rebusca dados do Armazenamento Azure quando o próximo iterno se move para o próximo segmento. Portanto, o uso da memória é controlado com um tamanho limitado, e a operação é rápida.

As APIs de listagem preguiçosa estão incluídas na Biblioteca de Clientes de Armazenamento para C++ na versão 2.2.0.

## <a name="conclusion"></a>Conclusão

Neste artigo, discutimos diferentes sobrecargas para a listagem de APIs para vários objetos na Biblioteca de Clientes de Armazenamento para C++ . Em resumo:

* As APIs asincronizadas são fortemente recomendadas em múltiplos cenários de rosca.
* A listagem segmentada é recomendada para a maioria dos cenários.
* A listagem preguiçosa é fornecida na biblioteca como um invólucro conveniente em cenários sincronizados.
* A listagem gananciosa não é recomendada e foi removida da biblioteca.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Armazenamento Azure e a Biblioteca de Clientes para C++, consulte os seguintes recursos.

* [Como utilizar o Blob Storage a partir de C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de Mesa a partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como utilizar o Armazenamento de Filas a partir do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteca de Clientes de Armazenamento Azure para documentação C++ API.](https://azure.github.io/azure-storage-cpp/)
* [Blog da equipe de armazenamento azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Documentação de Armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
