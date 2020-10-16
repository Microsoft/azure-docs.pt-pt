---
title: Otimize o tempo de consulta utilizando a estratégia de armazenamento da tabela TOAST na Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como otimizar o tempo de consulta com a estratégia de armazenamento da mesa TOAST numa Base de Dados Azure para PostgreSQL - Servidor Único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116187"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Otimize o tempo de consulta com a estratégia de armazenamento da mesa TOAST 
Este artigo descreve como otimizar os tempos de consulta com a estratégia de armazenamento de mesa de atributos de grandes dimensões (TORRADA).

## <a name="toast-table-storage-strategies"></a>Estratégias de armazenamento de mesas torradas
Quatro estratégias diferentes são usadas para armazenar colunas em disco que podem usar TORRADAs. Representam várias combinações entre a compressão e o armazenamento fora de linha. A estratégia pode ser definida ao nível do tipo de dados e ao nível da coluna.
- **A planície** evita a compressão ou o armazenamento fora de linha. Desativa a utilização de cabeçalhos de byte único para tipos varlena. Simples é a única estratégia possível para colunas de tipos de dados que não podem usar TORRADAs.
- **Estendida** permite tanto a compressão como o armazenamento fora de linha. Estendido é o padrão para a maioria dos tipos de dados que podem usar TORRADAs. A compressão é tentada primeiro. O armazenamento fora de linha é tentado se a linha ainda for muito grande.
- **O exterior** permite o armazenamento fora de linha, mas não a compressão. A utilização de external faz operações de sub-adstring em texto largo e colunas bytea mais rápidas. Esta velocidade vem com a penalização do aumento do espaço de armazenamento. Estas operações são otimizadas para obter apenas as partes necessárias do valor fora de linha quando não é comprimido.
- **O principal** permite a compressão, mas não o armazenamento fora de linha. O armazenamento fora de linha ainda é realizado para tais colunas, mas apenas como um último recurso. Ocorre quando não há outra maneira de fazer a linha pequena o suficiente para caber numa página.

## <a name="use-toast-table-storage-strategies"></a>Use estratégias de armazenamento de mesa TOAST
Se as suas consultas acederem a tipos de dados que podem utilizar o TOAST, considere utilizar a estratégia Principal em vez da opção padrão Extended para reduzir os tempos de consulta. O principal não exclui o armazenamento fora de linha. Se as suas consultas não acederem a tipos de dados que podem usar TORRADAs, pode ser benéfico manter a opção Extended. Uma porção maior das linhas da mesa principal cabe na cache do tampão partilhado, o que ajuda no desempenho.

Se tiver uma carga de trabalho que utilize um esquema com mesas largas e altas contagens de caracteres, considere usar as tabelas PostgreSQL TOAST. Uma tabela de clientes exemplo tinha mais de 350 colunas com várias colunas que se estenderam por 255 caracteres. Depois de ter sido convertido para a estratégia principal da tabela TOAST, o seu tempo de consulta de referência reduziu-se de 4203 segundos para 467 segundos. É uma melhoria de 89%.

## <a name="next-steps"></a>Passos seguintes
Reveja a sua carga de trabalho para as características anteriores. 

Reveja a seguinte documentação PostgreSQL: 
- [Capítulo 68, Armazenamento físico de base de dados](https://www.postgresql.org/docs/current/storage-toast.html) 