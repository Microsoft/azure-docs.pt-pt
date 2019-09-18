---
title: Conectar-se de forma privada a uma conta de armazenamento usando um ponto de extremidade privado do Azure
description: Saiba como conectar-se de forma privada a uma conta de armazenamento no Azure usando um ponto de extremidade privado.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 93ca3c8c4b7a4da0387d1302b301cf677b689281
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71074978"
---
# <a name="connect-privately-to-a-storage-account-using-an-azure-private-endpoint"></a>Conectar-se de forma privada a uma conta de armazenamento usando um ponto de extremidade privado do Azure
O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, uma conta de armazenamento com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta de armazenamento da VM.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta de armazenamento neste exemplo).

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


### <a name="create-virtual-machine"></a>Criar a máquina virtual

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
    ||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="create-your-private-endpoint"></a>Criar seu ponto de extremidade privado
Nesta seção, você criará uma conta de armazenamento particular usando um ponto de extremidade privado para ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento**.

1. Em **criar conta de armazenamento-noções básicas**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Insira *mystorageaccount*. Se esse nome for obtido, crie um nome exclusivo. |
    | Região | Selecione **WestCentralUS**. |
    | Desempenho| Deixe o **padrão**padrão. |
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v2)** . |
    | Replicação | Selecione **armazenamento com redundância geográfica com acesso de leitura (ra-grs)** . |
    |||
  
3. Selecione **avançar: Rede**.
4. Em **criar uma conta de armazenamento –** método de conectividade, rede, selecione **ponto de extremidade privado**.
5. Em **criar uma conta de armazenamento – rede**, selecione **Adicionar ponto de extremidade privado**. 
6. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    |Location|Selecione **WestCentralUS**.|
    |Name|Insira *myPrivateEndpoint*.  |
    |Subrecurso de armazenamento|Deixe o **blob**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Subnet | Selecione *mysubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO**|  |
    | Integrar com a zona DNS privada  | Deixe o padrão **Sim**. |
    | Zona de DNS privado  | Deixe o padrão  ** (novo) privatelink.blob.Core.Windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **revisão + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 
10. Navegue até o recurso de conta de armazenamento que você acabou de criar.
11. Selecione **chaves de acesso** no menu conteúdo à esquerda.
12. Selecione **copiar** na cadeia de conexão para key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Acessar a conta de armazenamento de forma privada da VM

Nesta seção, você se conectará de forma privada à conta de armazenamento usando o ponto de extremidade privado.

> [!IMPORTANT]
> A configuração de DNS para armazenamento precisa de uma modificação manual no arquivo de hosts para incluir o FQDN da conta específica, modifique o seguinte arquivo usando permissões de administrador no Windows: c:\Windows\System32\Drivers\etc\hosts ou Linux/etc/hosts Inclua as informações de DNS para a conta da etapa anterior no seguinte formato [endereço IP privado] myaccount.blob.core.windows.net

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Digite `nslookup mystorageaccount.blob.core.windows.net` você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
