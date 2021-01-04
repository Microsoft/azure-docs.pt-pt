---
title: Saída Azure Synapse Analytics da Azure Stream Analytics
description: Este artigo descreve a Azure Synapse Analytics como uma saída para o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 65752321f94bfa1403839889ead284d46b5f7191
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740067"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Saída Azure Synapse Analytics da Azure Stream Analytics

[A Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) é um serviço de análise ilimitado que reúne armazenamento de dados da empresa e análise de Big Data. 

Os empregos do Azure Stream Analytics podem ser colocados numa mesa de bilhar SQL dedicada no Azure Synapse Analytics e podem processar taxas de produção até 200MB/seg. Isto suporta as necessidades de análise em tempo real mais exigentes e necessidades de processamento de dados em percursos quentes para cargas de trabalho, tais como relatórios e dashboarding.  

A mesa de bilhar SQL dedicada deve existir antes de poder adicioná-la como saída para o seu trabalho stream Analytics. O esquema da mesa deve coincidir com os campos e os seus tipos na produção do seu trabalho. 

Para utilizar o Azure Synapse como saída, tem de se certificar de que tem a conta de armazenamento configurada. Navegue para as definições da conta de armazenamento para configurar a conta de armazenamento. Apenas são permitidos os tipos de conta de armazenamento que suportam tabelas: V2 de uso geral e V1 de uso geral. Selecione Apenas Standard Tier. O nível premium não é suportado.

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação da saída am Azure Synapse Analytics.

|Nome da propriedade|Description|
|-|-|
|Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta base de dados. |
|Base de Dados |nome de piscina SQL dedicado para onde está a enviar a sua saída. |
|Nome do servidor |Nome do servidor Azure Synapse.  |
|Nome de utilizador |O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
|Palavra-passe |A senha para ligar à base de dados. |
|Tabela  | O nome da mesa onde a saída está escrita. O nome da mesa é sensível a maiôs. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera.|

## <a name="next-steps"></a>Passos seguintes

* [Utilize identidades geridas para aceder à Base de Dados Azure SQL ou Azure Synapse Analytics a partir de um trabalho de Azure Stream Analytics (Preview)](sql-database-output-managed-identity.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)