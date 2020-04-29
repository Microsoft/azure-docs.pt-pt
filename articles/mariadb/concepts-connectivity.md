---
title: Erros transitórios de conectividade - Base de Dados Azure para MariaDB
description: Aprenda a lidar com erros de conectividade transitórios para a Base de Dados Azure para O MariaDB.
keywords: conexão mysql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 3e6c5c8b6c3f118f1b19c5e2b3455f1f66f7e70e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100809"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Tratamento de erros transitórios de conectividade para a Base de Dados Azure para MariaDB

Este artigo descreve como lidar com erros transitórios que ligam a Base de Dados Azure para O MariaDB.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que se resolverá sozinho. Normalmente, estes erros manifestam-se como uma ligação ao servidor de base de dados que está a ser eliminado. Também não podem ser abertas novas ligações a um servidor. Erros transitórios podem ocorrer, por exemplo, quando o hardware ou falha de rede acontece. Outra razão poderia ser uma nova versão de um serviço PaaS que está a ser lançado. A maioria destes eventos são automaticamente atenuados pelo sistema em menos de 60 segundos. Uma boa prática para conceber e desenvolver aplicações na nuvem é esperar erros transitórios. Assuma que podem acontecer em qualquer componente a qualquer momento e ter a lógica adequada para lidar com estas situações.

## <a name="handling-transient-errors"></a>Manuseamento de erros transitórios

Os erros transitórios devem ser tratados utilizando a lógica de retry. Situações que devem ser consideradas:

* Um erro ocorre quando se tenta abrir uma ligação
* Uma ligação inativa é deixada no lado do servidor. Quando se tenta emitir um comando, não pode ser executado.
* Uma ligação ativa que está atualmente a executar um comando é abandonada.

O primeiro e o segundo caso são bastante diretos para lidar. Tente abrir a ligação de novo. Quando se consegue, o erro transitório foi atenuado pelo sistema. Pode voltar a utilizar a sua Base de Dados Azure para o MariaDB. Recomendamos que tenha esperas antes de voltar a experimentar a ligação. Afaste-se se as tentativas iniciais falharem. Desta forma, o sistema pode utilizar todos os recursos disponíveis para ultrapassar a situação de erro. Um bom padrão a seguir é:

* Aguarde 5 segundos antes da primeira tentativa.
* Para cada tentativa seguinte, o aumento exponencial da espera, até 60 segundos.
* Detete um número máximo de repetições no momento em que a sua aplicação considera que a operação falhou.

Quando uma ligação com uma transação ativa falha, é mais difícil lidar corretamente com a recuperação. Há dois casos: Se a transação foi lida apenas na natureza, é seguro reabrir a ligação e voltar a tentar a transação. Se, no entanto, se a transação também estava escrita na base de dados, deve determinar se a transação foi reposta ou se foi bem sucedida antes do erro transitório. Nesse caso, pode não ter recebido o reconhecimento do servidor de base de dados.

Uma maneira de fazer isto, é gerar uma identificação única no cliente que é usado para todas as tentativas. Passa este ID único como parte da transação para o servidor e armazena-o numa coluna com um constrangimento único. Desta forma, pode voltar a tentar a transação com segurança. Será bem sucedido se a transação anterior foi revertida e o cliente gerou identificação única ainda não existe no sistema. Falhará indicando uma violação da chave duplicada se o ID único foi previamente armazenado porque a transação anterior foi concluída com sucesso.

Quando o seu programa comunica com a Base de Dados Azure para MariaDB através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de retry para erros transitórios.

Certifique-se de testar a lógica de novo. Por exemplo, tente executar o seu código enquanto escala para cima ou para baixo os recursos computacionais do seu servidor Azure. A sua aplicação deve tratar do breve tempo de paragem que se encontra durante esta operação sem qualquer problema.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de ligação ao Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
