---
title: Grupos de disponibilidade do SQL Server - máquinas virtuais do Azure - pré-requisitos | Documentos da Microsoft
description: Este tutorial mostra como configurar os pré-requisitos para a criação de um grupo de disponibilidade Always On do SQL Server em VMs do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: abe211ad4745564cb2698aa1261a1aeb6dd63af7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781736"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Concluir os pré-requisitos para a criação de grupos de disponibilidade Always On em máquinas virtuais do Azure

Este tutorial mostra como concluir os pré-requisitos para a criação de um [SQL Server Always On o grupo de disponibilidade em máquinas virtuais do Azure (VMs)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Quando concluir os pré-requisitos, terá um controlador de domínio, duas VMs do SQL Server e o servidor-testemunha num grupo de recursos único.

**Estimativa de tempo**: Poderá demorar algumas horas para concluir os pré-requisitos. Grande parte desse tempo é gasto a criação de máquinas virtuais.

O diagrama seguinte ilustra o que criar no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Consulte a documentação do grupo de disponibilidade

Este tutorial parte do princípio de que tem uma noção básica dos grupos de disponibilidade Always On do SQL Server. Se não estiver familiarizado com essa tecnologia, consulte [descrição geral de grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Crie uma conta Azure
Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) ou [ativar benefícios de subscritor do Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Inicie sessão no [portal do Azure](http://portal.azure.com).
2. Clique em **+** para criar um novo objeto no portal.

   ![Novo objeto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Tipo **grupo de recursos** no **Marketplace** janela de pesquisa.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Clique em **grupo de recursos**.
5. Clique em **Criar**.
6. Sob **nome do grupo de recursos**, escreva um nome para o grupo de recursos. Por exemplo, digite **sql-ha-rg**.
7. Se tiver várias subscrições do Azure, certifique-se de que a subscrição é que deseja criar o grupo de disponibilidade na subscrição do Azure.
8. Selecione uma localização. A localização é a região do Azure onde pretende criar o grupo de disponibilidade. Este artigo baseia-se todos os recursos num único local do Azure.
9. Certifique-se de que **afixar ao dashboard** está marcada. Esta definição opcional coloca um atalho para o grupo de recursos no dashboard do portal do Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Clique em **criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e pins um atalho para o grupo de recursos no portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e sub-redes
A próxima etapa é criar as redes e sub-redes no grupo de recursos do Azure.

A solução utiliza uma rede virtual com duas sub-redes. O [descrição geral de rede Virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre redes no Azure.

Para criar a rede virtual:

1. No portal do Azure, no grupo de recursos, clique em **+ adicionar**. 

   ![Novo item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Procure **rede virtual**.

     ![Procurar rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Clique em **rede Virtual**.
4. Sobre o **rede Virtual**, clique no **Resource Manager** modelo de implementação e, em seguida, clique **criar**.

    A tabela seguinte mostra as definições para a rede virtual:

   | **Campo** | Value |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereços** |10.33.0.0/24 |
   | **Nome da sub-rede** |Administração |
   | **Intervalo de endereços da sub-rede** |10.33.0.0/29 |
   | **Subscrição** |Especifique a subscrição que pretende utilizar. **Subscrição** está em branco se tiver apenas uma subscrição. |
   | **Grupo de recursos** |Escolher **utilizar existente** e escolha o nome do grupo de recursos. |
   | **Localização** |Especifique a localização do Azure. |

   O intervalo de endereços de espaço e sub-rede endereço pode ser diferente da tabela. Consoante a sua subscrição, o portal sugere um espaço de endereços disponíveis e o intervalo de endereços de sub-rede correspondente. Se nenhum espaço de endereços suficiente estiver disponível, utilize uma subscrição diferente.

   O exemplo utiliza o nome da sub-rede **administrador**. Esta sub-rede é para os controladores de domínio.

5. Clique em **Criar**.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

O Azure faz retorna ao dashboard do portal e notifica-o quando é criada a nova rede.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma sub-rede, com o nome **administrador**. Os controladores de domínio, utilize esta sub-rede. A utilização de VMs do SQL Server com o nome uma segunda sub-rede **SQL**. Para configurar esta sub-rede:

1. No seu dashboard, clique no grupo de recursos que criou, **SQL-HA-RG**. Localizar a rede no grupo de recursos sob **recursos**.

    Se **SQL-HA-RG** não estiver visível, encontrá-lo ao clicar em **grupos de recursos** e a filtragem pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. 
3. Na **autoHAVNET** virtual em rede **definições** selecionar **sub-redes**.

    Tenha em atenção a sub-rede que já criou.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ sub-rede**.
6. No **adicionar sub-rede**, configurar a sub-rede, escrevendo **sqlsubnet** sob **nome**. Azure automaticamente Especifica válido **intervalo de endereços**. Certifique-se de que este intervalo de endereços tem, pelo menos, 10 endereços na mesma. Num ambiente de produção, poderá necessitar de mais endereços.
7. Clique em **OK**.

    ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A tabela seguinte resume as definições de configuração de rede:

| **Campo** | Value |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereços** |Este valor depende dos espaços de endereços disponíveis na sua subscrição. Um valor de típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**admin** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor de típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor de típico é 10.0.1.0/24. |
| **Subscrição** |Especifique a subscrição que pretende utilizar. |
| **Grupo de Recursos** |**SQL-HA-RG** |
| **Localização** |Especifique a mesma localização que escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, terá de criar conjuntos de disponibilidade. Conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planeada ou não planeada. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos que o Azure coloca em domínios físicos de falhas e domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade têm recursos separados de energia e rede. Um domínio de atualização garante que os membros do conjunto de disponibilidade não são colocados para manutenção ao mesmo tempo. Para obter mais informações, consulte [gerir a disponibilidade das máquinas virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Precisa de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para as VMs do SQL Server.

Para criar um conjunto de disponibilidade, vá para o grupo de recursos e clique em **adicionar**. Filtrar os resultados, escrevendo **conjunto de disponibilidade**. Clique em **conjunto de disponibilidade** nos resultados e, em seguida, clique **criar**.

Configure dois conjuntos de disponibilidade, de acordo com os parâmetros na tabela a seguir:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade do SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, retornar para o grupo de recursos no portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, sub-redes, conjuntos de disponibilidade e um balanceador de carga com acesso à Internet, está pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Crie máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, volte para o **SQL-HA-RG** grupo de recursos.

1. Clique em **Adicionar**. 
2. Tipo **do Windows Server 2016 Datacenter**.
3. Clique em **do Windows Server 2016 Datacenter**. Na **Windows Server 2016 Datacenter**, certifique-se de que o modelo de implementação é **Gestor de recursos**e, em seguida, clique em **criar**. 

Repita os passos anteriores para criar duas máquinas virtuais. Dê um nome de duas máquinas virtuais:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > O **ad-secundário-dc** máquina virtual é opcional, para proporcionar elevada disponibilidade para serviços de domínio do Active Directory.
  >
  >

A tabela seguinte mostra as definições para essas duas máquinas:

| **Campo** | Value |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-principal-dc*.</br>Controlador de domínio de segundo *ad-secundário-dc*. |
| **Tipo de disco da VM** |SSD |
| **Nome de utilizador** |DomainAdmin |
| **Palavra-passe** |Contoso!0000 |
| **Subscrição** |*A sua subscrição* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localização** |*Sua localização* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Utilizar discos geridos** - **Sim** |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |admin |
| **Endereço IP público** |*Mesmo nome da VM* |
| **Grupo de segurança de rede** |*Mesmo nome da VM* |
| **Conjunto de disponibilidade** |adavailabilityset </br>**Domínios de falha**: 2 </br>**Domínios de atualização**: 2|
| **Diagnóstico** |Ativado |
| **Conta de armazenamento de diagnóstico** |*Criado automaticamente* |

   >[!IMPORTANT]
   >Só é possível colocar uma VM num conjunto quando criá-la de disponibilidade. Não é possível alterar a conjunto de disponibilidade depois é criada uma VM. Ver [gerir a disponibilidade das máquinas virtuais](../manage-availability.md).

O Azure cria as máquinas virtuais.

Depois das máquinas virtuais são criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nos passos seguintes, configurar o **ad-principal-dc** máquinas como controlador de domínio para corp.contoso.com.

1. No portal, abra a **SQL-HA-RG** recursos de grupo e selecione o **ad-principal-dc** máquina. No **ad-principal-dc**, clique em **Connect** para abrir um ficheiro RDP para acesso de ambiente de trabalho remoto.

    ![Ligar a uma máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Inicie sessão com a sua conta de administrador configurada (**\DomainAdmin**) e palavra-passe (**Contoso! 0000**).
3. Por predefinição, o **Gestor de servidor** dashboard deve ser apresentado.
4. Clique nas **para adicionar funções e funcionalidades** link no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **próxima** até chegar à **funções de servidor** secção.
6. Selecione o **serviços de domínio do Active Directory** e **servidor DNS** funções. Quando lhe for pedido, adicione quaisquer funcionalidades adicionais que são necessários para estas funções.

   > [!NOTE]
   > Windows avisa-o de que não existe nenhum endereço IP estático. Se estiver a testar a configuração, clique em **continuar**. Para cenários de produção, definir o endereço IP estático no portal do Azure, ou [utilizar o PowerShell para definir o endereço IP estático da máquina de controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Adicionar a caixa de diálogo de funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Clique em **próxima** até chegar a **confirmação** secção. Selecione o **reiniciar automaticamente o servidor de destino quando necessário** caixa de verificação.
8. Clique em **Instalar**.
9. Depois dos recursos de concluir a instalação, volte para o **Gestor de servidor** dashboard.
10. Selecione a nova **AD DS** opção no painel esquerdo.
11. Clique nas **mais** ligação na barra de aviso amarelo.

    ![Caixa de diálogo do AD DS na VM de servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Na **ação** coluna da **todos os detalhes da tarefa de servidor de** caixa de diálogo, clique em **promover este servidor a controlador de domínio**.
13. Na **Assistente do configuração de serviços de domínio do Active Directory**, utilize os seguintes valores:

    | **Página** | Definição |
    | --- | --- |
    | **Configuração de implementação** |**Adicionar uma nova floresta**<br/> **Nome de domínio de raiz** = corp.contoso.com |
    | **Opções de controlador de domínio** |**Palavra-passe DSRM** = Contoso! 0000<br/>**Confirmar palavra-passe** = Contoso! 0000 |
14. Clique em **seguinte** passar por outras páginas no assistente. Sobre o **verificação de pré-requisitos** página, certifique-se de que vê a mensagem seguinte: **Todas as verificações de pré-requisitos passaram com êxito**. Pode rever as mensagens de aviso aplicável, mas é possível continuar com a instalação.
15. Clique em **Instalar**. O **ad-principal-dc** máquina virtual é reiniciada automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Tenha em atenção o endereço IP do controlador de domínio primário

Utilize o controlador de domínio primário para o DNS. Tenha em atenção o endereço IP de controlador de domínio primário.

Uma forma de obter o endereço IP de controlador de domínio primário é através do portal do Azure.

1. No portal do Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No controlador de domínio primário, clique em **interfaces de rede**.

![Interfaces de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Tenha em atenção o endereço IP privado para este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configurar o DNS de rede virtual
Depois de criar o primeiro controlador de domínio e ativar o DNS no primeiro servidor, configure a rede virtual para utilizar este servidor para o DNS.

1. No portal do Azure, clique na rede virtual.

2. Sob **configurações**, clique em **servidor DNS**.

3. Clique em **personalizado**e escreva o endereço IP privado do controlador de domínio primário.

4. Clique em **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configurar o controlador de domínio de segundo
Depois de reinicia o controlador de domínio primário, pode configurar o controlador de domínio de segundo. Este passo opcional é para elevada disponibilidade. Siga estes passos para configurar o controlador de domínio de segundo:

1. No portal, abra a **SQL-HA-RG** recursos de grupo e selecione o **ad-secundário-dc** máquina. No **ad-secundário-dc**, clique em **Connect** para abrir um ficheiro RDP para acesso de ambiente de trabalho remoto.
2. Inicie sessão para a VM com a sua conta de administrador configurada (**BUILTIN\DomainAdmin**) e palavra-passe (**Contoso! 0000**).
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio.
4. Na **Centro de partilha de rede e**, clique em interface de rede.
   ![Interface de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Selecione **protocolo IP versão 4 (TCP/IPv4)** e clique em **propriedades**.
7. Selecione **utilizar os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **servidor DNS preferencial**.
8. Clique em **OK**e, em seguida **fechar** para consolidar as alterações. Agora é possível associar a VM para **corp.contoso.com**.

   >[!IMPORTANT]
   >Se perder a ligação ao seu ambiente de trabalho remoto depois de alterar a definição de DNS, aceda ao portal do Azure e reinicie a máquina virtual.

9. A partir do ambiente de trabalho remoto para o controlador de domínio secundário, abra **Dashboard do Gestor de servidor**.
10. Clique nas **para adicionar funções e funcionalidades** link no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecione **próxima** até chegar à **funções de servidor** secção.
12. Selecione o **serviços de domínio do Active Directory** e **servidor DNS** funções. Quando lhe for pedido, adicione quaisquer funcionalidades adicionais que são necessários para estas funções.
13. Depois dos recursos de concluir a instalação, volte para o **Gestor de servidor** dashboard.
14. Selecione a nova **AD DS** opção no painel esquerdo.
15. Clique nas **mais** ligação na barra de aviso amarelo.
16. Na **ação** coluna da **todos os detalhes da tarefa de servidor de** caixa de diálogo, clique em **promover este servidor a controlador de domínio**.
17. Sob **configuração da implementação**, selecione **adicionar um controlador de domínio a um domínio existente**.
   ![Configuração de implementação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Clique em **Selecionar**.
19. Ligar utilizando a conta de administrador (**Corp. CONTOSO.COM\domainadmin**) e palavra-passe (**Contoso! 0000**).
20. Na **selecionar um domínio da floresta**, clique no seu domínio e, em seguida, clique em **OK**.
21. Na **opções de controlador de domínio**, utilize os valores predefinidos e defina uma palavra-passe do DSRM.

   >[!NOTE]
   >O **opções DNS** página poderá avisá-lo que não é possível criar uma delegação para este servidor DNS. Pode ignorar este aviso em ambientes de não produção.
22. Clique em **próxima** até atinge a caixa de diálogo a **pré-requisitos** verificar. Em seguida, clique em **Instalar**.

Depois do servidor concluir as alterações de configuração, reinicie o servidor.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicionar o endereço de IP privado para o segundo controlador de domínio para o servidor de DNS de VPN

No portal do Azure, na rede virtual, altere o servidor de DNS para incluir o endereço IP do controlador de domínio secundário. Esta definição permite a redundância de serviço DNS.

### <a name="DomainAccounts"></a> Configure as contas de domínio

Os passos seguintes, irá configurar as contas do Active Directory. A tabela seguinte mostra as contas:

| |Conta de instalação<br/> |sqlserver-0 <br/>Conta do SQL Server e o serviço de agente do SQL |sqlserver-1<br/>Conta do SQL Server e o serviço de agente do SQL
| --- | --- | --- | ---
|**Nome próprio** |Instalar |SQLSvc1 | SQLSvc2
|**SamAccountName de utilizador** |Instalar |SQLSvc1 | SQLSvc2

Utilize os seguintes passos para criar cada conta.

1. Inicie sessão para o **ad-principal-dc** máquina.
2. Na **Gestor de servidores**, selecione **ferramentas**e, em seguida, clique em **Centro de administração do Active Directory**.   
3. Selecione **corp (local)** no painel à esquerda.
4. À direita **tarefas** painel, selecione **New**e, em seguida, clique em **utilizador**.
   ![Centro de administração do Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Defina uma palavra-passe complexa para cada conta.<br/> Para ambientes de não produção, defina a conta de utilizador para nunca expirar.

5. Clique em **OK** para criar o utilizador.
6. Repita os passos anteriores para cada um dos três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceder as permissões necessárias para a conta de instalação
1. Na **Centro de administração do Active Directory**, selecione **corp (local)** no painel esquerdo. Em seguida, em direito **tarefas** painel, clique em **propriedades**.

    ![Propriedades de utilizador do CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecione **extensões**e, em seguida, clique nas **avançadas** botão o **segurança** separador.
3. Na **definições de segurança avançadas para corp** caixa de diálogo, clique em **Add**.
4. Clique em **selecionar um principal**, procure **CORP\Install**e, em seguida, clique em **OK**.
5. Selecione o **ler todas as propriedades** caixa de verificação.

6. Selecione o **criar objetos de computador** caixa de verificação.

     ![Permissões de utilizador do Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Clique em **OK**e, em seguida, clique em **OK** novamente. Fechar o **corp** janela Propriedades.

Agora que tiver terminado de configurar o Active Directory e os objetos de utilizador, crie duas VMs do SQL Server e um servidor-testemunha VM. Em seguida, Junte-se a todas as três ao domínio.

## <a name="create-sql-server-vms"></a>Criar VMs do SQL Server

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias do SQL Server. Terceira máquina virtual irá funcionar como um testemunho. Windows Server 2016 pode utilizar um [testemunho de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), no entanto, para manter a consistência com sistemas operativos anteriores neste documento usa uma máquina virtual para um testemunho.  

Antes de continuar, considere as seguintes decisões de design.

* **-Azure Managed Disks de armazenamento**

   Para o armazenamento de máquina virtual, utilize o Managed Disks do Azure. A Microsoft recomenda o Managed Disks para máquinas virtuais do SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter informações adicionais, veja [Azure Managed Disks Overview](../managed-disks-overview.md) (Descrição Geral do Managed Disks). Para obter informações específicas sobre os discos geridos num conjunto de disponibilidade, consulte [utilizar discos geridos para VMs num conjunto de disponibilidade](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Endereços de IP privados de rede - em produção**

   Para as máquinas virtuais, este tutorial utiliza endereços IP públicos. Um endereço IP público permite a ligação remota diretamente para a máquina virtual através da internet – ela torna mais fácil os passos de configuração. Em ambientes de produção, a Microsoft recomenda apenas endereços IP privados para reduzir os requisitos de espaço de vulnerabilidade de recurso de VM de instância do SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs do SQL Server
Em seguida, crie três VMs, duas VMs do SQL Server e uma VM para um nó do cluster adicional. Para criar cada uma das VMs, volte para o **SQL-HA-RG** grupo de recursos, clique em **Add**, procure o item da Galeria apropriado, clique em **Máquina Virtual**e, em seguida, clique em  **Galeria do**. Utilize as informações na tabela a seguir para ajudar a criar as VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item de galeria apropriado |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
| Configuração da máquina virtual **Noções básicas** |**Nome** = fsw de cluster<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = sua localização do azure |**Name** = sqlserver-0<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = sua localização do azure |**Name** = sqlserver-1<br/>**Nome de utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso! 0000<br/>**Subscrição** = a sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = sua localização do azure |
| Configuração da máquina virtual **tamanho** |**SIZE** = DS1\_V2 (1 vCPU, 3.5 GB) |**SIZE** = DS2\_V2 (2 vCPUs, 7 GB)</br>O tamanho tem de suportar armazenamento SSD (suporte de disco Premium. )) |**SIZE** = DS2\_V2 (2 vCPUs, 7 GB) |
| Configuração da máquina virtual **definições** |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerada automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Monitorização diagnóstico** = ativada<br/>**Conta de armazenamento de diagnóstico** = utilizar uma conta de armazenamento geradas automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerada automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Monitorização diagnóstico** = ativada<br/>**Conta de armazenamento de diagnóstico** = utilizar uma conta de armazenamento geradas automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerada automaticamente.<br/>**Grupo de segurança de rede** = None<br/>**Monitorização diagnóstico** = ativada<br/>**Conta de armazenamento de diagnóstico** = utilizar uma conta de armazenamento geradas automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |
| Configuração da máquina virtual **definições do SQL Server** |Não aplicável |**Conectividade SQL** = privada (na Virtual Network)<br/>**Porta** = 1433<br/>**Autenticação do SQL** = Disable<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patches automatizada** = Domingo 2:00<br/>**Cópia de segurança automatizada** = desativado</br>**Integração do Cofre de chaves do Azure** = desativado |**Conectividade SQL** = privada (na Virtual Network)<br/>**Porta** = 1433<br/>**Autenticação do SQL** = Disable<br/>**Configuração de armazenamento** = geral<br/>**Aplicação de patches automatizada** = Domingo 2:00<br/>**Cópia de segurança automatizada** = desativado</br>**Integração do Cofre de chaves do Azure** = desativado |

<br/>

> [!NOTE]
> Os tamanhos de máquina sugeridos aqui destinam-se para grupos de disponibilidade de teste em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, veja as recomendações para tamanhos de máquina do SQL Server e as configurações no [melhores práticas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois das três VMs estão totalmente aprovisionadas, terá de associá-las para o **corp.contoso.com** domínio e conceder direitos administrativos de CORP\Install para as máquinas.

### <a name="joinDomain"></a>Associar os servidores ao domínio

Agora pode associar as VMs **corp.contoso.com**. Siga os passos abaixo para as VMs do SQL Server e o servidor de testemunho de partilha de ficheiros:

1. Ligar remotamente à máquina virtual com o **BUILTIN\DomainAdmin**.
2. Na **Gestor de servidores**, clique em **servidor Local**.
3. Clique nas **grupo de trabalho** ligação.
4. Na **nome do computador** secção, clique em **alteração**.
5. Selecione o **domínio** caixa de verificação e tipo **corp.contoso.com** na caixa de texto. Clique em **OK**.
6. Na **segurança do Windows** caixa de diálogo de pop-up, especifique as credenciais para a conta de administrador de domínio predefinida (**CORP\DomainAdmin**) e a palavra-passe (**Contoso! 0000**) .
7. Quando vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique em **OK**.
8. Clique em **fechar**e, em seguida, clique em **reiniciar agora** na caixa de diálogo pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicionar o utilizador de Corp\Install como um administrador em cada cluster VM

Depois de reiniciar cada máquina virtual como um membro do domínio, adicione **CORP\Install** como membro do grupo Administradores local.

1. Aguarde até que a VM é reiniciada, em seguida, inicie o arquivo RDP novamente do controlador de domínio primário para iniciar sessão no **sqlserver-0** utilizando o **CORP\DomainAdmin** conta.
   >[!TIP]
   >Certifique-se de que entre com a conta de administrador de domínio. Nos passos anteriores, utilizou a conta de administrador INCORPORADA IN. Agora que o servidor está no domínio, utilize a conta de domínio. Na sua sessão do RDP, especifique *domínio*\\*username*.

2. Na **Gestor de servidores**, selecione **ferramentas**e, em seguida, clique em **gestão de computadores**.
3. Na **gestão de computadores** janela, expanda **utilizadores e grupos locais**e, em seguida, selecione **grupos**.
4. Clique duas vezes o **administradores** grupo.
5. Na **propriedades de administradores** caixa de diálogo, clique no **Add** botão.
6. Introduza o utilizador **CORP\Install**e, em seguida, clique em **OK**.
7. Clique em **OK** para fechar a **propriedades do administrador** caixa de diálogo.
8. Repita os passos anteriores nos **sqlserver-1** e **cluster fsw**.

### <a name="setServiceAccount"></a>Definir as contas de serviço do SQL Server

Em cada VM do SQL Server, defina a conta de serviço do SQL Server. Utilize as contas que criou quando configurou as contas de domínio.

1. Open **Gestor de configuração do SQL Server**.
2. Com o botão direito do serviço do SQL Server e, em seguida, clique em **propriedades**.
3. Defina a conta e palavra-passe.
4. Repita estes passos em outras VM do SQL Server.  

Para grupos de disponibilidade do SQL Server, cada VM do SQL Server tem de ser executado como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Criar um início de sessão em cada VM do SQL Server para a conta de instalação

Utilize a conta de instalação (CORP\install) para configurar o grupo de disponibilidade. Esta conta tem de ser um membro do **sysadmin** função de servidor fixa em cada VM do SQL Server. Os passos seguintes criam um início de sessão para a conta de instalação:

1. Ligar ao servidor através do protocolo RDP (Remote Desktop), utilizando o  *\<MachineName\>\DomainAdmin* conta.

1. Abra o SQL Server Management Studio e ligue-se para a instância local do SQL Server.

1. Na **Object Explorer**, clique em **segurança**.

1. Com o botão direito **inícios de sessão**. Clique em **novo início de sessão**.

1. Na **início de sessão - novo**, clique em **pesquisa**.

1. Clique em **localizações**.

1. Introduza as credenciais de rede de administrador de domínio.

1. Utilize a conta de instalação.

1. Definir o início de sessão de ser um membro dos **sysadmin** função de servidor fixa.

1. Clique em **OK**.

Repita os passos anteriores em outras VM do SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicionar funcionalidades de Clustering de ativação pós-falha para ambas as VMs do SQL Server

Para adicionar funcionalidades de Clustering de ativação pós-falha, execute os seguintes passos em ambas as VMs do SQL Server:

1. Ligar à máquina virtual do SQL Server através do protocolo RDP (Remote Desktop) com o *CORP\install* conta. Open **Dashboard do Gestor de servidor**.
2. Clique nas **para adicionar funções e funcionalidades** link no dashboard.

    ![Gestor de servidor - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecione **próxima** até chegar à **funcionalidades de servidor** secção.
4. Na **funcionalidades**, selecione **Clustering de ativação pós-falha**.
5. Adicione quaisquer funcionalidades necessárias adicionais.
6. Clique em **instalar** para adicionar as funcionalidades.

Repita os passos em outras VM do SQL Server.

  >[!NOTE]
  > Neste passo, juntamente com associação, na verdade, as VMs do SQL Server para o cluster de ativação pós-falha, agora pode ser automatizado com [CLI de VM do SQL do Azure](virtual-machines-windows-sql-availability-group-cli.md) e [modelos de início rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Configurar o firewall em cada VM do SQL Server

A solução requer as seguintes portas TCP para ser aberta na firewall:

- **VM do SQL Server**:<br/>
   Porta 1433 para uma instância predefinida do SQL Server.
- **Sonda de Balanceador de carga do Azure:**<br/>
   Qualquer porta disponível. Exemplos utilizam com frequência 59999.
- **Ponto final de espelhamento de banco de dados:** <br/>
   Qualquer porta disponível. Exemplos utilizam com frequência 5022.

As portas de firewall tem de estar abertas em ambas as VMs do SQL Server.

O método de abrir as portas depende da solução de firewall que utilizar. A secção seguinte explica como abrir as portas na Firewall do Windows. Abra as portas necessárias em cada uma das suas VMs do SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abrir uma porta TCP na firewall

1. No primeiro servidor de SQL **começar** ecrã, inicie **Firewall do Windows com segurança avançada**.
2. No painel esquerdo, selecione **regras de entrada**. No painel da direita, clique em **nova regra**.
3. Para **tipo de regra**, escolha **porta**.
4. Para a porta, especifique **TCP** e escreva os números de porta adequado. Veja o seguinte exemplo:

   ![Firewall do SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Clique em **Seguinte**.
6. Sobre o **ação** página, manter **permitir a ligação** selecionada e, em seguida, clique em **seguinte**.
7. Sobre o **perfil** página, aceite as predefinições e, em seguida, clique em **próxima**.
8. No **nome** , especifique um nome de regra (como **sonda do Azure LB**) no **nome** caixa de texto e clique em **concluir**.

Repita estes passos na segunda VM do SQL Server.

## <a name="configure-system-account-permissions"></a>Configurar permissões de conta de sistema

Para criar uma conta para a conta de sistema e conceda permissões adequadas, conclua os seguintes passos em cada instância do SQL Server:

1. Criar uma conta para `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server. O seguinte script cria esta conta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda as seguintes permissões para `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server:

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

## <a name="next-steps"></a>Passos Seguintes

* [Criar um grupo de disponibilidade Always On do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
