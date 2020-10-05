---
title: Quickstart - Gerir pontos finais privados em Azure
description: Saiba como criar um Ponto Final Privado utilizando o portal Azure neste Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84021709"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Quickstart: Criar um ponto final privado utilizando o portal Azure

Um Ponto Final Privado é o bloco de construção fundamental para a ligação privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste Quickstart, você aprenderá a criar um VM em uma Rede Virtual Azure, um servidor SQL lógico com um ponto final privado Azure usando o portal Azure. Em seguida, pode aceder de forma segura à Base de Dados SQL a partir do VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM
Nesta secção, irá criar rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso Private Link (um servidor SQL em Azure neste exemplo).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, irá criar uma Rede Virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso De Ligação Privada.

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

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

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**máquina virtual  >  **compute de**recurso  >  **Virtual Machine**.

1. Na **Criar uma máquina virtual - Básicos, insira**ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da máquina virtual | *Insira o myVm*. |
    | Region | Selecione **WestCentralus**. |
    | Opções de disponibilidade | Deixar o incumprimento **Não é necessário um despedimento de infraestrutura**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows**Server . |
    | Tamanho | Deixe o **padrão DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
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
    |||


1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

1. Quando vir a mensagem **A validação passou**, selecione **Criar**.

## <a name="create-a-logical-sql-server"></a>Criar um servidor SQL lógico

Nesta secção, irá criar um servidor SQL lógico em Azure. 

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar uma**base de  >  dados SQL**de bases de dados de**recursos  >  **SQL database**.

1. Na **base de dados Create SQL - Básicos,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes da base de dados** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da base de dados  | Insira *a minha base de dados.* Se este nome for tomado, crie um nome único. |
    |||
5. No **Servidor**, selecione **Criar novo**. 
6. No **novo servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Nome do servidor  | *Insira o meu servidão.* Se este nome for tomado, crie um nome único.|
    | Início de sessão de administrador do servidor| Insira o nome de administrador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 8 caracteres de comprimento e satisfazer os requisitos definidos. |
    | Localização | Selecione uma região Azure onde pretende querer que o seu SQL Server resida. |
    
7. Selecione **OK**. 
8. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 
9. Quando vir a mensagem A validação passou, selecione **Criar**. 
10. Quando vir a mensagem A validação passou, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Nesta secção, irá criar um servidor SQL e adicionar-lhe um ponto final privado. 

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar um**Centro de Ligação Privada em Rede de Recursos  >  **Networking**  >  **(Pré-visualização)**.
2. No **Private Link Center - Overview,** sobre a opção de construir uma **ligação privada a um serviço**, selecione **Start**.
1. Em **Criar um ponto final privado (Pré-visualização) - Básicos, insira**ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DE INSTÂNCIA** |  |
    | Name | Introduza *myPrivateEndpoint*. Se este nome for tomado, crie um nome único. |
    |Region|Selecione **WestCentralus**.|
    |||
5. Selecione **Seguinte: Recurso**.
6. Em **Criar um ponto final privado - Recurso,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione ligar a um recurso Azure no meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Sql/servidores**. |
    | Recurso |Selecione *myServer*|
    |Recurso secundário de destino |Selecione *sqlServer*|
    |||
7. Selecione **Seguinte: Configuração**.
8. Em **Criar um ponto final privado (Pré-visualização) - Configuração,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |**REDES**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com zona DNS privada |Selecione **Sim**. |
    |Zona DNS Privada |Selecione *(Novo)privatelink.database.windows.net* |
    |||

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 
2. Quando vir a mensagem **A validação passou**, selecione **Criar**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ligar a uma VM com o Ambiente de Trabalho Remoto (RDP)


Depois de criar **o myVm,** ligue-o a partir da internet da seguinte forma: 

1. Na barra de pesquisa do portal, introduza *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o *ficheiro.rdp descarregado.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-sql-database-privately-from-the-vm"></a>Acesso SQL Base de Dados privada a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.

2. Introduza `nslookup myserver.database.windows.net`. 

    Irá receber uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instale [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Em **Ligar ao servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **Motor de Base de Dados**.|
    | Nome do servidor| Selecione *myserver.database.windows.net* |
    | Nome de utilizador | Introduza o nome de utilizador como username@servername o fornecido durante a criação do servidor SQL. |
    |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor SQL. |
    |Memorizar palavra-passe|Selecione **Sim**.|
    |||
1. Selecione **Ligar**.
2. Procure bases de dados a partir do menu esquerdo.
3. (Opcionalmente) Criar ou consultar informações a partir da minha base de dados.
4. Feche a ligação remota do ambiente de trabalho ao *myVm*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o ponto final privado, o servidor SQL e o VM, elimine o grupo de recursos e todos os recursos que contém: 
1. Introduza o *myResourceGroup* na caixa **de Pesquisa** no topo do portal e selecione o *myResourceGroup* a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Introduza o myResourceGroup para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um VM numa rede virtual, um servidor SQL lógico e um ponto final privado para acesso privado. Ligou-se a um VM da internet e comunicou-se de forma segura à Base de Dados SQL utilizando o Private Link. Para saber mais sobre os pontos finais privados, veja [o que é o ponto final privado do Azure?](private-endpoint-overview.md)
