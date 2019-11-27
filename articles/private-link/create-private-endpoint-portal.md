---
title: Início rápido-gerenciar pontos de extremidade privados no Azure
description: Saiba como criar um ponto de extremidade privado usando o portal do Azure neste guia de início rápido
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2cb3c1d798e48017604d72d8a438d6a1071199ea
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419762"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Início rápido: criar um ponto de extremidade privado usando portal do Azure

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, um servidor de banco de dados SQL com um ponto de extremidade privado do Azure usando o portal do Azure. Em seguida, você pode acessar com segurança o servidor do banco de dados SQL da VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


> [!NOTE]
> Não são permitidos pontos de extremidade particulares em conjunto com pontos de extremidades de serviço na mesma sub-rede!

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um SQL Server no Azure neste exemplo).

### <a name="create-the-virtual-network"></a>Criar a rede virtual


Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual**.
1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Localização | Selecione **WestCentralUS**.|
    | Nome da sub-rede | Insira *mysubnet*. |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
1. Deixe o restante como padrão e selecione **criar**.


### <a name="create-virtual-machine"></a>Criar Máquina Virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 datacenter**. |
    | Tamanho | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Utilizador | Insira um nome de usuário de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar senha | Digite a senha novamente. |
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
    | Subnet | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-IP**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Selecione **http** e **RDP**.|
    |||


1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL
Nesta seção, você criará um servidor de banco de dados SQL no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **bancos** de **dados > banco de dados SQL**.

1. Em **criar banco de dados SQL-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do banco de dados** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da base de dados  | Digite *MyDatabase*. Se esse nome for obtido, crie um nome exclusivo. |
    |||
5. Em **servidor**, selecione **criar novo**. 
6. Em **novo servidor**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Nome do servidor  | Digite *meuservidor*. Se esse nome for obtido, crie um nome exclusivo.|
    | Início de sessão de administrador do servidor| Insira um nome de administrador de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A senha deve ter pelo menos 8 caracteres e atender aos requisitos definidos. |
    | Localização | Selecione uma região do Azure na qual deseja que o SQL Server resida. |
    
7. Selecione **OK**. 
8. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem validação aprovada, selecione **criar**. 
10. Quando você vir a mensagem validação aprovada, selecione criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um SQL Server e adicionará um ponto de extremidade privado a ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **rede** > **central de links privado (versão prévia)** .
2. No **central de links privado-visão geral**, na opção de **criar uma conexão privada com um serviço**, selecione **Iniciar**.
1. Em **criar um ponto de extremidade privado (visualização)-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome | Insira * myPrivateEndpoint*. Se esse nome for obtido, crie um nome exclusivo. |
    |Região|Selecione **WestCentralUS**.|
    |||
5. Selecione **Avançar: recurso**.
6. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione conectar a um recurso do Azure em meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft. SQL/Servers**. |
    | Resource |Selecionar *meuservidor*|
    |Sub-recurso de destino |Selecionar *sqlServer*|
    |||
7. Selecione **Avançar: configuração**.
8. Em **criar um ponto de extremidade privado (visualização)-configuração**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Subnet | Selecione *mysubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privada |Selecione **Sim**. |
    |Zona de DNS privado |Selecione *(novo) privatelink. Database. Windows. net* |
    |||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
2. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando Área de Trabalho Remota (RDP)


Depois de criar **myVm*, conecte-se a ele da Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **baixar arquivo RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo. rdp * baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

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
    ```
3. Instale o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Em **conectar ao servidor**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **Motor de Base de Dados**.|
    | Nome do servidor| Selecionar *MyServer.Database.Windows.net* |
    | Nome de utilizador | Insira o nome de usuário como username@servername que é fornecido durante a criação do SQL Server. |
    |Palavra-passe |Insira uma senha fornecida durante a criação do SQL Server. |
    |Lembrar senha|Selecione **Sim**.|
    |||
1. Selecione **Ligar**.
2. Procurar bancos de dados no menu à esquerda.
3. Opcionalmente Criar ou consultar informações de MyDatabase.
4. Feche a conexão de área de trabalho remota para *myVm*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando você terminar de usar o ponto de extremidade privado, o SQL Server e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 
1. Insira o *MyResource* na caixa de **pesquisa** na parte superior do portal e selecione o *MyResource* nos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Insira MyResource GROUP para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou uma VM em uma rede virtual, um servidor de banco de dados SQL e um ponto de extremidade privado para acesso privado. Você se conectou a uma VM da Internet e se comunica com segurança ao servidor do banco de dados SQL usando o link privado. Para saber mais sobre pontos de extremidade privados, consulte [o que é o ponto de extremidades privado do Azure?](private-endpoint-overview.md).
