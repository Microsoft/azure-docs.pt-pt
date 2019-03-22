---
title: Oferecem elevada disponibilidade bases de dados SQL no Azure Stack | Documentos da Microsoft
description: Saiba como criar um fornecedor de recursos do SQL Server, computador anfitrião e bases de dados de elevada disponibilidade SQL AlwaysOn, com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: b61bf4f05f52b9d28ee09bb6c0de30544396e815
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091601"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Tutorial: Oferecem elevada disponibilidade bases de dados do SQL

Como um operador de pilha do Azure, pode configurar as VMs de servidor para alojar bases de dados do SQL Server. Depois do SQL server alojamento com êxito é criado e gerido pelo Azure Stack, os utilizadores no qual se inscreveu ao SQL services podem criar facilmente bases de dados SQL.

Este tutorial mostra como utilizar um modelo de início rápido do Azure Stack para criar uma [grupo de Disponibilidade AlwaysOn do SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), adicioná-la como um servidor de alojamento de SQL do Azure Stack e, em seguida, crie uma base de dados SQL altamente disponível.

O que irá aprender:

> [!div class="checklist"]
> * Criar um grupo de Disponibilidade AlwaysOn do SQL Server a partir de um modelo
> * Criar um servidor de alojamento de SQL para o Azure Stack
> * Criar uma base de dados SQL altamente disponível

Neste tutorial, um grupo de Disponibilidade AlwaysOn do SQL Server do VM dois será criado e configurado através de itens de marketplace disponíveis do Azure Stack. 

Antes de iniciar os passos neste tutorial, certifique-se de que o [fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) foi instalado com êxito e os seguintes itens disponíveis no mercado do Azure Stack:

> [!IMPORTANT]
> Todas as ações seguintes são necessárias para o modelo de início rápido do Azure Stack ser utilizado.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) imagem do marketplace.
- SQL Server 2016 SP1 ou SP2 (Enterprise, Standard ou desenvolvedor) na imagem de servidor do Windows Server 2016. Este tutorial utiliza a [SQL Server 2016 SP2 Enterprise no Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) imagem do marketplace.
- [Extensão de IaaS do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) versão 1.2.30 ou superior. A extensão de IaaS do SQL instala os componentes necessários, que são necessários para os itens do Marketplace SQL Server para todas as versões do Windows. Permite que as definições de SQL específicas ser configuradas em máquinas virtuais do SQL. Se a extensão não estiver instalada no mercado local, o aprovisionamento do SQL irá falhar.
- [Extensão de script personalizado para o Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) versão 1.9.1 ou superior. Extensão de Script personalizado é uma ferramenta que pode ser utilizada para iniciar automaticamente tarefas de personalização de VM de pós-implementação.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) versão 2.76.0.0 ou superior. DSC é uma plataforma de gestão do Windows PowerShell que permite a implementação e gestão de dados de configuração de serviços de software e gerenciamento do ambiente em que estes serviços são executados.

