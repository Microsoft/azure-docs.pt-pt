---
title: Conectar-se a uma conta do Azure cosmos com o link privado do Azure
description: Saiba como acessar com segurança a conta do Azure Cosmos de uma VM criando um ponto de extremidade privado.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: e54aa00df9efa60cce0fd6fa1da32720f2947b12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851201"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Conectar-se de forma privada a uma conta do Azure Cosmos usando o link privado do Azure

O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.

Neste artigo, você aprenderá a criar uma VM em uma rede virtual do Azure e uma conta do Azure cosmos com um ponto de extremidade privado usando o portal do Azure. Em seguida, você pode acessar com segurança a conta do Azure Cosmos da VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Entre no [portal do Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Criar uma VM

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (uma conta do Azure Cosmos neste exemplo).

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

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

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
    | Palavra-passe | Insira uma senha de sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-Passe | Insira a senha novamente. |
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

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Crie uma [conta da API do SQL do Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Para simplificar, você pode criar a conta do Azure Cosmos na mesma região que os outros recursos (que é "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Criar um ponto de extremidade privado para sua conta do Azure Cosmos

Crie um link privado para sua conta do Azure Cosmos, conforme descrito na seção [criar um link privado usando a Portal do Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) do artigo vinculado.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **transferir ficheiro RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo *. rdp* baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Acessar a conta do Azure Cosmos de forma privada da VM

Nesta seção, você se conectará de forma privada à conta do Azure Cosmos usando o ponto de extremidade privado. 

1. Para incluir o endereço IP e o mapeamento DNS, entre na sua máquina virtual *myVM*, abra o arquivo `c:\Windows\System32\Drivers\etc\hosts` e inclua as informações de DNS da etapa anterior no seguinte formato:

   [Endereço IP privado] [Ponto de extremidade da conta]. Documents. Azure. com

   **Exemplo:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Na Área de Trabalho Remota de *myVM*, instale [Gerenciador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selecione **contas de Cosmos dB (versão prévia)** com o clique com o botão direito do mouse.

1. Selecione **conectar-se a Cosmos DB**.

1. Selecione **API**.

1. Insira a cadeia de conexão colando as informações copiadas anteriormente.

1. Selecione **Seguinte**.

1. Selecione **Ligar**.

1. Procure os bancos de dados do Azure Cosmos e contêineres de *mycosmosaccount*.

1. (Opcionalmente) adicione novos itens a *mycosmosaccount*.

1. Feche a conexão de área de trabalho remota para *myVM*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar o ponto de extremidade privado, a conta do Azure Cosmos e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira o *MyResource* na caixa de **pesquisa** na parte superior do portal e selecione o *MyResource* nos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Insira o grupo de *recursos* de para **digite o nome** e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou uma VM em uma rede virtual, uma conta do Azure Cosmos e um ponto de extremidade privado. Você se conectou à VM da Internet e se comunica com segurança à conta do Azure Cosmos usando o link privado.

* Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](private-endpoint-overview.md).

* Para saber mais sobre a limitação do ponto de extremidade privado ao usar com Azure Cosmos DB, consulte o [link privado do Azure com Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artigo.