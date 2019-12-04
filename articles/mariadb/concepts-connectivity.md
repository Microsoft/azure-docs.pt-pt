---
title: Erros de conectividade transitórios-banco de dados do Azure para MariaDB
description: Saiba como lidar com erros de conectividade transitórios para o banco de dados do Azure para MariaDB.
keywords: conexão do MySQL, Cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f061f9cc6d3f03acf01995e2632b229aaea5ab8f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772867"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Tratamento de erros de conectividade transitórios para o banco de dados do Azure para MariaDB

Este artigo descreve como lidar com erros transitórios se conectando ao banco de dados do Azure para MariaDB.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido por si só. Geralmente, esses erros são manifestados como uma conexão com o servidor de banco de dados que está sendo Descartado. Também não é possível abrir novas conexões com um servidor. Erros transitórios podem ocorrer por exemplo, quando ocorre falha de hardware ou de rede. Outro motivo pode ser uma nova versão de um serviço PaaS que está sendo distribuído. A maioria desses eventos é automaticamente mitigada pelo sistema em menos de 60 segundos. Uma prática recomendada para projetar e desenvolver aplicativos na nuvem é esperar erros transitórios. Suponha que eles possam ocorrer em qualquer componente a qualquer momento e que haja a lógica apropriada em vigor para lidar com essas situações.

## <a name="handling-transient-errors"></a>Manipulando erros transitórios

Os erros transitórios devem ser tratados usando a lógica de repetição. Situações que devem ser consideradas:

* Ocorrerá um erro quando você tentar abrir uma conexão
* Uma conexão ociosa é descartada no lado do servidor. Quando você tenta emitir um comando, ele não pode ser executado
* Uma conexão ativa que atualmente está executando um comando é descartada.

O primeiro e o segundo caso são bastante diretos para manipular. Tente abrir a conexão novamente. Quando tiver sucesso, o erro transitório foi mitigado pelo sistema. Você pode usar o banco de dados do Azure para MariaDB novamente. Recomendamos que haja esperas antes de tentar novamente a conexão. Fazer logoff se as tentativas iniciais falharem. Dessa forma, o sistema pode usar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a ser seguido é:

* Aguarde 5 segundos antes da primeira tentativa.
* Para cada repetição seguinte, aumente a espera exponencialmente, até 60 segundos.
* Defina um número máximo de repetições no ponto em que seu aplicativo considera que a operação falhou.

Quando uma conexão com uma transação ativa falha, é mais difícil lidar com a recuperação corretamente. Há dois casos: se a transação era somente leitura por natureza, é seguro reabrir a conexão e tentar novamente a transação. Se, no entanto, se a transação também estiver gravando no banco de dados, você deverá determinar se a transação foi revertida ou se foi bem-sucedida antes que ocorreu o erro transitório. Nesse caso, você pode simplesmente não ter recebido a confirmação de confirmação do servidor de banco de dados.

Uma maneira de fazer isso é gerar uma ID exclusiva no cliente que é usada para todas as tentativas. Você passa essa ID exclusiva como parte da transação para o servidor e a armazena em uma coluna com uma restrição UNIQUE. Dessa forma, você pode repetir a transação com segurança. Ele será bem sucedido se a transação anterior tiver sido revertida e a ID exclusiva gerada pelo cliente ainda não existir no sistema. Ele falhará indicando uma violação de chave duplicada se a ID exclusiva tiver sido armazenada anteriormente porque a transação anterior foi concluída com êxito.

Quando seu programa se comunicar com o banco de dados do Azure para MariaDB por meio de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Certifique-se de testar a lógica de repetição. Por exemplo, tente executar seu código ao escalar ou reduzir verticalmente os recursos de computação do banco de dados do Azure para o servidor MariaDB. Seu aplicativo deve lidar com o breve tempo de inatividade encontrado durante essa operação sem problemas.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de ligação ao Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