Para saber mais sobre como adicionar itens ao mercado do Azure Stack, veja a [descrição geral do Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Criar um grupo de Disponibilidade AlwaysOn do SQL Server
Utilize os passos nesta secção para implementar o grupo de Disponibilidade AlwaysOn do SQL Server com o [modelo de início rápido do Azure Stack de alwayson de sql 2016](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Este modelo implementa duas instâncias de SQL Server Enterprise, Standard ou desenvolvedor num grupo de Disponibilidade AlwaysOn. Cria os seguintes recursos:

- Um grupo de segurança de rede
- Uma rede virtual
- Quatro contas de armazenamento (um para o Active Directory (AD), uma para SQL, uma para o testemunho de partilha de ficheiros e para diagnósticos de VM)
- Quatro endereços IP públicos (um para o AD, dois para cada VM do SQL Server e para o Balanceador de carga público vinculado ao serviço de escuta do AlwaysOn de SQL)
- Balanceador de carga externo de uma das VMS de SQL com o IP público associado para o serviço de escuta do SQL AlwaysOn
- Uma VM (Windows Server 2016) configurada como controlador de domínio para uma nova floresta com um único domínio
- Duas VMs (Windows Server 2016) configurado com o SQL Server 2016 SP1 ou SP2 Enterprise, Standard ou Developer Edition e em cluster. Estes têm de ser imagens do marketplace.
- Uma VM (Windows Server 2016) configurada como testemunho de partilha de ficheiros para o cluster
- Um conjunto que contém o testemunho de partilha ficheiros e SQL VMs de disponibilidade  

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Selecione **\+** **criar um recurso** > **personalizado**e, em seguida, **implementação do modelo**.

   ![Implementação de modelo personalizado](media/azure-stack-tutorial-sqlrp/1.png)


3. Sobre o **implementação personalizada** painel, selecione **Editar modelo** > **modelo de início rápido** e, em seguida, utilize a lista de lista pendente de disponíveis modelos personalizados para Selecione o **alwayson de sql 2016** modelo, clique em **OK**e, em seguida **guardar**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Selecione o modelo de início rápido")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Sobre o **implementação personalizada** painel, selecione **Editar parâmetros** e reveja os valores predefinidos. Modifique os valores conforme necessário para fornecer todas as informações de parâmetro necessário e, em seguida, clique em **OK**.<br><br> No mínimo:

    - Fornece palavras-passe complexas para os parâmetros ADMINPASSWORD SQLSERVERSERVICEACCOUNTPASSWORD e SQLAUTHPASSWORD.
    - Introduza o sufixo DNS de pesquisa inversa em todas as letras minúsculas para o parâmetro DNSSUFFIX (**azurestack.external** para instalações de ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Editar parâmetros de implementação personalizada")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Sobre o **implementação personalizada** painel, selecione a subscrição para utilizar e criar um novo grupo de recursos ou selecione um grupo de recursos existente para a implementação personalizada.<br><br> Em seguida, selecione a localização do grupo de recursos (**local** para instalações de ASDK) e, em seguida, clique em **criar**. As definições de implementação personalizadas serão validadas e, em seguida, irá iniciar a implementação.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Criar implementação personalizada")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que criou para a implementação personalizada (**grupo de recursos** para este exemplo). Ver o estado da implementação para garantir que todas as implementações foram concluídas com êxito.<br><br>Em seguida, reveja os itens do grupo de recursos e selecione o **SQLPIPsql\<nome do grupo de recursos\>**  item de endereço IP público. Registe o endereço IP público e o FQDN completo do IP público do Balanceador de carga. Precisará fornecer isso a um operador de pilha do Azure, para que podem criar um servidor de alojamento de SQL tirar partido deste grupo de disponibilidade SQL AlwaysOn.

   > [!NOTE]
   > A implementação do modelo irá demorar várias horas a concluir.

   ![Conclusão da implantação personalizada](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Ativar o seeding automático
Depois que o modelo com êxito é implementado e configurado o grupo de disponibilidade SQL AlwaysON, tem de ativar [seeding automático](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada instância do SQL Server no grupo de disponibilidade. 

Quando cria um grupo de disponibilidade com o seeding automático, do SQL Server cria automaticamente as réplicas secundárias para cada base de dados no grupo de sem intervenção manual necessário para garantir a elevada disponibilidade de bases de dados do AlwaysOn.

Utilize estes comandos SQL para configurar o seeding automático para o grupo de Disponibilidade AlwaysOn. Substitua \<InstanceName\> com o principal de instância nome do SQL Server e < availability_group_name > com o nome do grupo de Disponibilidade AlwaysOn conforme necessário. 

Na instância do SQL principal:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Script de instância SQL primária](./media/azure-stack-tutorial-sqlrp/sql1.png)

Em instâncias SQL secundárias:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Script de instância SQL secundário](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configurar a autenticação de banco de dados independente
Antes de adicionar a base de dados contida para um grupo de disponibilidade, certifique-se de que a opção de servidor de autenticação de base de dados contida está definida como 1 em cada instância de servidor que aloja uma réplica de disponibilidade para o grupo de disponibilidade. Para obter mais informações, consulte [autenticação de base de dados contida](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Utilize estes comandos para definir a opção de servidor de autenticação de banco de dados independente para cada instância do SQL Server no grupo de disponibilidade:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Autenticação de base de dados do conjunto contido](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Criar um servidor de alojamento de SQL para o Azure Stack
Depois do grupo de disponibilidade do SQL Server AlwayOn foi criado e corretamente configurado, um operador de pilha do Azure tem de criar um servidor de alojamento das SQL do Azure Stack para disponibilizar a capacidade adicional para os utilizadores criar bases de dados. 

Certifique-se de que usar o IP público ou o FQDN completo para o IP público do Balanceador de carga SQL registou anteriormente quando foi criado o grupo de recursos do grupo de Disponibilidade AlwaysOn do SQL (**SQLPIPsql\<nome do grupo de recursos\>** ). Além disso, precisa saber as credenciais de autenticação utilizadas para aceder às instâncias de SQL no grupo de Disponibilidade AlwaysOn do SQL Server.

> [!NOTE]
> Este passo deve ser executado a partir do portal de administração do Azure Stack por um operador de pilha do Azure.

Com o ouvinte de Balanceador de carga do grupo de disponibilidade SQL AlwaysOn informações de início de sessão de autenticação de IP público e o SQL, um operador de pilha do Azure podem agora [criar um servidor de alojamento de SQL com o grupo de disponibilidade do AlwaysOn de SQL](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Certifique-se também de que tiver criado a planos e ofertas para facilitar a criação de base de dados do SQL AlwaysOn disponíveis aos utilizadores. O operador terá de adicionar o **Microsoft.SqlAdapter** para um plano de serviço e criar uma quota de novo especificamente para bases de dados de elevada disponibilidade. Para obter mais informações sobre a criação de planos, consulte [descrição geral do plano, oferta, quota e subscrição](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> O **Microsoft.SqlAdapter** serviço não estará disponível para o adicionar aos planos de até a [fornecedor de recursos do SQL Server tiver sido implementada](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Criar uma base de dados SQL altamente disponível
Depois do SQL AlwaysOn grupo de disponibilidade foi criado, configurado e adicionado como um servidor de alojamento de SQL do Azure Stack por um operador de pilha do Azure, um utilizador do inquilino com uma subscrição que inclui capacidades de base de dados do SQL Server pode criar bases de dados SQL com suporte Funcionalidade AlwaysOn, seguindo os passos nesta secção. 

> [!NOTE]
> Execute estes passos a partir do portal de utilizador do Azure Stack como um utilizador do inquilino com uma assinatura do fornecimento de capacidades do SQL Server (Microsoft.SQLAdapter serviço).

1. 
   [!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Selecione **\+** **criar um recurso** > **dados \+ armazenamento**e, em seguida, **base de dados SQL**.<br><br>Forneça as informações de propriedade da base de dados necessários, incluindo o nome, agrupamento, tamanho máximo e a subscrição, grupo de recursos e localização para utilizar na implementação. 

   ![Criar base de dados SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Selecione **SKU** e, em seguida, escolha o adequado SQL que aloja servidor SKU para utilizar. Neste exemplo, se criou o operador do Azure Stack a **Enterprise-HA** SKU para suportar uma elevada disponibilidade para grupos de disponibilidade do AlwaysOn de SQL.

   ![Selecione o SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Selecione **início de sessão** > **criar um novo início de sessão** e, em seguida, forneça as credenciais de autenticação de SQL a ser utilizado para a nova base de dados. Quando terminar, clique em **OK** e, em seguida **criar** para iniciar o processo de implementação de base de dados.

   ![Criar início de sessão](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Quando concluída com êxito a implementação de base de dados do SQL, reveja as propriedades de base de dados para detetar a cadeia de ligação a utilizar para ligar para a nova base de dados de elevada disponibilidade. 

   ![Ver cadeia de ligação](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um grupo de Disponibilidade AlwaysOn do SQL Server a partir de um modelo
> * Criar um servidor de alojamento de SQL para o Azure Stack
> * Criar uma base de dados SQL altamente disponível

Avance para o próximo tutorial para saber como:
> [!div class="nextstepaction"]
> [Criar bases de dados MySQL elevada disponibilidade](azure-stack-tutorial-mysql.md)