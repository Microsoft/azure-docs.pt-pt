---
title: 'Quickstart: Criar um servidor - Portal Azure - Base de Dados Azure para o MySQL'
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar rapidamente uma Base de Dados do Azure de exemplo para o servidor MySQL, em cerca de cinco minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: a3438293bcbf656a371b55605c64a005ae4d599a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541405"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Quickstart: Criar uma Base de Dados Azure para servidor MySQL no portal Azure

A Base de Dados do Azure para MySQL é um serviço gerido que utiliza para executar, gerir e dimensionar as Bases de Dados MySQL de elevada disponibilidade na cloud. Este Início Rápido mostra-lhe como criar um servidor da Base de Dados do Azure para MySQL em cerca de cinco minutos com o portal do Azure.  

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser e, em seguida, avance para o [portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Irá criar uma Base de Dados do Azure para o servidor MySQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-pricing-tiers.md). Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor MySQL:

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Base de Dados**  >  **Azure Databases para o MySQL** . Também pode inserir **o MySQL** na caixa de pesquisa para encontrar o serviço.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Base de Dados do Azure para opção MySQL":::

3. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Base de Dados do Azure para opção MySQL":::

**Definição** | **Valor sugerido** | **Descrição do campo** 
---|---|---
Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
Grupo de recursos | *grupo myresource* | Forneça um novo nome do grupo de recursos ou um existente. O grupo de recursos pode ser usado para organizar as suas dependências que pertencem a um único projeto.
Nome do servidor | Nome de servidor exclusivo | Introduza um nome único que identifique a sua Base de Dados Azure para o servidor MySQL. Por exemplo, "mysqldbserver". O nome do servidor pode conter apenas letras minúsculas, números e o caracteres hífen (-) do hífen. Tem de conter entre 3 e 63 carateres.
Origem de dados |*Nenhuma* | Selecione *Nenhum* para criar um novo servidor de raiz. (Você selecionaria *Backup* se estivesse a criar um servidor a partir de uma cópia de segurança de uma base de dados Azure existente para servidor MySQL).
Início de sessão de administrador do servidor | myadmin | Introduza um nome de utilizador para o administrador do seu servidor. Não é possível utilizar **azure_superuser,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público** como nome de utilizador administrativo.
Palavra-passe | *A sua escolha* | Forneça uma palavra-passe nova para a conta de administrador do servidor. A palavra-passe deve ter 8 a 128 caracteres de comprimento com uma combinação de letras maiúsculas ou minúsculas, números e caracteres não alfanuméricos (!, $, #, %, e assim por diante).
Confirmar palavra-passe | *A sua escolha*| Confirme a palavra-passe da conta de administrador.
Localização | *A região mais próxima dos seus utilizadores*| Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
Versão | *A versão principal mais recente*| A versão principal mais recente (a não ser que tenha requisitos específicos que exijam outra versão).
Cálculo + Armazenamento | **Fins Gerais** , **Geração 5** , **2 vCores** , **5 GB** , **7 dias** , **Geograficamente Redundante** |As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **o servidor Configurar** . Em seguida, selecione o nível de preços apropriado, para mais informações, consulte [a página de preços](https://azure.microsoft.com/pricing/details/mysql/). Para ativar as cópias de segurança do servidor no armazenamento geo-redundante, selecione **Geographically Redundante** das **Opções de Redundância de Backup** . Selecione **OK** .

   > [!NOTE]
   > Considere usar o nível de preços básico se o cálculo leve e a I/O forem adequados para a sua carga de trabalho. Note que os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para Final Geral ou Memória Otimizada. 

4. Selecione **Review + criar** para obter o servidor. O aprovisionamento pode demorar até 20 minutos.
   
5. Selecione **Notificações** na barra de ferramentas (o ícone da campainha) para monitorizar o processo de implementação.
   
Por predefinição, as seguintes bases de dados são criadas no seu servidor: **information_schema** , **mysql** , **performance_schema** e **sys** .

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor
Por predefinição, o servidor criado está protegido com uma firewall e não está acessível publicamente. Para dar acesso ao seu IP, aceda ao seu recurso de servidor no portal Azure e selecione a segurança de **ligação** do menu do lado esquerdo para o seu recurso do servidor. Não sei como encontrar o seu recurso, veja [como abrir um recurso.](../azure-resource-manager/management/manage-resources-portal.md#open-resources)

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Base de Dados do Azure para opção MySQL":::
   
Agora **selecione Adicionar o endereço IP do cliente atual** e, em seguida, selecione **Guardar** . Pode adicionar IPs adicionais ou fornecer uma gama IP para ligar ao seu servidor a partir desses IPs. Para obter mais informações, consulte [Como gerir as regras de firewall na Base de Dados Azure para o servidor MySQL](./concepts-firewall-rules.md)

> [!NOTE]
> Verifique se a sua rede permite tráfego de saída sobre a porta 3306 que é utilizada pela Azure Database para o MySQL para evitar problemas de conectividade.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Ligue-se à Base de Dados Azure para servidor MySQL utilizando o cliente da linha de comando mysql
Pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) para ligar ao servidor a partir do ambiente local. Neste arranque rápido, correremos **mysql.exe** em [Azure Cloud Shell](../cloud-shell/overview.md) para ligar ao servidor.

1. Lance Azure Cloud Shell no portal clicando no ícone realçado no lado superior esquerdo. Tome nota do nome do seu servidor, nome de login de administrador do servidor, palavra-passe e subscrição do seu servidor recém-criado a partir da secção **'Vista Geral',** tal como mostrado na imagem abaixo.

    >[!NOTE]
    >Se estiver a lançar a casca de nuvem pela primeira vez, verá um pedido para criar um grupo de recursos, uma conta de armazenamento. Este é um passo único e será automaticamente anexado para todas as sessões. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Base de Dados do Azure para opção MySQL":::
2. Executar este comando no terminal Azure Cloud Shell. Substitua os valores pelo nome real do servidor e pelo nome de início de sessão do utilizador administrativo. O nome de utilizador de administração requer '@ \<servername> como mostrado abaixo para Azure Database for MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Eis como a experiência se parece no terminal Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. No mesmo terminal Azure Cloud Shell, crie um **hóspede** de base de dados 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Alterar para **hóspede** de base de dados
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Escreva ```quit``` , e, em seguida, selecione a tecla Entrar para sair do mysql.   

## <a name="clean-up-resources"></a>Limpar os recursos
Criou com sucesso uma Base de Dados Azure para servidor MySQL num grupo de recursos.  Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos ou simplesmente eliminar o servidor MySQL. Para eliminar o grupo de recursos, siga estes passos:
1. No portal Azure, procure e selecione **grupos de Recursos.** 
2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.
3. Na página geral do seu grupo de recursos, selecione **Delete resource group** .
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete** .

Para eliminar o servidor, pode clicar no botão **Eliminar** na página **de visão geral** do seu servidor, tal como mostrado abaixo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Base de Dados do Azure para opção MySQL":::

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
>[Construir uma aplicação PHP no Windows com o MySQL](../app-service/tutorial-php-mysql-app.md) 
> [Construa aplicativo PHP no Linux com MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux) 
> [Construir aplicativo de primavera baseado em Java com MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)