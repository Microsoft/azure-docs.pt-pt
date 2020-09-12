---
title: O que é Azure SQL Edge (Preview)?
description: Saiba mais sobre a borda do Azure SQL (Pré-visualização)
keywords: introdução ao SQL Edge,o que é SQL Edge, visão geral sql Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 20e709db9e6992f52b04934cb0f6eb65d3dcb44c
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489532"
---
# <a name="what-is-azure-sql-edge-preview"></a>O que é Azure SQL Edge (Preview)?

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implementações IoT e IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. O Azure SQL Edge fornece capacidades para transmitir, processar e analisar dados relacionais e não relacionais, tais como JSON, gráfico e dados da série de tempo, o que a torna a escolha certa para uma variedade de aplicações IoT modernas.

O Azure SQL Edge é construído nas versões mais recentes do Microsoft SQL Database Engine (/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json), que fornece capacidades de processamento de desempenho, segurança e consulta líderes do setor. Uma vez que o Azure SQL Edge é construído no mesmo motor que o [SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json) e [o Azure SQL,](https://docs.microsoft.com/azure/azure-sql/)fornece a mesma área de programação T-SQL que torna o desenvolvimento de aplicações ou soluções mais fácil e rápido, e ao mesmo tempo torna a portabilidade da aplicação entre dispositivos IoT Edge, centros de dados e a nuvem em linha direta.

> [!NOTE]
> O Azure SQL Edge está atualmente em pré-visualização e, como tal, não deve ser utilizado em ambientes de produção.

## <a name="deployment-models"></a>Modelos de implantação

O Azure SQL Edge está disponível no Azure Marketplace e pode ser implantado como módulo para [Azure IoT Edge](../iot-edge/about-iot-edge.md). Para mais informações, consulte [Deploy Azure SQL Edge](deploy-portal.md).<br>

![Diagrama de visão geral da borda SQL](media/overview/overview.png)

## <a name="editions-of-sql-edge"></a>Edições de SQL Edge

SQL Edge está disponível com duas edições diferentes ou planos de software. Estas edições têm conjuntos de funcionalidades idênticos e apenas diferem em termos dos seus direitos de utilização e da quantidade de cpu/memória que suportam.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  Desenvolvimento apenas sku, cada recipiente SQL Edge está limitado a até 4 núcleos e 32 GB de memória  |
   |SQL do Azure no Edge    |  Sku de produção, cada recipiente SQL Edge está limitado a até 8 núcleos e 64 GB de memória . |

## <a name="pricing-and-availability"></a>Preços e Disponibilidade

A Azure SQL Edge está atualmente em pré-visualização. Para obter mais informações sobre o preço e disponibilidade, consulte [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidade entre O Azure SQL Edge e SQL Server, bem como as diferenças entre as diferentes opções de Azure SQL Edge, consulte [as funcionalidades suportadas do Azure SQL Edge](features.md).

## <a name="streaming-capabilities"></a>Capacidades de streaming  

O Azure SQL Edge fornece capacidades de streaming para análises em tempo real e processamento complexo de eventos. A capacidade de streaming é construída usando as mesmas construções que [a Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e capacidades semelhantes às do [Azure Stream Analytics no IoT Edge](../stream-analytics/stream-analytics-edge.md).

O motor de streaming do Azure SQL Edge foi concebido para uma baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. 

Para obter mais informações sobre o streaming de dados em SQL Edge, consulte [o Streaming de Dados](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Capacidades de Aprendizagem Automática e Inteligência Artificial

O Azure SQL Edge fornece capacidades de aprendizagem e análise de máquinas incorporadas, integrando o tempo de funcionamento onnx (Open Neural Network Exchange), que permite o intercâmbio de modelos de aprendizagem profunda e rede neural entre diferentes quadros. Para mais informações sobre ONNX, consulte [aqui.](https://onnx.ai/) O tempo de execução ONNX proporciona a flexibilidade para desenvolver modelos num idioma ou ferramentas à sua escolha, que podem ser convertidos para o formato ONNX para execução dentro do SQL Edge. Para mais informações, consulte [Machine Learning e Artificial Intelligence com ONNX em SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Trabalhar com a Azure SQL Edge

O Azure SQL Edge torna o desenvolvimento e manutenção de aplicações mais fáceis e produtivas. Os utilizadores podem usar todas as ferramentas e habilidades familiares para construir grandes apps e soluções para as suas necessidades IoT Edge. O utilizador pode desenvolver-se em SQL Edge utilizando ferramentas como:

- [O portal Azure](https://portal.azure.com/) - Uma aplicação baseada na web para gerir todos os serviços Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Uma aplicação gratuita de cliente descarregada para gerir qualquer infraestrutura SQL, desde o SQL Server até à BASE de Dados SQL.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) - Uma aplicação gratuita e transferível para o cliente para o desenvolvimento de bases de dados relacionais do SQL Server, bases de dados SQL, pacotes de Serviços de Integração, modelos de dados de serviços de análise e relatórios de Serviços de Relatórios.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uma ferramenta de base de dados gratuita, transferível e transversal para o profissional de dados utilizando a família Microsoft de plataformas de dados em nuvem no Windows, macOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Um editor de código gratuito, transferível e de código aberto para Windows, macOS e Linux. Suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta do Microsoft SQL Server, Azure SQL Database e Azure Synapse Analytics.


## <a name="next-steps"></a>Próximos passos

- [Implementar sql edge através do portal Azure](deploy-portal.md)
- [Machine Learning e Inteligência Artificial com SqL Edge](onnx-overview.md)
- [Construção de uma solução IoT de ponta a ponta com SQL Edge](tutorial-deploy-azure-resources.md)
