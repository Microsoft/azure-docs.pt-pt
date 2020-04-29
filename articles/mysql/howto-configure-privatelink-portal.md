---
title: Private Link - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar link privado para Base de Dados Azure para MySQL a partir do portal Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4a4824a9f8340b12bca7e18562d723eb24e58b71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371924"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Criar e gerir link privado para base de dados Azure para MySQL usando o Portal

Um Private Endpoint é o bloco de construção fundamental para ligação privada em Azure. Permite que os recursos azure, como as Máquinas Virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste artigo, você aprenderá a usar o portal Azure para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para servidor MySQL com um ponto final privado Azure.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para mySQL suporta níveis de preços otimizados para fins gerais e memória.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar um VM Azure

Nesta secção, irá criar uma rede virtual e a subnet para acolher o VM que é utilizado para aceder ao seu recurso Private Link (um servidor MySQL em Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta secção, irá criar uma Rede Virtual e a subnet para acolher o VM que é utilizado para aceder ao seu recurso Private Link.

1. No lado superior esquerdo do ecrã, selecione **Criar uma** > **rede virtual**de**rede** > de recursos .
2. Na **Create rede virtual,** introduza ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myvirtualNetwork*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **Criar novo,** introduza *o meu Grupo de Recursos*e, em seguida, selecione **OK**. |
    | Localização | Selecione **Europa Ocidental**.|
    | Subnet - Nome | Insira *a minha Subnet*. |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
3. Deixe o resto como padrão e selecione **Criar**.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um recurso** > **Compute** > **Virtual Machine**.

2. Em **Criar uma máquina virtual - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *o myVm*. |
    | Região | Selecione **Europa Ocidental**. |
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

## <a name="create-an-azure-database-for-mysql"></a>Criar uma Base de Dados do Azure para MySQL

Nesta secção, irá criar uma Base de Dados Azure para o servidor MySQL em Azure. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** > base de dados de**dados** > de recursos**Azure para MySQL**.

1. Na Base de **Dados Azure para MySQL** fornecer estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Introduza *o meu Servidor*. Se este nome for tomado, crie um nome único.|
    | Nome de utilizador de administrador| Insira um nome de administrador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 8 caracteres de comprimento e satisfazer os requisitos definidos. |
    | Localização | Selecione uma região Azure onde deseja que o seu Servidor MySQL resida. |
    |Versão  | Selecione a versão base de dados do servidor MySQL que é necessária.|
    | Computação + Armazenamento| Selecione o nível de preços necessário para o servidor com base na carga de trabalho. |
    |||
 
7. Selecione **OK**. 
8. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
9. Quando vir a mensagem de validação passada, selecione **Criar**. 
10. Quando vir a mensagem de validação passada, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Nesta secção, irá criar um servidor MySQL e adicionar-lhe um ponto final privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um link** > **privado**de**rede** > de recursos .

2. No **Private Link Center - Visão geral,** sobre a opção de construir uma **ligação privada a um serviço,** selecione **Iniciar**.

    ![Visão geral do Link Privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Em **Criar um ponto final privado - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **Detalhes de instância** |  |
    | Nome | Insira *o meu PrivateEndpoint*. Se este nome for tomado, crie um nome único. |
    |Região|Selecione **Europa Ocidental**.|
    |||

5. Selecione **Seguinte: Recurso**.
6. Em **Criar um ponto final privado - Recurso,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione ligar-se a um recurso Azure no meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.DBforMySQL/servidores**. |
    | Recurso |Selecione *myServer*|
    |Sub-recurso-alvo |Selecione *mysqlServer*|
    |||
7. Selecione **Seguinte: Configuração**.
8. Em **Criar um ponto final privado - Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |**NETWORKING**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Subrede | Selecione *mySubnet*. |
    |**Integração privada do DNS**||
    |Integrar com zona privada de DNS |Selecione **Sim**. |
    |Zona Privada de DNS |Selecione *(New)privatelink.mysql.database.azure.com* |
    |||

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
2. Quando vir a **mensagem de validação passada,** selecione **Criar**. 

    ![Link Privado criado](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > O FQDN na definição de DNS do cliente não resolve o IP privado configurado. Terá de configurar uma zona DNS para o FQDN configurado, como mostrado [aqui](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ligue-se a um VM utilizando o Ambiente de Trabalho Remoto (RDP)


Depois de criar o **myVm,** ligue-o a partir da internet da seguinte forma: 

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

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Aceda ao servidor MySQL em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.

2. Introduza. `nslookup  myServer.privatelink.mysql.database.azure.com` 

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a ligação de ligação privada para o servidor MySQL utilizando qualquer cliente disponível. No exemplo abaixo usei a [bancada mySQL](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para fazer a operação.

4. Em **nova ligação,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **MySQL**.|
    | Nome do servidor| Selecione *myServer.privatelink.mysql.database.azure.com* |
    | Nome de utilizador | Introduza o username@servername nome de utilizador como o que é fornecido durante a criação do servidor MySQL. |
    |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor MySQL. |
    |SSL|Selecione **Necessário**.|
    ||

5. Selecione Ligar.

6. Consulte as bases de dados do menu esquerdo.

7. (Opcionalmente) Criar ou consultar informações a partir do servidor MySQL.

8. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar usando o ponto final privado, o servidor MySQL e o VM, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o *myResourceGroup* na caixa **de pesquisa** na parte superior do portal e selecione *myResourceGroup* a partir dos resultados da pesquisa.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza o myResourceGroup para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste como-fazer, criou um VM numa rede virtual, uma Base de Dados Azure para o MySQL e um ponto final privado para acesso privado. Ligou-se a um VM da internet e comunicou-se de forma segura ao servidor MySQL utilizando o Private Link. Para saber mais sobre pontos finais privados, consulte o que é o [ponto final privado do Azure.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
