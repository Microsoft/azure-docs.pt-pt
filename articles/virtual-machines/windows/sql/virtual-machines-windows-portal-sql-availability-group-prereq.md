---
title: 'Tutorial: pré-requisitos para o grupo de disponibilidade'
description: Este tutorial mostra como configurar os pré-requisitos para criar um grupo de disponibilidade SQL Server Always On em VMs do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 4c89489964410104726b65e1b1fc3577945ce14a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224759"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Pré-requisitos para a criação de grupos de disponibilidade Always On em SQL Server em máquinas virtuais do Azure

Este tutorial mostra como concluir os pré-requisitos para criar um grupo de [disponibilidade SQL Server Always on em VMS (máquinas virtuais) do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Quando você tiver concluído os pré-requisitos, terá um controlador de domínio, duas VMs SQL Server e um servidor testemunha em um único grupo de recursos.

**Tempo estimado**: pode levar algumas horas para concluir os pré-requisitos. Grande parte desse tempo é gasto criando máquinas virtuais.

O diagrama a seguir ilustra o que você cria no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Documentação do grupo de disponibilidade de revisão

Este tutorial pressupõe que você tenha um entendimento básico de SQL Server grupos de disponibilidade Always On. Se você não estiver familiarizado com essa tecnologia, consulte [visão geral de Always on grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Crie uma conta Azure
Precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) ou [ativar os benefícios do assinante do Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **+** para criar um novo objeto no Portal.

   ![Novo objeto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Digite **grupo de recursos** na janela de pesquisa do **Marketplace** .

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Clique em **grupo de recursos**.
5. Clique em **Criar**.
6. Em **nome do grupo de recursos**, digite um nome para o grupo de recursos. Por exemplo, digite **SQL-ha-RG**.
7. Se você tiver várias assinaturas do Azure, verifique se a assinatura é a assinatura do Azure na qual você deseja criar o grupo de disponibilidade.
8. Selecione uma localização. O local é a região do Azure onde você deseja criar o grupo de disponibilidade. Este artigo compila todos os recursos em um local do Azure.
9. Verifique se **a opção fixar no painel** está marcada. Essa configuração opcional coloca um atalho para o grupo de recursos no painel portal do Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Clique em **criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e fixa um atalho para o grupo de recursos no Portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e as sub-redes
A próxima etapa é criar as redes e sub-redes no grupo de recursos do Azure.

A solução usa uma rede virtual com duas sub-redes. A [visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre redes no Azure.

Para criar a rede virtual:

1. No portal do Azure, em seu grupo de recursos, clique em **+ Adicionar**. 

   ![Novo item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Pesquisar **rede virtual**.

     ![Pesquisar rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Clique em **rede virtual**.
4. Na **rede virtual**, clique no modelo de implantação do **Gerenciador de recursos** e, em seguida, clique em **criar**.

    A tabela a seguir mostra as configurações para a rede virtual:

   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereços** |10.33.0.0/24 |
   | **Nome da sub-rede** |administrador |
   | **Intervalo de endereços da sub-rede** |10.33.0.0/29 |
   | **Subscrição** |Especifique a assinatura que você pretende usar. A **assinatura** estará em branco se você tiver apenas uma assinatura. |
   | **Grupo de recursos** |Escolha **usar existente** e escolha o nome do grupo de recursos. |
   | **Localização** |Especifique o local do Azure. |

   O espaço de endereço e o intervalo de endereços de sub-rede podem ser diferentes da tabela. Dependendo de sua assinatura, o portal sugere um espaço de endereço disponível e o intervalo de endereços de sub-rede correspondente. Se não houver espaço de endereço suficiente disponível, use uma assinatura diferente.

   O exemplo usa o **administrador**de nome de sub-rede. Essa sub-rede destina-se aos controladores de domínio.

5. Clique em **Criar**.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

O Azure retorna você ao painel do portal e o notifica quando a nova rede é criada.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma sub-rede denominada **admin**. Os controladores de domínio usam essa sub-rede. As VMs SQL Server usam uma segunda sub-rede chamada **SQL**. Para configurar esta sub-rede:

1. No painel, clique no grupo de recursos que você criou, **SQL-ha-RG**. Localize a rede no grupo de recursos em **recursos**.

    Se o **SQL-ha-RG** não estiver visível, localize-o clicando em **grupos de recursos** e filtrando pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. 
3. Na rede virtual **autoHAVNET** , em **configurações** , selecione **sub-redes**.

    Observe a sub-rede que você já criou.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ sub-rede**.
6. Em **Adicionar sub-rede**, configure a sub-rede digitando **sqlsubnet** em **nome**. O Azure especifica automaticamente um **intervalo de endereços**válido. Verifique se esse intervalo de endereços tem pelo menos 10 endereços. Em um ambiente de produção, você pode precisar de mais endereços.
7. Clique em **OK**.

    ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A tabela a seguir resume os parâmetros de configuração de rede:

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereços** |Esse valor depende dos espaços de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**ADM** |
| **Intervalo de endereços da sub-rede** |Esse valor depende dos intervalos de endereços disponíveis em sua assinatura. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços da sub-rede** |Esse valor depende dos intervalos de endereços disponíveis em sua assinatura. Um valor típico é 10.0.1.0/24. |
| **Subscrição** |Especifique a assinatura que você pretende usar. |
| **Grupo de Recursos** |**SQL-HA-RG** |
| **Localização** |Especifique o mesmo local que você escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, você precisa criar conjuntos de disponibilidade. Conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planejados ou não planejados. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos que o Azure coloca em domínios de falha física e domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade tenham recursos de energia e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não sejam desativados para manutenção ao mesmo tempo. Para obter mais informações, consulte [gerenciar a disponibilidade de máquinas virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Você precisa de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para as VMs SQL Server.

Para criar um conjunto de disponibilidade, vá para o grupo de recursos e clique em **Adicionar**. Filtre os resultados digitando **conjunto de disponibilidade**. Clique em **conjunto de disponibilidade** nos resultados e, em seguida, clique em **criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros na tabela a seguir:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade de SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Atualizar domínios** |5 |3 |

Depois de criar os conjuntos de disponibilidade, retorne ao grupo de recursos no portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, as sub-redes e os conjuntos de disponibilidade, você estará pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Criar máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, retorne ao grupo de recursos **SQL-ha-RG** .

1. Clique em **Adicionar**. 
2. Digite **Windows Server 2016 datacenter**.
3. Clique em **Windows Server 2016 datacenter**. No **Windows Server 2016 datacenter**, verifique se o modelo de implantação é **Resource Manager**e, em seguida, clique em **criar**. 

Repita as etapas anteriores para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > A máquina virtual **ad-Secondary-DC** é opcional, para fornecer alta disponibilidade para Active Directory Domain Services.
  >
  >

A tabela a seguir mostra as configurações para essas duas máquinas:

| **Campo** | Valor |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-Primary-DC*.</br>Segundo controlador de domínio *ad-Secondary-DC*. |
| **Tipo de disco da VM** |SSD |
| **Nome de utilizador** |DomainAdmin |
| **Palavra-passe** |Contoso!0000 |
| **Subscrição** |*A sua subscrição* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localização** |*Seu local* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Usar discos gerenciados** - **Sim** |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |ADM |
| **Endereço IP público** |*Mesmo nome que a VM* |
| **Grupo de segurança de rede** |*Mesmo nome que a VM* |
| **Conjunto de disponibilidade** |adavailabilityset </br>**Domínios de falha**: 2 </br>**Domínios de atualização**: 2|
| **Diagnóstico** |Ativado |
| **Conta de armazenamento de diagnóstico** |*Criado automaticamente* |

   >[!IMPORTANT]
   >Você só pode posicionar uma VM em um conjunto de disponibilidade ao criá-la. Não é possível alterar o conjunto de disponibilidade após a criação de uma VM. Consulte [gerenciar a disponibilidade de máquinas virtuais](../manage-availability.md).

O Azure cria as máquinas virtuais.

Depois que as máquinas virtuais forem criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nas etapas a seguir, configure o computador **ad-Primary-DC** como um controlador de domínio para Corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-ha-RG** e selecione o computador **ad-Primary-DC** . Em **ad-Primary-DC**, clique em **conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.

    ![Ligar a uma máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Entre com sua conta de administrador configurada ( **\DomainAdmin**) e senha (**contoso! 0000**).
3. Por padrão, o painel de **Gerenciador do servidor** deve ser exibido.
4. Clique no link **adicionar funções e recursos** no painel.

    ![Gerenciador do Servidor-adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **Avançar** até chegar à seção **funções de servidor** .
6. Selecione as funções de **servidor** **Active Directory Domain Services** e DNS. Quando solicitado, adicione os recursos adicionais necessários para essas funções.

   > [!NOTE]
   > O Windows avisa que não há endereço IP estático. Se você estiver testando a configuração, clique em **continuar**. Para cenários de produção, defina o endereço IP como estático no portal do Azure ou [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Caixa de diálogo Adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Clique em **Avançar** até chegar à seção de **confirmação** . Marque a caixa de seleção **reiniciar o servidor de destino automaticamente se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos terminarem de ser instalados, retorne ao painel do **Gerenciador do servidor** .
10. Selecione a opção novo **AD DS** no painel esquerdo.
11. Clique no link **mais** na barra de aviso amarelo.

    ![Caixa de diálogo AD DS na VM do servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Na coluna **ação** da caixa de diálogo **detalhes da tarefa todos os servidores** , clique em **promover este servidor a um controlador de domínio**.
13. No **Assistente de configuração do Active Directory Domain Services**, use os seguintes valores:

    | **Web** | Definição |
    | --- | --- |
    | **Configuração de implantação** |**Adicionar uma nova floresta**<br/> **Nome do domínio raiz** = Corp.contoso.com |
    | **Opções do controlador de domínio** |**Senha do DSRM** = contoso! 0000<br/>**Confirmar senha** = contoso! 0000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **verificação de pré-requisitos** , verifique se você vê a seguinte mensagem: **todas as verificações de pré-requisitos foram aprovadas com êxito**. Você pode examinar todas as mensagens de aviso aplicáveis, mas é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ad-Primary-DC** é reinicializada automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Anote o endereço IP do controlador de domínio primário

Use o controlador de domínio primário para o DNS. Anote o endereço IP do controlador de domínio primário.

Uma maneira de obter o endereço IP do controlador de domínio primário é por meio do portal do Azure.

1. Na portal do Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No controlador de domínio primário, clique em **interfaces de rede**.

![Interfaces de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Anote o endereço IP privado para este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configurar o DNS da rede virtual
Depois de criar o primeiro controlador de domínio e habilitar o DNS no primeiro servidor, configure a rede virtual para usar esse servidor para DNS.

1. Na portal do Azure, clique na rede virtual.

2. Em **configurações**, clique em **servidor DNS**.

3. Clique em **personalizado**e digite o endereço IP privado do controlador de domínio primário.

4. Clique em **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configurar o segundo controlador de domínio
Depois que o controlador de domínio primário for reinicializado, você poderá configurar o segundo controlador de domínio. Essa etapa opcional é para alta disponibilidade. Siga estas etapas para configurar o segundo controlador de domínio:

1. No portal, abra o grupo de recursos **SQL-ha-RG** e selecione o computador **ad-Secondary-DC** . Em **ad-Secondary-DC**, clique em **conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.
2. Entre na VM usando sua conta de administrador configurada (**BUILTIN\DomainAdmin**) e senha (**contoso! 0000**).
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio.
4. Em **central de rede e compartilhamento**, clique na interface de rede.
   ![Interface de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Selecione **protocolo IP versão 4 (TCP/IPv4)** e clique em **Propriedades**.
7. Selecione **usar os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **servidor DNS preferencial**.
8. Clique em **OK**e em **fechar** para confirmar as alterações. Agora você pode unir a VM ao **Corp.contoso.com**.

   >[!IMPORTANT]
   >Se você perder a conexão com a área de trabalho remota depois de alterar a configuração de DNS, vá para a portal do Azure e reinicie a máquina virtual.

9. Na área de trabalho remota para o controlador de domínio secundário, abra **Gerenciador do servidor painel**.
10. Clique no link **adicionar funções e recursos** no painel.

    ![Gerenciador do Servidor-adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecione **Avançar** até chegar à seção **funções de servidor** .
12. Selecione as funções de **servidor** **Active Directory Domain Services** e DNS. Quando solicitado, adicione os recursos adicionais necessários para essas funções.
13. Depois que os recursos terminarem de ser instalados, retorne ao painel do **Gerenciador do servidor** .
14. Selecione a opção novo **AD DS** no painel esquerdo.
15. Clique no link **mais** na barra de aviso amarelo.
16. Na coluna **ação** da caixa de diálogo **detalhes da tarefa todos os servidores** , clique em **promover este servidor a um controlador de domínio**.
17. Em **configuração de implantação**, selecione **Adicionar um controlador de domínio a um domínio existente**.
    ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png) de ![de configuração de implantação
18. Clique em **Selecionar**.
19. Conecte-se usando a conta de administrador (**Corp. CONTOSO. COM\domainadmin**) e a senha (**contoso! 0000**).
20. Em **selecionar um domínio da floresta**, clique em seu domínio e em **OK**.
21. Em **Opções do controlador de domínio**, use os valores padrão e defina uma senha do DSRM.

    >[!NOTE]
    >A página **Opções de DNS** pode avisá-lo que uma delegação para este servidor DNS não pode ser criada. Você pode ignorar esse aviso em ambientes que não são de produção.
22. Clique em **Avançar** até que a caixa de diálogo atinja a verificação de **pré-requisitos** . Em seguida, clique em **Instalar**.

Depois que o servidor concluir as alterações de configuração, reinicie o servidor.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicionar o endereço IP privado ao segundo controlador de domínio para o servidor DNS VPN

No portal do Azure, em rede virtual, altere o servidor DNS para incluir o endereço IP do controlador de domínio secundário. Essa configuração permite a redundância do serviço DNS.

### <a name="DomainAccounts"></a>Configurar as contas de domínio

Nas próximas etapas, você configurará as contas de Active Directory. A tabela a seguir mostra as contas:

| |Conta de instalação<br/> |sqlserver-0 <br/>SQL Server e conta de serviço do SQL Agent |sqlserver-1<br/>SQL Server e conta de serviço do SQL Agent
| --- | --- | --- | ---
|**Nome** |Instalar |SQLSvc1 | SQLSvc2
|**SamAccountName do usuário** |Instalar |SQLSvc1 | SQLSvc2

Use as etapas a seguir para criar cada conta.

1. Entre no computador **ad-Primary-DC** .
2. Em **Gerenciador do servidor**, selecione **ferramentas**e, em seguida, clique em **centro administrativo do Active Directory**.   
3. Selecione **Corp (local)** no painel esquerdo.
4. No painel **tarefas** à direita, selecione **novo**e clique em **usuário**.
   ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Defina uma senha complexa para cada conta.<br/> Para ambientes de não produção, defina a conta de usuário para nunca expirar.

5. Clique em **OK** para criar o usuário.
6. Repita as etapas anteriores para cada uma das três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceder as permissões necessárias para a conta de instalação
1. Na **centro administrativo do Active Directory**, selecione **Corp (local)** no painel esquerdo. Em seguida, no painel **tarefas** à direita, clique em **Propriedades**.

    ![Propriedades do usuário CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecione **extensões**e, em seguida, clique no botão **avançado** na guia **segurança** .
3. Na caixa de diálogo **configurações de segurança avançadas para Corp** , clique em **Adicionar**.
4. Clique em **selecionar uma entidade de segurança**, procure **CORP\Install**e clique em **OK**.
5. Marque a caixa de seleção **ler todas as propriedades** .

6. Marque a caixa de seleção **criar objetos de computador** .

     ![Permissões de usuário Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Clique em **OK**e em **OK** novamente. Feche a janela Propriedades da **Corp** .

Agora que você terminou de configurar Active Directory e os objetos de usuário, crie duas VMs SQL Server e uma VM de servidor testemunha. Em seguida, junte-se a todos os três ao domínio.

## <a name="create-sql-server-vms"></a>Criar SQL Server VMs

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias de SQL Server. Uma terceira máquina virtual funcionará como uma testemunha. O Windows Server 2016 pode usar uma [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), no entanto, para fins de consistência com sistemas operacionais anteriores, este documento usa uma máquina virtual para uma testemunha.  

Antes de prosseguir, considere as seguintes decisões de design.

* **Armazenamento-Managed Disks do Azure**

   Para o armazenamento de máquina virtual, use o Managed Disks do Azure. A Microsoft recomenda Managed Disks para SQL Server máquinas virtuais. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter informações adicionais, veja [Azure Managed Disks Overview](../managed-disks-overview.md) (Descrição Geral do Managed Disks). Para obter informações específicas sobre discos gerenciados em um conjunto de disponibilidade, consulte [usar Managed disks para VMs em um conjunto de disponibilidade](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rede-endereços IP privados na produção**

   Para as máquinas virtuais, este tutorial usa endereços IP públicos. Um endereço IP público permite a conexão remota diretamente com a máquina virtual pela Internet – facilita as etapas de configuração. Em ambientes de produção, a Microsoft recomenda apenas endereços IP privados para reduzir a superfície de vulnerabilidade do recurso de VM de instância de SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs de SQL Server
Em seguida, crie três VMs – duas VMs SQL Server e uma VM para um nó de cluster adicional. Para criar cada uma das VMs, volte para o grupo de recursos **SQL-ha-RG** , clique em **Adicionar**, procure o item da Galeria apropriado, clique em **máquina virtual**e, em seguida, clique em **da Galeria**. Use as informações na tabela a seguir para ajudá-lo a criar as VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item de galeria apropriado |**Windows Server 2016 datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
| **Noções básicas** de configuração de máquina virtual |**Nome** = cluster-FSW<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = contoso! 0000<br/>**Assinatura** = sua assinatura<br/>**Grupo de recursos** = SQL-ha-RG<br/>**Local** = seu local do Azure |**Nome** = SqlServer-0<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = contoso! 0000<br/>**Assinatura** = sua assinatura<br/>**Grupo de recursos** = SQL-ha-RG<br/>**Local** = seu local do Azure |**Nome** = SqlServer-1<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = contoso! 0000<br/>**Assinatura** = sua assinatura<br/>**Grupo de recursos** = SQL-ha-RG<br/>**Local** = seu local do Azure |
| **Tamanho** da configuração da máquina virtual |**Tamanho** = DS1\_v2 (1 vCPU, 3,5 GB) |**Tamanho** = DS2\_v2 (2 vCPUs, 7 GB)</br>O tamanho deve oferecer suporte ao armazenamento SSD (suporte a disco Premium. )) |**Tamanho** = DS2\_v2 (2 vCPUs, 7 GB) |
| **Definições** de configuração de máquina virtual |**Armazenamento**: use discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub** -rede = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = nenhum<br/>**Diagnóstico de monitoramento** = habilitado<br/>**Conta de armazenamento de diagnóstico** = usar uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlavailabilityset<br/> |**Armazenamento**: use discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub** -rede = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = nenhum<br/>**Diagnóstico de monitoramento** = habilitado<br/>**Conta de armazenamento de diagnóstico** = usar uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlavailabilityset<br/> |**Armazenamento**: use discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub** -rede = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = nenhum<br/>**Diagnóstico de monitoramento** = habilitado<br/>**Conta de armazenamento de diagnóstico** = usar uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlavailabilityset<br/> |
| **Configurações de SQL Server** de configuração de máquina virtual |Não aplicável |**Conectividade do SQL** = particular (dentro da rede virtual)<br/>**Porta** = 1433<br/>**Autenticação do SQL** = desabilitar<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patch automatizada** = domingo às 2:00<br/>**Backup automatizado** = desabilitado</br>**Integração de Azure Key Vault** = desabilitada |**Conectividade do SQL** = particular (dentro da rede virtual)<br/>**Porta** = 1433<br/>**Autenticação do SQL** = desabilitar<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patch automatizada** = domingo às 2:00<br/>**Backup automatizado** = desabilitado</br>**Integração de Azure Key Vault** = desabilitada |

<br/>

> [!NOTE]
> Os tamanhos de máquina sugeridos aqui servem para testar grupos de disponibilidade em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações para SQL Server tamanhos de máquina e configuração em [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois que as três VMs forem totalmente provisionadas, você precisará associá-las ao domínio **Corp.contoso.com** e conceder direitos administrativos CORP\Install aos computadores.

### <a name="joinDomain"></a>Unir os servidores ao domínio

Agora você pode unir as VMs ao **Corp.contoso.com**. Execute as etapas a seguir para as VMs SQL Server e o servidor testemunha de compartilhamento de arquivos:

1. Conecte-se remotamente à máquina virtual com **BUILTIN\DomainAdmin**.
2. Em **Gerenciador do servidor**, clique em **servidor local**.
3. Clique no link **grupo de trabalho** .
4. Na seção **nome do computador** , clique em **alterar**.
5. Marque a caixa de seleção **domínio** e digite **Corp.contoso.com** na caixa de texto. Clique em **OK**.
6. Na caixa de diálogo pop-up **segurança do Windows** , especifique as credenciais para a conta de administrador de domínio padrão (**CORP\DomainAdmin**) e a senha (**contoso! 0000**).
7. Quando você vir a mensagem "bem-vindo ao domínio corp.contoso.com", clique em **OK**.
8. Clique em **fechar**e, em seguida, clique em **reiniciar agora** na caixa de diálogo pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicionar o usuário Corp\Install como um administrador em cada VM de cluster

Depois que cada máquina virtual for reiniciada como um membro do domínio, adicione **CORP\Install** como um membro do grupo local de administradores.

1. Aguarde até que a VM seja reiniciada e inicie o arquivo RDP novamente do controlador de domínio primário para entrar no **SqlServer-0** usando a conta **CORP\DomainAdmin** .
   >[!TIP]
   >Certifique-se de entrar com a conta de administrador de domínio. Nas etapas anteriores, você estava usando a conta de administrador interno. Agora que o servidor está no domínio, use a conta de domínio. Em sua sessão RDP, especifique *domínio*\\*nome de usuário*.

2. Em **Gerenciador do servidor**, selecione **ferramentas**e, em seguida, clique em **Gerenciamento do computador**.
3. Na janela **Gerenciamento do computador** , expanda **usuários e grupos locais**e selecione **grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de administradores** , clique no botão **Adicionar** .
6. Insira o usuário **CORP\Install**e clique em **OK**.
7. Clique em **OK** para fechar a caixa de diálogo **Propriedades do administrador** .
8. Repita as etapas anteriores em **SqlServer-1** e **cluster-FSW**.

### <a name="setServiceAccount"></a>Definir as contas de serviço SQL Server

Em cada VM SQL Server, defina a conta de serviço SQL Server. Use as contas que você criou quando configurou as contas de domínio.

1. Abra **SQL Server Configuration Manager**.
2. Clique com o botão direito do mouse no serviço SQL Server e clique em **Propriedades**.
3. Defina a conta e a senha.
4. Repita essas etapas no outro SQL Server VM.  

Para SQL Server grupos de disponibilidade, cada VM SQL Server precisa ser executada como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Criar uma entrada em cada VM SQL Server para a conta de instalação

Use a conta de instalação (CORP\install) para configurar o grupo de disponibilidade. Essa conta precisa ser um membro da função de servidor fixa **sysadmin** em cada VM SQL Server. As etapas a seguir criam uma entrada para a conta de instalação:

1. Conecte-se ao servidor por meio do protocolo RDP (RDP) usando a conta *\<MachineName\>\DomainAdmin* .

1. Abra SQL Server Management Studio e conecte-se à instância local do SQL Server.

1. No Pesquisador de **objetos**, clique em **segurança**.

1. Clique com o botão direito do mouse em **logons**. Clique em **novo logon**.

1. Em **logon-novo**, clique em **Pesquisar**.

1. Clique em **locais**.

1. Insira as credenciais de rede do administrador de domínio.

1. Use a conta de instalação do.

1. Defina a entrada como membro da função de servidor fixa **sysadmin** .

1. Clique em **OK**.

Repita as etapas anteriores no outro SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicionar recursos de clustering de failover a ambas as VMs SQL Server

Para adicionar recursos de clustering de failover, execute as seguintes etapas em ambas as VMs SQL Server:

1. Conecte-se à máquina virtual SQL Server por meio do protocolo RDP (RDP) usando a conta *CORP\install* . Abra o **painel Gerenciador do servidor**.
2. Clique no link **adicionar funções e recursos** no painel.

    ![Gerenciador do Servidor-adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecione **Avançar** até chegar à seção de **recursos do servidor** .
4. Em **recursos**, selecione **clustering de failover**.
5. Adicione quaisquer recursos adicionais necessários.
6. Clique em **instalar** para adicionar os recursos.

Repita as etapas na outra VM SQL Server.

  >[!NOTE]
  > Essa etapa, juntamente com a junção das VMs de SQL Server para o cluster de failover, agora pode ser automatizada com a [CLI de VM do SQL do Azure](virtual-machines-windows-sql-availability-group-cli.md) e os [modelos de início rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> configurar o firewall em cada VM SQL Server

A solução requer que as seguintes portas TCP estejam abertas no firewall:

- **VM SQL Server**:<br/>
   Porta 1433 para uma instância padrão do SQL Server.
- **Investigação do Azure Load Balancer:**<br/>
   Qualquer porta disponível. Os exemplos frequentemente usam 59999.
- **Ponto de extremidade de espelhamento de banco de dados:** <br/>
   Qualquer porta disponível. Os exemplos frequentemente usam 5022.

As portas de firewall precisam ser abertas em ambas as VMs SQL Server.

O método de abrir as portas depende da solução de firewall que você usa. A próxima seção explica como abrir as portas no firewall do Windows. Abra as portas necessárias em cada uma das suas VMs SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abrir uma porta TCP no firewall

1. Na primeira tela SQL Server **Iniciar** , inicie o **Firewall do Windows com segurança avançada**.
2. No painel esquerdo, selecione **regras de entrada**. No painel direito, clique em **nova regra**.
3. Para **tipo de regra**, escolha **porta**.
4. Para a porta, especifique **TCP** e digite os números de porta apropriados. Veja o seguinte exemplo:

   ![Firewall do SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Clique em **Seguinte**.
6. Na página **ação** , mantenha **permitir a conexão** selecionada e clique em **Avançar**.
7. Na página **perfil** , aceite as configurações padrão e clique em **Avançar**.
8. Na página **nome** , especifique um nome de regra (como **investigação do Azure lb**) na caixa de texto **nome** e clique em **concluir**.

Repita essas etapas na segunda VM SQL Server.

## <a name="configure-system-account-permissions"></a>Configurar permissões de conta do sistema

Para criar uma conta para a conta do sistema e conceder as permissões apropriadas, conclua as seguintes etapas em cada instância de SQL Server:

1. Crie uma conta para `[NT AUTHORITY\SYSTEM]` em cada instância de SQL Server. O script a seguir cria essa conta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda as seguintes permissões para `[NT AUTHORITY\SYSTEM]` em cada instância de SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   O script a seguir concede estas permissões:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Passos seguintes

* [Criar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
