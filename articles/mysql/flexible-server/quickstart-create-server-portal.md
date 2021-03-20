---
title: 'Quickstart: Criar uma base de dados Azure para servidor flexível MySQL - Portal Azure'
description: Este artigo acompanha-o através do portal Azure para criar uma Base de Dados Azure para o servidor flexível MySQL em minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 074b799a4f0e83c47aac0b2b3fca5386bd45429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521973"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Quickstart: Use o portal Azure para criar uma Base de Dados Azure para servidor flexível MySQL

Azure Database for MySQL Flexible Server é um serviço gerido que pode utilizar para executar, gerir e escalar servidores MySQL altamente disponíveis na nuvem. Este quickstart mostra-lhe como criar um servidor flexível utilizando o portal Azure.

> [!IMPORTANT] 
> A Azure Database for MySQL Flexible Server encontra-se atualmente em pré-visualização pública.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Aceda ao [Portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar uma base de dados Azure para servidor flexível MySQL

Cria-se um servidor flexível com um conjunto definido de recursos de [computação e armazenamento.](./concepts-compute-storage.md) Irá criar o servidor dentro de um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md).

Complete estes passos para criar um servidor flexível:

1. Procure e selecione **Azure Database para servidores MySQL** no portal:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Screenshot que mostra uma pesquisa por Azure Database para servidores MySQL.":::

2. Selecione **Adicionar**. 

3. Na **página de opções de implementação do MySQL Select Azure Database,** selecione **o servidor flexível** como opção de implementação:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Screenshot que mostra a opção de servidor flexível.":::    

