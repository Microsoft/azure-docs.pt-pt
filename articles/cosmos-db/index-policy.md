---
title: O Azure Cosmos DB, políticas de indexação
description: Saiba como configurar e alterar a predefinição de política para indexação automática e melhor desempenho no Azure Cosmos DB de indexação.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 67bc3076be91ade140b39b7dd8037299902546a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046323"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, cada contêiner tem uma política de indexação que determina como os itens do contentor devem ser indexados. A predefinição de política para de indexação recentemente criado índices de contentores todas as propriedades de cada item, a imposição de índices de intervalo para qualquer cadeia de caracteres ou um número e os índices espaciais para qualquer objeto GeoJSON do ponto de tipo. Isto permite-lhe obter o desempenho de consulta elevado sem ter de pensar a respeito de indexação e a gestão de índices de antemão.

Em algumas situações, talvez queira substituir este comportamento automático para se adequar melhor às suas necessidades. Pode personalizar a política de indexação de um contentor ao definir seu *modo de indexação*e incluir ou excluir *caminhos de propriedade*.

## <a name="indexing-mode"></a>Modo de indexação

O Azure Cosmos DB suporta dois modos de indexação:

- **Consistente**: Se a política de indexação de um contentor é definida como consistência, o índice é atualizado de forma síncrona como criar, atualizar ou eliminar itens. Isso significa que a consistência das suas consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).

- **Nenhum**: Se a política de indexação de um contentor é definida como None, a indexação efetivamente está desativada nesse contentor. Isto é normalmente utilizado quando um contentor é utilizado como um arquivo de chave-valor puro, sem a necessidade de índices secundários. Também pode ajudar a acelerar a massa operações de inserção.

## <a name="including-and-excluding-property-paths"></a>Incluir e excluir caminhos de propriedade

Uma política de indexação personalizada pode especificar caminhos de propriedade que são explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos que são indexados, pode reduzir a quantidade de armazenamento utilizado pelo seu contentor e melhorar a latência das operações de escrita. Estes caminhos são definidos a seguir [o método descrito na secção de descrição geral de indexação](index-overview.md#from-trees-to-property-paths) com as seguintes adições:

- um caminho para um valor escalar (cadeia de caracteres ou número) termina com `/?`
- elementos de uma matriz são abordados em conjunto através de `/[]` notação (em vez de `/0`, `/1` etc.)
- o `/*` com carateres universais que podem ser utilizado para corresponder a todos os elementos abaixo do nó

Colocar novamente o mesmo exemplo:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- o `headquarters`do `employees` caminho é `/headquarters/employees/?`
- o `locations`' `country` caminho é `/locations/[]/country/?`
- o caminho para nada em `headquarters` é `/headquarters/*`

Quando um caminho é explicitamente incluído na política de indexação, ele também deve definir os tipos de índice devem ser aplicados para esse caminho e para cada tipo de índice, o tipo de dados, este índice aplica-se a:

| Tipo de índice | Tipos de dados de destino permitidos |
| --- | --- |
| Intervalo | Cadeia de caracteres ou um número |
| Espacial | Ponto, LineString ou polígono |

Por exemplo, pode incluir a `/headquarters/employees/?` caminho e especificar que um `Range` índice deve ser aplicado nesse caminho para ambos `String` e `Number` valores.

### <a name="includeexclude-strategy"></a>Incluir/excluir estratégia

Qualquer política de indexação tem de incluir o caminho de raiz `/*` como um incluída ou um caminho excluído.

- Inclua o caminho de raiz para seletivamente excluir caminhos que não necessitam de ser indexados. Esta é a abordagem recomendada, já que permite a qualquer nova propriedade, que pode ser adicionada ao seu modelo de índice proativamente o Azure Cosmos DB.
- Exclua o caminho de raiz para seletivamente incluem caminhos que têm de ser indexados.

Ver [esta secção](how-to-manage-indexing-policy.md#indexing-policy-examples) para exemplos de política de indexação.

## <a name="modifying-the-indexing-policy"></a>Modificar a política de indexação

Política de indexação de um contentor pode ser atualizada em qualquer altura [utilizando o portal do Azure ou um dos SDKs suportados](how-to-manage-indexing-policy.md). Uma atualização para a política de indexação aciona uma transformação do índice antiga para a nova, que é realizada online e no local (para que não existe espaço de armazenamento adicional é consumido durante a operação). Índice da política antigo com eficiência é transformada para a nova política sem afetar a disponibilidade de escrita ou o débito aprovisionado no contentor. Transformação de índice é uma operação assíncrona e o tempo que demora a concluir depende do débito aprovisionado, o número de itens e seu tamanho. 

> [!NOTE]
> Enquanto a reindexação está em curso, consultas podem não devolver todos os resultados correspondentes e façam isso sem devolver erros. Isso significa que os resultados da consulta poderão não ser consistente com até a transformação de índice está concluída. É possível controlar o progresso da transformação de índice [utilizando um dos SDKs](how-to-manage-indexing-policy.md).

Se o modo da nova política de indexação está definido para consistência, nenhuma outra alteração de política de indexação pode ser aplicada quando a transformação de índice, está em curso. Uma transformação de índice em execução pode ser cancelada através da definição de modo a política de indexação para None (que imediatamente irá remover o índice).

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

O [Time-to-Live (TTL) funcionalidade](time-to-live.md) requer a indexação para ser o Active Directory no contentor está ligado. Isso significa que:

- Não é possível ativar o TTL num contêiner em que o modo de indexação está definido como None,
- Não é possível definir o modo de indexação para nenhum num contêiner em que o valor de TTL é ativado.

Para cenários em que nenhum caminho de propriedade tem de ser indexados, mas é necessário o valor de TTL, pode utilizar uma política de indexação com:

- um modo de indexação definido a consistência, e
- nenhum caminho incluído, e
- `/*` como o único caminho excluído.

## <a name="obsolete-attributes"></a>Atributos obsoletos

Ao trabalhar com políticas de indexação, pode encontrar os seguintes atributos que agora são obsoletos:

- `automatic` um valor booleano está definida na raiz de uma política de indexação. Ele agora é ignorado e pode ser definido como `true`, quando a ferramenta está a utilizar o exigir.
- `precision` um número é definido ao nível do índice para os caminhos incluídos. Ele agora é ignorado e pode ser definido como `-1`, quando a ferramenta está a utilizar o exigir.
- `hash` é um tipo de índice é agora substituído pelo tipo de intervalo.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Descrição geral de indexação](index-overview.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
