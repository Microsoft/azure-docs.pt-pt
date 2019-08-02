---
title: Listar recursos de armazenamento do Azure com a biblioteca C++ de cliente de armazenamento para | Microsoft Docs
description: Saiba como usar as APIs de listagem na biblioteca de cliente Armazenamento do Microsoft Azure C++ para para enumerar contêineres, BLOBs, filas, tabelas e entidades.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 3a87e39c9435ba02357b4b655e95e96666242b71
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721925"
---
# <a name="list-azure-storage-resources-in-c"></a>Listar recursos de armazenamento do Azure noC++

As operações de listagem são fundamentais para muitos cenários de desenvolvimento com o armazenamento do Azure. Este artigo descreve como enumerar com mais eficiência os objetos no armazenamento do Azure usando as APIs de listagem fornecidas na biblioteca C++de cliente do armazenamento do Microsoft Azure para o.

> [!NOTE]
> Este guia destina-se à biblioteca de cliente C++ do armazenamento do Azure para a versão 2. x, que está disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

A biblioteca de cliente de armazenamento fornece uma variedade de métodos para listar ou consultar objetos no armazenamento do Azure. Este artigo aborda os seguintes cenários:

* Listar contêineres em uma conta
* Listar BLOBs em um contêiner ou diretório de blob virtual
* Listar filas em uma conta
* Listar tabelas em uma conta
* Consultar entidades em uma tabela

Cada um desses métodos é mostrado usando sobrecargas diferentes para cenários diferentes.

## <a name="asynchronous-versus-synchronous"></a>Assíncrono versus síncrono

