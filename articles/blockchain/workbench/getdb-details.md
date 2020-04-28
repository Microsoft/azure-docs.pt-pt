---
title: Obter detalhes da base de dados do Azure Blockchain Workbench
description: Saiba como obter a base de dados de pré-visualização da bancada de trabalho do Azure Blockchain e informações do servidor de base de dados.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74324696"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Obter informações sobre a base de dados do Azure Blockchain Workbench

Este artigo mostra como obter informações detalhadas sobre a sua base de dados de pré-visualização da bancada de trabalho Azure Blockchain.

## <a name="overview"></a>Descrição geral

São fornecidas informações sobre as aplicações, os fluxos de trabalho e a execução de contrato inteligente através das vistas de base de dados na BD SQL do Blockchain Workbench. Os desenvolvedores podem usar esta informação ao usar ferramentas como Microsoft Excel, Power BI, Visual Studio e SQL Server Management Studio.

Antes de um programador conseguir ligar à base de dados, precisa de:

* Acesso de cliente externo permitido na firewall da base de dados. Este artigo explica como configurar uma firewall de base de dados e permitir o acesso.
* O nome do servidor da base de dados e o nome da base de dados.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ligar à base de dados do Blockchain Workbench

Para ligar à base de dados:

1. Inscreva-se no portal Azure com uma conta que tem permissões do **Proprietário** para os recursos da bancada Azure Blockchain.
2. Na navegação à esquerda, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para a sua implementação do Blockchain Workbench.
4. Selecione **Tipo** para ordenar a lista de recursos e, em seguida, selecione **SQL Server**. A lista ordenada na captura de ecrã seguinte mostra duas bases de dados SQL, a "mestra" e a que utiliza "lhgn" como **Prefixo de recurso**.

   ![Lista de recursos ordenada do Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Para ver informações detalhadas sobre a base de dados do Blockchain Workbench, selecione a ligação para a base de dados com o **Prefixo de recurso** fornecido para a implementação do Blockchain Workbench.

   ![Detalhes da base de dados](./media/getdb-details/workbench-db-details.png)

O nome do servidor da base de dados e o nome da base de dados permitem-lhe ligar à base de dados do Blockchain Workbench com a sua ferramenta de desenvolvimento ou de relatórios.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)