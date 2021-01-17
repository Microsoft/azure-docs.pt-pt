---
title: 'Quickstart: Criar um servidor - Portal Azure - Base de Dados Azure para o MySQL'
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar uma Base de Dados do Azure de exemplo para o servidor MySQL em cerca de cinco minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: a2e5cd43add6db080441812e4350d6f003ef81a2
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538491"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Quickstart: Criar uma base de dados Azure para servidor MySQL utilizando o portal Azure

A azure Database for MySQL é um serviço gerido que utiliza para executar, gerir e escalar bases de dados MySQL altamente disponíveis na nuvem. Este quickstart mostra-lhe como usar o portal Azure para criar uma Base de Dados Azure para o servidor único MySQL. Também mostra como se conectar ao servidor.

## <a name="prerequisites"></a>Pré-requisitos
É necessária uma subscrição Azure. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Criar uma base de dados Azure para o servidor único MySQL
1. Vá ao [portal Azure](https://portal.azure.com/) para criar uma base de dados MySQL Single Server. Procure e selecione **Azure Database para o MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Encontre a base de dados Azure para o MySQL":::

1. Selecione **Adicionar**.

2. Na **base de dados Select Azure para a página de opções de implementação mySQL,** selecione  **Servidor Único**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Screenshot que mostra a opção de servidor único.":::

3. Introduza as definições básicas para um novo servidor único:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Screenshot que mostra a página do servidor Create MySQL.":::

   **Definição** | **Valor sugerido** | **Descrição**
   ---|---|---
   Subscrição | A sua subscrição | Selecione a subscrição Azure desejada.
   Grupo de recursos | **grupo myresource** | Insira um novo grupo de recursos ou um existente a partir da sua subscrição.
   Nome do servidor | **mydemoserver** | Introduza um nome exclusivo. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Deve conter 3 a 63 caracteres.
   Origem de dados |**Nenhuma** | Selecione **Nenhum** para criar um novo servidor de raiz. Selecione **Backup** apenas se estiver a restaurar a partir de um geo-backup de um servidor existente.
   A localização |A sua localização desejada | Selecione uma localização da lista.
   Versão | A versão principal mais recente| Use a versão mais recente. Consulte [todas as versões suportadas.](../mysql/concepts-supported-versions.md)
   Computação e armazenamento | Use os predefinidos| O nível de preços predefinidos é **Finalidade Geral** com **4 vCores** e armazenamento **de 100 GB.** A retenção de backup está definida para **7 dias,** com a opção de backup **geograficamente redundante.**<br/>[Reveja a](https://azure.microsoft.com/pricing/details/mysql/) página de preços e atualize os predefinidos se necessário.
   Nome de utilizador de administrador | **mydemoadmin** | Insira o nome de utilizador do seu administrador de servidor. Não pode utilizar **azure_superuser,** **administrador,** **raiz,** **hóspede** ou **público** para o nome de utilizador administrativo. 
   Palavra-passe | Uma palavra-passe | Uma nova palavra-passe para o utilizador de administração do servidor. A palavra-passe deve ter 8 a 128 caracteres e conter uma combinação de letras maiúsculas ou minúsculas, números e caracteres não alfanuméricos (!, $, #, %, e assim por diante).
  

   > [!NOTE]
   > Considere usar o nível de preços básico se o cálculo leve e a I/O forem adequados para a sua carga de trabalho. Note que os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para Final Geral ou Memória Otimizada.

4. Selecione **Review + criar** para obter o servidor.

5. Aguarde até que a página do portal apresente **A implementação está concluída**. Selecione **Ir para** o recurso para ir para a página do servidor recém-criada:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Screenshot que mostra a sua implementação é mensagem completa.":::

[Tendo problemas? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Por predefinição, o novo servidor está protegido com uma firewall. Para se conectar, deve fornecer acesso ao seu IP, completando estes passos:

1. Aceda à segurança de **ligação** do painel esquerdo para o seu recurso de servidor. Se não sabe como encontrar o seu recurso, consulte [Como abrir um recurso.](../azure-resource-manager/management/manage-resources-portal.md#open-resources)

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Screenshot que mostra a página de regras de > firewall de segurança de ligação.":::

2. **Selecione Adicionar o endereço IP do cliente atual** e, em seguida, selecione **Guardar**.

   > [!NOTE]
   > Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída sobre a porta 3306, que é utilizada pela Azure Database para o MySQL. 

Pode adicionar mais IPs ou fornecer uma gama IP para ligar ao seu servidor a partir desses IPs. Para obter mais informações, consulte [como gerir as regras de firewall numa Base de Dados Azure para o servidor MySQL](./concepts-firewall-rules.md).


[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Ligue-se ao servidor utilizando mysql.exe
Pode utilizar [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [mySQL Workbench](./connect-workbench.md) para se ligar ao servidor a partir do seu ambiente local. Neste arranque rápido, usaremos mysql.exe em [Azure Cloud Shell](../cloud-shell/overview.md) para ligar ao servidor.


1. Abra o Azure Cloud Shell no portal selecionando o primeiro botão na barra de ferramentas, como mostra a imagem seguinte. Note o nome do servidor, o nome de administração do servidor e a subscrição do seu novo servidor na secção **Vista Geral,** tal como mostrado na imagem.

    > [!NOTE]
    > Se abrir a Cloud Shell pela primeira vez, será solicitado a criar um grupo de recursos e uma conta de armazenamento. Este é um passo único. Será automaticamente anexado para todas as sessões.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Screenshot que mostra Cloud Shell no portal Azure.":::
2. Executar o seguinte comando no terminal Azure Cloud Shell. Substitua os valores mostrados aqui pelo nome de utilizador do servidor e pelo nome de utilizador administrativo. Para a base de dados Azure para o MySQL, tem de adicionar `@\<servername>` ao nome de utilizador administrativo, como mostrado aqui: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Aqui está o que parece no terminal Cloud Shell:

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
3. No mesmo terminal Azure Cloud Shell, crie uma base de dados chamada `guest` :
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Mude para a `guest` base de dados:
      ```
      mysql> USE guest;
      Database changed
      ```
5. `quit`Insira e, em seguida, selecione **Enter** para sair do mysql.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Limpar recursos
Criou agora uma Base de Dados Azure para servidor MySQL num grupo de recursos.  Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos, ou simplesmente apagar o servidor MySQL. Para eliminar o grupo de recursos, complete estes passos:
1. No portal Azure, procure e selecione **grupos de Recursos.**
2. Na lista de grupos de recursos, selecione o nome do seu grupo de recursos.
3. Na página **'Vista Geral'** para o seu grupo de recursos, selecione **Eliminar o grupo de recursos**.
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

Para eliminar o servidor, pode selecionar **Eliminar** na página **'Vista Geral'** para o seu servidor, como mostrado aqui:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Screenshot que mostra o botão Eliminar na página de visão geral do servidor.":::

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
>[Construir uma aplicação PHP no Windows com o MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Construa aplicativo PHP no Linux com MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)
