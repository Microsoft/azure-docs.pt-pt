---
title: Otimizar o tempo de consulta utilizando a estratégia de armazenamento de mesa TOAST na Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como otimizar o tempo de consulta com a estratégia de armazenamento de mesa TOAST numa Base de Dados Azure para PostgreSQL - Servidor Único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066988"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Otimizar o tempo de consulta com a estratégia de armazenamento de mesa TOAST 
Este artigo descreve como otimizar os tempos de consulta com a estratégia de armazenamento de mesa de atributos de grandes dimensões (TOAST).

## <a name="toast-table-storage-strategies"></a>Estratégias de armazenamento de mesa toast
Quatro estratégias diferentes são usadas para armazenar colunas em disco que podem usar TORRADAS. Representam várias combinações entre compressão e armazenamento fora de linha. A estratégia pode ser definida ao nível do tipo de dados e ao nível da coluna.
- **A planície** impede a compressão ou o armazenamento fora de linha. Desativa a utilização de cabeçalhos de byte único para tipos de varlena. A planície é a única estratégia possível para colunas de tipos de dados que não podem usar TORRADAS.
- **O alargamento** permite tanto a compressão como o armazenamento fora de linha. Estendido é o padrão para a maioria dos tipos de dados que podem usar TORRADAS. A compressão é tentada primeiro. O armazenamento fora de linha é tentado se a linha ainda for muito grande.
- **O exterior** permite armazenamento fora de linha, mas não compressão. A utilização do Exterior torna as operações de subcordas em colunas de texto largo e bytea mais rápidas. Esta velocidade vem com a penalidade de um espaço de armazenamento aumentado. Estas operações são otimizadas para obter apenas as partes necessárias do valor fora de linha quando não é comprimido.
- **O principal** permite a compressão, mas não o armazenamento fora de linha. O armazenamento fora de linha ainda é realizado para tais colunas, mas apenas como um último recurso. Ocorre quando não há outra maneira de tornar a fila pequena o suficiente para caber numa página.

## <a name="use-toast-table-storage-strategies"></a>Utilize estratégias de armazenamento de mesa TOAST
Se as suas consultas acederem a tipos de dados que podem utilizar o TOAST, considere utilizar a estratégia Principal em vez da opção padrão Estendida para reduzir os tempos de consulta. O principal não exclui o armazenamento fora de linha. Se as suas consultas não acederem a tipos de dados que podem usar TORRADAS, pode ser benéfico manter a opção Extended. Uma parte maior das linhas da mesa principal encaixam na cache tampão partilhada, o que ajuda ao desempenho.

Se tiver uma carga de trabalho que utilize um esquema com mesas largas e altas contagens de caracteres, considere usar mesas de TOAST PostgreSQL. Uma tabela de clientes de exemplo tinha mais de 350 colunas com várias colunas que se estenderam por 255 caracteres. Depois de ter sido convertido para a mesa TOAST A estratégia principal, o seu tempo de consulta de referência reduziu-se de 4203 segundos para 467 segundos. É uma melhoria de 89%.

## <a name="next-steps"></a>Passos seguintes
Reveja a sua carga de trabalho para as características anteriores. 

Reveja a seguinte documentação postgresql: 
- [Capítulo 68, Armazenamento físico da base de dados](https://www.postgresql.org/docs/current/storage-toast.html) 