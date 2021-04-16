---
title: Query Store boas práticas em Azure Database for PostgreSQL - Flex Server
description: Este artigo descreve as melhores práticas para a Loja de Consultas na Base de Dados Azure para PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559237"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Melhores práticas para Loja de Consultas - Servidor Flexível

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Flex Server versões 11, 12

Este artigo descreve as melhores práticas para a utilização da Loja de Consultas na Base de Dados Azure para PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Desa ajuste o modo de captura de consulta ideal
Deixe a Query Store capturar os dados que lhe interessam. 

|**pg_qs.consulta_capture_mode** | **Cenário**|
|---|---|
|_Todos_  |Analise cuidadosamente a sua carga de trabalho em termos de todas as consultas e suas frequências de execução e outras estatísticas. Identifique novas consultas na sua carga de trabalho. Detete se as consultas ad hoc forem usadas para identificar oportunidades para o utilizador ou parametrização automática. _Tudo_ vem com um aumento do custo de consumo de recursos. |
|_Início_  |Concentre a sua atenção em consultas de topo - as emitidas pelos clientes.
|_Nenhuma_ |Se for definida para Nenhuma, a Loja de Consultas não capturará novas consultas. Já capturou um conjunto de consultas e uma janela de tempo que quer investigar e quer eliminar as distrações que outras consultas podem introduzir. _Nenhum_ é adequado para ambientes de teste e marcação de bancada. _Nenhum_ deve ser usado com cuidado, pois pode perder a oportunidade de rastrear e otimizar novas consultas importantes. |


> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode não for _nenhum,_ a definição pgms_wait_sampling.query_capture_mode não tem qualquer efeito. 


## <a name="keep-the-data-you-need"></a>Guarde os dados de que precisa
O parâmetro **pg_qs.retenção_period_in_days** especifica em dias o período de retenção de dados para a Loja de Consultas. Os dados de consulta e estatística mais antigos são eliminados. Por predefinição, a Query Store está configurada para reter os dados durante 7 dias. Evite guardar dados históricos que não pretende utilizar. Aumente o valor se precisar de manter os dados por mais tempo.


## <a name="next-steps"></a>Passos seguintes
- Saiba como obter ou definir parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md) ou o [Azure CLI](howto-configure-server-parameters-using-cli.md).
