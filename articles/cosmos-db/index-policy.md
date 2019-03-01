---
title: O Azure Cosmos DB, políticas de indexação
description: Compreenda como a indexação funciona no Azure Cosmos DB. Saiba como configurar e alterar a política de indexação para indexação automática e melhor desempenho.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/1/2019
ms.author: mjbrown
ms.openlocfilehash: 2b46638a7e0fa3dc80fa4d2fa23d49b37b8885ec
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193160"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Política de índice no Azure Cosmos DB

Pode substituir a predefinição de política num contentor do Cosmos do Azure de indexação ao configurar os seguintes parâmetros:

* **Incluir ou excluir itens e os caminhos do índice**: Pode excluir ou incluir itens específicos no índice ao inserir ou substituir os itens dentro de um contêiner. Também pode incluir ou excluir caminhos/propriedades específicas ser indexados em contentores. Caminhos podem incluir padrões de carateres universais, por exemplo, *.

* **Configurar tipos de índice**: Além para variar caminhos indexados, pode adicionar outros tipos de índices, tais como espaciais.

* **Configurar os modos de índice**: Ao utilizar a política de indexação num contentor, pode configurar diferentes modos de indexação, como *consistência* ou *nenhum*.

## <a name="indexing-modes"></a>Modos de indexação

O Azure Cosmos DB suporta dois modos de indexação que pode configurar num contentor do Cosmos do Azure. Pode configurar os seguintes dois modos indexação através da política de indexação:

* **Consistente**: Se a política de um contentor Cosmos do Azure estiver definida para consistência, as consultas num contêiner específico, siga o mesmo nível de consistência que foi especificado para leituras de ponto (por exemplo, sólido, prescrição vinculada, sessão ou eventual). 

  O índice é atualizado de forma síncrona como os itens de atualização. Por exemplo, inserir, substituir, atualização e eliminar operações num item resultará na atualização do índice. Indexação consistente suporta consultas consistentes ao custo de afetar o débito de escrita. A redução no débito de escrita depende "caminhos incluído na indexação" e o "nível de consistência". Consistência de modo a indexação foi concebida para escrita rapidamente e consulta imediatamente cargas de trabalho.

* **Nenhum**: Um contentor que tem um nenhum modo de índice não tem nenhum índice associado ao mesmo. Isto é normalmente utilizado se a base de dados do Cosmos do Azure é utilizado como um armazenamento de chave-valor e itens são acedidos apenas por sua propriedade de ID.

  > [!NOTE]
  > Configurar o modo de indexação como um None tem o efeito colateral de remover qualquer índice existente. Deve utilizar esta opção se os padrões de acesso requerem o ID ou Self-apenas a ligação.

Níveis de consistência de consulta são mantidos semelhantes para as operações de leitura normais. Base de dados do Cosmos do Azure devolve um erro se consultar o contentor que tem um nenhum modo de indexação. Pode executar as consultas como verificações por meio do explícita **x-ms-documentdb-enable-análise** cabeçalho na REST API ou a **EnableScanInQuery** pedido de opção com o SDK de .NET. Algumas consultas a recursos, como ORDER BY não são atualmente suportadas com **EnableScanInQuery**, porque eles impor um índice correspondente.

> [!NOTE]
> Azure Cosmos DB tem um terceiro, o modo de indexação lento. No entanto isso está sendo com ênfase reduzida porque o desempenho da consulta e o custo podem ser imprevisíveis. Recomendamos que utilize a indexação de modo consistente.

## <a name="modifying-the-indexing-policy"></a>Modificar a política de indexação

No Azure Cosmos DB, pode atualizar a política de indexação de um contentor em qualquer altura. Uma alteração na política num contentor do Cosmos do Azure de indexação pode levar a uma alteração na forma do índice. Esta alteração afeta os caminhos que podem ser indexados, sua precisão e o modelo de consistência do índice em si. Uma alteração na política de indexação com eficiência requer uma transformação do índice antigo num novo índice.

### <a name="index-transformations"></a>Transformações de índice

Todas as transformações de índice são executadas online. Os itens indexados pela política antiga com eficiência são transformados pela nova política sem afetar a disponibilidade de escrita ou o débito aprovisionado para o contentor. A consistência de leitura e escrita é de operações que são executadas utilizando a API REST, SDKs, ou a partir de procedimentos armazenados e acionadores não são afetados durante a transformação de índice.

Alterar a política de indexação é uma operação assíncrona e o tempo para concluir a operação depende do número de itens, débito aprovisionado e o tamanho dos itens. Enquanto a reindexação está em curso, a consulta não pode retornar todos os resultados correspondentes, se as consultas usar o índice que está a ser modificado e consultas não retornará quaisquer erros/falhas. Embora a reindexação está em curso, as consultas são eventualmente consistentes, independentemente da configuração do modo de indexação. Depois do índice de transformação estiver concluída, irá continuar a ver resultados consistentes. Isto aplica-se às consultas emitidas por interfaces, como a REST API, SDKs, ou procedimentos armazenados e acionadores. Transformação de índice é executada de forma assíncrona em segundo plano nas réplicas através dos recursos sobressalentes disponíveis para uma réplica específica.

Todas as transformações de índice são feitas no local. O Azure Cosmos DB não mantém duas cópias do índice. Portanto, não existe espaço em disco adicional é necessário ou consumido nos seus contentores, enquanto ocorre a transformação de índice.

Quando alterar a política de indexação, as alterações são aplicadas para mover do índice antigo para o novo índice baseiam-se principalmente nas configurações de modo indexação. Indexação de modo de configurações desempenham um papel importante em comparação com outras propriedades, como caminhos incluído/excluído, tipos de índice e precisão.

Se a indexação de utilização de políticas novas e antigas **consistência** indexação, a base de dados do Cosmos do Azure realiza uma transformação de índice online. Não é possível aplicar a outra alteração na diretiva indexação que tenha o modo de indexação consistente enquanto a transformação está em curso. Quando move para nenhum modo de indexação, o índice é removido imediatamente. Mover para None é útil quando deseja cancelar uma transformação em andamento e comece do zero com uma política de indexação diferente.

Para transformação do índice com êxito a concluir, certifique-se de que o contentor tem o espaço de armazenamento suficiente. Se o contentor de atingir a quota de armazenamento, a transformação de índice é colocada em pausa. Transformação de índice retoma automaticamente quando o espaço de armazenamento está disponível, por exemplo, ao eliminar alguns itens.

## <a name="modifying-the-indexing-policy---examples"></a>Modificar a política de indexação - exemplos

Seguem-se os casos de utilização mais comuns em que atualizar uma política de indexação:

* Se pretender ter resultados consistentes durante o funcionamento normal, mas o fallback para o **None** indexação modo durante importações de dados em massa.

* Se pretender começar a utilizar funcionalidades de indexação no seus contentores do Azure Cosmos atuais. Por exemplo, pode utilizar consultas geoespaciais, que requer que o tipo de índice geográfico ou ORDER BY / consultas de intervalo, que requerem o tipo de índice do intervalo de cadeia de caracteres de cadeias de caracteres.

* Se pretender selecionar manualmente as propriedades para ser indexados e alterá-los ao longo do tempo que se ajustar às suas cargas de trabalho.

* Se quiser otimizar a precisão de indexação para melhorar o desempenho de consulta ou para reduzir o armazenamento consumido.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre a indexação nos seguintes artigos:

* [Descrição geral de indexação](index-overview.md)
* [Tipos de índice](index-types.md)
* [Caminhos de índice](index-paths.md)
* [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
