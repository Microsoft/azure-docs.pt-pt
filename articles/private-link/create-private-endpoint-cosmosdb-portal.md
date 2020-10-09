---
title: Ligue-se a uma conta da Azure Cosmos com a Azure Private Link
description: Aprenda a aceder de forma segura à conta Azure Cosmos a partir de um VM, criando um Ponto Final Privado.
author: asudbring
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 72dbddd449afb262941de93fd812428554496339
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849015"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Ligar de forma privada a uma conta do Azure Cosmos com o Azure Private Link

Azure Private Endpoint é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem em privado com os recursos de Private Link.

Neste artigo, ficará a saber como criar uma VM na rede virtual do Azure e uma conta do Azure Cosmos com um Ponto Final Privado com o portal do Azure. Em seguida, poderá aceder em segurança à conta do Azure Cosmos a partir da VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Criar uma VM

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar uma rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso Private Link (uma conta Azure Cosmos neste exemplo).

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup|
| **\<virtual-network-name>** | myVirtualNetwork         |
| **\<region-name>**          | E.U.A. Centro-Oeste     |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**máquina Virtual  >  **Compute de**recurso  >  **Virtual machine**.

1. Na **Criar uma máquina virtual - Básicos, insira**ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da máquina virtual | *Insira o myVm*. |
    | Região | Selecione **WestCentralus**. |
    | Opções de disponibilidade | Deixar o incumprimento **Não é necessário um despedimento de infraestrutura**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows**Server . |
    | Tamanho | Deixe o **padrão DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Insira uma senha à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reentre na senha. |
    | **REGRAS DA PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **Nenhum**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o **nº**padrão . |
    |||

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual – Discos**, mantenha as predefinições e selecione **Seguinte: Redes**.

1. Na **Criação de uma máquina virtual - Networking,** selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a **Rede MyVirtual padrão**.  |
    | Espaço de endereços | Deixe o **padrão 10.1.0.0/24**.|
    | Sub-rede | Deixe o **mySubnet predefinido (10.1.0.0/24)**.|
    | IP público | Deixe o **padrão (novo) myVm-ip**. |
    | Portas de entrada públicas | Selecione **Deixe as portas selecionadas.** |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

1. Quando vir a mensagem **A validação passou**, selecione **Criar**.

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Criar uma [conta Azure Cosmos SQL API](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Para simplificar, pode criar a conta Azure Cosmos na mesma região que os outros recursos (que é "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Crie um Ponto Final Privado para a sua conta Azure Cosmos

Crie uma ligação privada para a sua conta Azure Cosmos, conforme descrito no Criar uma Ligação Privada utilizando a secção do [portal Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) do artigo ligado.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm VM* da internet da seguinte forma:

1. Na barra de pesquisa do portal, introduza *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *.rdp* descarregado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Aceda à conta Azure Cosmos em privado a partir do VM

Nesta secção, você irá ligar-se em privado à conta Azure Cosmos usando o Private Endpoint. 

1. Para incluir o endereço IP e o mapeamento DNS, inscreva-se na sua máquina Virtual *myVM,* abra o `c:\Windows\System32\Drivers\etc\hosts` ficheiro e inclua as informações DNS do passo anterior no seguinte formato:

   [Endereço IP privado] [Ponto final da conta].documents.azure.com

   **Exemplo:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. No Ambiente de Trabalho Remoto do *myVM,* instale o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Selecione **As Contas DB do Cosmos (Pré-visualização)** com o clique direito.

1. Selecione **Connect to Cosmos DB**.

1. Selecione **API**.

1. Introduza o fio de ligação colando as informações previamente copiadas.

1. Selecione **Seguinte**.

1. Selecione **Ligar**.

1. Percorra as bases de dados e contentores da Azure Cosmos a partir da *conta mycosmosaccount*.

1. (Opcionalmente) adicione novos itens à *conta mycosmosac.*

1. Feche a ligação remota do ambiente de trabalho ao *myVM*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a conta Private Endpoint, Azure Cosmos e o VM, elimine o grupo de recursos e todos os recursos que contém: 

1. Introduza o *myResourceGroup* na caixa **de Pesquisa** no topo do portal e selecione o *myResourceGroup* a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *o myResourceGroup* para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um VM numa rede virtual, uma conta Azure Cosmos e um Private Endpoint. Ligou-se ao VM a partir da internet e comunicou-se de forma segura à conta Azure Cosmos utilizando o Private Link.

* Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?](private-endpoint-overview.md)

* Para saber mais sobre a limitação do Private Endpoint ao usar com a Azure Cosmos DB, consulte o artigo [Azure Private Link com Azure Cosmos DB.](../cosmos-db/how-to-configure-private-endpoints.md)
