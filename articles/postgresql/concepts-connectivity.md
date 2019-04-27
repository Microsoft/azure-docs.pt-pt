---
title: Tratamento de erros de conectividade transitória da base de dados do Azure para PostgreSQL
description: Saiba como lidar com erros de conectividade transitória da base de dados do Azure para PostgreSQL.
keywords: ligação do postgresql, cadeia de ligação, problemas de conectividade, erro transitório, erro de ligação
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 264656da38608026e3f9e866e2184ff55ba102d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559900"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-postgresql"></a>Tratamento de erros de conectividade transitória da base de dados do Azure para PostgreSQL

Este artigo descreve como lidar com erros transitórios, ligar à base de dados do Azure para PostgreSQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido de forma automática. Mais frequentemente estes erros de manifesto como uma ligação para o servidor de base de dados a ser removido. Também não não possível abrir novas ligações a um servidor. Erros transitórios podem ocorrer por exemplo quando ocorre falha de hardware ou de rede. Outra razão pode ser uma nova versão de um serviço de PaaS que está a ser lançada. A maioria desses eventos automaticamente é atenuada pelo sistema em menos de 60 segundos. Uma prática recomendada para projetar e desenvolver aplicações na cloud é esperar erros transitórios. Partem do princípio de que podem acontecer em qualquer componente em qualquer altura para ter a lógica apropriada para lidar com essas situações.

## <a name="handling-transient-errors"></a>Tratamento de erros transitórios

Erros transitórios devem ser processados com a lógica de repetição. Situações que devem ser consideradas:

* Ocorre um erro ao tentar abrir uma ligação
* Uma ligação inativa é removida no lado do servidor. Quando tenta emitir um comando não pode ser executado
* Uma ligação ativa, que atualmente é executar um comando é ignorada.

O primeiro e o segundo caso são bastante simples para processar. Tente abrir a ligação novamente. Quando tiver êxito, o erro transitório foi reduzido pelo sistema. Pode utilizar novamente a base de dados do Azure para PostgreSQL. Recomendamos ter aguarda antes de tentar novamente a ligação. Volta e se as repetições iniciais falharem. Desta forma, o sistema pode utilizar todos os recursos disponíveis para superar a situação de erro. É um bom padrão a seguir:

* Aguarde 5 segundos antes de sua primeira repetição.
* Para cada tentativa seguinte, o aumento a espera exponencialmente, até 60 segundos.
* Defina um número máximo de repetições, altura em que seu aplicativo considera a operação falhou.

Quando uma conexão com uma transação ativa falha, é mais difícil de lidar corretamente com a recuperação. Existem dois casos: Se a transação foi só de leitura por natureza, é seguro para reabrir a ligação e repita a transação. Se entretanto se a transação também estava escrevendo para a base de dados, tem de determinar se a transação foi revertida ou se ele foi concluída com êxito antes do erro transitório aconteceu. Nesse caso, poderá simplesmente não recebeu a confirmação da consolidação do servidor de base de dados.

Uma forma de fazer isso, é gerar um ID exclusivo do cliente que é utilizado para todas as repetições. Passa este ID exclusivo como parte da transação para o servidor e armazená-los numa coluna com uma restrição exclusiva. Desta forma, que pode tentar novamente a transação com segurança. Será bem sucedida se a transação anterior foi revertida e o ID exclusivo do cliente gerado ainda não existir no sistema. Ocorrerá uma falha que indica uma violação de chave duplicada, se o ID exclusivo armazenado anteriormente porque a transação anterior foi concluída com êxito.

Quando o seu programa se comunica com a base de dados do Azure para PostgreSQL através de middleware de terceiros, solicitar ao fornecedor se o middleware contém a lógica de repetição para erros transitórios.

Certifique-se testar a lógica de repetição. Por exemplo, tente executar o seu código ao mesmo tempo, aumentando ou reduzindo os recursos de computação de que a base de dados do Azure para o servidor PostgreSQL. Seu aplicativo deve manipular o tempo de inatividade breve que é encontrado durante esta operação sem problemas.

## <a name="next-steps"></a>Passos Seguintes

* [Resolver problemas de ligação à Base de Dados do Azure para PostgreSQL](howto-troubleshoot-common-connection-issues.md)
