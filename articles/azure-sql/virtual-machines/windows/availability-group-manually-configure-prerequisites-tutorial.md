---
title: 'Tutorial: Pré-requisitos para o grupo de disponibilidade'
description: Este tutorial mostra como configurar os pré-requisitos para a criação de um grupo de disponibilidade SQL Server Always On em VMs Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f7fdced5a777c5303e402a5cc58048e076b104b4
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049268"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Pré-requisitos para criar sempre em grupos de disponibilidade em SQL Server em máquinas virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial mostra como completar os pré-requisitos para a criação de um [grupo de disponibilidade sQL Server Always On em máquinas virtuais Azure (VMs)](availability-group-manually-configure-tutorial.md). Quando terminar os pré-requisitos, tem um controlador de domínio, dois VMs sQL Server e um servidor de testemunhas num único grupo de recursos.

**Estimativa de tempo:** Pode levar algumas horas para completar os pré-requisitos. Grande parte deste tempo é gasto a criar máquinas virtuais.

O diagrama que se segue ilustra o que se constrói no tutorial.

![Grupo de disponibilidade](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Analisar documentação do grupo de disponibilidade

Este tutorial assume que você tem uma compreensão básica de SQL Server Sempre em grupos de disponibilidade. Se não estiver familiarizado com esta tecnologia, consulte a [visão geral dos grupos always on availability (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Criar uma conta do Azure
Precisa de uma conta do Azure. Pode [abrir uma conta Azure gratuita](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) ou ativar benefícios de subscrição do [Visual Studio.](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **+** para criar um novo objeto no portal.

   ![Novo objeto](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Digite **o grupo de recursos** na janela de pesquisa do **Marketplace.**

   ![Grupo de recursos](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)
4. Clique **no grupo De recursos**.
5. Clique em **Criar**.
6. Sob o nome do **grupo Recursos,** escreva um nome para o grupo de recursos. Por exemplo, digite **sql-ha-rg**.
7. Se tiver várias subscrições do Azure, verifique se a subscrição é a subscrição do Azure em que pretende criar o grupo de disponibilidade.
8. Selecione uma localização. A localização é a região de Azure onde pretende criar o grupo de disponibilidade. Este artigo constrói todos os recursos numa localização Azure.
9. Verifique se pin o painel de **instrumentos** está verificado. Esta configuração opcional coloca um atalho para o grupo de recursos no painel do portal Azure.

   ![Grupo de recursos](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Clique em **Criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e coloca um atalho para o grupo de recursos no portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e as subredes
O próximo passo é criar as redes e subredes do grupo de recursos Azure.

A solução utiliza uma rede virtual com duas subredes. A visão geral da [rede Virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre redes no Azure.

Para criar a rede virtual:

1. No portal Azure, no seu grupo de recursos, clique **+ Adicionar**. 

   ![Novo item](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Pesquisa por **rede virtual**.

     ![Pesquisar rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Clique na **rede Virtual**.
4. Na **rede Virtual,** clique no modelo de implementação do Gestor de **Recursos** e, em seguida, clique em **Criar**.

    A tabela que se segue mostra as definições para a rede virtual:

   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereço** |10.33.0.0/24 |
   | **Nome da sub-rede** |Administrador |
   | **Intervalo de endereços subnet** |10.33.0.0/29 |
   | **Subscrição** |Especifique a subscrição que pretende utilizar. **A subscrição** fica em branco se tiver apenas uma subscrição. |
   | **Grupo de recursos** |Escolha **utilizar o uso existente** e escolha o nome do grupo de recursos. |
   | **Localização** |Especifique a localização Azure. |

   O espaço de endereço e o intervalo de endereços da sub-rede podem ser diferentes da tabela. Dependendo da sua subscrição, o portal sugere um espaço de endereço disponível e uma gama de endereços de subnet correspondente. Se não houver espaço suficiente para endereços, utilize uma subscrição diferente.

   O exemplo usa o nome de sub-rede **Admin**. Esta sub-rede é para os controladores de domínio.

5. Clique em **Criar**.

   ![Configure a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

O Azure devolve-o ao portal e notifica-o quando a nova rede é criada.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma subnet, chamada **Admin.** Os controladores de domínio usam esta sub-rede. Os VMs do Servidor SQL usam uma segunda sub-rede chamada **SQL**. Para configurar esta sub-rede:

1. No seu painel de instrumentos, clique no grupo de recursos que criou, **SQL-HA-RG**. Localize a rede no grupo de recursos no âmbito **da Resources**.

    Se o **SQL-HA-RG** não estiver visível, encontre-o clicando **em Grupos** de Recursos e filtrando pelo nome do grupo de recursos.
2. Clique **em autoHAVNET** na lista de recursos. 
3. Na rede virtual **autoHAVNET,** em **Definições** selecione **Subnets**.

    Repare na sub-rede que já criou.

   ![Configure a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique **+ Subnet**.
6. Em **Adicionar sub-rede,** configure a sub-rede digitando **sqlsubnet** em **nome**. O Azure especifica automaticamente uma gama de **endereços**válida . Verifique se este intervalo de endereços tem pelo menos 10 endereços. Num ambiente de produção, pode precisar de mais endereços.
7. Clique em **OK**.

    ![Configure a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

A tabela que se segue resume as definições de configuração da rede:

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereço** |Este valor depende dos espaços de endereço disponíveis na sua subscrição. Um valor típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**admin** |
| **Intervalo de endereços subnet** |Este valor depende das gamas de endereços disponíveis na sua subscrição. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços subnet** |Este valor depende das gamas de endereços disponíveis na sua subscrição. Um valor típico é 10.0.1.0/24. |
| **Subscrição** |Especifique a subscrição que pretende utilizar. |
| **Grupo de Recursos** |**SQL-HA-RG** |
| **Localização** |Especifique a mesma localização que escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, precisa de criar conjuntos de disponibilidade. Os conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planeados ou não planeados. Um conjunto de disponibilidade azure é um grupo lógico de recursos que o Azure coloca nos domínios de falhas físicas e nos domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade têm recursos de energia e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não são reduzidos para manutenção ao mesmo tempo. Para obter mais informações, veja [Gerir a disponibilidade das máquinas virtuais](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Precisa de dois conjuntos de disponibilidade. Uma é para os controladores de domínio. A segunda é para os VMs do Servidor SQL.

Para criar um conjunto de disponibilidade, vá ao grupo de recursos e clique em **Adicionar**. Filtrar os resultados digitando **o conjunto de disponibilidade**. Clique em **Conjunto de Disponibilidade** nos resultados e, em seguida, clique em **Criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros na tabela seguinte:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade do Servidor SQL |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, volte ao grupo de recursos no portal Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, subredes e conjuntos de disponibilidade, está pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Criar máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, volte ao grupo de recursos **SQL-HA-RG.**

1. Clique em **Adicionar**. 
2. Tipo **Windows Server 2016 Datacenter**.
3. Clique no **Windows Server 2016 Datacenter**. No **Datacenter do Windows Server 2016,** verifique se o modelo de implementação é **O Gestor**de Recursos , e, em seguida, clique em **Criar**. 

Repita os passos anteriores para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primário-dc
* ad-secundário-dc

  > [!NOTE]
  > A máquina virtual **ad-secondary-dc** é opcional, para fornecer alta disponibilidade para Serviços de Domínio de Diretório Ativo.
  >
  >

A tabela que se segue mostra as definições para estas duas máquinas:

| **Campo** | Valor |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-primary-dc*.</br>Segundo controlador de domínio *ad-secundário-dc*. |
| **Tipo de disco da VM** |SSD |
| **Nome de utilizador** |DomainAdmin |
| **Palavra-passe** |Contoso!0000 |
| **Subscrição** |*A sua subscrição* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localização** |*A sua localização* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Utilize discos geridos**  -  **Sim, é o** que |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |admin |
| **Endereço IP público** |*O mesmo nome que o VM* |
| **Grupo de segurança da rede** |*O mesmo nome que o VM* |
| **Conjunto de disponibilidade** |adavailabilityset </br>**Domínios de avaria**:2 </br>**Domínios de atualização**:2|
| **Diagnóstico** |Ativado |
| **Conta de armazenamento de diagnóstico** |*Criado automaticamente* |

   >[!IMPORTANT]
   >Só é possível colocar um VM num conjunto de disponibilidade quando o criar. Não é possível alterar o conjunto de disponibilidade sumido após a criação de um VM. Ver [Gerir a disponibilidade de máquinas virtuais](../../../virtual-machines/linux/manage-availability.md).

Azure cria as máquinas virtuais.

Depois de criadas as máquinas virtuais, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configure o controlador de domínio
Nos seguintes passos, configure a máquina **ad-primary-dc** como controlador de domínio para corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione a máquina **ad-primary-dc.** No **anúncio-primário-dc,** clique **em Connect** para abrir um ficheiro RDP para acesso remoto ao ambiente de trabalho.

    ![Ligar a uma máquina virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)
2. Inscreva-se na sua conta de administrador configurada **(\DomainAdmin)** e na palavra-passe **(Contoso!0000**).
3. Por predefinição, o painel do Gestor do **Servidor** deve ser apresentado.
4. Clique no link **Adicionar e funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
5. Selecione **Seguinte** até chegar à secção funções do **servidor.**
6. Selecione os serviços de domínio de **diretório ativo** e as funções **do Servidor DNS.** Quando for solicitado, adicione quaisquer funcionalidades adicionais que sejam necessárias por estas funções.

   > [!NOTE]
   > O Windows avisa que não existe um endereço IP estático. Se estiver a testar a configuração, clique em **Continuar**. Para cenários de produção, detete o endereço IP para estático no portal Azure ou utilize o [PowerShell para definir o endereço IP estático da máquina do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Adicionar diálogo de papéis](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)
7. Clique em **Seguida** até chegar à secção **de Confirmação.** Selecione o **reinício do servidor de destino automaticamente se for necessário** a caixa de verificação.
8. Clique em **Instalar**.
9. Depois de as funcionalidades terminarem a instalação, volte ao painel do Gestor do **Servidor.**
10. Selecione a nova opção **AD DS** no painel da mão esquerda.
11. Clique no link **Mais** na barra de aviso amarela.

    ![Diálogo AD DS no DNS Server VM](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
12. Na coluna **de ação** do diálogo de detalhes de **tarefas de todos os servidores,** clique em Promover este servidor para um controlador de **domínio**.
13. No Assistente de Configuração de Serviços de Domínio de **Diretório Ativo,** utilize os seguintes valores:

    | **Página** | Definição |
    | --- | --- |
    | **Configuração da Implementação** |**Adicione uma nova floresta**<br/> **Nome** de domínio raiz = corp.contoso.com |
    | **Opções de Controlador de Domínio** |**DSRM Password** = Contoso!0000<br/>**Confirmar Palavra-passe** = Contoso!0000 |
14. Clique em **Next** para ver as outras páginas do assistente. Na página **pré-requisitos Verifique** se vê a seguinte mensagem: **Todas as verificações pré-requisitos passadas com sucesso**. Pode rever quaisquer mensagens de aviso aplicáveis, mas é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ad-primary-dc** reinicia automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Note o endereço IP do controlador de domínio primário

Utilize o controlador de domínio primário para DNS. Note o endereço IP do controlador de domínio primário.

Uma maneira de obter o endereço IP do controlador de domínio primário é através do portal Azure.

1. No portal Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No controlador de domínio primário, clique em **interfaces de rede**.

![Interfaces de rede](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Note o endereço IP privado para este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configure a rede virtual DNS
Depois de criar o primeiro controlador de domínio e ativar o DNS no primeiro servidor, configure a rede virtual para utilizar este servidor para DNS.

1. No portal Azure, clique na rede virtual.

2. Em **Definições,** clique no **Servidor DNS**.

3. Clique em **Costume**, e escreva o endereço IP privado do controlador de domínio primário.

4. Clique em **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configure o segundo controlador de domínio
Depois de o controlador de domínio primário reiniciar, pode configurar o segundo controlador de domínio. Este passo opcional é para alta disponibilidade. Siga estes passos para configurar o segundo controlador de domínio:

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione a máquina **ad-secondary-dc.** No **ad-secondary-dc,** clique **em Connect** para abrir um ficheiro RDP para acesso remoto ao ambiente de trabalho.
2. Inscreva-se no VM utilizando a sua conta de administrador configurada **(BUILTIN\DomainAdmin)** e a palavra-passe **(Contoso!0000**).
3. Altere o endereço de servidor DNS preferido para o endereço do controlador de domínio.
4. No Centro de **Rede e Partilha,** clique na interface de rede.
   ![Interface de rede](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Selecione versão 4 do Protocolo de **Internet (TCP/IPv4)** e clique em **Propriedades**.
7. Selecione **Utilize os seguintes endereços do servidor DNS** e especifique o endereço do controlador de domínio primário no **servidor DNS preferido**.
8. Clique **ok**, e depois **feche** para comprometer as alterações. Pode agora juntar-se ao VM para **corp.contoso.com.**

   >[!IMPORTANT]
   >Se perder a ligação ao seu ambiente de trabalho remoto depois de alterar a definição de DNS, vá ao portal Azure e reinicie a máquina virtual.

9. Do ambiente de trabalho remoto ao controlador de domínio secundário, abra o **Dashboard do Gestor**do Servidor .
10. Clique no link **Adicionar e funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Selecione **Seguinte** até chegar à secção funções do **servidor.**
12. Selecione os serviços de domínio de **diretório ativo** e as funções **do Servidor DNS.** Quando for solicitado, adicione quaisquer funcionalidades adicionais que sejam necessárias por estas funções.
13. Depois de as funcionalidades terminarem a instalação, volte ao painel do Gestor do **Servidor.**
14. Selecione a nova opção **AD DS** no painel da mão esquerda.
15. Clique no link **Mais** na barra de aviso amarela.
16. Na coluna **de ação** do diálogo de detalhes de **tarefas de todos os servidores,** clique em Promover este servidor para um controlador de **domínio**.
17. Sob configuração de **implementação,** selecione **Adicionar um controlador de domínio a um domínio existente**.
    ![Configuração de implementação](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)
18. Clique em **Selecionar**.
19. Conecte-se utilizando a conta do administrador **(CORP. CONTOSO.COM\domainadmin**) e palavra-passe **(Contoso!0000**).
20. Em **Selecione um domínio da floresta,** clique no seu domínio e, em seguida, clique em **OK**.
21. Nas opções do controlador de **domínio,** utilize os valores predefinidos e detete uma palavra-passe DSRM.

    >[!NOTE]
    >A página **DeOpÇões DNS** pode avisá-lo que uma delegação para este servidor DNS não pode ser criada. Pode ignorar este aviso em ambientes de não produção.
22. Clique em **seguida** até que o diálogo atinja a verificação **de pré-requisitos.** Em seguida, clique em **Instalar**.

Depois de o servidor terminar as alterações de configuração, reinicie o servidor.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicione o Endereço IP Privado ao segundo controlador de domínio ao Servidor DNS VPN

No portal Azure, em rede virtual, altere o Servidor DNS para incluir o endereço IP do controlador de domínio secundário. Esta definição permite o despedimento do serviço DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Configure as contas de domínio

Nos próximos passos, configura as contas do Diretório Ativo. O quadro seguinte mostra as contas:

| |Conta de instalação<br/> |sqlserver-0 <br/>Conta de Serviço de Servidor SQL e SQL |sqlserver-1<br/>Conta de Serviço de Servidor SQL e SQL
| --- | --- | --- | ---
|**Nome próprio** |Instalar |SQLSvc1 | SQLSvc2
|**User SamAccountName** |Instalar |SQLSvc1 | SQLSvc2

Utilize os seguintes passos para criar cada conta.

1. Inscreva-se na máquina **de anúncios primários-dc.**
2. No **Gestor do Servidor,** selecione **Ferramentas,** clique em **Ative Directory Administrative Center**.   
3. Selecione **o corpo (local)** a partir do painel esquerdo.
4. No painel de **tarefas** certos, selecione **New**, e, em seguida, clique em **Utilizador**.
   ![Centro de Administração do Active Directory](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Detete uma senha complexa para cada conta.<br/> Para ambientes não produtivos, detete a conta de utilizador para nunca expirar.

5. Clique em **OK** para criar o utilizador.
6. Repita os passos anteriores para cada uma das três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceda as permissões necessárias à conta de instalação
1. No **Centro Administrativo de Diretório Ativo,** selecione **corp (local)** no painel esquerdo. Em seguida, no painel de **tarefas** à direita, clique em **Propriedades**.

    ![Propriedades dos utilizadores da CORP](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)
2. **Selecione Extensões**e, em seguida, clique no botão **Avançado** no separador **Segurança.**
3. Nas **Definições avançadas** de segurança para o diálogo de corpo, clique em **Adicionar**.
4. Clique **em Selecionar um principal,** procurar POR **CORP\Instalar**e, em seguida, clicar **EM OK**.
5. Selecione a caixa de verificação **de todas as propriedades.**

6. Selecione a caixa de verificação de **objetos Create Computer.**

     ![Permissões de utilizador da Corp](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)
7. Clique **OK**e, em seguida, clique **OK** novamente. Feche a janela das propriedades **do corpo.**

Agora que terminou de configurar o Diretório Ativo e os objetos do utilizador, crie dois VMs de Servidor SQL e um VM do servidor de testemunhas. Então junte-se aos três ao domínio.

## <a name="create-sql-server-vms"></a>Criar VMs de servidor SQL

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias do Servidor SQL. Uma terceira máquina virtual funcionará como testemunha. O Windows Server 2016 pode utilizar uma testemunha na [nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), no entanto, para consistência com sistemas operativos anteriores, este documento utiliza uma máquina virtual para uma testemunha.  

Antes de proceder, considere as seguintes decisões de design.

* **Armazenamento - Discos Geridos Azure**

   Para o armazenamento de máquinas virtuais, utilize discos geridos azure. A Microsoft recomenda discos geridos para máquinas virtuais Do Servidor SQL. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter informações adicionais, veja [Azure Managed Disks Overview](../../../virtual-machines/linux/managed-disks-overview.md) (Descrição Geral do Managed Disks). Para obter detalhes sobre discos geridos num conjunto de disponibilidade, consulte [os Discos Geridos de Utilização para VMs num conjunto de disponibilidade](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rede - Endereços IP privados em produção**

   Para as máquinas virtuais, este tutorial utiliza endereços IP públicos. Um endereço IP público permite a ligação remota diretamente à máquina virtual através da internet - facilita os passos de configuração. Em ambientes de produção, a Microsoft recomenda apenas endereços IP privados de forma a reduzir a pegada vulnerabilidade do recurso VM de instância sQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar os VMs do Servidor SQL
Em seguida, crie três VMs-- dois VMs sQL Server e um VM para um nó de cluster adicional. Para criar cada um dos VMs, volte ao grupo de recursos **SQL-HA-RG,** clique em **Adicionar,** procure o item apropriado da galeria, clique em **Máquina Virtual,** e clique em **From Gallery**. Utilize as informações na tabela seguinte para ajudá-lo a criar os VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item apropriado da galeria |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
| **Básicos** de configuração da máquina virtual |**Nome** = cluster-fsw<br/>**Nome do utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso!0000<br/>**Subscrição** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |**Nome** = sqlserver-0<br/>**Nome do utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso!0000<br/>**Subscrição** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |**Nome** = sqlserver-1<br/>**Nome do utilizador** = DomainAdmin<br/>**Palavra-passe** = Contoso!0000<br/>**Subscrição** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |
| **Tamanho da** configuração da máquina virtual |**TAMANHO** = DS1 \_ V2 (1 vCPU, 3,5 GB) |**TAMANHO** = DS2 \_ V2 (2 vCPUs, 7 GB)</br>O tamanho deve suportar o armazenamento SSD (suporte de disco Premium. )) |**TAMANHO** = DS2 \_ V2 (2 vCPUs, 7 GB) |
| **Definições de** configuração da máquina virtual |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorização** de Diagnósticos = Ativado<br/>**Conta** de armazenamento de diagnóstico = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorização** de Diagnósticos = Ativado<br/>**Conta** de armazenamento de diagnóstico = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorização** de Diagnósticos = Ativado<br/>**Conta** de armazenamento de diagnóstico = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |
| Definições de **servidor sql de** configuração virtual da máquina |Não aplicável |**Conectividade SQL** = Privada (dentro da Rede Virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desativar<br/>**Configuração de armazenamento** = Geral<br/>**Patching automatizado** = Domingo às 14:00<br/>**Backup automatizado** = Desativado</br>**Integração** do cofre de chaves Azure = Desativado |**Conectividade SQL** = Privada (dentro da Rede Virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desativar<br/>**Configuração de armazenamento** = Geral<br/>**Patching automatizado** = Domingo às 14:00<br/>**Backup automatizado** = Desativado</br>**Integração** do cofre de chaves Azure = Desativado |

<br/>

> [!NOTE]
> Os tamanhos da máquina aqui sugeridos destinam-se a testar grupos de disponibilidade em VMs Azure. Para obter o melhor desempenho nas cargas de trabalho de produção, consulte as recomendações para os tamanhos das máquinas Do Servidor SQL e a configuração nas [melhores práticas de Desempenho para o Servidor SQL em máquinas virtuais Azure](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois de os três VMs estarem totalmente provisionados, você precisa se juntar a eles ao domínio **corp.contoso.com** e conceder CORP\Instale direitos administrativos às máquinas.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Junte-se aos servidores para o domínio

Agora pode juntar-se aos VMs para **corp.contoso.com.** Faça os seguintes passos tanto para os VMs do Servidor SQL como para o servidor de testemunhas de partilha de ficheiros:

1. Ligue-se remotamente à máquina virtual com **BUILTIN\DomainAdmin**.
2. No **Gestor do Servidor,** clique no **Servidor Local**.
3. Clique no link **WORKGROUP.**
4. Na secção Nome do **Computador,** clique em **Alterar**.
5. Selecione a caixa de verificação **de domínio** e **escreva corp.contoso.com** na caixa de texto. Clique em **OK**.
6. No diálogo popup **Windows Security,** especifique as credenciais para a conta de administrador de domínio padrão **(CORP\DomainAdmin**) e a palavra-passe **(Contoso!0000**).
7. Quando vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique em **OK**.
8. Clique em **Fechar**e, em seguida, clique em **Reiniciar Agora** no diálogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicione o utilizador Corp\Instale como administrador em cada cluster VM

Depois de cada máquina virtual recomeçar como membro do domínio, adicione **o CORP\Instale** como membro do grupo de administradores locais.

1. Aguarde até que o VM seja reiniciado e, em seguida, volte a lançar o ficheiro RDP a partir do controlador de domínio primário para iniciar o **sqlserver-0** utilizando a conta **CORP\DomainAdmin.**
   >[!TIP]
   >Certifique-se de que faz o insessão com a conta de administrador de domínio. Nos passos anteriores, estava a utilizar a conta de administrador built IN. Agora que o servidor está no domínio, use a conta de domínio. Na sua sessão de RDP, especifique o nome de utilizador *DOM* \\ *username*.

2. No **Gestor do Servidor,** selecione **Ferramentas**e, em seguida, clique em **Gestão de Computadores**.
3. Na janela **de Gestão** de Computadores, expanda **os Utilizadores e Grupos Locais**e, em seguida, selecione **Grupos**.
4. Clique duas **vezes** no grupo Administradores.
5. No diálogo **'Propriedades administradores',** clique no botão **Adicionar.**
6. Introduza o utilizador **CORP\Instale,** e, em seguida, clique **OK**.
7. Clique **em OK** para fechar o diálogo **'Propriedades do Administrador'.**
8. Repita os passos anteriores no **sqlserver-1** e **no cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Definir as contas de serviço do Servidor SQL

Em cada VM do Servidor SQL, detete a conta de serviço SQL Server. Utilize as contas que criou quando configuraas as contas de domínio.

1. Abra o **Gestor de Configuração do SQL Server**.
2. Clique no serviço SQL Server e, em seguida, clique em **Propriedades**.
3. Desloque a conta e a senha.
4. Repita estes passos no outro VM do Servidor SQL.  

Para os grupos de disponibilidade do Servidor SQL, cada VM do Servidor SQL precisa de ser executado como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Crie um sessão em cada VM do Servidor SQL para a conta de instalação

Utilize a conta de instalação (instalação CORP\) para configurar o grupo de disponibilidade. Esta conta tem de ser um membro da função de servidor fixo **sysadmin** em cada VM do Servidor SQL. Os seguintes passos criam um sessão para a conta de instalação:

1. Ligue-se ao servidor através do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando a conta * \<MachineName\> \DomainAdmin.*

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se à instância local do Servidor SQL.

1. No **Object Explorer,** clique em **Segurança**.

1. **Logins**de clique à direita . Clique **em Novo Login**.

1. Em **Login - Novo,** clique em **Procurar**.

1. Clique em **Localizações**.

1. Introduza as credenciais de rede de administrador de domínio.

1. Utilize a conta de instalação.

1. Detete o sign-in para ser um membro da função de servidor fixo de **sisadmina.**

1. Clique em **OK**.

Repita os passos anteriores no outro VM do Servidor SQL.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicione funcionalidades de clustering failover a ambos os VMs do servidor SQL

Para adicionar funcionalidades de Clustering Failover, faça os seguintes passos em ambos os VMs do Servidor SQL:

1. Ligue-se à máquina virtual do Servidor SQL através do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando a conta *de instalação CORP\.* Painel **de instrumentos de gestor**de servidores abertos.
2. Clique no link **Adicionar e funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
3. Selecione **Seguinte** até chegar à secção funcionalidades do **servidor.**
4. Em **Funcionalidades,** selecione **Failover Clustering**.
5. Adicione quaisquer funcionalidades adicionais necessárias.
6. Clique **em Instalar** para adicionar as funcionalidades.

Repita os passos no outro VM do Servidor SQL.

  >[!NOTE]
  > Este passo, juntamente com a adesão dos VMs do Servidor SQL ao cluster failover, pode agora ser automatizado com modelos [Azure SQL VM CLI](availability-group-az-cli-configure.md) e [Azure Quickstart.](availability-group-quickstart-template-configure.md)


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Configure a firewall em cada VM do servidor SQL

A solução requer que as seguintes portas TCP sejam abertas na firewall:

- **SQL Server VM:**<br/>
   Porta 1433 para uma instância predefinida do Servidor SQL.
- **Sonda de equilíbrio de carga Azure:**<br/>
   Qualquer porta disponível. Exemplos frequentemente usam 59999.
- **Ponto final espelhado na base de dados:** <br/>
   Qualquer porta disponível. Exemplos frequentemente usam 5022.

As portas de firewall precisam de ser abertas em ambos os VMs do Servidor SQL.

O método de abertura das portas depende da solução de firewall que utiliza. A secção seguinte explica como abrir as portas no Windows Firewall. Abra as portas necessárias em cada um dos seus VMs do Servidor SQL.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abra uma porta TCP na firewall

1. No primeiro ecrã SQL Server **Iniciar,** lance **o Windows Firewall com Advanced Security**.
2. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.
3. Para o Tipo de **Regra,** escolha **Porta**.
4. Para a porta, especifique **tCP** e escreva os números de porta apropriados. Veja o seguinte exemplo:

   ![Firewall SQL](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Clique em **Seguinte**.
6. Na página **Ação,** mantenha **permitir a ligação** selecionada e, em seguida, clique em **Seguinte**.
7. Na página **'Perfil',** aceite as definições predefinidas e, em seguida, clique em **Next**.
8. Na página **Nome,** especifique um nome de regra (como **a sonda Azure LB)** na caixa de texto **Nome** e, em seguida, clique em **Terminar**.

Repita estes passos no segundo VM do Servidor SQL.

## <a name="configure-system-account-permissions"></a>Configurar permissões de conta de sistema

Para criar uma conta para a conta do sistema e conceder permissões apropriadas, complete os seguintes passos em cada instância do Servidor SQL:

1. Crie uma conta `[NT AUTHORITY\SYSTEM]` em cada instância do Servidor SQL. O seguinte script cria esta conta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda as seguintes permissões `[NT AUTHORITY\SYSTEM]` em cada instância do Servidor SQL:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   O seguinte guião concede estas permissões:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Próximos passos

* [Crie um servidor SQL sempre em grupo de disponibilidade em máquinas virtuais Azure](availability-group-manually-configure-tutorial.md)
