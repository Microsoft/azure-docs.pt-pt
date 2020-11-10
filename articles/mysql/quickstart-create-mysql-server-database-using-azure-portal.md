---
title: 'Quickstart: Criar um servidor - Portal Azure - Base de Dados Azure para o MySQL'
description: Este artigo mostra-lhe como utilizar o portal do Azure para criar rapidamente uma Base de Dados do Azure de exemplo para o servidor MySQL, em cerca de cinco minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: eabc077080e16578857f9ba06e6874441dad07ee
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425861"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Quickstart: Criar uma Base de Dados Azure para servidor MySQL no portal Azure

A Base de Dados do Azure para MySQL é um serviço gerido que utiliza para executar, gerir e dimensionar as Bases de Dados MySQL de elevada disponibilidade na cloud. Este quickstart mostra-lhe como usar o portal Azure para criar uma Base de Dados Azure para o MySQL Single Server e ligar-se ao servidor.

## <a name="prerequisites"></a>Pré-requisitos
É necessária uma subscrição Azure. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Criar uma base de dados Azure para o MySQL Single Server
Vá ao [portal Azure](https://portal.azure.com/) para criar uma base de dados MySQL Single Server. Procure e selecione *Azure Database para o MySQL*.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Encontre a base de dados Azure para o MySQL":::

1. Selecione **Adicionar**.

2. Na base de dados De Azure para a página MySQL , selecione  **Servidor Único**.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Escolha um servidor único":::

3. Agora introduza as definições básicas para um novo servidor Single.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Formulário de criação de servidor":::

   **Definição** | **Valor sugerido** | **Descrição do campo**
   ---|---|---
   Subscrição | A sua subscrição | Selecione a subscrição Azure desejada.
   Grupo de recursos | *grupo myresource* | Um novo ou um grupo de recursos existente da sua subscrição.
   Nome do servidor | *mydemoserver* | Introduza um nome exclusivo. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
   Origem de dados |*Nenhuma* | Selecione Nenhum para criar um novo servidor de raiz. Selecione Backup apenas se estiver a restaurar a partir de um geo-backup de um servidor existente.
   Nome de utilizador de administrador | *mydemoadmin* | Insira o nome de utilizador do seu administrador de serviço. Não é possível utilizar **azure_superuser,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público** como nome de utilizador administrativo.
   Palavra-passe | a sua palavra-passe | Uma nova palavra-passe para o utilizador de administração do servidor. A palavra-passe deve ter 8 a 128 caracteres de comprimento com uma combinação de letras maiúsculas ou minúsculas, números e caracteres não alfanuméricos (!, $, #, %, e assim por diante).
   Localização |sua localização desejada | Selecione uma localização da lista de dropdown.
   Versão | versão principal mais recente| Use a versão mais recente. Ver [todas as versões suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
   Cálculo + Armazenamento | usar padrão| O nível de preços predefinidos é Finalidade Geral com **4 vCores** e armazenamento **de 100 GB.** A retenção de backup está definida para **7 dias** com a opção de backup geograficamente redundante.<br/>Saiba mais sobre os [preços](https://azure.microsoft.com/pricing/details/mysql/) e atualize os predefinidos se necessário.

   > [!NOTE]
   > Considere usar o nível de preços básico se o cálculo leve e a I/O forem adequados para a sua carga de trabalho. Note que os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para Final Geral ou Memória Otimizada.

4. Selecione **Review + criar** para obter o servidor.

5. Aguarde que a página do portal para exibir a **sua implantação esteja completa**. Selecione **Ir para** o recurso para ir para a página do servidor recém-criada.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="implementação bem sucedida":::

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Por predefinição, o servidor criado está protegido com uma firewall. Para se ligar deve dar acesso ao seu IP seguindo estes passos:

1. Aceda à **segurança de ligação** do menu do lado esquerdo para o seu recurso de servidor. Não sei como encontrar o seu recurso, veja [como abrir um recurso.](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Segurança da ligação - regras de firewall":::

2. **Selecione Adicionar o endereço IP do cliente atual** e, em seguida, selecione **Guardar**.

   > [!NOTE]
   > Verifique se a sua rede permite tráfego de saída sobre a porta 3306 que é utilizada pela Azure Database para o MySQL para evitar problemas de conectividade.

Pode adicionar IPs adicionais ou fornecer uma gama IP para ligar ao seu servidor a partir desses IPs. Para obter mais informações, consulte [Como gerir as regras de firewall na Base de Dados Azure para o servidor MySQL](./concepts-firewall-rules.md)


[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-with-mysql-command-line-client"></a>Ligue-se ao servidor com o cliente da linha de comando mysql
Pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) para ligar ao servidor a partir do ambiente local. Neste arranque rápido, correremos **mysql.exe** em [Azure Cloud Shell](../cloud-shell/overview.md) para ligar ao servidor.


1. Lance Azure Cloud Shell no portal clicando no ícone realçado no lado superior esquerdo. Tome nota do nome do seu servidor, nome de login de administrador do servidor, palavra-passe e subscrição do seu servidor recém-criado a partir da secção **'Vista Geral',** tal como mostrado na imagem abaixo.

    > [!NOTE]
    > Se estiver a lançar a casca de nuvem pela primeira vez, verá um pedido para criar um grupo de recursos, uma conta de armazenamento. Este é um passo único e será automaticamente anexado para todas as sessões.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Porta-a-cabeça de nuvem de vista completa":::
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

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Limpar recursos
Criou com sucesso uma Base de Dados Azure para servidor MySQL num grupo de recursos.  Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos ou simplesmente eliminar o servidor MySQL. Para eliminar o grupo de recursos, siga estes passos:
1. No portal Azure, procure e selecione **grupos de Recursos.**
2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.
3. Na página geral do seu grupo de recursos, selecione **Delete resource group**.
4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

Para eliminar o servidor, pode clicar no botão **Eliminar** na página **de visão geral** do seu servidor, tal como mostrado abaixo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Eliminar os seus recursos":::

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
>[Construir uma aplicação PHP no Windows com o MySQL](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[Construa aplicativo PHP no Linux com MySQL](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)
