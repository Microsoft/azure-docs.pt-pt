---
title: Link Privado - Portal Azure - Base de Dados Azure para MariaDB
description: Saiba como configurar link privado para Azure Database for MariaDB a partir do portal Azure
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 79b3c3f8eca2fa4442a7845ca4aa3921d0302453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659629"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Criar e gerir o Link Privado para Azure Database para MariaDB usando o Portal

Um Ponto Final Privado é o bloco de construção fundamental para a ligação privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem privadamente com recursos de ligação privada.  Neste artigo, você aprenderá a usar o portal Azure para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para servidor MariaDB com um ponto final privado Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> A funcionalidade de ligação privada só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de base de dados está num destes níveis de preços.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar um VM Azure

Nesta secção, irá criar rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso Private Link (um servidor MariaDB em Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta secção, irá criar uma Rede Virtual e a sub-rede para hospedar o VM que é utilizado para aceder ao seu recurso De Ligação Privada.

1. No lado superior esquerdo do ecrã, **selecione Criar uma** rede virtual  >  **de rede de rede de** recursos  >  .
2. Na **Criar rede virtual,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *a MyVirtualNetwork*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | **Selecione Criar novo,** insira *o myResourceGroup* e, em seguida, selecione **OK**. |
    | Localização | Selecione **Europa Ocidental**.|
    | Sub-rede - Nome | Insira *o mySubnet.* |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
3. Deixe o resto como padrão e **selecione Criar**.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** máquina virtual  >  **compute de** recurso  >  .

2. Na **Criar uma máquina virtual - Básicos, insira** ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da máquina virtual | *Insira o myVm*. |
    | Region | Selecione **Europa Ocidental**. |
    | Opções de disponibilidade | Deixar o incumprimento **Não é necessário um despedimento de infraestrutura**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows** Server . |
    | Tamanho | Deixe o **padrão DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reentre na senha. |
    | **REGRAS DA PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **Nenhum**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o **nº** padrão . |
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

## <a name="create-an-azure-database-for-mariadb"></a>Criar um Azure Database for MariaDB

Nesta secção, irá criar uma Base de Dados Azure para o servidor MariaDB em Azure. 

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar uma** base de  >  **dados de recursos**  >  **Azure Databases for MariaDB**.

1. Na **Base de Dados Azure para MariaDB** fornecem estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | *Insira o meu servidão.* Se este nome for tomado, crie um nome único.|
    | Nome de utilizador de administrador| Insira o nome de administrador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 8 caracteres de comprimento e satisfazer os requisitos definidos. |
    | Localização | Selecione uma região Azure onde pretende querer que o seu Servidor MariaDB resida. |
    |Versão  | Selecione a versão de base de dados do servidor MariaDB que é necessário.|
    | Cálculo + Armazenamento| Selecione o nível de preços necessário para o servidor com base na carga de trabalho. |
    |||

7. Selecione **OK**. 
8. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 
9. Quando vir a mensagem de validação passada, **selecione Criar**. 
10. Quando vir a mensagem de validação passada, selecione Criar. 

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para MariaDB e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.DBforMariaDB** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Nesta secção, irá criar um ponto final privado para o servidor MariaDB. 

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar uma**  >    >  **ligação privada** de rede de recursos .
2. No **Private Link Center - Overview,** sobre a opção de construir uma **ligação privada a um serviço**, selecione **Start**.

    ![Visão geral do Link Privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Na **Criação de um ponto final privado - Básicos, insira** ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **Detalhes da instância** |  |
    | Name | Introduza *myPrivateEndpoint*. Se este nome for tomado, crie um nome único. |
    |Region|Selecione **Europa Ocidental**.|
    |||
5. Selecione **Seguinte: Recurso**.
6. Em **Criar um ponto final privado - Recurso,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione ligar a um recurso Azure no meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.DBforMariaDB/servidores**. |
    | Recurso |Selecione *myServer*|
    |Recurso secundário de destino |Selecione *mariadbServer*|
    |||
7. Selecione **Seguinte: Configuração**.
8. Em **Criar um ponto final privado - Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |**REDES**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com zona DNS privada |Selecione **Yes** (Sim). |
    |Zona DNS Privada |Selecione *(Nova)privatelink.mariadb.database.azure.com* |
    |||

    > [!Note] 
    > Utilize a zona de DNS privada predefinida para o seu serviço ou forneça o nome da zona DE DNS preferida. Consulte a configuração da [zona DNS dos serviços Azure](../private-link/private-endpoint-dns.md) para obter mais detalhes.

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 
2. Quando vir a mensagem **A validação passou**, selecione **Criar**. 

    ![Link Privado criado](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > O FQDN na definição de DNS do cliente não se resolve com o IP privado configurado. Terá de configurar uma zona DNS para o FQDN configurado, como mostrado [aqui.](../dns/dns-operations-recordsets-portal.md)

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

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Aceda ao servidor MariaDB em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.

2. Insira  `nslookup mydemomserver.privatelink.mariadb.database.azure.com` . 

    Irá receber uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a ligação de ligação privada para o servidor MariaDB utilizando qualquer cliente disponível. No exemplo abaixo, usei a [bancada mySQL workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para fazer a operação.


4. Em **Nova ligação,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **MariaDB**.|
    | Nome do servidor| Selecione *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Nome de utilizador | Insira o nome de utilizador como username@servername o fornecido durante a criação do servidor MariaDB. |
    |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor MariaDB. |
    |SSL|**Selecione Necessário**.|
    ||

5. Selecione **a ligação de teste** ou **OK**.

6. (Opcionalmente) Pesquise bases de dados a partir do menu esquerdo e Crie ou consulte informações da base de dados MariaDB

7. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando terminar de usar o ponto final privado, o servidor MariaDB e o VM, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o  *myResourceGroup*   na caixa de **Pesquisa** no topo do portal e selecione o  *myResourceGroup*   a partir dos resultados da pesquisa.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza o myResourceGroup para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste como fazer, criou um VM numa rede virtual, uma Base de Dados Azure para MariaDB e um ponto final privado para acesso privado. Ligou-se a um VM da internet e comunicou-se de forma segura ao servidor MariaDB utilizando o Private Link. Para saber mais sobre os pontos finais privados, consulte [o que é o ponto final privado do Azure.](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md