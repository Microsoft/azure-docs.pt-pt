---
title: Quickstart - Gerir pontos finais privados em Azure
description: Saiba como criar um Endpoint privado usando o portal Azure neste Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021709"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Quickstart: Criar um endpoint privado usando o portal Azure

Um Private Endpoint é o bloco de construção fundamental para ligação privada em Azure. Permite que os recursos azure, como as Máquinas Virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste Quickstart, você aprenderá a criar um VM numa Rede Virtual Azure, um servidor SQL lógico com um ponto final privado Azure usando o portal Azure. Em seguida, pode aceder de forma segura à Base de Dados SQL a partir do VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta secção, irá criar rede virtual e a subnet para acolher o VM que é utilizado para aceder ao seu recurso Private Link (um servidor SQL em Azure neste exemplo).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar uma Rede Virtual e a subnet para acolher o VM que é utilizado para aceder ao seu recurso Private Link.

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | E.U.A. Centro-Oeste    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um recurso**  >  **Compute**Virtual  >  **Machine**.

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
    |||


1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração.

1. Quando vir a **mensagem de validação passada,** selecione **Criar**.

## <a name="create-a-logical-sql-server"></a>Criar um servidor SQL lógico

Nesta secção, irá criar um servidor SQL lógico no Azure. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**base de dados  >  **Databases**  >  **SQL**de base de dados de recursos .

1. Na Base de **dados Create SQL - Basics,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes da base de dados** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da base de dados  | Insira *a minha base de dados.* Se este nome for tomado, crie um nome único. |
    |||
5. No **Servidor,** selecione **Criar novo**. 
6. No **novo servidor,** introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Nome do servidor  | Insira *o meu servidor.* Se este nome for tomado, crie um nome único.|
    | Início de sessão de administrador do servidor| Insira um nome de administrador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 8 caracteres de comprimento e satisfazer os requisitos definidos. |
    | Localização | Selecione uma região Azure onde deseja que o seu Servidor SQL resida. |
    
7. Selecione **OK**. 
8. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
9. Quando vir a mensagem de validação passada, selecione **Criar**. 
10. Quando vir a mensagem de validação passada, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Nesta secção, irá criar um servidor SQL e adicionar-lhe um ponto final privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um Centro**de Ligação Privada de Rede de recursos  >  **Networking**  >  **(Pré-visualização)**.
2. No **Private Link Center - Visão geral,** sobre a opção de construir uma **ligação privada a um serviço,** selecione **Iniciar**.
1. Em **Criar um ponto final privado (Pré-visualização) - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | Name | Insira *o meu PrivateEndpoint*. Se este nome for tomado, crie um nome único. |
    |Região|Selecione **WestCentralUS**.|
    |||
5. Selecione **Seguinte: Recurso**.
6. Em **Criar um ponto final privado - Recurso,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione ligar-se a um recurso Azure no meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Sql/servidores**. |
    | Recurso |Selecione *myServer*|
    |Sub-recurso-alvo |Selecione *sqlServer*|
    |||
7. Selecione **Seguinte: Configuração**.
8. Criar **um ponto final privado (Pré-visualização) - Configuração,** introduzir ou selecionar esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |**NETWORKING**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Subrede | Selecione *mySubnet*. |
    |**Integração privada do DNS**||
    |Integrar com zona privada de DNS |Selecione **Sim**. |
    |Zona Privada de DNS |Selecione *(New)privatelink.database.windows.net* |
    |||

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
2. Quando vir a **mensagem de validação passada,** selecione **Criar**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ligue-se a um VM utilizando o Ambiente de Trabalho Remoto (RDP)


Depois de criar o **myVm,** ligue-o a partir da internet da seguinte forma: 

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *download.rdp.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas**  >  **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-sql-database-privately-from-the-vm"></a>Aceder à Base de Dados SQL em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.

2. Introduza `nslookup myserver.database.windows.net`. 

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instale o [Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. No **Connect ao servidor,** introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **Motor de Base de Dados**.|
    | Nome do servidor| Selecione *myserver.database.windows.net* |
    | Nome de utilizador | Introduza o nome de utilizador como username@servername o que é fornecido durante a criação do servidor SQL. |
    |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor SQL. |
    |Lembre-se da palavra-passe|Selecione **Sim**.|
    |||
1. Selecione **Ligar**.
2. Consulte as bases de dados do menu esquerdo.
3. (Opcionalmente) Crie ou questione informações a partir da minha base de dados.
4. Feche a ligação remota de ambiente de trabalho ao *myVm*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar usando o ponto final privado, o servidor SQL e o VM, elimine o grupo de recursos e todos os recursos que contém: 
1. Introduza o *myResourceGroup* na caixa **de pesquisa** na parte superior do portal e selecione *myResourceGroup* a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Introduza o myResourceGroup para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um VM numa rede virtual, um servidor SQL lógico e um ponto final privado para acesso privado. Ligou-se a um VM da internet e comunicou-se de forma segura à Base de Dados SQL utilizando o Private Link. Para saber mais sobre pontos finais privados, veja o que é o ponto final privado do [Azure?](private-endpoint-overview.md)
