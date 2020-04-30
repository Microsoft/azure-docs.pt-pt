---
title: Criar uma regra de firewall ao nível do servidor
description: Criar uma regra de firewall de nível de servidor de base de dados SQL para bases de dados únicas e agrofadas
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: ff2508952b75bad88ff8ff92388c20ba52f50f42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73818247"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Quickstart: Criar uma regra de firewall ao nível do servidor para bases de dados únicas e agrofadas utilizando o portal Azure

Este quickstart explica como criar uma regra de firewall ao [nível do servidor](sql-database-firewall-configure.md) para bases de dados únicas e agrofadas na Base de Dados Azure SQL utilizando o portal Azure para permitir a ligação a servidores de base de dados, bases de dados únicas e piscinas elásticas e suas bases de dados. É necessária uma regra de firewall para se ligar a partir de outros recursos Azure e a partir de recursos no local.

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart utiliza os recursos criados na [Create uma única base de dados usando o portal Azure](sql-database-single-database-get-started.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall IP ao nível do servidor

O serviço De base de dados SQL cria uma firewall ao nível do servidor de base de dados para bases de dados individuais e agrofadas. Esta firewall impede que as aplicações do cliente se conectem ao servidor ou a qualquer uma das suas bases de dados únicas ou agrofadas, a menos que crie uma regra de firewall IP para abrir a firewall. Para uma ligação a partir de um endereço IP fora do Azure, crie uma regra de firewall para um endereço IP específico ou um leque de endereços que pretende ser capaz de ligar. Para obter mais informações sobre as regras de firewall IP ao nível do servidor e da base de dados, consulte as regras de firewall IP ao nível do servidor e do [nível da base de dados SQL](sql-database-firewall-configure.md).

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não pode ligar-se ao seu servidor de base de dados Azure SQL a menos que o seu departamento de TI abra a porta 1433.
> [!IMPORTANT]
> Uma regra de firewall de 0.0.0.0 permite que todos os serviços Do Azure passem pela regra de firewall ao nível do servidor e tentem ligar-se a uma única ou pooled database através do servidor. 

Siga estes passos para criar uma regra de firewall IP ao nível do servidor para o endereço IP do seu cliente e permitir a conectividade externa através da firewall sQL Database apenas para o seu endereço IP.

1. Após a implementação pré-requisito da base de [dados Azure SQL,](#prerequisites) selecione bases de **dados SQL** do menu à esquerda e, em seguida, escolha **mySampleDatabase** na página de bases de **dados SQL.** A página de visão geral da sua base de dados abre-se, mostrando-lhe o nome do servidor totalmente qualificado (como **mynewserver-20170824.database.windows.net)** e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilizar quando estiver ligado ao seu servidor e às suas bases de dados em outros arranques rápidos.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **Definir a firewall** do servidor na barra de ferramentas. A página de **definições de Firewall** para o servidor de base de dados abre.

   ![regra de firewall IP de nível servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Escolha **adicionar IP** do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall IP de nível de servidor. Uma regra de firewall IP de nível de servidor pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

   > [!IMPORTANT]
   > Por predefinição, o acesso através da firewall SQL Database é desativado para todos os serviços Azure. Escolha **ON** nesta página se pretender ativar o acesso a todos os serviços Do Azure.
   >

5. Selecione **Guardar**. Uma regra de firewall IP de nível de servidor foi criada para a sua porta de abertura de endereço IP atual 1433 no servidor Base de Dados SQL.

6. Feche a página de definições da **Firewall.**

Utilizando o SQL Server Management Studio ou outra ferramenta à sua escolha, pode agora ligar-se ao servidor De base de dados SQL e às suas bases de dados a partir deste endereço IP utilizando a conta de administração do servidor criada anteriormente.

## <a name="clean-up-resources"></a>Limpar recursos

Guarde estes recursos se pretender aceder aos [Próximos passos](#next-steps) e saiba como ligar e consultar a base de dados utilizando um número de métodos diferentes. Se, no entanto, pretender eliminar os recursos que criou neste arranque rápido, utilize os seguintes passos.

1. A partir do menu à esquerda no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup**.
2. Na página do grupo de recursos, selecione **Eliminar**, digitar **o myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

- Agora que tem uma base de dados, pode [ligar e consultar](sql-database-connect-query.md) com uma das suas ferramentas ou linguagens favoritas, incluindo
  - [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para saber como criar a sua primeira base de dados, criar tabelas e inserir dados, veja um destes tutoriais:
  - [Desenhe a sua primeira base de dados única na Base de Dados Azure SQL utilizando SSMS](sql-database-design-first-database.md)
  - [Desenhe uma única base de dados na Base de Dados Azure SQL e conecte-se com C# e ADO.NET](sql-database-design-first-database-csharp.md)
