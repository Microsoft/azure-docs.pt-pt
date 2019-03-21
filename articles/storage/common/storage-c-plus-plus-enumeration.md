---
title: Listar recursos de armazenamento do Azure com a biblioteca de cliente de armazenamento para C++ | Documentos da Microsoft
description: Saiba como utilizar a lista de APIs na biblioteca do cliente de armazenamento do Microsoft Azure para C++ para enumerar os contentores, blobs, filas, tabelas e entidades.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.subservice: common
ms.openlocfilehash: 13ddb4d64908421e999174623003acd2fb24024d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014902"
---
# <a name="list-azure-storage-resources-in-c"></a>Listar recursos de armazenamento do Azure em C++
Operações de listagem são fundamentais para muitos cenários de desenvolvimento com o armazenamento do Azure. Este artigo descreve a forma mais eficiente enumerar objetos no armazenamento do Azure com a listagem APIs fornecidas na biblioteca de cliente de armazenamento do Microsoft Azure para C++.

> [!NOTE]
> Este guia destina-se a biblioteca de cliente de armazenamento do Azure para a versão C++ 2.x, que está disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

A biblioteca de cliente de armazenamento fornece uma variedade de métodos para objetos de lista ou a consulta no armazenamento do Azure. Este artigo aborda os seguintes cenários:

* Lista de contentores numa conta
* Listar os blobs num contentor ou diretório virtual de blob
* Lista de filas em contas
* Lista de tabelas numa conta
* Consultar entidades numa tabela

Cada um desses métodos é mostrada com sobrecargas diferentes para diferentes cenários.

