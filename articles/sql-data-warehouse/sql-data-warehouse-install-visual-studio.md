---
title: Instalar o Visual Studio e SSDT para SQL Data Warehouse | Documentos da Microsoft
description: Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/05/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f67c2a4547ee923e5c1b49302c38693e9ffe87c4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262215"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio e SSDT para SQL Data Warehouse
Utilize o Visual Studio 2017 para desenvolver aplicativos para o SQL Data Warehouse. Atualmente o SSDT de 2019 do Visual Studio não é suportada para o SQL Data Warehouse. 

Com o Visual Studio com SSDT permite-lhe utilizar o SQL Server Object Explorer para visualmente explorar tabelas, Exibições, procedimentos armazenados e muitos mais objetos no seu armazém de dados SQL, bem como executar consultas.

> [!NOTE]
> O SQL Data Warehouse ainda não suporta Projetos de Base de Dados do Visual Studio. Para receber atualizações periódicas sobre esta funcionalidade, vote [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Passo 1: Instalar o Visual Studio
Siga estas ligações para transferir e instalar o Visual Studio. Se já tiver o Visual Studio 2013 ou posterior instalado, pode avançar para o passo 2, instale o SSDT.

1. [Transferir o Visual Studio][].
2. Siga os [instalar o Visual Studio] [ Installing Visual Studio] orientar no MSDN e escolha as configurações predefinidas.

## <a name="step-2-install-ssdt"></a>Passo 2: Instalar o SSDT
Para instalar o SSDT para Visual Studio, verifique primeiro para uma atualização do SSDT a partir do Visual Studio, seguindo estes passos.

1. No Visual Studio, clique em **ferramentas** / **extensões e atualizações...** / **Atualizações**
2. Selecione **Atualizações de Produtos** e, em seguida, procure **Atualização do Microsoft SQL Server para Ferramentas de Base de Dados**

Se não for encontrada uma atualização, deve ter a versão mais recente instalada.  Para confirmar que o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure SQL Server Data Tools na lista. Se a opção de instalação não está disponível a partir do Visual Studio, em alternativa, pode visitar o [transferência do SSDT] [ SSDT Download] página para transferir e instalar manualmente o SSDT.

## <a name="next-steps"></a>Passos Seguintes
Agora que tem a versão mais recente do SSDT, está pronto para [ligar] [ connect] para o SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Transferir o Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
