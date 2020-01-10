---
title: Detecção de ameaças para serviços de dados na central de segurança do Azure | Microsoft Docs
description: Este artigo apresenta os alertas dos serviços de dados disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665739"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detecção de ameaças para serviços de dados na central de segurança do Azure

 A central de segurança do Azure analisa os logs dos serviços de armazenamento de dados e dispara alertas quando detecta uma ameaça aos seus recursos de dados. Este artigo lista os alertas que a central de segurança gera para os seguintes serviços:

* [Banco de dados SQL do Azure e Azure SQL Data Warehouse](#data-sql)
* [Storage do Azure](#azure-storage)
* [BD do Cosmos para o Azure](#cosmos-db)

## Banco de dados SQL e SQL Data Warehouse<a name="data-sql"></a>

A proteção avançada contra ameaças para o banco de dados SQL do Azure detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Você verá alertas quando houver atividades suspeitas de banco de dados, vulnerabilidades potenciais ou ataques de injeção de SQL, bem como padrões de consulta e acesso de banco de dados anormais.

A proteção avançada contra ameaças para o banco de dados SQL do Azure e o SQL fazem parte do pacote unificado do [ADS (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para recursos avançados de segurança do SQL, que abrangem os bancos de dados SQL do Azure, instâncias gerenciadas do Azure SQL Database, bancos de dados do Azure SQL data warehouse e SQL Servers em máquinas virtuais do Azure.

Para obter mais informações, veja:

* [Como habilitar a proteção avançada contra ameaças para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como habilitar a proteção avançada contra ameaças para servidores SQL em máquinas virtuais do Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para o banco de dados SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Armazenamento do Azure<a name="azure-storage"></a>

A proteção avançada contra ameaças para armazenamento (atualmente disponível somente para armazenamento de BLOBs) detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem exigir que você seja um especialista em segurança e ajuda você a gerenciar seus sistemas de monitoramento de segurança.

>[!NOTE]
>A proteção avançada contra ameaças para armazenamento não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

Para obter mais informações, veja:

* [Como habilitar a proteção avançada contra ameaças para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o armazenamento do Azure](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas de Azure Cosmos DB são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Azure Cosmos DB.

Para obter mais informações, veja:

* [Proteção avançada contra ameaças para Azure Cosmos DB (versão prévia)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para Azure Cosmos DB (versão prévia)](alerts-reference.md#alerts-azurecosmos)
