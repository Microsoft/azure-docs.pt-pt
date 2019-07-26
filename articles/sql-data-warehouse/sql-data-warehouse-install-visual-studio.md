---
title: Instalar o Visual Studio e o SSDT para SQL Data Warehouse | Microsoft Docs
description: Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479481"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio e o SSDT para SQL Data Warehouse
Use o Visual Studio 2019 para desenvolver aplicativos para SQL Data Warehouse. Atualmente, o Visual Studio 2019 SSDT não tem suporte para SQL Data Warehouse. 

Usar o Visual Studio com o SSDT permite que você use o Pesquisador de Objetos do SQL Server para explorar tabelas, exibições, procedimentos armazenados e muitos outros objetos visualmente em seu SQL Data Warehouse. Ele também permite que você execute consultas.

> [!NOTE]
> O SQL Data Warehouse ainda não suporta Projetos de Base de Dados do Visual Studio. Para receber atualizações periódicas sobre esse recurso, vote no [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Passo 1: Instalar o Visual Studio
Siga estes links para baixar e instalar o Visual Studio. Se você já tiver Visual Studio 2013 ou posterior instalado, poderá pular para a etapa 2, instalar o SSDT.

1. [Baixar o Visual Studio][].
2. Siga o guia [Instalar o Visual Studio][Installing Visual Studio] na MSDN e escolha as configurações predefinidas.

## <a name="step-2-install-ssdt"></a>Passo 2: Instalar o SSDT
Para instalar o SSDT para o Visual Studio, primeiro verifique se há uma atualização do SSDT de dentro do Visual Studio seguindo estas etapas.

1. No Visual Studio, clique em **ferramentas** / **extensões e atualizações...** / **Actualiza**
2. Selecione **Atualizações de Produtos** e, em seguida, procure **Atualização do Microsoft SQL Server para Ferramentas de Base de Dados**

Você deve ter a versão mais recente instalada se uma atualização não for encontrada. Para confirmar que o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure SQL Server Data Tools na lista. Se a opção de instalar não estiver disponível no Visual Studio, você poderá visitar a página de [download do SSDT][SSDT Download] para baixar e instalar o SSDT manualmente.

## <a name="next-steps"></a>Passos seguintes
Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar][connect] ao seu SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixar o Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
