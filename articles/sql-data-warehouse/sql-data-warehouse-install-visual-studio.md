---
title: Instalar o Visual Studio 2019 para SQL Data Warehouse | Microsoft Docs
description: Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 65cbd6679c85e82eaa5fd28bb91d54b443cc891e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515469"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Introdução ao Visual Studio 2019 para SQL Data Warehouse
O SSDT (Visual Studio **2019** SQL Server Data Tools) é uma única ferramenta que permite que você faça o seguinte:

- Conecte-se, consulte e desenvolva aplicativos para SQL Data Warehouse 
- Aproveite um pesquisador de objetos para explorar visualmente todos os objetos em seu modelo de dados, incluindo tabelas, exibições, procedimentos armazenados e etc.
- Gerar scripts de linguagem de definição de dados (DDL) T-SQL para seus objetos
- Desenvolva seu data warehouse usando uma abordagem baseada em estado com projetos de banco de dados SSDT
- Integre seu projeto de banco de dados com sistemas de controle do código-fonte, como git com Azure DevOps repositórios
- Configurar pipelines de implantação e integração contínua com servidores de automação, como o Azure DevOps [em breve]

> [!NOTE]
> Atualmente, os projetos de banco de dados SSDT do Visual Studio estão em versão prévia. Para receber atualizações periódicas sobre esse recurso, vote no [UserVoice].

## <a name="install-visual-studio-2019"></a>Instalar o Visual Studio 2019
Use o link a seguir para baixar e instalar o Visual Studio: [Baixe o Visual Studio 2019][]. Você precisa selecionar a carga de trabalho de armazenamento e processamento de dados durante a instalação. Não há mais instalação SSDT autônoma necessária no Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Relatando problemas com o SSDT Visual Studio 2019 (visualização)

Para relatar problemas ao usar o SSDT com o SQL Data Warehouse, envie um email para a seguinte lista de distribuição de email:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Passos Seguintes

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar][connect] ao seu SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixe o Visual Studio 2019]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
