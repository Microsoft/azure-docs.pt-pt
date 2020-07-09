---
title: Ligar de forma privada a uma conta de armazenamento com o Ponto Final Privado do Azure
description: Saiba como ligar-se privadamente a uma conta de armazenamento em Azure utilizando um Ponto Final Privado.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1ccbb685ceb406fd7a52edf793b53d9e1c32630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737330"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Ligar de forma privada a uma conta de armazenamento com o Ponto Final Privado do Azure
Azure Private Endpoint é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem em privado com os recursos de Private Link.

Neste Quickstart, você aprenderá a criar um VM numa rede virtual Azure, uma conta de armazenamento com um Private Endpoint usando o portal Azure. Em seguida, pode aceder de forma segura à conta de armazenamento a partir do VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta secção, irá criar rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu Recurso de Ligação Privada (uma conta de armazenamento neste exemplo).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso Private Link.

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | E.U.A. Centro-Oeste      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Criar a máquina virtual

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
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reentre na senha. |
    | **REGRAS PORTUÁRIAS DE ENTRADA** |  |
    | Portos de entrada pública | Deixe o padrão **Nenhum**. |
    | **POUPAR DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o **nº**padrão . |
    |||

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual - Discos,** deixar as predefinições e selecionar **Seguinte: Networking**.

1. Na **Criação de uma máquina virtual - Networking,** selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a **Rede MyVirtual padrão**.  |
    | Espaço de endereços | Deixe o **padrão 10.1.0.0/24**.|
    | Subrede | Deixe o **mySubnet predefinido (10.1.0.0/24)**.|
    | IP público | Deixe o **padrão (novo) myVm-ip**. |
    | Portos de entrada pública | Selecione **Deixe as portas selecionadas.** |
    | Selecione portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Rever + criar**. É levado para a página **'Rever +' onde** o Azure valida a sua configuração.

1. Quando vir a mensagem **de validação passada,** selecione **Criar**.

## <a name="create-your-private-endpoint"></a>Crie o seu Ponto Final Privado
Nesta secção, irá criar uma conta de armazenamento privada utilizando um Ponto Final Privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**conta de armazenamento  >  **de recursos**  >  **Storage account**.

1. Na **conta de armazenamento Create - Básicos, insira**ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da conta de armazenamento  | *Insira a conta de mystorage.* Se este nome for tomado, crie um nome único. |
    | Região | Selecione **WestCentralus**. |
    | Desempenho| Deixe o **padrão padrão**. |
    | Tipo de conta | Deixe o armazenamento predefinido **(finalidade geral v2)**. |
    | Replicação | Selecione **armazenamento geo-redundante de acesso de leitura (RA-GRS)**. |
    |||
  
3. Selecione **Seguinte: Networking**.
4. In **Create a storage account - Networking,** connectivity method, select Private **Endpoint**.
5. In **Create a storage account - Networking**, select Add Private **Endpoint**. 
6. Em **Criar Ponto Final Privado,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    |Localização|Selecione **WestCentralus**.|
    |Name|Insira *o meuPrivateEndpoint*.  |
    |Sub-recurso de armazenamento|Deixe a **Bolha**padrão . |
    | **NETWORKING** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* do grupo de recursos *myResourceGroup*. |
    | Subrede | Selecione *mySubnet*. |
    | **INTEGRAÇÃO PRIVADA DE DNS**|  |
    | Integrar-se com a zona privada de DNS  | Deixe o padrão **Sim**. |
    | Zona DNS Privado  | Deixe o **padrão (Novo) privatelink.blob.core.windows.net**. |
    |||
7. Selecione **OK**. 
8. Selecione **Rever + criar**. É levado para a página **'Rever +' onde** o Azure valida a sua configuração. 
9. Quando vir a mensagem **de validação passada,** selecione **Criar**. 
10. Navegue pelo recurso da conta de armazenamento que acabou de criar.
11. Selecione **As Teclas** de acesso do menu de conteúdo esquerdo.
12. Selecione **Copiar** na cadeia de ligação para a tecla1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm VM* da internet da seguinte forma:

1. Na barra de pesquisa do portal, insira *o myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o *ficheiro.rdp descarregado.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Conta de armazenamento de acesso privada a partir do VM

Nesta secção, irá ligar-se privadamente à conta de armazenamento utilizando o Ponto Final Privado.

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.
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
4. Selecione **contas de Armazenamento** com o clique direito.
5. Selecione **Ligar a um armazenamento azul**.
6. **Selecione Utilize uma cadeia de ligação**.
7. Selecione **Seguinte**.
8. Introduza o fio de ligação colando as informações previamente copiadas.
9. Selecione **Seguinte**.
10. Selecione **Ligar**.
11. Percorra os recipientes Blob da conta mystorage 
12. (Opcionalmente) Crie pastas e/ou faça upload de ficheiros para a *conta mystorage .* 
13. Feche a ligação remota do ambiente de trabalho ao *myVM*. 

Opções adicionais para aceder à conta de armazenamento:
- O Microsoft Azure Storage Explorer é uma aplicação independente da Microsoft que lhe permite trabalhar visualmente com dados de armazenamento Azure no Windows, macOS e Linux. Pode instalar a aplicação para navegar em privado o conteúdo da conta de armazenamento. 
 
- O utilitário AzCopy é outra opção para transferência de dados scriptáveis de alto desempenho para armazenamento Azure. Utilize o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas. 


## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o Ponto Final Privado, a conta de armazenamento e o VM, elimine o grupo de recursos e todos os recursos que contém: 
1. Introduza o *myResourceGroup* na caixa **de Pesquisa** no topo do portal e selecione o *myResourceGroup* a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Introduza *o myResourceGroup* para **type the RESOURCE GROUP NAME** e selecione **Delete**. 

## <a name="next-steps"></a>Próximos passos
Neste Quickstart, criou um VM numa rede virtual e numa conta de armazenamento e num Private Endpoint. Ligou-se a um VM da internet e comunicou-se de forma segura à conta de armazenamento utilizando o Private Link. Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?](private-endpoint-overview.md)
