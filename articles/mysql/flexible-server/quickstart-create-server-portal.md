---
title: 'Quickstart: Criar um Azure DB para MySQL Flexible Server - Portal Azure'
description: Este artigo passa por você através do portal Azure para criar rapidamente uma Base de Dados Azure para o MySQL Flexible Server em vários minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 0c082c797c75ba912bafead15d24ea3941cfc25e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534197"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Quickstart: Use o portal Azure para criar uma base de dados Azure para o MySQL Flexible Server

Azure Database for MySQL Flexible Server é um serviço gerido que utiliza para executar, gerir e escalar servidores MySQL altamente disponíveis na nuvem. Este quickstart mostra-lhe como criar um servidor flexível em vários usando o portal Azure.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser e, em seguida, avance para o [portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar uma base de dados Azure para o MySQL Flexible Server

Cria-se um servidor flexível com um conjunto definido de recursos de [computação e armazenamento.](./concepts-compute-storage.md) Irá criar o servidor dentro de um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md).

Siga estes passos para criar um servidor flexível:

1. Procure "Azure Database for MySQL" no portal utilizando a caixa de pesquisa para encontrar o serviço. 
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL":::

2. Selecione **Adicionar** . 

3. Na página "Selecione a página de opção de implementação", selecione **o servidor Flexível** como opção de implementação.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL":::    

4. Preencha o formulário Básico com as **seguintes** informações: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL"::: 
                                    
    |**Definição**|**Valor Sugerido**|**Descrição**|
    |---|---|---|
    Subscrição|Nome da sua subscrição|A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual gostaria de ser faturada pelo recurso.|
    Grupo de recursos|*grupo myresource*| Um nome de grupo de recursos novo ou um já existente na sua subscrição.|
    Nome do servidor |*mydemoserver*|Um nome único que identifica o seu servidor flexível. O nome de domínio *mysql.database.azure.com* é anexado ao nome do servidor que fornece. O servidor só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de conter, pelo menos, 3 a 63 carateres.|
    Nome de utilizador de administrador |*mydemouser*| A sua própria conta de início de sessão quando se ligar ao servidor. O nome de login não pode ser **azure_superuser,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público.**|
    Palavra-passe |A sua palavra-passe| Uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. A sua palavra-passe tem de conter carateres de três das categorias seguintes: letras em maiúscula inglesas, letras em minúscula inglesas, números (0 a 9) e carateres não alfanuméricos (!, $, #, %, etc.).|
    Região|A região mais próxima dos seus utilizadores| A localização que esteja mais próxima dos seus utilizadores.|
    Versão|5.7| Versão principal do MySQL.|
    Computação e armazenamento | **Burstable** , **Standard_B1ms** , **10 GiB,** **7 dias** | As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **o servidor Configurar** . *Burstable* , *Standard_B1ms* , *10 GiB* , e *7 dias* são os valores padrão para     **o nível computo,** **tamanho computo,** **armazenamento** e período **de retenção de backup** . Pode deixar os sliders como está ou ajustá-los. Para guardar esta seleção de cálculo e armazenamento, **selecione Guardar** para continuar com as configurações. A imagem abaixo mostra as opções de cálculo e armazenamento.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL":::

5. Opções de rede de configuração

    No separador 'Rede', pode escolher como o seu servidor é acessível. A Azure Database for MySQL Flexible Server oferece duas opções para se conectar ao seu servidor através do *acesso público (endereços IP permitidos)* e *acesso privado (VNet Integration)* . Com *acesso público (endereços IP permitidos)* , o acesso ao seu servidor limita-se a endereços IP autorizados adicionados a uma regra de firewall. Impede que aplicações e ferramentas externas se conectem ao servidor e a quaisquer bases de dados no servidor, a menos que crie uma regra para abrir a firewall para um endereço ou intervalo IP específico. Com *acesso privado (Integração VNet)* , o acesso ao seu servidor está limitado à sua rede virtual. Neste arranque rápido, mostramos-lhe como permitir o acesso do público a ligar-se ao servidor. Saiba mais sobre métodos de conectividade no [artigo de conceitos.](./concepts-networking.md)

    > [!NOTE]
    > O método de conectividade não pode ser alterado após a criação do servidor. Por exemplo, se selecionou *acesso público (endereços IP permitidos)* durante a criação, então não poderá alterar para *acesso privado (Integração VNet)* após a criação. Recomendamos vivamente a criação de um servidor com acesso privado para aceder de forma segura ao seu servidor utilizando a Integração VNet. Saiba mais sobre o acesso privado no [artigo de conceitos.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL":::  

6. Selecione **Review + crie** para rever a configuração flexível do servidor.

7. Selecione **Criar** para aprovisionar o servidor. O provisionamento pode levar alguns minutos.

8. Selecione **Notificações** na barra de ferramentas (o ícone da campainha) para monitorizar o processo de implementação. Uma vez feita a implementação, pode selecionar **Pin para dashboard** , que cria um azulejo para este servidor flexível no painel de instrumentos do portal Azure como um atalho para a página **geral** do servidor. Selecionar **Ir para recurso** abre a página **Descrição geral** do servidor.

Por predefinição, as seguintes bases de dados são criadas no seu servidor: **information_schema** , **mysql** , **performance_schema** e **sys** .

> [!NOTE]
> Verifique se a sua rede permite tráfego de saída sobre a porta 3306 que é utilizada pela Base de Dados Azure para o MySQL Flexible Server para evitar problemas de conectividade.  

## <a name="connect-to-using-mysql-command-line-client"></a>Ligue-se ao cliente da linha de comando mysql

Se criou o seu servidor flexível com *acesso privado (VNet Integration)* , terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível.

Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)* , pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor.

Pode escolher [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) para ligar ao servidor a partir do ambiente local. 

Com mysql.exe, conecte-se utilizando o comando abaixo. Substitua os valores pelo nome e senha do seu servidor. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Limpar os recursos
Criou com sucesso uma Base de Dados Azure para o MySQL Flexible Server num grupo de recursos.  Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos ou simplesmente eliminar o servidor MySQL. Para eliminar o grupo de recursos, siga estes passos:

1. No portal Azure, procure e selecione **grupos de Recursos.**
1. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.
1. Na página geral do seu grupo de recursos, selecione **Delete resource group** .
1. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete** .

Para eliminar o servidor, pode clicar no botão **Eliminar** na página **de visão geral** do seu servidor, tal como mostrado abaixo:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Pesquisa rumo à Base de Dados Azure para o MySQL":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)