## <a name="asynchronous-versus-synchronous"></a>Assíncrona síncrona versus
Como a biblioteca de cliente de armazenamento para C++ foi criada por cima do [biblioteca de C++ REST](https://github.com/Microsoft/cpprestsdk), suportamos inerentemente operações assíncronas usando [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operações síncronas encapsular as operações assíncronas correspondentes:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se estiver a trabalhar com várias aplicações ou serviços de threading, recomendamos que utilize o async APIs diretamente em vez de criar um thread para chamar a APIs, o que afeta significativamente o desempenho de sincronização.

## <a name="segmented-listing"></a>Listagem segmentada
A escala de armazenamento na cloud requer listagem segmentada. Por exemplo, pode ter ao longo de um milhão de blobs no contentor de Blobs do Azure ou através de um bilhão de entidades numa tabela do Azure. Essas não são números teóricos, mas os casos de utilização de clientes reais.

Portanto, é impraticável para listar todos os objetos numa única resposta. Em vez disso, pode listar objetos usando a paginação. Cada um a listagem APIs tem um *segmentados* de sobrecarga.

A resposta para uma operação de listagem segmentados inclui:

* <i>_segment</i>, que contém o conjunto de resultados devolvidos para uma única chamada à API de listagem.
* *continuation_token*, que é transmitido para a chamada seguinte para obter a próxima página de resultados. Quando não existirem mais resultados para retornar, o token de continuação é nulo.

Por exemplo, uma chamada típica para listar todos os blobs num contentor pode parecer como o seguinte fragmento de código. O código está disponível no nosso [amostras](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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

Tenha em atenção que o número de resultados retornados numa página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se não especificar a *max_results* parâmetro, a predefinição de valor máximo de até 5000 resultados é retornado numa única página.

Observe também que uma consulta com o armazenamento de tabelas do Azure pode retornar nenhum registo ou registos menos que o valor do *max_results* parâmetro que especificou, mesmo que o token de continuação não está vazio. Uma das razões pode ser que a consulta não foi possível concluir em cinco segundos. Desde que o token de continuação não estiver vazio, a consulta deve continuar, e seu código não deve assumir que o tamanho de resultados de segmento.

O padrão de codificação recomendado na maioria dos cenários é altamente segmentado listagem, que fornece o curso explícito de listagem ou consultar e como o serviço responde a cada pedido. Particularmente para aplicativos C++ ou serviços, controlo de nível inferior do progresso listagem pode ajudar a memória do controle e o desempenho.

## <a name="greedy-listing"></a>Listagem greedy
Versões anteriores da biblioteca de cliente do armazenamento para C++ (versão 0.5.0 de versões de pré-visualização e versões anteriores) incluídos não-segmentado listagem APIs para tabelas e filas, como no exemplo seguinte:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Esses métodos foram implementados como wrappers de APIs segmentadas. Por cada resposta de listagem segmentada, o código acrescentados os resultados para um vetor e devolvidos todos os resultados depois dos contentores completos foram analisados.

Esta abordagem poderá funcionar quando a conta de armazenamento ou a tabela contém um pequeno número de objetos. No entanto, com um aumento no número de objetos, a memória necessária poderia aumentar sem limite, uma vez que todos os resultados restou na memória. Operação de listagem pode demorar muito tempo, durante o qual o autor da chamada não tinha informação sobre o progresso.

Estas APIs no SDK de listagem greedy não existem no c#, Java ou o ambiente de node. js do JavaScript. Para evitar os problemas em potencial de usar essas APIs greedy, removemos-los na versão 0.6.0 pré-visualização.

Se seu código é chamar essas greedy APIs:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Em seguida, deve modificar o código para usar a listagem segmentada APIs:

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

Ao especificar os *max_results* parâmetro do segmento, pode balancear entre os números de pedidos e a utilização de memória para atender às considerações de desempenho para a sua aplicação.

Além disso, se estiver usando a listagem segmentada APIs, mas armazenar os dados numa coleção local num estilo "greedy", também recomendamos vivamente que refatorar seu código para manipular o armazenamento de dados numa coleção local cuidadosamente em escala.

## <a name="lazy-listing"></a>Listagem lenta
Embora a listagem greedy gerado potenciais problemas, é conveniente se não existirem demasiados objetos no contentor.

Se também estiver usando c# ou SDKs de Java de Oracle, deve estar familiarizado com o modelo de programação Enumerable, que oferece um lento-estilo de listagem, onde os dados num determinado deslocamento só são obtidos se for necessário. No C++, o modelo com base no iterador também fornece uma abordagem semelhante.

Uma listagem de lenta típica API, usando **list_blobs** como exemplo, semelhante ao seguinte:

```cpp
list_blob_item_iterator list_blobs() const;
```

Um trecho de código típico que usa o padrão de listagem lento pode ser assim:

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

Tenha em atenção que a listagem lenta só está disponível no modo síncrono.

Em comparação com a listagem greedy, listagem lenta obtém dados apenas quando necessário. Nos bastidores, ele obtém dados do armazenamento do Azure apenas quando o iterador seguinte passa para o próximo segmento. Por conseguinte, a utilização de memória é controlada com um tamanho vinculado e a operação é rápida.

Listagem lenta APIs estão incluídos na biblioteca de cliente de armazenamento para C++ na versão 2.2.0.

## <a name="conclusion"></a>Conclusão
Neste artigo, discutimos sobrecargas diferentes para listar as APIs para vários objetos na biblioteca de cliente de armazenamento para C++. Para resumir:

* APIs de Async são vivamente recomendados em vários cenários de threads.
* Listagem segmentada recomenda-se na maioria dos cenários.
* Listagem lenta é fornecida na biblioteca como um wrapper conveniente em cenários síncronos.
* Listagem greedy não é recomendada e foi removida da biblioteca.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o armazenamento do Azure e a biblioteca de cliente para C++, consulte os seguintes recursos.

* [Como utilizar o armazenamento de Blobs do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o armazenamento de tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como utilizar o armazenamento de filas do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteca de clientes de armazenamento do Azure para documentação da API de C++.](https://azure.github.io/azure-storage-cpp/)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)

