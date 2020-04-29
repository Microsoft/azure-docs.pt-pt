---
title: Ligar de forma privada a uma conta de armazenamento com o Ponto Final Privado do Azure
description: Aprenda a ligar-se em privado a uma conta de armazenamento em Azure usando um Ponto Final Privado.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115113"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Ligar de forma privada a uma conta de armazenamento com o Ponto Final Privado do Azure
Azure Private Endpoint é o bloco de construção fundamental para a Private Link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem em privado com recursos de Private Link.

Neste Quickstart, você vai aprender a criar um VM numa rede virtual Azure, uma conta de armazenamento com um Private Endpoint usando o portal Azure. Em seguida, pode aceder seguramente à conta de armazenamento a partir do VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta secção, irá criar uma rede virtual e a subnet para acolher o VM que é utilizado para aceder ao seu Private Link Resource (uma conta de armazenamento neste exemplo).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar uma rede virtual e a subnet para acolher o VM que é utilizado para aceder ao seu recurso Private Link.

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroup |
| **\<>de nome de rede virtual** | myVirtualNetwork          |
| **\<>de nome da região**          | E.U.A. Centro-Oeste      |
| **\<>espaço de endereçoI4**   | 10.1.0.0\16          |
| **\<>de nome de subnet**          | mySubnet        |
| **\<>de endereços-endereço de subnet** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Criar a máquina virtual

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
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
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

## <a name="create-your-private-endpoint"></a>Crie o seu Ponto Final Privado
Nesta secção, irá criar uma conta de armazenamento privada utilizando um Ponto Final Privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** > conta de**armazenamento** > **Storage account**de recursos .

1. Na **conta de armazenamento Create - Basics,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | Insira a minha conta de *armazenamento.* Se este nome for tomado, crie um nome único. |
    | Região | Selecione **WestCentralUS**. |
    | Desempenho| Deixe o **Padrão**padrão . |
    | Tipo de conta | Deixe o Armazenamento predefinido **(finalidade geral v2)**. |
    | Replicação | Selecione **armazenamento geo-redundante de acesso de leitura (RA-GRS)**. |
    |||
  
3. Selecione **Seguinte: Networking**.
4. Em **Criar uma conta de armazenamento - Networking,** método de conectividade, selecione Private **Endpoint**.
5. Em Criar uma conta de **armazenamento - Networking**, selecione **Adicionar ponto final privado**. 
6. In **Create Private Endpoint,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    |Localização|Selecione **WestCentralUS**.|
    |Nome|Insira *o meu PrivateEndpoint*.  |
    |Sub-recurso de armazenamento|Deixe a **Blob**predefinida . |
    | **NETWORKING** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* do grupo de recursos *myResourceGroup*. |
    | Subrede | Selecione *mySubnet*. |
    | **Integração privada do DNS**|  |
    | Integrar com zona privada de DNS  | Deixe o padrão **Sim.** |
    | Zona DNS Privado  | Deixe o padrão **(Novo) privatelink.blob.core.windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
9. Quando vir a **mensagem de validação passada,** selecione **Criar**. 
10. Navegue no recurso da conta de armazenamento que acabou de criar.
11. Selecione **Teclas** de acesso a partir do menu de conteúdo esquerdo.
12. Selecione **Copiar** na cadeia de ligação para o teclado1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm* VM da internet da seguinte forma:

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *download.rdp.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Conta de armazenamento de acesso privada a partir do VM

Nesta secção, irá ligar-se em privado à conta de armazenamento utilizando o Ponto Final Privado.

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.
2. Insira `nslookup mystorageaccount.blob.core.windows.net` Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Instale o [Explorador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selecione **contas de Armazenamento** com o clique certo.
5. Selecione **Ligar a um armazenamento azul**.
6. Selecione **Utilize uma corda de ligação**.
7. Selecione **Seguinte**.
8. Introduza a cadeia de ligação colando as informações anteriormente copiadas.
9. Selecione **Seguinte**.
10. Selecione **Ligar**.
11. Navegue nos recipientes Blob a partir da minha conta de armazenamento 
12. (Opcionalmente) Crie pastas e/ou ficheiros de upload para *a minha conta de armazenamento*. 
13. Feche a ligação remota de ambiente de trabalho ao *myVM*. 

Opções adicionais para aceder à conta de armazenamento:
- O Microsoft Azure Storage Explorer é uma aplicação gratuita autónoma da Microsoft que lhe permite trabalhar visualmente com dados de armazenamento Azure no Windows, macOS e Linux. Pode instalar a aplicação para navegar em privado o conteúdo da conta de armazenamento. 
 
- O utilitário AzCopy é outra opção para transferência de dados scriptable de alto desempenho para armazenamento Azure. Utilize o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas. 


## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar usando o Private Endpoint, a conta de armazenamento e o VM, elimine o grupo de recursos e todos os recursos que contém: 
1. Introduza o *myResourceGroup* na caixa **de pesquisa** na parte superior do portal e selecione *myResourceGroup* a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Introduza *o myResourceGroup* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes
Neste Quickstart, criou um VM numa rede virtual e conta de armazenamento e um Private Endpoint. Ligou-se a um VM da internet e comunicou-se de forma segura à conta de armazenamento utilizando o Private Link. Para saber mais sobre private endpoint, veja [o que é Azure Private Endpoint?](private-endpoint-overview.md)
