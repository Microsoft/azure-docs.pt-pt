---
title: O que é Azure SQL Edge (Pré-visualização)?
description: Saiba mais sobre o Azure SQL Edge (Pré-visualização)
keywords: introdução ao SQL Edge, o que é SQL Edge, SQL Edge visão geral
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5d7274712f3e7f5b16dc5be28d0ad47cfefba9fa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685149"
---
# <a name="what-is-azure-sql-edge-preview"></a>O que é Azure SQL Edge (Pré-visualização)?

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implementações IoT e IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. O Azure SQL Edge fornece capacidades para transmitir, processar e analisar dados relacionais e não relacionais como jSON, dados gráficos e séries de tempo, o que a torna a escolha certa para uma variedade de aplicações ioT modernas.

O Azure SQL Edge baseia-se nas versões mais recentes do [Microsoft SQL Server Database Engine,](/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json)que fornece capacidades de desempenho, segurança e processamento de consultas líderes na indústria. Uma vez que o Azure SQL Edge é construído no mesmo motor que o SQL Server e o Azure SQL Database, fornece a mesma área de superfície de programação T-SQL que facilita e acelera o desenvolvimento de aplicações ou soluções e, ao mesmo tempo, torna a portabilidade da aplicação entre dispositivos IoT Edge, centros de dados e a nuvem diretamente para a frente.

> [!NOTE]
> O Azure SQL Edge encontra-se atualmente em pré-visualização e, como tal, não deve ser utilizado em ambientes de produção.

## <a name="deployment-models"></a>Modelos de implantação

O Azure SQL Edge está disponível no Azure Marketplace e pode ser implantado como um módulo para [o Azure IoT Edge](../iot-edge/about-iot-edge.md). Para mais informações, consulte [Deploy Azure SQL Edge](deploy-portal.md).<br>

![Diagrama de visão geral do Borda SQL](media/overview/overview.png)

## <a name="editions-of-sql-edge"></a>Edições da SQL Edge

O SQL Edge está disponível com duas edições diferentes ou planos de software. Estas edições têm conjuntos de funcionalidades idênticos e só diferem em termos dos seus direitos de utilização e da quantidade de cpu/memória que suportam.

   |**Planear**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor de borda sql azure  |  Desenvolvimento apenas sku, cada recipiente SQL Edge está limitado a até 4 núcleos e 32 GB De Memória  |
   |Borda SQL Azure    |  Sku de produção, cada recipiente SQL Edge está limitado a até 8 núcleos e 64 GB de memória. |

## <a name="pricing-and-availability"></a>Preços e Disponibilidade

O Azure SQL Edge encontra-se atualmente em pré-visualização. Para obter mais informações sobre os preços e disponibilidade, consulte [Azure SQL Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Para compreender as diferenças de funcionalidade sintetizadas entre o Azure SQL Edge e o SQL Server, bem como as diferenças entre diferentes opções azure SQL Edge, consulte [as funcionalidades suportadas do Azure SQL Edge](features.md).

## <a name="streaming-capabilities"></a>Capacidades de streaming  

O Azure SQL Edge fornece capacidades de streaming incorporadas para análise em tempo real e processamento complexo de eventos. A capacidade de streaming é construída utilizando as mesmas construções que o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e capacidades semelhantes às [do Azure Stream Analytics no IoT Edge](../stream-analytics/stream-analytics-edge.md).

O motor de streaming do Azure SQL Edge foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. 

Para obter mais informações sobre o streaming de dados no SQL Edge, consulte [o Data Streaming](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning e Capacidades de Inteligência Artificial

O Azure SQL Edge fornece capacidades de aprendizagem automática e análise incorporadas, integrando o formato aberto ONNX (Open Neural Network Exchange), que permite o intercâmbio de modelos de deep learning e redes neurais entre diferentes estruturas. Para mais informações sobre onNX, consulte [aqui](https://onnx.ai/). O tempo de execução onNX proporciona a flexibilidade para desenvolver modelos num idioma ou ferramentas à sua escolha, que podem ser convertidos para o formato ONNX para execução dentro do SQL Edge. Para mais informações, consulte [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Trabalhar com a Borda SQL Azure

O Azure SQL Edge torna o desenvolvimento e a manutenção de aplicações mais fáceis e produtivas. Os utilizadores podem usar todas as ferramentas e habilidades familiares para construir grandes apps e soluções para as suas necessidades ioT Edge. O utilizador pode desenvolver-se no SQL Edge utilizando ferramentas como as seguintes:

- [O portal Azure](https://portal.azure.com/) - Uma aplicação baseada na web para gerir todos os serviços Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Uma aplicação de cliente gratuita e transferível para gerir qualquer infraestrutura SQL, desde o SQL Server até à Base de Dados SQL.
- Ferramentas de dados do [Servidor SQL em Estúdio Visual](/sql/ssdt/download-sql-server-data-tools-ssdt/) - uma aplicação de cliente gratuita e transferível para o desenvolvimento de bases de dados relacionais do SQL Server, bases de dados SQL, pacotes de Serviços de Integração, modelos de dados de Serviços de Análise e Relatórios de Serviços de Informação.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uma ferramenta de base de dados gratuita, transferível e transversal para o profissional de dados que utiliza a família Microsoft de plataformas de dados no local e na nuvem no Windows, macOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Um editor de código gratuito, transferível e aberto para Windows, macOS e Linux. Suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta do Microsoft SQL Server, Azure SQL Database e Azure SQL Data Warehouse.


## <a name="next-steps"></a>Passos seguintes

- [Implementar sQL Edge através do portal Azure](deploy-portal.md)
- [Machine Learning e Inteligência Artificial com Borda SQL](onnx-overview.md)
- [Construção de uma solução IoT de ponta a ponta com SQL Edge](tutorial-deploy-azure-resources.md)
