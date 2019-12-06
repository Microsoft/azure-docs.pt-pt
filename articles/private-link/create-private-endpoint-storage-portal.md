---
title: Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
description: Saiba como conectar-se de forma privada a uma conta de armazenamento no Azure usando um ponto de extremidade privado.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: bd3e33fde3f4249064bfbe1973ee95f680630673
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851167"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Conectar-se de forma privada a uma conta de armazenamento usando o ponto de extremidade privado do Azure
O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, uma conta de armazenamento com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta de armazenamento da VM.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta de armazenamento neste exemplo).

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar o recurso de link privado.

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual**.
1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Localização | Selecione **WestCentralUS**.|
    | Nome da sub-rede | Insira *mysubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |
    |||
1. Deixe o restante como padrão e selecione **criar**.


### <a name="create-virtual-machine"></a>Criar a máquina virtual

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
    | Selecione as portas de entrada | Selecione **http** e **RDP**.|
    ||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="create-your-private-endpoint"></a>Criar seu ponto de extremidade privado
Nesta seção, você criará uma conta de armazenamento particular usando um ponto de extremidade privado para ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento**.

1. Em **criar conta de armazenamento-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Insira *mystorageaccount*. Se esse nome for obtido, crie um nome exclusivo. |
    | Região | Selecione **WestCentralUS**. |
    | Desempenho| Deixe o **padrão**padrão. |
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v2)** . |
    | Replicação | Selecione **armazenamento com redundância geográfica com acesso de leitura (ra-grs)** . |
    |||
  
3. Selecione **Avançar: rede**.
4. Em **criar uma conta de armazenamento –** método de conectividade, rede, selecione **ponto de extremidade privado**.
5. Em **criar uma conta de armazenamento – rede**, selecione **Adicionar ponto de extremidade privado**. 
6. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    |Localização|Selecione **WestCentralUS**.|
    |Nome|Insira *myPrivateEndpoint*.  |
    |Subrecurso de armazenamento|Deixe o **blob**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Subrede | Selecione *mysubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO**|  |
    | Integrar com a zona DNS privada  | Deixe o padrão **Sim**. |
    | Zona DNS Privado  | Deixe o padrão * * (New) privatelink.blob.core.windows.net * *. |
    |||
7. Selecione **OK**. 
8. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 
10. Navegue até o recurso de conta de armazenamento que você acabou de criar.
11. Selecione **chaves de acesso** no menu conteúdo à esquerda.
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
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Acessar a conta de armazenamento de forma privada da VM

Nesta seção, você se conectará de forma privada à conta de armazenamento usando o ponto de extremidade privado.

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.
2. Insira `nslookup mystorageaccount.blob.core.windows.net` você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Instale o [Explorador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecione **contas de armazenamento** com o botão direito do mouse.
5. Selecione **conectar a um armazenamento do Azure**.
6. Selecione **usar uma cadeia de conexão**.
7. Selecione **Seguinte**.
8. Insira a cadeia de conexão colando as informações copiadas anteriormente.
9. Selecione **Seguinte**.
10. Selecione **Ligar**.
11. Procurar os contêineres de blob de mystorageaccount 
12. Opcionalmente Crie pastas e/ou carregue arquivos no *mystorageaccount*. 
13. Feche a conexão de área de trabalho remota para *myVM*. 

Opções adicionais para acessar a conta de armazenamento:
- Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo gratuito da Microsoft que permite que você trabalhe visualmente com os dados do armazenamento do Azure no Windows, no macOS e no Linux. Você pode instalar o aplicativo para navegar de forma privada o conteúdo da conta de armazenamento. 
 
- O utilitário AzCopy é outra opção para transferência de dados programável por scripts de alto desempenho para o armazenamento do Azure. Utilize o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas. 


## <a name="clean-up-resources"></a>Limpar recursos 
Quando você terminar de usar o ponto de extremidade privado, a conta de armazenamento e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 
1. Insira o *MyResource* na caixa de **pesquisa** na parte superior do portal e selecione o *MyResource* nos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Insira o grupo de *recursos* de para **digite o nome** e selecione **excluir**. 

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você criou uma VM em uma rede virtual e uma conta de armazenamento e um ponto de extremidade privado. Você se conectou a uma VM da Internet e se comunica com segurança à conta de armazenamento usando o link privado. Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](private-endpoint-overview.md).
