---
title: Características suportadas do Edge Azure SQL (Pré-visualização)
description: Conheça detalhes das funcionalidades suportadas por Azure SQL Edge (Pré-visualização)
keywords: introdução ao SQL Edge, o que é SQL Edge, Visão geral sQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f0994ac1d28118869f0d5c2844a034623d101ee8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684364"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Características suportadas do Edge Azure SQL (Pré-visualização) 

Este artigo fornece detalhes das funcionalidades suportadas pelo Azure SQL Edge. O Azure SQL Edge, é construído na versão mais recente do Microsoft SQL Server Database Engine no Linux, e suporta um subconjunto das funcionalidades suportadas no SQL Server 2019 para o Linux, além de algumas funcionalidades que atualmente não são suportadas ou disponíveis no SQL Server 2019 no Linux ou no Windows. Para obter uma lista completa das funcionalidades suportadas no SQL Server no Linux, consulte [Edições e funcionalidades suportadas do SQL Server 2019 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Para edições e funcionalidades suportadas do SQL Server no Windows, consulte [Edições e funcionalidades suportadas do SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> O Azure SQL Edge encontra-se atualmente em (Preview) e, como tal, NÃO deve ser utilizado em ambientes de produção. A Microsoft pode recomendar executar o Azure SQL Edge em ambientes de produção após a validação da implementação e dos cenários de utilização.

## <a name="azure-sql-edge-editions"></a>Edições Azure SQL Edge

O Azure SQL Edge está disponível com duas edições diferentes ou planos de software. Estas edições têm conjuntos de funcionalidades idênticos e só diferem em termos dos seus direitos de utilização e da quantidade de cpu/memória a que podem aceder no sistema de acolhimento.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor de borda sql azure  |  Desenvolvimento apenas sku, cada recipiente Azure SQL Edge Developer está limitado a até 4 núcleos e 32 GB de memória  |
   |Borda SQL Azure    |  Produção sku, cada recipiente Azure SQL Edge está limitado a até 8 núcleos e 64 GB De Memória  |

## <a name="operating-system"></a>Sistema Operativo

Os contentores Azure SQL Edge são atualmente baseados em Ubuntu 16.04 e, como tal, só são suportados para funcionar em anfitriões de estivadores que executam Ubuntu 16.04 (recomendado) ou Ubuntu 18.04. O Azure SQL Edge também pode ser executado em outros anfitriões do sistema operativo, por exemplo, outras distribuições de Linux ou no Windows (utilizando Docker CE ou Docker EE), no entanto estas configurações não são extensivamente testadas pela Microsoft.

O Azure SQL Edge é atualmente suportado apenas para implantação através do Azure IoT Edge. Para obter mais informações sobre os sistemas suportados para o Azure IoT Edge, consulte os [sistemas suportados pelo Azure IoT Edge.](https://docs.microsoft.com/azure/iot-edge/support)

A configuração recomendada para executar o Azure SQL Edge no Windows é configurar um Ubuntu VM no anfitrião do Windows e, em seguida, executar O SQL Edge dentro do VM linux.

## <a name="hardware-support"></a>Suporte de hardware

O Azure SQL Edge requer um processador de 64 bits, que pode ser da Intel, AMD ou ARM, com um mínimo de um processador e um GB de RAM no hospedeiro. Embora a pegada de memória de arranque do Azure SQL Edge seja de perto de 500 MB, a memória adicional é necessária para outros módulos IoT Edge que estão em execução no dispositivo de borda.

## <a name="azure-sql-edge-components"></a>Componentes de borda SQL Azure

O Azure SQL Edge apenas suporta o motor de base de dados e não inclui suporte para outros componentes disponíveis com o SQL Server 2019 no Windows ou com o SQL Server 2019 no Linux. Especificamente, o Azure SQL Edge não suporta componentes do SQL Server como Serviços de Análise, Serviços de Reporte, Serviços de Integração, Serviços de Dados Mestres, Serviços de Aprendizagem Automática (Base de Dados) e Servidor de Aprendizagem Automática (autónomo).

## <a name="supported-features"></a>Funcionalidades Suportadas

Além de suportar um subconjunto de SQL Server nas funcionalidades linux, o Azure SQL Edge inclui suporte para as seguintes novas funcionalidades. 

- O SQL Streaming, que se baseia no mesmo motor que alimenta o Azure Stream Analytics, fornece capacidades de streaming de dados em tempo real no Azure SQL Edge. 
- Nova função T-SQL chama Date_Bucket para análise de dados da Série De Tempo.
- Capacidades de Machine Learning através do tempo de execução ONNX, incluído sem motor SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>SQL Server em funcionalidades linux não suportadas no Azure SQL Edge

A lista abaixo inclui o SQL Server 2019 nas funcionalidades Linux que atualmente não são suportadas no Azure SQL Edge.

| Área | Funcionalidade ou serviço não suportado |
|-----|-----|
| **Design de base de dados** | In-Memory OLTP e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Tipo de dados hierárquico e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Tipo de dados espaciais e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Estiramento DB e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas de gestão dinâmica |
| &nbsp; | Índices de texto completos e pesquisa e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas de gestão dinâmica|
| &nbsp; | FileTable, FILESTREAM e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão|
| **Motor de Base de Dados** | Replicação. No entanto, o Azure SQL Edge pode ser configurado como um subscritor de uma topologia de replicação. |
| &nbsp; | A Base. No entanto, o Azure SQL Edge pode ser configurado como um alvo para tabelas externas na Polybase |
| &nbsp; | A extibilidade linguística através de Java e Spark |
| &nbsp; | Integração do Active Directory |
| &nbsp; | Snapshots de base de dados |
| &nbsp; | Suporte para Memória Persistente |
| &nbsp; | Coordenador de Transações Distribuídas da Microsoft |
| &nbsp; | Governador de Recursos e Governança de Recursos Da IO |
| &nbsp; | Extensão da piscina tampão |
| &nbsp; | Consulta distribuída com conexões de terceiros |
| &nbsp; | Servidores Ligados |
| &nbsp; | Procedimentos armazenados alargados do sistema (XP_CMDSHELL, etc.) |
| &nbsp; | Conjuntos CLR e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão |
| &nbsp; | Funções T-SQL dependentes do CLR como MONTAGEM, FORMATO, PARSE, TRY_PARSE |
| &nbsp; | Pontos de vista, funções e cláusulas de catálogo dependentes de CLR |
| &nbsp; | Extensão da piscina tampão |
| &nbsp; | Correio de Base de Dados |
| **Agente do SQL Server** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Alertas |
| &nbsp; | Cópia de Segurança Gerida |
| **Elevada Disponibilidade** | Sempre em Grupos de Disponibilidade  |
| &nbsp; | Grupos básicos de disponibilidade |
| &nbsp; | Sempre na instância de cluster Failover |
| &nbsp; | Espelhamento da Base de Dados |
| &nbsp; | Hot adicionar memória e CPU |
| **Segurança** | Gestão de Chaves Extensíveis |
| &nbsp; | Integração de diretórios ativos|
| &nbsp; | Apoio aos Enclaves Seguros|
| **Serviços** | Navegador de Servidor SQL |
| &nbsp; | Aprendizagem automática através de R e Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Serviços de Qualidade de Dados |
| &nbsp; | Serviços de Dados Mestres |
| &nbsp; | Repetição distribuída |
| **Capacidade de gestão** | Ponto de controlo de utilitário do servidor SQL |

## <a name="next-steps"></a>Passos Seguintes

- [Implementar borda SQL Azure](deploy-portal.md)
- [Configure Azure SQL Edge](configure.md)
- [Ligue-se a uma instância de Azure SQL Edge utilizando ferramentas de cliente do SQL Server](connect.md)
- [Backup e restaurar bases de dados em Azure SQL Edge](backup-restore.md)
