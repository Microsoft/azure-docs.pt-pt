---
title: Oferecem elevada disponibilidade bases de dados MySQL no Azure Stack | Documentos da Microsoft
description: Saiba como criar um fornecedor de recursos do servidor MySQL computador anfitrião e de elevada disponibilidade bases de dados MySQL com o Azure Stack.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: ff1b137fb6461c4ebd17451b89a180364d833d4e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116797"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Tutorial: Oferecem elevada disponibilidade bases de dados MySQL

Como um operador de pilha do Azure, pode configurar as VMs de servidor para alojar bases de dados do servidor MySQL. Após um MySQL cluster com êxito é criado e gerido pelo Azure Stack, os utilizadores no qual se inscreveu para serviços MySQL podem criar facilmente bases de dados MySQL elevada disponibilidade.

Este tutorial mostra como utilizar os itens do marketplace do Azure Stack para criar uma [MySQL com o cluster de replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Esta solução utiliza várias VMs para replicar as bases de dados a partir do nó principal para um número configurável de réplicas. Depois de criado, o cluster pode, em seguida, ser adicionado como um servidor de alojamento de MySQL do Azure Stack e, em seguida, os utilizadores podem criar uma elevada disponibilidade bases de dados MySQL.

> [!IMPORTANT]
> O **MySQL com a replicação** item do mercado do Azure Stack pode não estar disponível para todos os ambientes de subscrição de cloud do Azure. Certifique-se de que o item do marketplace está disponível na sua subscrição antes de tentar seguir o restante neste tutoral.

O que irá aprender:

> [!div class="checklist"]
> * Criar um cluster de servidor MySQL a partir de itens do marketplace
> * Criar um MySQL do Azure Stack que aloja o servidor
> * Criar uma base de dados MySQL elevada disponibilidade

Neste tutorial, um cluster de servidor MySQL de VM três será criado e configurado através de itens de marketplace disponíveis do Azure Stack. 

Antes de iniciar os passos neste tutorial, certifique-se de que o [fornecedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) foi instalado com êxito e que os seguintes itens estão disponíveis no mercado do Azure Stack:

> [!IMPORTANT]
> Todas as ações seguintes são necessários para criar o cluster do MySQL.

- [MySQL com a replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Este é o modelo de solução de Bitnami que será utilizado para a implementação de cluster do MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). Debian 8 "Jessie" com o kernel de backports para o Microsoft Azure fornecido pelo credativ. Debian GNU/Linux é uma das distribuições de Linux mais populares.
- [Script personalizado para linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Extensão de Script personalizado é uma ferramenta para executar a sua VM personalização tarefas post VM aprovisionar. Quando esta extensão é adicionada a uma Máquina Virtual, pode transferir os scripts do armazenamento do Azure e executá-los na VM. Tarefas de extensão de Script personalizadas também podem ser automatizadas usando os cmdlets do PowerShell do Azure e a Interface de linha de comandos do Azure para várias plataformas (xPlat CLI).
- Acesso VM para Linux extensão 1.4.7. A extensão de acesso à VM permite-lhe para repor a palavra-passe, a chave SSH ou as configurações de SSH, pelo que pode recuperar o acesso à sua VM. Também pode adicionar um novo utilizador com palavra-passe ou chave SSH, ou eliminar um utilizador com esta extensão. Esta extensão destina-se as VMs do Linux.

Para saber mais sobre como adicionar itens ao mercado do Azure Stack, veja a [descrição geral do Azure Stack Marketplace](azure-stack-marketplace.md).

Também precisará de um cliente SSH, como [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) para iniciar sessão nas Linux VMs após elas serem implantadas.

## <a name="create-a-mysql-server-cluster"></a>Criar um cluster de servidor MySQL 
Utilize os passos nesta secção para implementar o servidor MySQL do cluster utilizando o [MySQL com a replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) item do marketplace. Este modelo implementa três instâncias de servidor MySQL configuradas num cluster MySQL de elevada disponibilidade. Por predefinição, este cria os seguintes recursos:

- Uma rede virtual
- Um grupo de segurança de rede
- Uma conta de armazenamento
- Um conjunto de disponibilidade
- Três interfaces (um para cada uma das VMs padrão) de rede
- Um endereço IP público (para o cluster MySQL primário VM)
- Três VMs do Linux para alojar o cluster de MySQL

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Selecione **\+** **criar um recurso** > **computação**e, em seguida, **MySQL com a replicação**.

   ![Implementação de modelo personalizado](media/azure-stack-tutorial-mysqlrp/1.png)

3. Fornecer informações de implementação básica sobre o **Noções básicas** página. Reveja os valores predefinidos e altere conforme necessário e clique em **OK**.<br><br>No mínimo, forneça o seguinte:
   - Nome da implementação (a predefinição é mymysql)
   - Palavra-passe de raiz de aplicação. Forneça uma senha de alfanuméricos 12 carateres com **sem carateres especiais**
   - Nome da base de dados de aplicação (a predefinição é bitnami)
   - Número de VMs para criar de réplica de base de dados do MySQL (a predefinição é 2)
   - Selecione a subscrição a utilizar
   - Selecione o grupo de recursos para utilizar ou crie um novo
   - Selecione a localização (a predefinição é o local para ASDK)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Noções básicas da implantação")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. Sobre o **configuração do ambiente** página, forneça as seguintes informações e, em seguida, clique em **OK**: 
   - Palavra-passe ou SSH chave pública a utilizar para autenticação de secure shell (SSH). Se utilizar uma palavra-passe, tem de conter letras, números e **pode** conter carateres especiais
   - Tamanho da VM (a predefinição é padrão D1 v2 VMs)
   - Tamanho em GB clique do disco de dados **OK**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Configuração do ambiente")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Reveja a implantação **resumo**. Opcionalmente, pode transferir o modelo personalizado e os parâmetros e, em seguida, clique em **OK**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Resumo")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Clique em **Create** sobre o **comprar** página para iniciar a implementação.

   ![Comprar](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > A implementação irá demorar cerca de uma hora. Certifique-se de que a implementação estiver concluída e o cluster MySQL tiver sido completamente configurado antes de continuar. 

7. Depois de todas as implementações foram concluídas com êxito, reveja os itens do grupo de recursos e selecione o **mysqlip** item de endereço IP público. Registe o endereço IP público e o FQDN completo do IP público para o cluster.<br><br>Precisará fornecer isso a um operador de pilha do Azure, para que podem criar um servidor de hospedagem do MySQL tirar partido deste cluster do MySQL.


### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede
Por predefinição, nenhum acesso público está configurado para MySQL para o anfitrião VM. Para o fornecedor de recursos do MySQL do Azure Stack para se ligar e gerir o cluster do MySQL, uma regra de grupo (NSG) de segurança de rede de entrada tem de ser criada.

1. No portal do administrador, navegue para o grupo de recursos criado quando implementar o cluster MySQL e selecione o grupo de segurança de rede (**predefinição-sub-rede-sg**):

   ![abre](media/azure-stack-tutorial-mysqlrp/6.png)

2. Selecione **regras de segurança de entrada** e, em seguida, clique em **Add**.<br><br>Introduza **3306** no **intervalo de portas de destino** e, opcionalmente, forneça uma descrição no **nome** e **Descrição** campos. Clique em Adicionar para fechar a caixa de diálogo de regra de segurança de entrada.

   ![abre](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Configurar o acesso externo ao MySQL cluster
Antes do cluster MySQL pode ser adicionado como um anfitrião do servidor MySQL do Azure Stack, o acesso externo tem de estar ativado.

1. Utilizar um cliente SSH, este exemplo utiliza [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), inicie sessão na máquina primária MySQL de um computador que pode acessar o IP público. O nome da VM de MySQL primário, normalmente, termina com **0** e tem um IP público atribuído ao mesmo.<br><br>Utilizar o IP público e o registo na VM com o nome de utilizador **bitnami** e a palavra-passe de aplicação que criou anteriormente sem carateres especiais.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. Na janela de cliente de SSH, utilize o seguinte comando para garantir que o serviço de bitnami está ativa e em execução. Forneça a palavra-passe do bitnami novamente quando lhe for pedido:

   `sudo service bitnami status`

   ![Verificação de serviço](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Crie uma conta de utilizador de acesso remoto a ser utilizada pelo servidor de alojamento das MySQL do Azure Stack para ligar ao MySQL e sair, em seguida, o cliente SSH.<br><br>Execute os seguintes comandos para iniciar sessão no MySQL como raiz, usando a palavra-passe de raiz criada anteriormente e criar um novo utilizador de administrador, substitua *\<nome de utilizador\>* e *\<palavra-passe\>* conforme necessário para o seu ambiente. Neste exemplo, o utilizador a ser criado é denominado **sqlsa** e uma palavra-passe segura é usada:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Criar utilizador de admin](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Registe as novas informações de utilizador do MySQL.<br><br>Terá de fornecer o nome de utilizador e palavra-passe, juntamente com o endereço IP público ou o FQDN completo do IP público para o cluster, a um operador de pilha do Azure para que podem criar um servidor de alojamento do MySQL com este cluster MySQL.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Criar um MySQL do Azure Stack que aloja o servidor
Depois do cluster de servidor MySQL foi criado e corretamente configurado, um operador de pilha do Azure tem de criar um servidor de alojamento das MySQL do Azure Stack para disponibilizar a capacidade adicional para os utilizadores criar bases de dados. 

Certifique-se de que a utilizar o IP público ou o FQDN completo para o IP público do cluster MySQL VM principal registou anteriormente quando foi criado o grupo de recursos do cluster MySQL (**mysqlip**). Além disso, o operador tem de saber o servidor MySQL que criou para aceder remotamente a base de dados do cluster do MySQL de credenciais de autenticação.

> [!NOTE]
> Este passo deve ser executado a partir do portal de administração do Azure Stack por um operador de pilha do Azure.

Utilizar IP público e o MySQL início de sessão informações de autenticação o cluster de MySQL, um operador de pilha do Azure podem agora [criar um servidor de hospedagem do MySQL com o novo cluster de MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Certifique-se também de que tiver criado a planos e ofertas para facilitar a criação de base de dados MySQL disponíveis aos utilizadores. Um operador terá de adicionar o **Microsoft.MySqlAdapter** para um plano de serviço e criar uma quota de novo especificamente para bases de dados de elevada disponibilidade. Para obter mais informações sobre a criação de planos, consulte [descrição geral do plano, oferta, quota e subscrição](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> O **Microsoft.MySqlAdapter** serviço não estará disponível para o adicionar aos planos de até a [fornecedor de recursos do servidor MySQL tiver sido implementada](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Criar uma base de dados MySQL elevada disponibilidade
Depois do cluster MySQL foi criado, configurado e adicionado como um servidor de alojamento de MySQL do Azure Stack por um operador de pilha do Azure, um utilizador de inquilino com uma subscrição que inclui capacidades de base de dados do servidor MySQL pode criar bases de dados altamente disponíveis MySQL por seguindo os passos nesta secção. 

> [!NOTE]
> Execute estes passos a partir do portal de utilizador do Azure Stack como um utilizador de inquilino com uma subscrição fornecer funcionalidades de servidor MySQL (Microsoft.MySQLAdapter serviço).

1. 
   [!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Selecione **\+** **criar um recurso** > **dados \+ armazenamento**e, em seguida, **base de dados MySQL** .<br><br>Forneça as informações de propriedade da base de dados necessários, incluindo o nome, agrupamento, a subscrição que pretende utilizar e a localização para utilizar na implementação. 

   ![Criar base de dados MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Selecione **SKU** e, em seguida, escolha o adequado MySQL que aloja servidor SKU para utilizar. Neste exemplo, se criou o operador do Azure Stack a **MySQL-HA** SKU para suportar uma elevada disponibilidade para bases de dados do MySQL cluster.

   ![Selecione o SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Selecione **início de sessão** > **criar um novo início de sessão** e, em seguida, forneça as credenciais de autenticação a ser utilizado para a nova base de dados MySQL. Quando terminar, clique em **OK** e, em seguida **criar** para iniciar o processo de implementação de base de dados.

   ![Adicionar início de sessão](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Quando concluída com êxito a implementação de base de dados do MySQL, reveja as propriedades de base de dados para detetar a cadeia de ligação a utilizar para ligar para a nova base de dados de elevada disponibilidade. 

   ![Ver cadeia de ligação](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um cluster de servidor MySQL a partir de itens do marketplace
> * Criar um MySQL do Azure Stack que aloja o servidor
> * Criar uma base de dados MySQL elevada disponibilidade

Avance para o próximo tutorial para saber como:
> [!div class="nextstepaction"]
> [Oferecer aplicações web](azure-stack-tutorial-app-service.md)
