---
title: Otimizar o tempo de consulta ao utilizar a estratégia de armazenamento de tabela ALERTAS na base de dados do Azure para o PostgreSQL – servidor único
description: Este artigo descreve como otimizar o tempo de consulta com a estratégia de armazenamento de tabela ALERTAS numa base de dados do Azure para PostgreSQL - único servidor.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066988"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Otimizar o tempo de consulta com a estratégia de armazenamento de tabela de alerta 
Este artigo descreve como otimizar os tempos de consultas com a estratégia de armazenamento de tabela do armazenamento de atributo superdimensionadas técnica (alerta).

## <a name="toast-table-storage-strategies"></a>Estratégias de armazenamento de tabela de alerta
Quatro estratégias diferentes são utilizadas para armazenar colunas no disco que pode utilizar o alerta. Eles representam várias combinações entre compactação e o armazenamento de fora de linha. A estratégia pode ser definida no nível de tipo de dados e ao nível da coluna.
- **Sem formatação** impede o armazenamento de compactação ou fora de linha. Desativa a utilização de cabeçalhos de byte único para tipos de varlena. Simples é a estratégia apenas possível para colunas de tipos de dados que não é possível utilizar o alerta.
- **Expandido** permite o armazenamento de compactação e fora de linha. Expandido é a predefinição para a maioria dos tipos de dados que pode utilizar o alerta. Compressão é a primeira tentativa. Armazenamento de fora de linha é tentado se a linha ainda é demasiado grande.
- **Externo** permite o armazenamento fora de linha, mas não a compressão. Utilização externa faz operações de subcadeia em grandes colunas texto e bytea mais rápidas. Esta rapidez é fornecido com a penalidade de maior espaço de armazenamento. Estas operações são otimizadas para obter apenas as partes necessárias do valor fora da linha quando ele não é compactado.
- **Main** permite a compactação, mas não fora de linha de armazenamento. Armazenamento de fora de linha continua a ser feito para este tipo de colunas, mas apenas como último recurso. Isso ocorre quando não existe nenhuma outra forma de tornar a linha pequena o bastante para caber numa página.

## <a name="use-toast-table-storage-strategies"></a>Use estratégias de armazenamento de tabela de alerta
Se suas consultas aceder a tipos de dados que podem utilizar o alerta, considere a utilização da estratégia de principal em vez da opção de Extended padrão para reduzir os tempos de consulta. Main não exclui armazenamento fora de linha. Se as suas consultas não acessarem a tipos de dados que podem utilizar o alerta, poderá ser vantajoso manter a opção de Extended. Uma parte maior das linhas da tabela principal se enquadram na cache de memória intermédia partilhada, o que ajuda o desempenho.

Se tiver uma carga de trabalho que utiliza um esquema com tabelas largas e contagens de caracteres elevada, considere a utilização de tabelas de alerta do PostgreSQL. Um exemplo de tabela de cliente tinha mais de 350 colunas com várias colunas que abrangeu de 255 carateres. Depois que ela foi convertida para a tabela de alerta estratégia de Main, o seu tempo de consulta de benchmark reduzido de 4203 segundos para 467 segundos. Que é uma melhoria de 89%.

## <a name="next-steps"></a>Passos Seguintes
Reveja a sua carga de trabalho para as características anteriores. 

Reveja a seguinte documentação de PostgreSQL: 
- [Capítulo 68, armazenamento físico do banco de dados](https://www.postgresql.org/docs/current/storage-toast.html) 