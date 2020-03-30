---
title: O que é Azure SQL Database Edge? | Microsoft Docs
description: Saiba mais sobre o Edge de Base de Dados Azure SQL
keywords: introdução à borda da base de dados sql, o que é aresta de base de dados sql, visão geral da borda da base de dados sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246709"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>O que é a pré-visualização da borda da base de dados Azure SQL?

A Pré-visualização do Edge De base de dados Azure SQL é um motor de base de dados relacional otimizado, orientado para implementações IoT e IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. O Azure SQL Database Edge fornece capacidades para transmitir, processar e analisar dados relacionais e não relacionais, como jSON, gráfico e dados da série time, o que a torna a escolha certa para uma variedade de aplicações IoT modernas.

O Azure SQL Database Edge baseia-se nas versões mais recentes do [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), que fornece capacidades de desempenho, segurança e processamento de consultas líderes na indústria. Uma vez que o Azure SQL Database Edge é construído no mesmo motor que o SQL Server e o Azure SQL Database, fornece a mesma área de superfície de programação T-SQL que facilita e acelera o desenvolvimento de aplicações ou soluções e, ao mesmo tempo, torna a aplicação portabilidade entre dispositivos IoT Edge, centros de dados e a nuvem diretamente para a frente.

## <a name="deployment-models"></a>Modelos de implantação

O Azure SQL Database Edge está disponível no Mercado Azure e pode ser implantado como um módulo para [o Azure IoT Edge](../iot-edge/about-iot-edge.md). Para mais informações, consulte A ponta de base de [dados Azure SQL](deploy-portal.md).<br>

![Diagrama de visão geral da borda da base de dados SQL](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edições da SQL Database Edge

A SQL Database Edge está disponível com três edições ou planos de software diferentes. Estas edições têm conjuntos de funcionalidades idênticos e só diferem em termos dos seus direitos de utilização e da quantidade de cpu/memória que suportam.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor de borda de base de dados Azure SQL  |  Desenvolvimento apenas sku, cada recipiente SQL Database Edge está limitado a até 4 núcleos e 32 GB de memória  |
   |Base de Dados SQL do Azure no Edge    |  Sku de produção, cada recipiente SQL Database Edge está limitado a até 8 núcleos e 64 GB de memória. |

## <a name="pricing-and-availability"></a>Preços e Disponibilidade

A Base de Dados Azure SQL encontra-se atualmente em pré-visualização. Para obter mais informações sobre os preços e disponibilidade, consulte [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidadeentre o Edge de Base de Dados Azure SQL e o Servidor SQL, bem como as diferenças entre diferentes opções de Borda de Base de Dados Azure SQL, consulte as funcionalidades da Base de Dados de Base de [Dados SQL](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Capacidades de streaming  

O Azure SQL Database Edge fornece capacidades de streaming incorporadas para análise em tempo real e processamento complexo de eventos. A capacidade de streaming é construída utilizando as mesmas construções que o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e fornece capacidades semelhantes às [do Azure Stream Analytics no IoT Edge](../stream-analytics/stream-analytics-edge.md).

O motor de streaming do Azure SQL Database Edge foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning e Capacidades de Inteligência Artificial

O Azure SQL Database Edge fornece capacidades de aprendizagem automática e análise incorporadas, integrando o formato aberto ONNX (Open Neural Network Exchange), que permite o intercâmbio de modelos de deep learning e redes neurais entre diferentes estruturas. Para mais informações sobre onNX, consulte [aqui](https://onnx.ai/). O tempo de execução onNX proporciona a flexibilidade para desenvolver modelos num idioma ou ferramentas à sua escolha, que podem ser convertidos para o formato ONNX para execução dentro do SQL Database Edge. Para mais informações, consulte [Machine Learning e Inteligência Artificial com ONNX em SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Trabalhar com a Borda da Base de Dados Azure SQL

A Azure SQL Database Edge torna o desenvolvimento e a manutenção de aplicações mais fáceis e produtivas. Os utilizadores podem usar todas as ferramentas e habilidades familiares para construir grandes apps e soluções para as suas necessidades ioT Edge. O utilizador pode desenvolver-se no SQL Database Edge utilizando ferramentas como:

- [O portal Azure](https://portal.azure.com/) - Uma aplicação baseada na web para gerir todos os serviços Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Uma aplicação de cliente gratuita e transferível para gerir qualquer infraestrutura SQL, desde o SQL Server até à Base de Dados SQL.
- Ferramentas de dados do [Servidor SQL em Estúdio Visual](/sql/ssdt/download-sql-server-data-tools-ssdt/) - uma aplicação de cliente gratuita e transferível para o desenvolvimento de bases de dados relacionais do SQL Server, bases de dados SQL, pacotes de Serviços de Integração, modelos de dados de Serviços de Análise e Relatórios de Serviços de Informação.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uma ferramenta de base de dados gratuita, transferível e transversal para o profissional de dados que utiliza a família Microsoft de plataformas de dados no local e na nuvem no Windows, MacOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Um editor de código gratuito, transferível e aberto para Windows, macOS e Linux. Suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta do Microsoft SQL Server, Azure SQL Database e Azure SQL Data Warehouse.


## <a name="next-steps"></a>Passos seguintes

- Para obter preços e detalhes relacionados com a disponibilidade, consulte O Edge de Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database-edge/).
- Pedido para ativar o Limite de Base de Dados Azure SQL para a sua subscrição.
- Para começar, consulte o seguinte:
  - [Implementar borda de base de dados SQL através do portal Azure](deploy-portal.md)
  - [Machine Learning e Inteligência Artificial com Borda de Base de Dados SQL](onnx-overview.md)
