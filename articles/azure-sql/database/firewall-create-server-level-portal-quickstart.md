---
title: Criar uma regra de firewall ao nível do servidor
description: Criar uma regra de firewall ao nível do servidor
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: bef1e9bdafd91a51290fe84af074c8cece7cc371
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486731"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Quickstart: Criar uma regra de firewall ao nível do servidor utilizando o portal Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este quickstart percorre como criar uma regra de firewall ao [nível do servidor](firewall-configure.md) na Base de Dados Azure SQL utilizando o portal Azure para permitir a ligação a [servidores SQL lógicos,](logical-servers.md)bases de dados únicas e piscinas elásticas e suas bases de dados. É necessária uma regra de firewall para se ligar a partir de outros recursos Azure e de recursos no local. As regras de firewall ao nível do servidor não se aplicam à Instância Gerida Azure SQL.

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart utiliza os recursos criados na [Criação de uma única base de dados utilizando o portal Azure](single-database-create-quickstart.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall IP de nível de servidor

 A SQL Database cria uma firewall ao nível do servidor para bases de dados individuais e agrizadas. Esta firewall impede que as aplicações do cliente se conectem ao servidor ou a qualquer uma das suas bases de dados, a menos que crie uma regra de firewall IP para abrir a firewall. Para uma ligação a partir de um endereço IP fora de Azure, crie uma regra de firewall para um endereço IP específico ou intervalo de endereços a que pretende ser capaz de se conectar. Para obter mais informações sobre as regras de firewall IP de nível de servidor e de nível de base de [dados, consulte as regras de firewall IP ao nível do servidor e nível de base de dados](firewall-configure.md).

> [!NOTE]
> A Base de Dados SQL do Azure comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não pode ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 1433.
> [!IMPORTANT]
> Uma regra de firewall de 0.0.0.0 permite que todos os serviços Azure passem pela regra de firewall ao nível do servidor e tentem ligar-se a uma base de dados através do servidor.

Siga estes passos para criar uma regra de firewall IP de nível de servidor para o endereço IP do seu cliente e ativar a conectividade externa através da firewall Azure SQL Database apenas para o seu endereço IP.

1. Após a implementação da [base de dados](#prerequisites) concluída, selecione bases de **dados SQL** a partir do menu à esquerda e, em seguida, escolha **a minha Base de Dados** SQL na página de **bases de dados SQL.** A página geral da sua base de dados abre, mostrando-lhe o nome do servidor totalmente qualificado (como **mynewserver-20170824.database.windows.net**) e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilizar ao ligar ao seu servidor e às suas bases de dados em outros arranques rápidos.

   ![nome do servidor](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Selecione **Definir firewall** do servidor na barra de ferramentas. A página **de definições de Firewall** para o servidor abre.

   ![regra de firewall IP de nível de servidor](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Escolha adicionar o IP do **cliente** na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall IP de nível de servidor. Uma regra de firewall IP de nível de servidor pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

   > [!IMPORTANT]
   > Por predefinição, o acesso através da firewall Azure SQL Database é desativado para todos os serviços Azure. Escolha **ON** nesta página se quiser ativar o acesso a todos os serviços Azure.
   >

5. Selecione **Guardar**. Uma regra de firewall IP de nível de servidor é criada para o seu endereço IP atual que abre a porta 1433 no servidor.

6. Feche a página **de definições de Firewall.**

Utilizando o SQL Server Management Studio ou outra ferramenta à sua escolha, pode agora ligar-se ao servidor e às suas bases de dados a partir deste endereço IP utilizando a conta de administração do servidor criada anteriormente.

## <a name="clean-up-resources"></a>Limpar os recursos

Guarde estes recursos se pretender aceder aos [Próximos passos](#next-steps) e saiba como ligar e consultar a base de dados utilizando um número de métodos diferentes. Se, no entanto, pretender eliminar os recursos que criou neste arranque rápido, utilize os seguintes passos.

1. A partir do menu à esquerda no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup**.
2. Na sua página de grupo de recursos, selecione **Delete**, digite **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

- Agora que tem uma base de dados, pode [ligar e consultar](connect-query-content-reference-guide.md) com uma das suas ferramentas ou linguagens favoritas, incluindo
  - [Ligar e consultar com o SQL Server Management Studio](connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para saber como criar a sua primeira base de dados, criar tabelas e inserir dados, veja um destes tutoriais:
  - [Desenhe a sua primeira base de dados única na Base de Dados Azure SQL utilizando SSMS](design-first-database-tutorial.md)
  - [Desenhe uma única base de dados na Base de Dados Azure SQL e ligue-se a C# e ADO.NET](design-first-database-csharp-tutorial.md)
