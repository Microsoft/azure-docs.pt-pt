---
title: O Azure SQL Data Warehouse Gen2 suporta camadas inferiores de computação | Documentos da Microsoft
description: Agora, o Azure SQL Data Warehouse Gen2 suporta camadas inferiores de computação
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 22993395d60df23533b67ec48d0ea7f979d73e22
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692467"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Suporte do Azure SQL Data Warehouse Gen2 para camadas inferiores de computação

Microsoft está ajudando a reduzir o custo ao nível da entrada de executar um armazém de dados capaz de processar exigentes consultas ao adicionar a camadas inferiores de computação para o extremamente rápido do Azure SQL Data Warehouse Gen2. Os clientes podem funcionalidades de experiência do Azure SQL Data Warehouse líderes desempenho, flexibilidade e segurança a partir do cDWU 100 (unidades de armazém de dados) e dimensione a 30 000 cDWU em minutos. Os clientes podem beneficiar do desempenho de geração 2 e a flexibilidade com camadas de computação inferiores. 

Ao remover o ponto de entrada para armazenamento de dados de próxima geração, Microsoft abre as portas para os clientes baseadas no valor que pretendem avaliar todas as vantagens de um armazém de dados segura de alto desempenho sem adivinhar qual ambiente de avaliação é melhor para eles.  Os clientes poderão iniciar como tão baixo como 100 cDWU, para baixo do ponto de entrada de 500 cDWU atual.  SQL Data Warehouse Gen2 continua a suportar a colocação em pausa e retomar as operações e vai para além de apenas a flexibilidade na computação.  Colocação em cache adaptável funcionalidades de experiências de voltar a colocar em média de 5 vezes mais desempenho em comparação comparada a mesma unidade do armazém de dados no e geração 2 também suporta a capacidade de armazenamento de arquivo de colunas ilimitado, juntamente com mais de 2,5 vezes mais memória por consulta, até a 128 consultas em simultâneo Geração 1 ao mesmo preço.  As cópias de segurança georredundante são padrão para Gen2 com proteção de dados garantida incorporada. Azure SQL Data Warehouse Gen2 está pronto para ser dimensionado quando estiver.

Atualmente o portal não suporta a implementação de ou dimensionar para as instâncias de geração 2 de escalão inferior. Estamos a trabalhar arduamente para ativar esta funcionalidade, até lá, [submeter um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) se desejar aproveitar deste novo escalão.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Introdução ao Azure SQL Data Warehouse Gen2 

Os clientes podem optar por implementar uma nova instância de geração 2 ou atualiza uma instância de armazém de dados de geração 1 existente para experimentar a flexibilidade e desempenho do armazenamento de próxima geração dados. 

Tente [escalão de geração 2 otimizada de computação de armazém de dados SQL do Azure.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Atualize [do Azure SQL dados armazém computação otimizada geração 1 para ger2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) Watch Azure SQL Data Warehouse Gen2 em ação neste [vídeo do Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Regiões suportadas para os escalões de computação mais baixos

- US1 leste 
- E.U.A. Leste 2
- Europa Ocidental
- Europa do Norte
- E.U.A. Oeste 2
- Sudeste Asiático
- Centro-Sul
- EUA Central 
- Ásia Oriental
- Leste do Japão
- Índia Central
- Leste da Austrália
- Sudeste da Austrália
- Canadá Central
- Oeste do Japão 
- Canadá Central

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](upgrade-to-latest-generation.md) sobre o desempenho com otimização de computação ao atualizar o SQL Data Warehouse. 
