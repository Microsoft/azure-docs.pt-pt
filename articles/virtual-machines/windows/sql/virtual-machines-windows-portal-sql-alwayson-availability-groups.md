---
title: Configurar a elevada disponibilidade para VMs do Azure Resource Manager | Documentos da Microsoft
description: Este tutorial mostra-lhe como criar um grupo de disponibilidade Always On com máquinas virtuais do Azure no modo Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129558"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configure grupos de disponibilidade Always On em máquinas de virtuais do Azure automaticamente: Resource Manager

Este tutorial mostra-lhe como criar um grupo de disponibilidade do SQL Server que utiliza as máquinas virtuais do Azure Resource Manager. O tutorial utiliza os painéis do Azure para configurar um modelo. Pode rever as predefinições, escreva as definições necessárias e atualizar os painéis no portal, à medida que pode percorrer neste tutorial.

O tutorial completo cria um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure que incluem os seguintes elementos:

* Uma rede virtual que tem várias sub-redes, incluindo um front-end e uma sub-rede de back-end
* Dois controladores de domínio que têm um domínio do Active Directory
* Duas máquinas virtuais que executem o SQL Server e que são implementadas na sub-rede de back-end e associadas ao domínio do Active Directory
* Um cluster de ativação pós-falha de três nós com o modelo de quórum maioria de nós
* Um grupo de disponibilidade que tenha duas réplicas com consolidação síncrona de uma base de dados de disponibilidade

A ilustração seguinte representa a solução completa.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencerem a um grupo de recursos.

Antes de começar este tutorial, confirme o seguinte:

