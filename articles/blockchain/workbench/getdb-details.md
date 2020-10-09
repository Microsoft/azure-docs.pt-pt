---
title: Obter detalhes da base de dados do Azure Blockchain Workbench
description: Saiba como obter a base de dados de pré-visualização do Workbench Azure Blockchain e informações do servidor de bases de dados.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254655"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Obter informações sobre a base de dados do Azure Blockchain Workbench

Este artigo mostra como obter informações detalhadas sobre a sua base de dados de pré-visualização da bancada de trabalho Azure Blockchain.

## <a name="overview"></a>Descrição geral

São fornecidas informações sobre as aplicações, os fluxos de trabalho e a execução de contrato inteligente através das vistas de base de dados na BD SQL do Blockchain Workbench. Os desenvolvedores podem usar esta informação quando usam ferramentas como Microsoft Excel, Power BI, Visual Studio e SQL Server Management Studio.

Antes de um programador conseguir ligar à base de dados, precisa de:

* Acesso de cliente externo permitido na firewall da base de dados. Este artigo explica como configurar uma firewall de base de dados e permitir o acesso.
* O nome do servidor da base de dados e o nome da base de dados.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ligar à base de dados do Blockchain Workbench

Para ligar à base de dados:

1. Inscreva-se no portal Azure com uma conta que tem permissões **do Proprietário** para os recursos da bancada de trabalho Azure Blockchain.
2. Na navegação à esquerda, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para a sua implementação do Blockchain Workbench.
4. Selecione **Tipo** para ordenar a lista de recursos e, em seguida, selecione **SQL Server**. A lista ordenada na captura do ecrã seguinte mostra duas bases de dados, "master" e uma que usa "lhgn" como **prefixo de recursos.**

   ![Lista de recursos ordenada do Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Para ver informações detalhadas sobre a base de dados do Blockchain Workbench, selecione a ligação para a base de dados com o **Prefixo de recurso** fornecido para a implementação do Blockchain Workbench.

   ![Detalhes da base de dados](./media/getdb-details/workbench-db-details.png)

O nome do servidor da base de dados e o nome da base de dados permitem-lhe ligar à base de dados do Blockchain Workbench com a sua ferramenta de desenvolvimento ou de relatórios.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)