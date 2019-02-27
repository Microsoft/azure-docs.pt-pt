---
title: 'Portal do Azure: Criar uma base de dados - base de dados SQL do Azure | Documentos da Microsoft'
description: Criar e consultar uma base de dados na base de dados do SQL do Azure no portal do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: d7f1279dbe253a9bc18b9e99ac9dbdf2a7cf52a1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870606"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Início rápido: Criar uma base de dados na base de dados do SQL do Azure no portal do Azure

Criar uma [base de dados individual](sql-database-single-database.md) é a opção de implementação mais rápida e simples para a criação de uma base de dados na base de dados do Azure SQL. Este guia de introdução mostra-lhe como criar e, em seguida, consultar uma base de dados com o portal do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Para obter todos os passos neste início rápido, inicie sessão para o [portal do Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Criar uma base de dados

Uma base de dados tem um conjunto definido de recursos de computação, memória, e/s e armazenamento através de uma de duas [model(sql-database-purchase-models.md) de compra. Quando cria uma base de dados, também define um [servidor de base de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo dentro [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) numa determinada região.

Para criar uma base de dados que contém os dados de exemplo do AdventureWorksLT:

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **bases de dados** e, em seguida, selecione **base de dados SQL**.
3. Na **criar a base de dados do SQL** de formulário, escreva ou selecione os seguintes valores:

   - **Nome da base de dados**: Introduza *mySampleDatabase*.
   - **Subscrição**: Lista pendente e selecione a subscrição correta, se não aparecer.
   - **Grupo de recursos**: Selecione **criar novo**, tipo *myResourceGroup*e selecione **OK**.
   - **Selecionar origem**: Lista pendente e selecione **Sample (AdventureWorksLT)**.

    > [!IMPORTANT]
    > Certifique-se de selecionar o **Sample (AdventureWorksLT)** dados, pelo que pode seguir facilmente este e outros guias de introdução do Azure SQL Database que usam esses dados.
  
   ![Criar base de dados individual](./media/sql-database-get-started-portal/create-database-1.png)

4. Sob **servidor**, selecione **criar nova**.
5. Na **novo servidor** de formulário, escreva ou selecione os seguintes valores:

   - **Nome do servidor**: Enter *mysqlserver*.
   - **Início de sessão de administrador de servidor**: Type *azureuser*.
   - **palavra-passe**: Enter *Azure1234567*.
   - **Confirmar palavra-passe**: Escreva novamente a palavra-passe.
   - **Localização**: Lista pendente e selecione qualquer localização válida.  

   > [!IMPORTANT]
   > Não se esqueça de registar o início de sessão de administrador de servidor e a palavra-passe, pelo que pode iniciar sessão para o servidor e bases de dados para esse e outros guias de introdução. Se se esquecer da sua palavra-passe ou o início de sessão, pode obter o nome de início de sessão ou redefinir a senha sobre o **do SQL server** página. Para abrir o **do SQL server** , selecione o nome do servidor na base de dados **descrição geral** página após a criação da base de dados.

    ![Criar servidor](./media/sql-database-get-started-portal/create-database-server.png)

6. Escolha **Selecionar**.
7. Sobre o **base de dados SQL** formulário, selecione **escalão de preço**. Explore a quantidade de DTUs e de armazenamento disponível para cada camada de serviço.

   > [!NOTE]
   > Este início rápido utiliza a [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md), mas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) também está disponível.
   > [!IMPORTANT]
   > Mais de 1 TB de armazenamento no escalão Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, e.u.a. centro-oeste, US DoD regiões e Governo dos E.u. a centro. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

8. Neste início rápido, selecione o **padrão** camada de serviço e, em seguida, utilize o controlo de deslize para selecionar **10 DTUs (S0)** e **1** GB de armazenamento.
9. Selecione **Aplicar**.  

   ![Selecionar o preço](./media/sql-database-get-started-portal/create-database-s1.png)

10. Sobre o **base de dados SQL** formulário, selecione **criar** para implementar e aprovisionar o grupo de recursos, o servidor e a base de dados.

   Implementação demora alguns minutos. Pode selecionar **notificações** na barra de ferramentas para monitorizar o progresso da implementação.

   ![Notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-database"></a>Consultar a base de dados

Agora que criou a base de dados, utilize a ferramenta de consulta incorporada no portal do Azure para ligar à base de dados e consultar os dados.

1. Sobre o **base de dados SQL** page de sua base de dados, selecione **editor de consultas (pré-visualização)** no menu à esquerda.

   ![Inicie sessão no editor de consulta](./media/sql-database-get-started-portal/query-editor-login.png)

2. Introduza as suas informações de início de sessão e selecione **OK**.
3. Introduza a seguinte consulta no **editor de consultas** painel.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Selecione **execute**e, em seguida, reveja os resultados da consulta no **resultados** painel.

   ![resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

5. Fechar o **editor de consultas** página e selecione **OK** quando lhe for pedido para eliminar as edições não guardadas.

## <a name="clean-up-resources"></a>Limpar recursos

Manter este grupo de recursos, o servidor de base de dados e a única base de dados, se pretender ir para o [próximos passos](#next-steps). Os passos seguintes mostram como ligar e consultar a base de dados através de métodos diferentes.

Quando tiver terminado com estes recursos, pode eliminá-los da seguinte forma:

1. No menu à esquerda no portal do Azure, selecione **grupos de recursos**e, em seguida, selecione **myResourceGroup**.
2. Na página do grupo de recursos, selecione **eliminar grupo de recursos**.
3. Introduza *myResourceGroup* no campo e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

- Crie uma regra de firewall ao nível do servidor para ligar à base de dados única a partir de ferramentas remotas ou no local. Para obter mais informações, consulte [criar uma regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall ao nível do servidor, [ligar e consultar](sql-database-connect-query.md) sua base de dados com várias ferramentas diferentes e linguagens.
  - [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar uma base de dados com a CLI do Azure, veja [amostras de CLI do Azure](sql-database-cli-samples.md).
- Para criar uma base de dados com o Azure PowerShell, veja [exemplos do Azure PowerShell](sql-database-powershell-samples.md).
