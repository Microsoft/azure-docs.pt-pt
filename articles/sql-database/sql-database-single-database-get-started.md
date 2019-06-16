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
ms.author: ninarn
ms.reviewer: carlrab, sstein
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 1c24c8a10e35c0cf8979538c67fa8fb71b712741
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070173"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Início rápido: Criar uma base de dados na base de dados do SQL do Azure no portal do Azure

Criar uma [base de dados individual](sql-database-single-database.md) é a opção de implementação mais rápida e simples para a criação de uma base de dados na base de dados do Azure SQL. Este guia de introdução mostra-lhe como criar e, em seguida, consultar uma base de dados com o portal do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Para obter todos os passos neste início rápido, inicie sessão para o [portal do Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Criar uma base de dados

Uma base de dados pode ser criado no aprovisionado ou sem servidor camada de computação (pré-visualização).

- Uma base de dados na camada de computação aprovisionada tem uma quantidade definida de recursos de computação pré-alocada e um conjunto de recursos de memória e armazenamento através de uma de duas [modelos de compra](sql-database-purchase-models.md).
- Uma base de dados na camada de computação sem servidor tem uma variedade de recursos de computação que são dimensionamento automático mais uma quantidade especificada de memória por núcleo e uma determinada quantidade de recursos de armazenamento e só está disponível na [modelos de compra baseado em vCore ](sql-database-service-tiers-vcore.md).

Quando cria uma base de dados, também define um [servidor de base de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo dentro [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) numa determinada região.

> [!NOTE]
> Este início rápido utiliza a [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e o [sem servidor](sql-database-serverless.md) computação escalão, mas o [modelo de compra baseado em DTU](sql-database-service-tiers-DTU.md) também está disponível.

Para criar uma base de dados que contém os dados de exemplo do AdventureWorksLT:

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **bases de dados** e, em seguida, selecione **base de dados SQL** para abrir o **criar a base de dados do SQL** página.

   ![Criar base de dados individual](./media/sql-database-get-started-portal/create-database-1.png)

3. Sobre o **Noções básicas** separador a **detalhes do projeto** secção, escreva ou selecione os seguintes valores:

   - **Subscrição**: Lista pendente e selecione a subscrição correta, se não aparecer.
   - **Grupo de recursos**: Selecione **criar novo**, tipo `myResourceGroup`e selecione **OK**.

     ![Nova base de dados SQL - separador básico](media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Na **detalhes de base de dados** secção, escreva ou selecione os seguintes valores:

   - **Nome da base de dados**: Introduza `mySampleDatabase`.
   - **Servidor**: Selecione **criar novo** e introduza os valores seguintes e, em seguida, selecione **selecione**.
       - **Nome do servidor**: Tipo de `mysqlserver`; juntamente com alguns números de exclusividade.
       - **Início de sessão de administrador de servidor**: Digite `azureuser`.
       - **palavra-passe**: Escreva uma palavra-passe complexa que cumpra os requisitos de palavra-passe.
       - **Localização**: Escolha uma localização a partir do menu suspenso, como `West US 2`.

         ![Novo servidor](media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Não se esqueça de registar o início de sessão de administrador de servidor e a palavra-passe, pelo que pode iniciar sessão para o servidor e bases de dados para esse e outros guias de introdução. Se se esquecer da sua palavra-passe ou o início de sessão, pode obter o nome de início de sessão ou redefinir a senha sobre o **do SQL server** página. Para abrir o **do SQL server** , selecione o nome do servidor na base de dados **descrição geral** página após a criação da base de dados.

        ![Detalhes da base de dados SQL](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Pretende utilizar o agrupamento elástico de SQL**: Selecione o **não** opção.
   - **Computação + armazenamento**: Selecione **configurar base de dados** e para este início rápido, selecione **as opções de compra baseado em vCore**

     ![Opções de compra baseado em vCore](media/sql-database-get-started-portal/create-database-vcore.png)

   - Selecione **sem servidor**.

     ![escalão de computação sem servidor](media/sql-database-get-started-portal/create-database-serverless.png)

   - Reveja as definições da **máximo de vCores**, **Mín vCores**, **atraso de pausa automática**, e **tamanho máximo de dados**. Alterá-las conforme pretendido.
   - Aceite os termos de pré-visualização e clique em **OK**.
   - Selecione **Aplicar**.

5. Selecione o **definições adicionais** separador. 
6. Na **origem de dados** secção, em **utilizar dados existentes**, selecione `Sample`. 

   ![Definições adicionais da BD SQL](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Certifique-se de selecionar o **Sample (AdventureWorksLT)** dados, pelo que pode seguir facilmente este e outros guias de introdução do Azure SQL Database que usam esses dados.

7. Deixe o resto dos valores como padrão e selecione **rever + criar** na parte inferior do formulário.
8. Reveja as definições de finais e selecione **criar**.

9. Sobre o **base de dados SQL** formulário, selecione **criar** para implementar e aprovisionar o grupo de recursos, o servidor e a base de dados.

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
- Para criar uma base de dados na camada de computação aprovisionada com a CLI do Azure, veja [amostras de CLI do Azure](sql-database-cli-samples.md).
- Para criar uma base de dados na camada de computação aprovisionada com o Azure PowerShell, veja [exemplos do Azure PowerShell](sql-database-powershell-samples.md).
- Para criar uma base de dados na camada de computação sem servidor com o Azure Powershell, veja [criar base de dados sem servidor](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).