4. No separador **Informações Básicas**, introduza as seguintes informações: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Screenshot que mostra o separador Básico da página do servidor Flexível."::: 
                                    
    |**Definição**|**Valor sugerido**|**Descrição**|
    |---|---|---|
    Subscrição|Nome da sua subscrição|A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual deseja ser faturada para o recurso.|
    Grupo de recursos|**grupo myresource**| Um nome de grupo de recursos novo ou um já existente na sua subscrição.|
    Nome do servidor |**mydemoserver**|Um nome único que identifica o seu servidor flexível. O nome de domínio `mysql.database.azure.com` é anexado ao nome do servidor que fornece. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.|
    Nome de utilizador de administrador |**mydemouser**| A sua própria conta de inscrição para utilizar quando se ligar ao servidor. O nome de utilizador administrativo não pode ser **azure_superuser,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público.**|
    Palavra-passe |A sua palavra-passe| Uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. Deve ainda conter caracteres de três das seguintes categorias: letras maiúsculas inglesas, letras minúsculas inglesas, números (0 a 9) e caracteres não alfanuméricos (!, $, #, %, e assim por diante).|
    Region|A região mais próxima dos seus utilizadores| A localização que esteja mais próxima dos seus utilizadores.|
    Versão|**5.7**| Uma versão principal do MySQL.|
    Computação e armazenamento | **Burstable**, **Standard_B1ms**, **10 GiB,** **7 dias** | As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **o servidor Configurar**. **Burstable**, **Standard_B1ms**, **10 GiB**, e **7 dias** são os valores padrão para o **nível compute,** **tamanho computo,** tamanho de **armazenamento** e período de **retenção** de backup . Pode deixar esses valores como está ou ajustá-los. Para guardar a seleção de cálculo e armazenamento, **selecione Guardar** para continuar com a configuração. A imagem que se segue mostra as opções de cálculo e armazenamento.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Screenshot que mostra opções de computação e armazenamento.":::

5. Configure as opções de networking.

    No **separador 'Rede',** pode escolher como o seu servidor é acessível. A azure Database for MySQL Flexible Server fornece duas formas de se ligar ao seu servidor: 
   - Acesso público (endereços IP permitidos)
   - Acesso privado (Integração de VNet) 
   
   Quando utiliza o acesso público, o acesso ao seu servidor limita-se a endereços IP autorizados que adiciona a uma regra de firewall. Este método impede que aplicações e ferramentas externas se conectem ao servidor e a quaisquer bases de dados no servidor, a menos que crie uma regra para abrir a firewall para um endereço ou intervalo IP específico. Quando utiliza acesso privado (Integração VNet), o acesso ao seu servidor está limitado à sua rede virtual. Neste arranque rápido, irá aprender a permitir o acesso do público à ligação ao servidor. [Saiba mais sobre métodos de conectividade no artigo de conceitos.](./concepts-networking.md)

    > [!NOTE]
    > Não é possível alterar o método de conectividade depois de criar o servidor. Por exemplo, se selecionar **acesso público (endereços IP permitidos)** quando criar o servidor, não poderá alterar para **acesso privado (VNet Integration)** após a criação do servidor. Recomendamos vivamente que crie o seu servidor com acesso privado para ajudar a garantir o acesso ao seu servidor através da Integração VNet. [Saiba mais sobre o acesso privado no artigo conceitos.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Screenshot que mostra o separador De Rede.":::  

6. Selecione **Review + crie** para rever a configuração flexível do servidor.

7. Selecione **Criar** para aprovisionar o servidor. O provisionamento pode levar alguns minutos.

8. Selecione **Notificações** na barra de ferramentas (o botão de campainha) para monitorizar o processo de implantação. Depois de ter sido feita a implementação, pode selecionar **Pin para dashboard**, que cria um azulejo para o servidor flexível no seu painel de instrumentos do portal Azure. Este azulejo é um atalho para a página **geral** do servidor. Quando selecionar **Go to resource,** a página **de visão geral** do servidor abre.

Por padrão, estas bases de dados são criadas sob o seu servidor: information_schema, mysql, performance_schema e sys.

> [!NOTE]
> Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída sobre a porta 3306, que é utilizada pela Azure Database para o MySQL Flexible Server.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Ligue-se ao servidor utilizando mysql.exe

Se criou o seu servidor flexível utilizando o acesso privado (Integração VNet), terá de se ligar ao seu servidor a partir de um recurso dentro da mesma rede virtual que o seu servidor. Pode criar uma máquina virtual e adicioná-la à rede virtual criada com o seu servidor flexível. Consulte a [documentação de acesso privado](how-to-manage-virtual-network-portal.md) para saber mais.

Se criou o seu servidor flexível utilizando o acesso público (endereços IP autorizados), pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte a documentação das [regras de firewall](how-to-manage-firewall-portal.md) para obter orientação passo a passo.

Pode utilizar [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [mySQL Workbench](./connect-workbench.md) para se ligar ao servidor a partir do seu ambiente local. 

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se tiver provisionado o seu servidor flexível utilizando **o acesso público,** também pode utilizar [o Azure Cloud Shell](https://shell.azure.com/bash) para se ligar ao seu servidor flexível utilizando o cliente mysql pré-instalado, como mostrado abaixo:

Para utilizar o Azure Cloud Shell para ligar ao seu servidor flexível, terá de permitir o acesso em rede do Azure Cloud Shell ao seu servidor flexível. Para isso, pode aceder à lâmina **de rede** no portal Azure para o seu servidor flexível MySQL e verificar a caixa na secção **Firewall** que diz: "Permitir o acesso público de qualquer serviço Azure dentro do Azure a este servidor" e clicar em Guardar para persistir a definição.

> [!NOTE]
> A verificação do **acesso público a partir de qualquer serviço Azure dentro do Azure a este servidor** deve ser utilizado apenas para desenvolvimento ou teste. Configura a firewall para permitir ligações a partir de endereços IP atribuídos a qualquer serviço ou ativo Azure, incluindo ligações a partir das subscrições de outros clientes.

Clique em **Experimentá-lo** para lançar o Azure Cloud Shell e utilizar os seguintes comandos para ligar ao seu servidor flexível. Use o nome do servidor, o nome de utilizador e a palavra-passe no comando. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se vir a seguinte mensagem de erro enquanto se liga ao seu servidor flexível seguindo o comando anteriormente, falhou a definição da regra de firewall utilizando a regra "Permitir o acesso público de qualquer serviço Azure dentro do Azure a este servidor" mencionado anteriormente ou a opção não é guardada. Por favor, tente configurar a firewall e tente novamente.

ERROR 2002 (HY000): Não é possível ligar ao servidor MySQL <servername> em (115)

## <a name="clean-up-resources"></a>Limpar os recursos
Criou agora uma Base de Dados Azure para servidor flexível MySQL num grupo de recursos. Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos, ou simplesmente apagar o servidor MySQL. Para eliminar o grupo de recursos, complete estes passos:

1. No portal Azure, procure e selecione **grupos de Recursos.**
1. Na lista de grupos de recursos, selecione o nome do seu grupo de recursos.
1. Na página **'Vista Geral'** para o seu grupo de recursos, selecione **Eliminar o grupo de recursos**.
1. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

Para eliminar o servidor, pode selecionar **Eliminar** na página **'Vista Geral'** para o seu servidor, como mostrado aqui:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Screenshot que mostra como apagar um servidor.":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)
