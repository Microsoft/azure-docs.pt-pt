---
title: Funcionalidades suportadas do Azure SQL Edge (Pré-visualização)
description: Conheça os detalhes das funcionalidades suportadas pelo Azure SQL Edge (Preview)
keywords: introdução ao SQL Edge, o que é SQL Edge, SQL Edge visão geral
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233216"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Funcionalidades suportadas do Azure SQL Edge (Pré-visualização) 

Este artigo fornece detalhes das funcionalidades suportadas pela Azure SQL Edge. O Azure SQL Edge, é construído na versão mais recente do Microsoft SQL Server Database Engine no Linux, e suporta um subconjunto das funcionalidades suportadas no SQL Server 2019 para o Linux, além de algumas funcionalidades que não estão atualmente suportadas ou disponíveis no SQL Server 2019 no Linux ou no Windows. Para obter uma lista completa das funcionalidades suportadas no SQL Server no Linux, consulte [Edições e funcionalidades suportadas do SQL Server 2019 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Para edições e funcionalidades suportadas do SQL Server no Windows, consulte [Edições e funcionalidades suportadas do SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> A Azure SQL Edge encontra-se atualmente em (Preview) e, como tal, NÃO deve ser utilizada em ambientes de produção. A Microsoft pode recomendar a execução do Azure SQL Edge em ambientes de produção após a validação da implementação e dos cenários de caso de utilização.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge Editions

O Azure SQL Edge está disponível com duas edições diferentes ou planos de software. Estas edições têm conjuntos de funcionalidades idênticos e apenas diferem em termos dos seus direitos de utilização e da quantidade de cpu/memória a que podem aceder no sistema de acolhimento.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  Desenvolvimento apenas sku, cada recipiente Azure SQL Edge Developer está limitado a até 4 núcleos e 32 GB de memória  |
   |SQL do Azure no Edge    |  Sku de produção, cada recipiente Azure SQL Edge está limitado a até 8 núcleos e 64 GB de memória  |

## <a name="operating-system"></a>Sistema Operativo

Os recipientes Azure SQL Edge são atualmente baseados em Ubuntu 16.04 e, como tal, só são suportados para funcionar em anfitriões estivadores que executam Ubuntu 16.04 (recomendado) ou Ubuntu 18.04. O Azure SQL Edge também pode funcionar em outros anfitriões do sistema operativo, por exemplo, outras distribuições de Linux ou windows (utilizando Docker CE ou Docker EE), no entanto estas configurações não são amplamente testadas pela Microsoft.

A Azure SQL Edge é atualmente apenas suportado para implantação através do Azure IoT Edge. Para obter mais informações sobre os sistemas suportados para Azure IoT Edge, consulte [os sistemas suportados Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

A configuração recomendada para executar Azure SQL Edge no Windows é configurar um Ubuntu VM no anfitrião windows e, em seguida, executar SQL Edge dentro do linux VM.

## <a name="hardware-support"></a>Suporte de hardware

O Azure SQL Edge necessita de um processador de 64 bits, que pode ser da Intel, AMD ou ARM, com um mínimo de um processador e um GB de RAM no hospedeiro. Enquanto a pegada de memória de arranque do Azure SQL Edge está próxima dos 500 MB, a memória adicional é necessária para outros módulos IoT Edge que estão a funcionar no dispositivo de borda.

## <a name="azure-sql-edge-components"></a>Componentes de borda Azure SQL

O Azure SQL Edge suporta apenas o motor de base de dados e não inclui suporte para outros componentes disponíveis com o SQL Server 2019 no Windows ou com o SQL Server 2019 no Linux. Especificamente, o Azure SQL Edge não suporta componentes do SQL Server como Serviços de Análise, Serviços de Relatório, Serviços de Integração, Serviços de Dados Mestres, Serviços de Aprendizagem automática (Base de Dados In-Database) e Servidor de Aprendizagem automática (autónomo).

## <a name="supported-features"></a>Funcionalidades Suportadas

Além de suportar um subconjunto de SQL Server nas funcionalidades Linux, o Azure SQL Edge inclui suporte para as seguintes novas funcionalidades. 

- O SQL Streaming, que é baseado no mesmo motor que alimenta o Azure Stream Analytics, fornece capacidades de streaming de dados em tempo real em Azure SQL Edge. 
- Nova chamada de função T-SQL Date_Bucket para análise de dados da Série Tempo.
- Capacidades de Machine Learning através do tempo de funcionação ONNX, incluído com o motor SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>SQL Server em recursos Linux não suportados em Azure SQL Edge

A lista abaixo inclui o SQL Server 2019 em funcionalidades linux que não são suportadas no Azure SQL Edge.

| Área | Funcionalidade ou serviço não suportado |
|-----|-----|
| **Design de base de dados** | In-Memory OLTP e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | HierarquiaID Data Type e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Tipo de dados espaciais e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Alongamento DB e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Índices de texto completos e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão|
| &nbsp; | FileTable, FILESTREAM e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão|
| **Motor de Base de Dados** | A replicação. No entanto, o Azure SQL Edge pode ser configurado como um subscritor de um push de uma topologia de replicação. |
| &nbsp; | A polibase. No entanto, a borda Azure SQL pode ser configurada como um alvo para tabelas externas em Polybase |
| &nbsp; | Extensibilidade linguística através de Java e Faísca |
| &nbsp; | Integração do Active Directory |
| &nbsp; | Snapshots de base de dados |
| &nbsp; | Suporte para Memória Persistente |
| &nbsp; | Coordenador de transações distribuídas da Microsoft |
| &nbsp; | Governador de Recursos e Governação de Recursos IO |
| &nbsp; | Extensão da piscina tampão |
| &nbsp; | Consulta distribuída com ligações de terceiros |
| &nbsp; | Servidores Ligados |
| &nbsp; | Sistema alargado procedimentos armazenados (XP_CMDSHELL, etc.) |
| &nbsp; | Conjuntos CLR e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Funciona T-SQL dependente do CLR como ASSEMBLYProPERTY, FORMATO, PARSE, TRY_PARSE |
| &nbsp; | Vistas, funções e cláusulas de consulta dependentes do CLR |
| &nbsp; | Extensão da piscina tampão |
| &nbsp; | Correio de Base de Dados |
| **Agente do SQL Server** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Alertas |
| &nbsp; | Cópia de Segurança Gerida |
| **Alta Disponibilidade** | Sempre em Grupos de Disponibilidade  |
| &nbsp; | Grupos básicos de disponibilidade |
| &nbsp; | Sempre na instância de cluster failover |
| &nbsp; | Espelhamento da Base de Dados |
| &nbsp; | Hot add memory e CPU |
| **Segurança** | Gestão de Chaves Extensíveis |
| &nbsp; | Integração de diretórios ativos|
| &nbsp; | Apoio a Enclaves Seguros|
| **Serviços** | Navegador de servidor SQL |
| &nbsp; | Aprendizagem automática através de R e Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Serviços de Qualidade de Dados |
| &nbsp; | Principais Serviços de Dados |
| &nbsp; | Repetição distribuída |
| **Gestão** | Ponto de Controlo de Utilidade do Servidor SQL |

## <a name="next-steps"></a>Passos Seguintes

- [Implementar borda Azure SQL](deploy-portal.md)
- [Configure Azure SQL Edge](configure.md)
- [Ligue-se a uma instância de Azure SQL Edge usando ferramentas de clientes do SQL Server](connect.md)
- [Bases de dados de backup e restauro em Azure SQL Edge](backup-restore.md)