* Já tem uma conta do Azure. Se não tiver uma, [Inscreva-se uma conta de avaliação](https://azure.microsoft.com/pricing/free-trial/).
* Já sabe como usar a GUI para Aprovisionar uma máquina virtual do SQL Server da galeria da máquina virtual. Para obter mais informações, consulte [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Já tem uma boa compreensão dos grupos de disponibilidade. Para obter mais informações, consulte [Always On grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se estiver interessado em utilizar grupos de disponibilidade com o SharePoint, consulte também [configurar o SQL Server 2012 Always On grupos de disponibilidade para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
>
>

Neste tutorial, utilize o portal do Azure para:

* Escolha o modelo Always On no portal.
* Reveja as definições de modelo e atualizar algumas definições de configuração para o seu ambiente.
* Monitor do Azure à medida que cria todo o ambiente.
* Ligar a um controlador de domínio e, em seguida, para um servidor que executa o SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Aprovisione o cluster a partir da Galeria
O Azure proporciona uma imagem de galeria para a solução inteira. Para localizar o modelo:

1. Inicie sessão no portal do Azure com a sua conta.
2. No portal do Azure, clique em **criar um recurso** para abrir o **New** painel.
3. Sobre o **New** painel, procure **AlwaysOn**.
   ![Localizar o modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize **Cluster do SQL Server AlwaysOn**.
   ![Modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. No **selecionar um modelo de implementação**, escolha **Resource Manager**.

### <a name="basics"></a>Noções básicas
Clique em **Noções básicas** e configure as seguintes definições:

* **Nome de utilizador de administrador** é uma conta de utilizador que tenha permissões de administrador de domínio e é um membro da função de servidor fixa sysadmin do SQL Server nas duas instâncias do SQL Server. Neste tutorial, utilize **DomainAdmin**.
* **Palavra-passe** é a palavra-passe para a conta de administrador de domínio. Utilize uma palavra-passe complexa. Confirme a palavra-passe.
* **Subscrição** é a subscrição que o Azure cobra para executar todas implementados recursos para o grupo de disponibilidade. Se a sua conta tiver várias subscrições, pode especificar uma subscrição diferente.
* **Grupo de recursos** é o nome para o grupo ao qual pertencem a todos os recursos do Azure que são criados por este modelo. Neste tutorial, utilize **SQL-HA-RG**. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Localização** é a região do Azure em que este tutorial cria os recursos. Escolha uma região do Azure.

Captura de ecrã seguinte é um concluídos **Noções básicas** painel:

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Clique em **OK**.

### <a name="domain-and-network-settings"></a>Definições de domínio e de rede
Este modelo de galeria do Azure cria um domínio e controladores de domínio. Ele também cria uma rede e duas sub-redes. O modelo não é possível criar servidores num domínio existente ou a rede virtual. O passo seguinte configura as definições de domínio e de rede.

Sobre o **definições de domínio e de rede** painel, reveja os valores predefinidos para o domínio e as definições de rede:

* **Nome de domínio de raiz de floresta** é o nome de domínio para o domínio do Active Directory que aloja o cluster. Para o tutorial, utilize **contoso.com**.
* **Nome de rede virtual** é o nome de rede para a rede virtual do Azure. Para o tutorial, utilize **autohaVNET**.
* **Nome de sub-rede do controlador de domínio** é o nome de uma parte da rede virtual que aloja o controlador de domínio. Use **subnet-1**. Esta sub-rede utiliza o prefixo de endereço **10.0.0.0/24**.
* **Nome de sub-rede do SQL Server** é o nome de uma parte da rede virtual que aloja os servidores que execução do SQL Server e o ficheiro de testemunho de partilha. Use **subnet-2**. Esta sub-rede utiliza o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre as redes virtuais no Azure, veja [descrição geral de rede Virtual](../../../virtual-network/virtual-networks-overview.md).  

O **definições de domínio e de rede** deve ser semelhante à seguinte captura de ecrã:

![Definições de domínio e de rede](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, pode alterar esses valores. Neste tutorial, utilize os valores predefinidos.

Reveja as definições e, em seguida, clique em **OK**.

### <a name="availability-group-settings"></a>Definições do grupo de disponibilidade
No **as definições do grupo de disponibilidade**, reveja os valores predefinidos para o grupo de disponibilidade e o serviço de escuta.

* **Nome do grupo de disponibilidade** é o nome de recurso em cluster para o grupo de disponibilidade. Neste tutorial, utilize **Contoso-ag**.
* **Nome de serviço de escuta do grupo de disponibilidade** é utilizado pelo cluster e o Balanceador de carga interno. Clientes que se ligam ao SQL Server podem utilizar este nome para ligar à réplica da base de dados apropriada. Neste tutorial, utilize **Contoso-listener**.
* **Porta de serviço de escuta do grupo de disponibilidade** Especifica a porta TCP do serviço de escuta do SQL Server. Neste tutorial, utilize a porta predefinida, **1433**.

Se necessário, pode alterar esses valores. Neste tutorial, utilize os valores predefinidos.  

![Definições do grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Clique em **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, as definições de armazenamento
No **tamanho da VM, as definições de armazenamento**, escolha um tamanho de máquina virtual do SQL Server e reveja as outras definições.

* **Tamanho da máquina virtual do SQL Server** é o tamanho para máquinas virtuais que executam o SQL Server. Escolha um tamanho de máquina virtual adequado para sua carga de trabalho. Se estiver criando neste ambiente para o tutorial, utilize **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que pode suportar a carga de trabalho. Muitas cargas de trabalho de produção exigem **DS4** ou superior. O modelo cria duas máquinas virtuais desta dimensão e instala o SQL Server em cada um. Para obter mais informações, consulte [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure instala a Enterprise Edition do SQL Server. O custo depende da edição e o tamanho de máquina virtual. Para obter informações detalhadas sobre os custos atuais, consulte [preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Tamanho de máquina virtual do controlador de domínio** é o tamanho de máquina virtual para os controladores de domínio. Para este tutorial utilizam **D2**.
* **Tamanho da máquina virtual de testemunho de partilha de ficheiros** é o tamanho de máquina virtual para o testemunho de partilha de ficheiros. Neste tutorial, utilize **A1**.
* **Conta de armazenamento de SQL** é o nome da conta de armazenamento que contém os dados do SQL Server e os discos de sistema operativo. Neste tutorial, utilize **alwaysonsql01**.
* **Conta de armazenamento de DC** é o nome da conta de armazenamento para os controladores de domínio. Neste tutorial, utilize **alwaysondc01**.
* **Tamanho do disco de dados do SQL Server** em TB é o tamanho do disco de dados do SQL Server em TB. Especifique um número entre 1 e 4. Neste tutorial, utilize **1**.
* **Otimização de armazenamento** define as definições de configuração de armazenamento específico para as máquinas de virtuais do SQL Server com base no tipo de carga de trabalho. Todas as máquinas de virtuais do SQL Server neste cenário utilizar o armazenamento premium com a cache do anfitrião de disco do Azure definido como só de leitura. Além disso, pode otimizar as definições do SQL Server para a carga de trabalho ao escolher uma dessas três configurações:

  * **Carga de trabalho geral** define sem definições de configuração específicas.
  * **Processamento transacional** 1117 e 1118 de sinalizador de rastreio de conjuntos.
  * **Armazenamento de dados** 1117 do sinalizador de rastreio de conjuntos e 610.

Neste tutorial, utilize **carga de trabalho geral**.

![Definições de armazenamento de tamanho VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Reveja as definições e, em seguida, clique em **OK**.

#### <a name="a-note-about-storage"></a>Uma observação sobre armazenamento
Otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, o Azure adiciona um armazenamento adicional de premium 1 TB. Quando um servidor necessita de 2 TB ou mais, o modelo cria um agrupamento de armazenamento em cada máquina virtual do SQL Server. Um agrupamento de armazenamento é uma forma de Virtualização de armazenamento onde vários discos estão configurados para fornecer capacidade superior, resiliência e desempenho.  O modelo, em seguida, cria um espaço de armazenamento no agrupamento de armazenamento e apresenta um disco de dados única para o sistema operativo. O modelo designa este disco como o disco de dados para o SQL Server. O modelo ajusta o agrupamento de armazenamento para o SQL Server com as seguintes definições:

* Tamanho da faixa é a definição de intercalação para o disco virtual. Cargas de trabalho transacionais utilizam 64 KB. Cargas de trabalho de armazém de dados usar 256 KB.
* Resiliência é simples (sem resiliência).

> [!NOTE]
> Armazenamento premium do Azure é localmente redundante e mantém três cópias dos dados numa única região, para que resiliência adicional no agrupamento de armazenamento não é necessária.
>
>

* Contagem de colunas for igual ao número de discos no agrupamento de armazenamento.

Para obter informações adicionais sobre o espaço de armazenamento e agrupamentos de armazenamento, consulte:

* [Descrição geral dos espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx)
* [Cópia de segurança do Windows Server e agrupamentos de armazenamento](https://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre práticas recomendadas de configuração do SQL Server, consulte [melhores práticas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Definições do SQL Server
No **definições do SQL Server**, rever e modificar o prefixo de nome de máquina virtual do SQL Server, versão do SQL Server, conta de serviço do SQL Server e palavra-passe e a agenda de manutenção de aplicação de patches automática SQL.

* **Prefixo de nome do SQL Server** é utilizado para criar um nome para cada máquina virtual do SQL Server. Neste tutorial, utilize **sqlserver**. O modelo de nomes as máquinas virtuais do SQL Server *sqlserver-0* e *sqlserver-1*.
* **Versão do SQL Server** é a versão do SQL Server. Para este tutorial utilizam **SQL Server 2014**. Também pode escolher **SQL Server 2012** ou **SQL Server 2016**.
* **Nome de utilizador de conta de serviço do SQL Server** é o nome de conta de domínio para o serviço SQL Server. Neste tutorial, utilize **sqlservice**.
* **Palavra-passe** é a palavra-passe para a conta de serviço do SQL Server.  Utilize uma palavra-passe complexa. Confirme a palavra-passe.
* **Agenda de manutenção de correção automática do SQL** identifica o dia da semana que Azure automaticamente cria patches para os servidores SQL. Para este tutorial, escreva **Domingo**.
* **Hora de começar a correção automática do SQL manutenção** é a hora do dia para a região do Azure quando a aplicação de patches automática é iniciada.

> [!NOTE]
> A janela de aplicação de patches para cada máquina virtual é escalonada numa hora. Apenas uma máquina virtual é corrigida em simultâneo para evitar a interrupção dos serviços.
>
>

![Definições do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Reveja as definições e, em seguida, clique em **OK**.

### <a name="summary"></a>Resumo
Na página Resumo, o Azure valida as definições. Também pode transferir o modelo. Reveja o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Este painel final contém **termos de utilização**, e **política de privacidade**. Reveja estas informações. Quando estiver pronto para o Azure para começar a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, clique em **criar**.

O portal do Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorar implantação
Monitorize o progresso da implementação do portal do Azure. Um ícone que representa a implementação automaticamente está afixado ao dashboard do portal do Azure.

![Dashboard do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server
As novas instâncias do SQL Server estão em execução em máquinas virtuais que têm endereços IP ligado à internet. É possível o ambiente de trabalho remoto (RDP) diretamente a cada máquina virtual do SQL Server.

Para estabelecer o RDP para um SQL Server, siga estes passos:

1. No dashboard do portal do Azure, certifique-se de que a implementação foi concluída com êxito.
2. Clique em **recursos**.
3. Na **recursos** painel, clique em **sqlserver-0**, que é o nome do computador de uma das máquinas virtuais que está a executar o SQL Server.
4. No painel **sqlserver-0**, clique em **Connect**. O browser pede-lhe se pretende abrir ou guardar o objeto de ligação remota. Clique em **Open** (Abrir).
5. **Ligação de ambiente de trabalho remota** poderá avisá-lo que não é possível identificar o publicador desta ligação remota. Clique em **Ligar**.
6. Segurança do Windows pede-lhe para introduzir as suas credenciais para ligar ao endereço IP do controlador de domínio primário. Clique em **utilizar outra conta**. Para **nome de utilizador**, tipo **contoso\DomainAdmin**. Esta conta que configurou ao definir o administrador do nome de utilizador no modelo. Utilize a palavra-passe complexa que escolheu quando configurou o modelo.
7. **Ambiente de trabalho remoto** poderá avisá-lo que não foi possível autenticar o computador remoto devido a problemas com o seu certificado de segurança. Mostra o nome do certificado de segurança. Se seguiu o tutorial, o nome é **sqlserver 0.contoso.com**. Clique em **Sim**.

Está agora ligado com o RDP à máquina virtual do SQL Server. Pode abrir o SQL Server Management Studio, ligar para a instância predefinida do SQL Server e certifique-se de que o grupo de disponibilidade está configurado.
