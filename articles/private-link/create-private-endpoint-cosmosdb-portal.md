---
title: Ligue-se a uma conta Azure Cosmos com Ligação Privada Azure
description: Aprenda a aceder de forma segura à conta Azure Cosmos a partir de um VM criando um Ponto Final Privado.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252590"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Ligue-se em privado a uma conta Azure Cosmos usando o Azure Private Link

Azure Private Endpoint é o bloco de construção fundamental para a Private Link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem em privado com recursos de Private Link.

Neste artigo, você vai aprender a criar um VM em uma rede virtual Azure e uma conta Azure Cosmos com um Endpoint Privado usando o portal Azure. Depois, pode aceder com segurança à conta Azure Cosmos a partir do VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Criar uma VM

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar uma rede virtual e a subnet para acolher o VM que é usado para aceder ao seu recurso Private Link (uma conta Azure Cosmos neste exemplo).

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroup|
| **\<>de nome de rede virtual** | myVirtualNetwork         |
| **\<>de nome da região**          | E.U.A. Centro-Oeste     |
| **\<>espaço de endereçoI4**   | 10.1.0.0\16          |
| **\<>de nome de subnet**          | mySubnet        |
| **\<>de endereços-endereço de subnet** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** > máquina**Compute** > **Virtual**de recurso .

1. Em **Criar uma máquina virtual - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *o myVm*. |
    | Região | Selecione **WestCentralUS**. |
    | Opções de disponibilidade | Deixar o padrão Não é necessário um despedimento de **infraestrutura.** |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o **Padrão DS1 v2**padrão padrão padrão . |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma senha à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reintroduza a senha. |
    | **REGRAS PORTUÁRIAS DE ENTRADA** |  |
    | Portos de entrada pública | Deixe o padrão **Nenhum**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Nº**. |
    |||

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual - Discos,** deixe as predefinições e selecione **Seguinte: Networking**.

1. Em **Criar uma máquina virtual - Networking,** selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**predefinido .  |
    | Espaço de endereços | Deixe o padrão **10.1.0.0/24**.|
    | Subrede | Deixe a mySubnet por defeito **(10.1.0.0/24)**.|
    | IP público | Deixe o **myVm-ip padrão (novo)** |
    | Portos de entrada pública | **Selecione Permitir portas selecionadas**. |
    | Selecione portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração.

1. Quando vir a **mensagem de validação passada,** selecione **Criar**.

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Crie uma [conta API Azure Cosmos SQL](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Para a simplicidade, pode criar a conta Azure Cosmos na mesma região que os outros recursos (isto é, "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Crie um ponto final privado para a sua conta Azure Cosmos

Crie um Link Privado para a sua conta Azure Cosmos, conforme descrito na Create a Private Link utilizando a secção [do portal Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) do artigo ligado.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm* VM da internet da seguinte forma:

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *.rdp* descarregado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Aceda à conta Azure Cosmos em privado a partir do VM

Nesta secção, você irá ligar-se em privado à conta Azure Cosmos usando o Private Endpoint. 

1. Para incluir o endereço IP e o mapeamento dNS, `c:\Windows\System32\Drivers\etc\hosts` inscreva-se na sua máquina virtual *myVM,* abra o ficheiro e inclua as informações dNS do passo anterior no seguinte formato:

   [Endereço IP Privado] [Ponto final da conta].documents.azure.com

   **Exemplo:**

   10.1.255,13 mycosmosaccount.documents.azure.com

   10.1.255,14 mycosmosaccount-eastus.documents.azure.com


1. No ambiente de trabalho remoto do *myVM,* instale o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selecione **Contas DB cosmos (Pré-visualização)** com o clique direito.

1. Selecione **Ligar ao Cosmos DB**.

1. Selecione **API**.

1. Introduza a cadeia de ligação colando as informações anteriormente copiadas.

1. Selecione **Next**.

1. Selecione **Ligar**.

1. Navegue nas bases de dados e contentores da Azure Cosmos a partir da *mycosmosaccount*.

1. (Opcionalmente) adicione novos itens à *conta mycosmos.*

1. Feche a ligação remota de ambiente de trabalho ao *myVM*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar usando o Private Endpoint, a conta Azure Cosmos e o VM, elimine o grupo de recursos e todos os recursos que contém: 

1. Introduza o *myResourceGroup* na caixa **de pesquisa** na parte superior do portal e selecione *myResourceGroup* a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *o myResourceGroup* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um VM numa rede virtual, uma conta Azure Cosmos e um Private Endpoint. Ligou-se ao VM a partir da internet e comunicou-se de forma segura à conta Azure Cosmos usando private link.

* Para saber mais sobre private endpoint, veja [o que é Azure Private Endpoint?](private-endpoint-overview.md)

* Para saber mais sobre a limitação do Private Endpoint ao utilizar com o Azure Cosmos DB, consulte o azure private link com o artigo [da Azure Cosmos DB.](../cosmos-db/how-to-configure-private-endpoints.md)
