---
title: Link privado para o método de instalação do portal do banco de dados do Azure para MariaDB (versão prévia)
description: Saiba como configurar o link privado para o banco de dados do Azure para MariaDB do portal do Azure
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4e05de1fc1bfb880767d113e88d5c49082fc146c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976090"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-preview-using-portal"></a>Criar e gerenciar o link privado para o banco de dados do Azure para MariaDB (versão prévia) usando o portal

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.  Neste artigo, você aprenderá a usar o portal do Azure para criar uma VM em uma rede virtual do Azure e um banco de dados do Azure para MariaDB Server com um ponto de extremidade privado do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MariaDB dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um servidor MariaDB no Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual**.
2. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Localização | Selecione **Europa Ocidental**.|
    | Nome da sub-rede | Insira *mysubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |
    |||
3. Deixe o restante como padrão e selecione **criar**.

### <a name="create-virtual-machine"></a>Criar Máquina Virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

2. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **Europa Ocidental**. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 datacenter**. |
    | Tamanho | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de usuário de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-Passe | Digite a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **não**. |
    |||

1. Selecione **Avançar: discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

1. Em **criar uma máquina virtual-rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão.  |
    | Espaço de endereços | Deixe o **10.1.0.0/24**padrão.|
    | Subrede | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-IP**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Selecione **http** e **RDP**.|
    |||


1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="create-an-azure-database-for-mariadb"></a>Criar um Azure Database for MariaDB

Nesta seção, você criará um banco de dados do Azure para o servidor MariaDB no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **bancos** de **dados > banco de dados do Azure para MariaDB**.

1. No **banco de dados do Azure para MariaDB** , forneça estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Digite *meuservidor*. Se esse nome for obtido, crie um nome exclusivo.|
    | Nome de usuário do administrador| Insira um nome de administrador de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A senha deve ter pelo menos 8 caracteres e atender aos requisitos definidos. |
    | Localização | Selecione uma região do Azure onde você deseja que seu servidor MariaDB resida. |
    |Versão  | Selecione a versão do banco de dados do servidor MariaDB que é necessária.|
    | Computação + armazenamento| Selecione o tipo de preço necessário para o servidor com base na carga de trabalho. |
    |||
 
7. Selecione **OK**. 
8. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem validação aprovada, selecione **criar**. 
10. Quando você vir a mensagem validação aprovada, selecione criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um ponto de extremidade privado para o servidor MariaDB. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **rede** > **central de links privado (versão prévia)** .
2. No **central de links privado-visão geral**, na opção de **criar uma conexão privada com um serviço**, selecione **Iniciar**.

    ![Visão geral do link privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Em **criar um ponto de extremidade privado (visualização)-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **Detalhes da instância** |  |
    | Nome | Insira *myPrivateEndpoint*. Se esse nome for obtido, crie um nome exclusivo. |
    |Região|Selecione **Europa Ocidental**.|
    |||
5. Selecione **Avançar: recurso**.
6. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione conectar a um recurso do Azure em meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft. DBforMariaDB/servidores**. |
    | Recurso |Selecionar *meuservidor*|
    |Sub-recurso de destino |Selecionar *mariadbServer*|
    |||
7. Selecione **Avançar: configuração**.
8. Em **criar um ponto de extremidade privado (visualização)-configuração**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Subrede | Selecione *mysubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privada |Selecione **Sim**. |
    |Zona de DNS privado |Selecione *(novo) privatelink. Database. Azure. com* |
    |||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
2. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 

    ![Link privado criado](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando Área de Trabalho Remota (RDP)


Depois de criar o **myVm**, conecte-se a ele da Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **transferir ficheiro RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo *. rdp baixado* .

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Acessar o servidor MariaDB de forma privada da VM

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.

2. Insira `nslookup mydemomserver.mariadb.privatelink.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.mariadb.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MariaDB server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **MariaDB**.|
    | Server name| Select *mydemoserver.MariaDB.privatelink.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the MariaDB server creation. |
    |Password |Enter a password provided during the MariaDB server creation. |
    |SSL|Select **Required**.|
    ||

5. Select **Test Connection** or **OK**.

6. (Optionally) Browse databases from left menu and Create or query information from the MariaDB database

7. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, MariaDB server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for MariaDB, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the MariaDB server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).