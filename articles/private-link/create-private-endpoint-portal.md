---
title: Gerenciar pontos de extremidade privados no Azure
description: Saiba como criar um ponto de extremidade privado usando o portal do Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: e629a921b5c853f1cae2c224be35570cd71e5d37
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067802"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Criar um ponto de extremidade privado usando portal do Azure

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, um servidor de banco de dados SQL com um ponto de extremidade privado do Azure usando Azure PowerShell. Em seguida, você pode acessar com segurança o servidor do banco de dados SQL da VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um SQL Server no Azure neste exemplo).

### <a name="create-the-virtual-network"></a>Criar a rede virtual


Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No lado superior esquerdo da tela, selecione **criar um recurso** >  > rede redes**virtuais**.
1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Insira *MyVirtualNetwork*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscription | Selecione a sua subscrição.|
    | Resource group | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Location | Selecione **WestCentralUS**.|
    | Nome da sub-rede | Insira *mysubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |
    |||
1. Deixe o restante como padrão e selecione **criar**.


### <a name="create-virtual-machine"></a>Criar Máquina Virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **Myresourceattribute**. Você criou isso na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Image | Selecione **Windows Server 2019 datacenter**. |
    | Size | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de usuário de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Digite a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **não**. |
    |||

1. Selecione **avançar: Discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **avançar: Rede**.

1. Em **criar uma máquina virtual-rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão.  |
    | Espaço de endereços | Deixe o **10.1.0.0/24**padrão.|
    | Subnet | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP Público | Deixe o padrão **(novo) myVm-IP**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Selecione **http** e **RDP**.|
    |||


1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL
Nesta seção, você criará um servidor de banco de dados SQL no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **bancos** > de**dados SQL Database**.

1. Em **criar banco de dados SQL-noções básicas**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **Detalhes do banco de dados** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da base de dados  | Digite *MyDatabase*. Se esse nome for obtido, crie um nome exclusivo. |
    |||
5. Em **servidor**, selecione **criar novo**. 
6. Em **novo servidor**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    |Nome do servidor  | Digite *meuservidor*. Se esse nome for obtido, crie um nome exclusivo.|
    | Início de sessão de administrador do servidor| Insira um nome de administrador de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A senha deve ter pelo menos 8 caracteres e atender aos requisitos definidos. |
    | Location | Selecione uma região do Azure na qual deseja que o SQL Server resida. |
    
7. Selecione **OK**. 
8. Selecione **revisão + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem validação aprovada, selecione **criar**. 
10. Quando você vir a mensagem validação aprovada, selecione criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um SQL Server e adicionará um ponto de extremidade privado a ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **rede** > **central de links (versão prévia)** .
2. No **central de links privado-visão geral**, na opção de **criar uma conexão privada com um serviço**, selecione **Iniciar**.
1. Em **criar um ponto de extremidade privado (visualização)-noções básicas**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Name | *Insira myPrivateEndpoint*. Se esse nome for obtido, crie um nome exclusivo. |
    |Região|Selecione **WestCentralUS**.|
    |||
5. Selecione **avançar: Recurso**.
6. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    |Método de ligação  | Selecione conectar a um recurso do Azure em meu diretório.|
    | Subscription| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft. SQL/Servers**. |
    | Resource |Selecionar *meuservidor*|
    |Sub-recurso de destino |Selecionar *sqlServer*|
    |||
7. Selecione **avançar: Configuração**.
8. Em **criar um ponto de extremidade privado (visualização)-configuração**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Subnet | Selecione *mysubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privada |Selecione **Sim**. |
    |Zona de DNS privado |Selecione *(novo) privatelink. Database. Windows. net* |
    |||

1. Selecione **revisão + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
2. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando Área de Trabalho Remota (RDP)


Depois de criar **myVm*, conecte-se a ele da Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **transferir ficheiro RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo. rdp * baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais que você inseriu quando criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Acessar o servidor do banco de dados SQL de forma privada da VM

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Insira `nslookup myserver.database.windows.net`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

