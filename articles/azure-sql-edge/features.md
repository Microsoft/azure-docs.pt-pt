---
title: Características suportadas do Azure SQL Edge
description: Conheça os detalhes das funcionalidades suportadas pela Azure SQL Edge.
keywords: introdução ao SQL Edge, o que é SQL Edge, SQL Edge visão geral
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392032"
---
# <a name="supported-features-of-azure-sql-edge"></a>Características suportadas do Azure SQL Edge 

O Azure SQL Edge é construído na versão mais recente do SQL Database Engine. Suporta um subconjunto das funcionalidades suportadas no SQL Server 2019 no Linux, além de algumas funcionalidades que não estão atualmente suportadas ou disponíveis no SQL Server 2019 no Linux (ou no SQL Server no Windows).

Para obter uma lista completa das funcionalidades suportadas no SQL Server no Linux, consulte [Edições e funcionalidades suportadas do SQL Server 2019 no Linux](/sql/linux/sql-server-linux-editions-and-components-2019). Para edições e funcionalidades suportadas do SQL Server no Windows, consulte [Edições e funcionalidades suportadas do SQL Server 2019 (15.x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Edições Azure SQL Edge

O Azure SQL Edge está disponível com duas edições diferentes ou planos de software. Estas edições têm conjuntos de funcionalidades idênticos, e apenas diferem em termos dos seus direitos de utilização e da quantidade de memória e núcleos que podem aceder no sistema de anfitrião.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  Apenas para desenvolvimento. Cada recipiente Azure SQL Edge Developer está limitado a até 4 núcleos e memória de 32 GB.  |
   |SQL do Azure no Edge    |  Para produção. Cada recipiente Azure SQL Edge está limitado a até 8 núcleos e 64 GB de memória.  |

## <a name="operating-system"></a>Sistema operativo

Os recipientes Azure SQL Edge são baseados em Ubuntu 18.04, e como tal só são suportados para funcionar em anfitriões Docker que executam Ubuntu 18.04 LTS (recomendado) ou Ubuntu 20.04 LTS. É possível executar recipientes Azure SQL Edge em outros anfitriões do sistema operativo, por exemplo, pode funcionar em outras distribuições de Linux ou windows (usando Docker CE ou Docker EE), no entanto a Microsoft não recomenda que o faça, uma vez que esta configuração pode não ser testada extensivamente.

A configuração recomendada para executar Azure SQL Edge no Windows é configurar um Ubuntu VM no anfitrião do Windows e, em seguida, executar Azure SQL Edge dentro do Linux VM.

O sistema de ficheiros recomendado e suportado para Azure SQL Edge é EXT4 e XFS. Se estiverem a ser utilizados volumes persistentes para apoiar o armazenamento da base de dados Azure SQL Edge, então o sistema de ficheiros hospedeiros subjacentes tem de ser EXT4 e XFS.

## <a name="hardware-support"></a>Suporte de hardware

O Azure SQL Edge necessita de um processador de 64 bits (x64 ou ARM64), com um mínimo de um processador e um RAM GB no hospedeiro. Enquanto a pegada de memória de arranque do Azure SQL Edge está próxima dos 450MB, a memória adicional é necessária para outros módulos ou processos IoT Edge em execução no dispositivo de borda. Os requisitos reais de memória e CPU para Azure SQL Edge variarão em função da complexidade da carga de trabalho e do volume de dados que estão a ser tratados. Ao escolher um hardware para a sua solução, a Microsoft recomenda que faça testes de desempenho extensivos para garantir que as características de desempenho necessárias para a sua solução sejam satisfeitas.  

## <a name="azure-sql-edge-components"></a>Componentes Azure SQL Edge

O Azure SQL Edge suporta apenas o motor da base de dados. Não inclui suporte para outros componentes disponíveis com SQL Server 2019 no Windows ou com SQL Server 2019 no Linux. Especificamente, o Azure SQL Edge não suporta componentes do SQL Server como Serviços de Análise, Serviços de Relatório, Serviços de Integração, Serviços de Dados Mestres, Serviços de Aprendizagem automática (Base de Dados In-Database) e Servidor de Aprendizagem automática (autónomo).

## <a name="supported-features"></a>Funcionalidades suportadas

Além de suportar um subconjunto de funcionalidades do SQL Server no Linux, o Azure SQL Edge inclui suporte para as seguintes novas funcionalidades: 

- O streaming SQL, que é baseado no mesmo motor que alimenta o Azure Stream Analytics, fornece capacidades de streaming de dados em tempo real em Azure SQL Edge. 
- A função T-SQL `Date_Bucket` exige Time-Series análise de dados.
- Capacidades de aprendizagem automática através do tempo de funcionação ONNX, incluído com o motor SQL.

## <a name="unsupported-features"></a>Funcionalidades não suportadas

A lista seguinte inclui o SQL Server 2019 em funcionalidades linux que não são suportadas atualmente no Azure SQL Edge.

| Área | Funcionalidade ou serviço não suportado |
|-----|-----|
| **Design de base de dados** | Em memória OLTP, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão. |
| &nbsp; | `HierarchyID` tipo de dados, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão. |
| &nbsp; | `Spatial` tipo de dados, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão. |
| &nbsp; | Stretch DB, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão. |
| &nbsp; | Índices de texto completo e pesquisa, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão.|
| &nbsp; | `FileTable`, `FILESTREAM` e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão.|
| **Motor de base de dados** | A replicação. Note que pode configurar o Azure SQL Edge como um subscritor de um push topology. |
| &nbsp; | A polibase. Note que pode configurar a Borda SQL do Azure como um alvo para tabelas externas na Polybase. |
| &nbsp; | Extensibilidade linguística através de Java e Faísca. |
| &nbsp; | Integração do Diretório Ativo. |
| &nbsp; | Redução automática da base de dados. A propriedade de redução automática para uma base de dados pode ser definida usando o `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` comando, no entanto essa alteração não tem qualquer efeito. A tarefa de redução automática não será executada na base de dados. Os utilizadores ainda podem reduzir os ficheiros de base de dados utilizando os comandos 'DBCC'. |
| &nbsp; | Fotos da base de dados. |
| &nbsp; | Apoio à memória persistente. |
| &nbsp; | Coordenador de transações distribuído da Microsoft. |
| &nbsp; | Governador de recursos e governação de recursos IO. |
| &nbsp; | Extensão da piscina tampão. |
| &nbsp; | Consulta distribuída com ligações de terceiros. |
| &nbsp; | Servidores ligados. |
| &nbsp; | Sistema alargado procedimentos armazenados (tais como `XP_CMDSHELL` ). |
| &nbsp; | Conjuntos CLR, e comandos DDL relacionados e funções Transact-SQL, vistas de catálogo e vistas dinâmicas de gestão. |
| &nbsp; | Funções T-SQL dependentes do CLR, tais `ASSEMBLYPROPERTY` `FORMAT` como, `PARSE` , e `TRY_PARSE` . |
| &nbsp; | Vistas, funções e cláusulas de consulta dependentes do CLR. |
| &nbsp; | Extensão da piscina tampão. |
| &nbsp; | Correio da base de dados. |
| &nbsp; | Mediador de Serviço |
| &nbsp; | Gestão Baseada em Políticas |
| &nbsp; | Armazém de Dados de Gestão |
| &nbsp; | Bases de Dados Contidas |
| **Agente do SQL Server** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS e SSRS. |
| &nbsp; | Os alertas. |
| &nbsp; | Conseguiu reforços. |
| **Alta Disponibilidade** | Sempre em grupos de disponibilidade.  |
| &nbsp; | Grupos básicos de disponibilidade. |
| &nbsp; | Sempre na instância de cluster de failover. |
| &nbsp; | Espelhamento da base de dados. |
| &nbsp; | Hot add memory e CPU. |
| **Segurança** | Gestão de chaves extensível. |
| &nbsp; | Integração do Diretório Ativo.|
| &nbsp; | Apoio a enclaves seguros.|
| **Serviços** | Navegador de servidor SQL. |
| &nbsp; | Machine Learning através de R e Python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Serviços de Análise. |
| &nbsp; | Serviços de Reportagem. |
| &nbsp; | Serviços de Qualidade de Dados. |
| &nbsp; | Serviços De Dados Mestres. |
| &nbsp; | Repetição distribuída. |
| **Gestão** | Ponto de controlo de utilidade do servidor SQL. |

## <a name="next-steps"></a>Passos seguintes

- [Implementar borda Azure SQL](deploy-portal.md)
- [Configure Azure SQL Edge](configure.md)
- [Ligue-se a uma instância de Azure SQL Edge usando ferramentas de clientes do SQL Server](connect.md)
- [Backup e restaurar bases de dados em Azure SQL Edge](backup-restore.md)