Como a biblioteca de cliente de C++ armazenamento do é criada sobre a [ C++ biblioteca REST](https://github.com/Microsoft/cpprestsdk), essencialmente damos suporte a operações assíncronas usando o [PPLX:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

As operações síncronas encapsulam as operações assíncronas correspondentes:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se você estiver trabalhando com vários aplicativos ou serviços de Threading, recomendamos que você use as APIs assíncronas diretamente em vez de criar um thread para chamar as APIs de sincronização, o que afeta significativamente o desempenho.

## <a name="segmented-listing"></a>Listagem segmentada

A escala do armazenamento em nuvem requer listagem segmentada. Por exemplo, você pode ter mais de um milhão de BLOBs em um contêiner de BLOBs do Azure ou em um bilhão de entidades em uma tabela do Azure. Esses não são números teóricos, mas casos reais de uso do cliente.

Portanto, é impraticável listar todos os objetos em uma única resposta. Em vez disso, você pode listar objetos usando paginação. Cada uma das APIs de listagem tem uma sobrecarga segmentada.

A resposta para uma operação de listagem segmentada inclui:

* *_segment*, que contém o conjunto de resultados retornados para uma única chamada para a API de listagem.
* *continuation_token*, que é passado para a próxima chamada a fim de obter a próxima página de resultados. Quando não houver mais resultados a serem retornados, o token de continuação será nulo.

Por exemplo, uma chamada típica para listar todos os BLOBs em um contêiner pode ser parecida com o trecho de código a seguir. O código está disponível em nossos [exemplos](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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

Observe que o número de resultados retornados em uma página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se você não especificar o parâmetro *max_results* , o valor máximo padrão de até 5000 resultados será retornado em uma única página.

Observe também que uma consulta no armazenamento de tabelas do Azure pode não retornar registros ou menos registros do que o valor do parâmetro *max_results* que você especificou, mesmo se o token de continuação não estiver vazio. Um motivo pode ser que a consulta não pôde ser concluída em cinco segundos. Desde que o token de continuação não esteja vazio, a consulta deve continuar e seu código não deve assumir o tamanho dos resultados do segmento.

O padrão de codificação recomendado para a maioria dos cenários é a listagem segmentada, que fornece um progresso explícito de listagem ou consulta e como o serviço responde a cada solicitação. Particularmente para C++ aplicativos ou serviços, o controle de nível inferior do progresso da listagem pode ajudar a controlar a memória e o desempenho.

## <a name="greedy-listing"></a>Listagem de ávidos

As versões anteriores da biblioteca de cliente de C++ armazenamento para o (versões 0.5.0 Preview e anteriores) incluíam APIs de listagem não segmentadas para tabelas e filas, como no exemplo a seguir:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Esses métodos foram implementados como wrappers de APIs segmentadas. Para cada resposta da listagem segmentada, o código acrescentou os resultados a um vetor e retornou todos os resultados depois que os contêineres completos foram verificados.

Essa abordagem pode funcionar quando a conta de armazenamento ou a tabela contiver um pequeno número de objetos. No entanto, com um aumento no número de objetos, a memória necessária pode aumentar sem limite, pois todos os resultados permaneceram na memória. Uma operação de listagem pode levar muito tempo, durante a qual o chamador não tinha nenhuma informação sobre seu progresso.

Essas APIs de listagem de ávidos no SDK não existem C#no, Java ou no ambiente JavaScript node. js. Para evitar os possíveis problemas de usar essas APIs de ávido, nós as removemos na versão prévia do 0.6.0.

Se seu código estiver chamando essas APIs de ávido:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Em seguida, você deve modificar seu código para usar as APIs de listagem segmentadas:

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

Ao especificar o parâmetro *max_results* do segmento, você pode balancear entre os números de solicitações e o uso de memória para atender às considerações de desempenho para seu aplicativo.

Além disso, se você estiver usando APIs de listagem segmentadas, mas armazenar os dados em uma coleção local em um estilo "ávido", também é altamente recomendável refatorar seu código para lidar com o armazenamento de dados em uma coleção local cuidadosamente em escala.

## <a name="lazy-listing"></a>Listagem lenta

Embora a listagem de ávidos tenha gerado problemas potenciais, é conveniente se não houver muitos objetos no contêiner.

Se você também estiver usando C# o ou os SDKs do Oracle Java, você deve estar familiarizado com o modelo de programação enumerável, que oferece uma listagem de estilo lento, em que os dados em um determinado deslocamento só serão buscados se for necessário. No C++, o modelo baseado em iterador também fornece uma abordagem semelhante.

Uma API de listagem lenta típica, usando **list_blobs** como exemplo, tem a seguinte aparência:

```cpp
list_blob_item_iterator list_blobs() const;
```

Um trecho de código típico que usa o padrão de listagem lenta pode ser semelhante ao seguinte:

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

Observe que a listagem lenta só está disponível no modo síncrono.

Em comparação com a listagem de ávido, a listagem lenta busca dados somente quando necessário. Nos bastidores, ele busca dados do armazenamento do Azure somente quando o próximo iterador é movido para o próximo segmento. Portanto, o uso de memória é controlado com um tamanho limitado e a operação é rápida.

As APIs de listagem lentas estão incluídas na biblioteca do C++ cliente de armazenamento para a na versão 2.2.0.

## <a name="conclusion"></a>Conclusão

Neste artigo, discutimos sobrecargas diferentes para listar APIs para vários objetos na biblioteca do cliente de armazenamento C++ para o. Para resumir:

* As APIs assíncronas são altamente recomendadas em cenários de vários threads.
* A listagem segmentada é recomendada para a maioria dos cenários.
* A listagem lenta é fornecida na biblioteca como um wrapper conveniente em cenários síncronos.
* Listagem de ávidos não é recomendada e foi removida da biblioteca.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o armazenamento do Azure e C++a biblioteca de cliente do, consulte os recursos a seguir.

* [Como usar o armazenamento de blobs deC++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como usar o armazenamento de tabela doC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como usar o armazenamento de filas doC++](../storage-c-plus-plus-how-to-use-queues.md)
* [Documentação da biblioteca de cliente C++ de armazenamento do Azure para API.](https://azure.github.io/azure-storage-cpp/)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
