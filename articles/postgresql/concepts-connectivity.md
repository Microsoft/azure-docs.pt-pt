---
title: Lidar com erros de conectividade transitórios - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como lidar com erros de conectividade transitórios para a Base de Dados Azure para PostgreSQL - Servidor Único.
keywords: conexão pós-quadrado,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 026a0edf24d349c4b445d6229d3b1ad73decf87d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82097834"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Processamento de erros de conectividade transitórios da Base de Dados do Azure para PostgreSQL – Servidor Único

Este artigo descreve como lidar com erros transitórios ligados à Base de Dados Azure para PostgreSQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como falha transitória, é um erro que se resolverá sozinho. Normalmente, estes erros manifestam-se como uma ligação ao servidor de base de dados que está a ser largada. Também não podem ser abertas novas ligações a um servidor. Podem ocorrer erros transitórios, por exemplo, quando o hardware ou falha de rede acontece. Outra razão poderia ser uma nova versão de um serviço PaaS que está a ser lançado. A maioria destes eventos são automaticamente atenuados pelo sistema em menos de 60 segundos. Uma das melhores práticas para conceber e desenvolver aplicações na nuvem é esperar erros transitórios. Assuma que podem acontecer em qualquer componente a qualquer momento e ter a lógica adequada para lidar com estas situações.

## <a name="handling-transient-errors"></a>Manipulação de erros transitórios

Os erros transitórios devem ser manuseados utilizando lógica de repetição. Situações que devem ser consideradas:

* Um erro ocorre quando se tenta abrir uma ligação
* Uma ligação ociosa é deixada no lado do servidor. Quando se tenta emitir um comando, não pode ser executado.
* Uma ligação ativa que está atualmente a executar um comando é largada.

O primeiro e o segundo caso são bastante diretos para a frente. Tente abrir a ligação de novo. Quando tiver sucesso, o erro transitório foi atenuado pelo sistema. Pode utilizar novamente a sua Base de Dados Azure para PostgreSQL. Recomendamos que tenha esperado antes de voltar a tentar a ligação. Afaste-se se as primeiras tentativas falharem. Desta forma, o sistema pode utilizar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a seguir é:

* Aguarde 5 segundos antes da primeira repetição.
* Para cada uma das seguintes, aumente a espera exponencialmente, até 60 segundos.
* Descoda um número máximo de recauchutagens e o seu pedido considera que a operação falhou.

Quando uma ligação com uma transação ativa falha, é mais difícil lidar corretamente com a recuperação. Há dois casos: Se a transação foi apenas de natureza lida, é seguro reabrir a ligação e voltar a tentar a transação. Se, no entanto, a transação também estava a escrever para a base de dados, deve determinar se a transação foi revertida ou se foi bem sucedida antes do erro transitório acontecer. Nesse caso, pode não ter recebido o reconhecimento do servidor de base de dados.

Uma maneira de fazer isso, é gerar uma identificação única no cliente que é usado para todas as retrórias. Você passa este ID único como parte da transação para o servidor e para armazená-lo em uma coluna com uma restrição única. Desta forma, pode voltar a tentar a transação com segurança. Será bem sucedido se a transação anterior for revertida e o cliente gerado ID único ainda não existe no sistema. Falhará indicando uma violação de chave duplicada se o ID único foi previamente armazenado porque a transação anterior foi concluída com sucesso.

Quando o seu programa comunicar com a Azure Database para PostgreSQL através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Certifique-se de testar a sua lógica de relículo. Por exemplo, tente executar o seu código enquanto escala os recursos de computação do seu servidor Azure Database para o servidor PostgreSQL. A sua aplicação deve tratar do breve tempo de paragem que se encontra durante esta operação sem problemas.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de ligação à Base de Dados do Azure para PostgreSQL](howto-troubleshoot-common-connection-issues.md)
