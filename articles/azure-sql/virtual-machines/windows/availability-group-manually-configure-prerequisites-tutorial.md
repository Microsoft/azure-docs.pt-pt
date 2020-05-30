---
title: 'Tutorial: Pré-requisitos para grupo de disponibilidade'
description: Este tutorial mostra como configurar os pré-requisitos para a criação de um sql servidor sempre em grupo de disponibilidade em VMs Azure.
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
ms.openlocfilehash: bfb273ec0013925076669c99f08933bd10ffc465
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84197135"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Pré-requisitos para criar grupos de disponibilidade sempre em sql server em máquinas virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial mostra como completar os pré-requisitos para a criação de um [grupo de disponibilidade SQL Server Always On On availability em máquinas virtuais Azure (VMs)](availability-group-manually-configure-tutorial.md). Quando terminar os pré-requisitos, tem um controlador de domínio, dois SQL Server VMs e um servidor de testemunhas num único grupo de recursos.

**Estimativa do tempo**: Pode levar algumas horas para completar os pré-requisitos. Grande parte deste tempo é gasto a criar máquinas virtuais.

O diagrama seguinte ilustra o que se constrói no tutorial.

![Grupo de disponibilidade](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Documentação do grupo de disponibilidade de revisão

Este tutorial pressupõe que você tem uma compreensão básica do SQL Server Always On availability groups. Se não estiver familiarizado com esta tecnologia, consulte [a visão geral dos Grupos de Disponibilidade Sempre Disponível (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Criar uma conta do Azure
Precisa de uma conta do Azure. Pode [abrir uma conta Azure gratuita](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) ou [ativar os benefícios do assinante do Visual Studio.](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **+** para criar um novo objeto no portal.

   ![Novo objeto](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Digite **grupo de recursos** na janela de pesquisa do **Marketplace.**

   ![Grupo de recursos](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)
4. Clique **no grupo de recursos**.
5. Clique **em Criar**.
6. No **nome do grupo de recursos,** escreva um nome para o grupo de recursos. Por exemplo, tipo **sql-ha-rg**.
7. Se tiver várias subscrições do Azure, verifique se a subscrição é a subscrição Azure em que pretende criar o grupo de disponibilidade.
8. Selecione uma localização. A localização é a região de Azure onde pretende criar o grupo de disponibilidade. Este artigo constrói todos os recursos numa localização Azure.
9. Verifique se o Pin para o painel de **instrumentos** está verificado. Esta definição opcional coloca um atalho para o grupo de recursos no painel do portal Azure.

   ![Grupo de recursos](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Clique em **Criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e fixa um atalho para o grupo de recursos no portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e sub-redes
O próximo passo é criar as redes e sub-redes no grupo de recursos Azure.

A solução utiliza uma rede virtual com duas sub-redes. A [visão geral da rede Virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre redes em Azure.

Para criar a rede virtual:

1. No portal Azure, no seu grupo de recursos, clique **+ Adicionar**. 

   ![Novo item](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Procure por **rede virtual.**

     ![Pesquisar rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Clique **na rede Virtual.**
4. Na **rede Virtual,** clique no modelo de implementação **do Gestor de Recursos** e, em seguida, clique em **Criar**.

    A tabela a seguir mostra as definições para a rede virtual:

   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereço** |10.33.0.0/24 |
   | **Nome da sub-rede** |Administrador |
   | **Gama de endereços de sub-rede** |10.33.0.0/29 |
   | **Subscrição** |Especifique a subscrição que pretende utilizar. **A subscrição** está em branco se tiver apenas uma subscrição. |
   | **Grupo de recursos** |Escolha **Utilizar o nome existente** e escolher o nome do grupo de recursos. |
   | **Localização** |Especifique a localização do Azure. |

   O seu espaço de endereço e intervalo de endereços de sub-rede pode ser diferente da tabela. Dependendo da sua subscrição, o portal sugere um espaço de endereço disponível e uma gama de endereços correspondente. Se não houver espaço suficiente para o endereço, utilize uma subscrição diferente.

   O exemplo utiliza o nome de sub-rede **Admin.** Esta sub-rede destina-se aos controladores de domínio.

5. Clique **em Criar**.

   ![Configurar a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

O Azure devolve-o ao painel do portal e notifica-o quando a nova rede é criada.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma sub-rede, chamada **Administrador.** Os controladores de domínio utilizam esta sub-rede. Os VMs do Servidor SQL utilizam uma segunda sub-rede chamada **SQL**. Para configurar esta sub-rede:

1. No seu painel de instrumentos, clique no grupo de recursos que criou, **SQL-HA-RG**. Localizar a rede no grupo de recursos em **Recursos**.

    Se **o SQL-HA-RG** não estiver visível, encontre-o clicando em **Grupos de Recursos** e filtrando pelo nome do grupo de recursos.
2. Clique automaticamente no **'AUTOHAVNET'** na lista de recursos. 
3. Na rede virtual **autoHAVNET,** em **Definições** selecione **Sub-redes**.

    Note a sub-rede que já criou.

   ![Configurar a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)
5. Criar uma segunda sub-rede. Clique **+ Sub-rede**.
6. Na **sub-rede Add**, configurar a sub-rede digitando **sqlsubnet** em **nome**. O Azure especifica automaticamente uma gama de **endereços**válida. Verifique se este intervalo de endereços tem pelo menos 10 endereços. Num ambiente de produção, pode precisar de mais endereços.
7. Clique em **OK**.

    ![Configurar a rede virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

A tabela a seguir resume as definições de configuração da rede:

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereço** |Este valor depende dos espaços de endereço disponíveis na sua subscrição. Um valor típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**admin** |
| **Gama de endereços de sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Gama de endereços de sub-rede** |Este valor depende dos intervalos de endereços disponíveis na sua subscrição. Um valor típico é 10.0.1.0/24. |
| **Subscrição** |Especifique a subscrição que pretende utilizar. |
| **Grupo de Recursos** |**SQL-HA-RG** |
| **Localização** |Especifique o mesmo local que escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, tem de criar conjuntos de disponibilidade. Os conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planeados ou não planeados. Um conjunto de disponibilidade de Azure é um grupo lógico de recursos que o Azure coloca em domínios de falhas físicas e domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade tenham recursos de potência e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não são reduzidos para manutenção ao mesmo tempo. Para obter mais informações, veja [Gerir a disponibilidade das máquinas virtuais](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Precisa de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para os VMs do SqL Server.

Para criar um conjunto de disponibilidade, vá ao grupo de recursos e clique em **Adicionar**. Filtrar os resultados digitando **o conjunto de disponibilidades**. Clique em **Disponibilidade Definir** os resultados e, em seguida, clique em **Criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros do quadro seguinte:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade do servidor SQL |
| --- | --- | --- |
| **Nome** |conjunto de adavailability |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, volte ao grupo de recursos no portal Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, sub-redes e conjuntos de disponibilidade, está pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Criar máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, volte ao grupo de recursos **SQL-HA-RG.**

1. Clique em **Adicionar**. 
2. Tipo **Windows Server 2016 Datacenter**.
3. Clique no **Centro de Dados 2016 do Windows Server**. No **Centro de Dados do Windows Server 2016,** verifique se o modelo de implementação é **Gestor de Recursos**e, em seguida, clique em **Criar**. 

Repita os passos anteriores para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primary-dc
* ad-secundário-dc

  > [!NOTE]
  > A máquina virtual **ad-secondary-DC** é opcional, para fornecer alta disponibilidade para serviços de domínio de diretório ativo.
  >
  >

A tabela a seguir mostra as definições para estas duas máquinas:

| **Campo** | Valor |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-primary-dc*.</br>Segundo controlador de domínio *ad-secondary-dc*. |
| **Tipo de disco da VM** |SSD |
| **Nome de utilizador** |DomainAdmin |
| **Palavra-passe** |Contoso!0000 |
| **Subscrição** |*A sua subscrição* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localização** |*A sua localização* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Utilizar discos geridos**  -  **Sim, é o seu** |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |admin |
| **Endereço IP público** |*O mesmo nome do VM* |
| **Grupo de segurança de rede** |*O mesmo nome do VM* |
| **Conjunto de disponibilidade** |conjunto de adavailability </br>**Domínios de avarias**:2 </br>**Atualização de domínios**:2|
| **Diagnóstico** |Ativado |
| **Conta de armazenamento de diagnóstico** |*Criado automaticamente* |

   >[!IMPORTANT]
   >Só é possível colocar um VM num conjunto de disponibilidade quando o criar. Não é possível alterar o conjunto de disponibilidade após a criação de um VM. Ver [Gerir a disponibilidade de máquinas virtuais.](../../../virtual-machines/linux/manage-availability.md)

Azure cria as máquinas virtuais.

Após a criação das máquinas virtuais, configuure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configure o controlador de domínio
Nos passos seguintes, configuure a máquina **ad-primar-dc** como controlador de domínio para corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione a máquina **ad-primar-dc.** No **ad-primary-dc,** clique em **Connect** para abrir um ficheiro RDP para acesso remoto ao ambiente de trabalho.

    ![Ligar a uma máquina virtual](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)
2. Inscreva-se com a sua conta de administrador configurada **(\DomainAdmin)** e senha **(Contoso!0000**).
3. Por predefinição, o painel de instrumentos **do Gestor do Servidor** deve ser apresentado.
4. Clique nas **funções Add e link de funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
5. Selecione **Seguinte** até chegar à secção **'Funções do Servidor'.**
6. Selecione os **serviços de domínio do diretório ativo** e as funções **do Servidor DNS.** Quando for solicitado, adicione quaisquer funcionalidades adicionais que sejam necessárias por estas funções.

   > [!NOTE]
   > O Windows avisa que não existe nenhum endereço IP estático. Se estiver a testar a configuração, clique em **Continuar**. Para cenários de produção, desaponione o endereço IP para estática no portal Azure ou utilize o [PowerShell para definir o endereço IP estático da máquina do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Adicionar diálogo de papéis](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)
7. Clique **em seguida** até chegar à secção de **Confirmação.** Selecione **o servidor de destino reinicie automaticamente se necessário** caixa de verificação.
8. Clique **em Instalar**.
9. Depois de as funcionalidades terminarem de ser instaladas, volte ao painel **do Gestor do Servidor.**
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.

    ![Diálogo DS AD no DNS Server VM](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
12. Na coluna **de ação** do diálogo **'Detalhes de tarefas de todos os servidores',** clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração de Serviços de Domínio ativo,** utilize os seguintes valores:

    | **Página** | Definição |
    | --- | --- |
    | **Configuração da Implementação** |**Adicione uma nova floresta**<br/> **Nome de domínio raiz** = corp.contoso.com |
    | **Opções de Controlador de Domínio** |**Senha DSRM** = Contoso!0000<br/>**Confirmar Senha** = Contoso!0000 |
14. Clique **em seguida** para ver as outras páginas do assistente. Na página **Pré-Requisitos Verificar,** verifique se vê a seguinte mensagem: **Todas as verificações pré-requisitos passadas com sucesso**. Pode rever quaisquer mensagens de aviso aplicáveis, mas é possível continuar com a instalação.
15. Clique **em Instalar**. A máquina virtual **ad-primar-dc** reinicia automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Note o endereço IP do controlador de domínio primário

Utilize o controlador de domínio primário para DNS. Note o endereço IP do controlador de domínio primário.

Uma forma de obter o endereço IP do controlador de domínio primário é através do portal Azure.

1. No portal Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No controlador de domínio primário, clique nas **interfaces de Rede**.

![Interfaces de rede](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Note o endereço IP privado deste servidor.

### <a name="configure-the-virtual-network-dns"></a>Configurar o DNS de rede virtual
Depois de criar o primeiro controlador de domínio e de ativar o DNS no primeiro servidor, configuure a rede virtual para utilizar este servidor para DNS.

1. No portal Azure, clique na rede virtual.

2. Em **Definições**, clique **no Servidor DNS**.

3. Clique **em Personalizado**e digite o endereço IP privado do controlador de domínio primário.

4. Clique em **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configure o segundo controlador de domínio
Após o reinício do controlador de domínio primário, pode configurar o segundo controlador de domínio. Este passo opcional é para uma elevada disponibilidade. Siga estes passos para configurar o segundo controlador de domínio:

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione a máquina **ad-secondary-dc.** Em **ad-secondary-dc,** clique em **Connect** para abrir um ficheiro RDP para acesso remoto ao ambiente de trabalho.
2. Inscreva-se no VM utilizando a sua conta de administrador configurada **(BUILTIN\DomainAdmin)** e senha **(Contoso!0000**).
3. Altere o endereço do servidor DNS preferido para o endereço do controlador de domínio.
4. No **Centro de Rede e Partilha,** clique na interface de rede.
   ![Interface de rede](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Selecione **a versão 4 do Protocolo de Internet (TCP/IPv4)** e clique em **Propriedades**.
7. Selecione **Utilize os seguintes endereços do servidor DNS** e especifique o endereço do controlador de domínio primário no **servidor DNS Preferred**.
8. Clique **em OK**e, em seguida, **perto** para cometer as alterações. Pode agora juntar-se ao VM para **corp.contoso.com.**

   >[!IMPORTANT]
   >Se perder a ligação ao seu ambiente de trabalho remoto depois de alterar a definição de DNS, vá ao portal Azure e reinicie a máquina virtual.

9. Do ambiente de trabalho remoto ao controlador de domínio secundário, abra o **Painel de Gestão do Servidor**.
10. Clique nas **funções Add e link de funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Selecione **Seguinte** até chegar à secção **'Funções do Servidor'.**
12. Selecione os **serviços de domínio do diretório ativo** e as funções **do Servidor DNS.** Quando for solicitado, adicione quaisquer funcionalidades adicionais que sejam necessárias por estas funções.
13. Depois de as funcionalidades terminarem de ser instaladas, volte ao painel **do Gestor do Servidor.**
14. Selecione a nova opção **AD DS** no painel esquerdo.
15. Clique no link **Mais** na barra de aviso amarela.
16. Na coluna **de ação** do diálogo **'Detalhes de tarefas de todos os servidores',** clique em **Promover este servidor a um controlador de domínio**.
17. Na **configuração de implementação**, selecione **Adicione um controlador de domínio a um domínio existente**.
    ![Configuração de implementação](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)
18. Clique em **Selecionar**.
19. Conecte-se utilizando a conta de administrador **(CORP. CONTOSO.COM\domainadmin)** e senha **(Contoso!0000**).
20. Em **Selecione um domínio da floresta,** clique no seu domínio e, em seguida, clique em **OK**.
21. Nas **Opções de Controlador de Domínio,** utilize os valores predefinidos e desajuste uma palavra-passe DSRM.

    >[!NOTE]
    >A página **DNS Options** pode avisá-lo que não é possível criar uma delegação para este servidor DNS. Pode ignorar este aviso em ambientes não produtivos.
22. Clique **em seguida** até que o diálogo atinja a verificação **pré-requisitos.** Em seguida, clique em **Instalar**.

Depois de o servidor terminar as alterações de configuração, reinicie o servidor.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicione o Endereço IP Privado ao segundo controlador de domínio ao Servidor DNS VPN

No portal Azure, em rede virtual, altere o Servidor DNS para incluir o endereço IP do controlador de domínio secundário. Esta definição permite o despedimento do serviço DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Configurar as contas de domínio

Nos próximos passos, configura as contas do Ative Directory. A tabela a seguir mostra as contas:

| |Conta de instalação<br/> |sqlserver-0 <br/>Conta de Serviço de Servidor SQL e SQL |sqlserver-1<br/>Conta de Serviço de Servidor SQL e SQL
| --- | --- | --- | ---
|**Primeiro nome** |Instalar |SQLSvc1 | SQLSvc2
|**Nome do Utilizador SamAccount** |Instalar |SQLSvc1 | SQLSvc2

Utilize os seguintes passos para criar cada conta.

1. Inscreva-se na máquina de **ad-primar-dc.**
2. No **Gestor do Servidor**, selecione **Ferramentas**e, em seguida, clique no **Ative Directory Administrative Center**.   
3. Selecione **corp (local)** do painel esquerdo.
4. No painel **de tarefas** à direita, selecione **Novo**e, em seguida, clique em **User**.
   ![Centro de Administração do Active Directory](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Desateia uma senha complexa para cada conta.<br/> Para ambientes não produtivos, desaprote a conta de utilizador para nunca expirar.

5. Clique **em OK** para criar o utilizador.
6. Repita os passos anteriores para cada uma das três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceder as permissões necessárias à conta de instalação
1. No **Ative Directory Administrative Center**, selecione **corp (local)** no painel esquerdo. Em seguida, no painel **de tarefas** da direita, clique em **Propriedades**.

    ![Propriedades do utilizador CORP](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)
2. Selecione **Extensões**e, em seguida, clique no botão **Advanced** no separador **Segurança.**
3. Nas **Definições avançadas de segurança para diálogo de corp,** clique em **Adicionar**.
4. Clique **em Selecionar um principal,** procure por **CORP\Install**e, em seguida, clique em **OK**.
5. Selecione a caixa de verificação **de todas as propriedades.**

6. Selecione a caixa de verificação **de objetos do Computador Criar.**

     ![Permissões de utilizador da Corp](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)
7. Clique **em OK**e, em seguida, clique em **OK** novamente. Feche a janela das propriedades do **corp.**

Agora que terminou de configurar o Ative Directory e os objetos do utilizador, crie dois SQL Server VMs e um VM do servidor de testemunhas. Então junte os três ao domínio.

## <a name="create-sql-server-vms"></a>Criar VMs de servidor SQL

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias SQL Server. Uma terceira máquina virtual funcionará como testemunha. O Windows Server 2016 pode utilizar uma [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), no entanto, para a consistência com os sistemas operativos anteriores, este documento utiliza uma máquina virtual para uma testemunha.  

Antes de prosseguir, considere as seguintes decisões de design.

* **Armazenamento - Discos Geridos Azure**

   Para o armazenamento de máquinas virtuais, utilize discos geridos Azure. A Microsoft recomenda discos geridos para máquinas virtuais SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter informações adicionais, veja [Azure Managed Disks Overview](../../../virtual-machines/linux/managed-disks-overview.md) (Descrição Geral do Managed Disks). Para obter detalhes sobre discos geridos num conjunto de disponibilidade, consulte [Use Managed Disks para VMs num conjunto de disponibilidade](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rede - Endereços IP privados na produção**

   Para as máquinas virtuais, este tutorial utiliza endereços IP públicos. Um endereço IP público permite a ligação remota diretamente à máquina virtual através da internet - facilita os passos de configuração. Em ambientes de produção, a Microsoft recomenda apenas endereços IP privados para reduzir a pegada de vulnerabilidade do recurso VM de instância do SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar os VMs do Servidor SQL
Em seguida, crie três VMs-- dois SQL Server VMs e um VM para um nó de cluster adicional. Para criar cada um dos VMs, volte ao grupo de recursos **SQL-HA-RG,** clique em **Adicionar,** procure o item de galeria apropriado, clique em **Máquina Virtual**e, em seguida, clique em **Partir da Galeria**. Utilize as informações na tabela seguinte para ajudá-lo a criar os VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item de galeria apropriado |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
| Configuração de máquina virtual **Básicos** |**Nome** = cluster-fsw<br/>**Nome de utilizador** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |**Nome** = sqlserver-0<br/>**Nome de utilizador** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |**Nome** = sqlserver-1<br/>**Nome de utilizador** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = A sua subscrição<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Localização** = A sua localização azul |
| **Tamanho** da configuração da máquina virtual |**TAMANHO** = DS1 \_ V2 (1 vCPU, 3,5 GB) |**TAMANHO** = DS2 \_ V2 (2 vCPUs, 7 GB)</br>O tamanho deve suportar o armazenamento SSD (suporte de disco premium. )) |**TAMANHO** = DS2 \_ V2 (2 vCPUs, 7 GB) |
| Configurações de configuração de **máquinas** virtuais |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Diagnósticos de Monitorização** = Habilitados<br/>**Conta de armazenamento de diagnóstico** = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Diagnósticos de Monitorização** = Habilitados<br/>**Conta de armazenamento de diagnóstico** = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Utilize discos geridos.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet (10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Diagnósticos de Monitorização** = Habilitados<br/>**Conta de armazenamento de diagnóstico** = Utilize uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |
| Configuração virtual de configuração **do SQL Server** |Não aplicável |**Conectividade SQL** = Privado (dentro da Rede Virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desativar<br/>**Configuração de armazenamento** = Geral<br/>**Patching automatizado** = Domingo às 2:00<br/>**Backup automatizado** = Desativado</br>**Integração do Cofre de Chaves Azure** = Deficiente |**Conectividade SQL** = Privado (dentro da Rede Virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desativar<br/>**Configuração de armazenamento** = Geral<br/>**Patching automatizado** = Domingo às 2:00<br/>**Backup automatizado** = Desativado</br>**Integração do Cofre de Chaves Azure** = Deficiente |

<br/>

> [!NOTE]
> Os tamanhos da máquina aqui sugeridos destinam-se a testar grupos de disponibilidade em VMs Azure. Para obter o melhor desempenho nas cargas de trabalho de produção, consulte as recomendações para tamanhos de máquinas virtuais SQL Server e configuração nas [melhores práticas de desempenho para SQL Server em máquinas virtuais Azure](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois de os três VMs estarem totalmente a provisionados, é necessário juntar-se a eles no domínio **corp.contoso.com** e conceder à CORP\Instalar direitos administrativos às máquinas.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Junte os servidores ao domínio

Agora pode juntar-se aos VMs para **corp.contoso.com.** Faça os seguintes passos tanto para os VMs do SQL Server como para o servidor de testemunhas de partilha de ficheiros:

1. Ligue-se remotamente à máquina virtual com **BUILTIN\DomainAdmin**.
2. No **Gestor do Servidor,** clique no **Servidor Local**.
3. Clique no link **WORKGROUP.**
4. Na secção Nome do **Computador,** clique em **Alterar**.
5. Selecione a caixa **de verificação de domínio** e escreva **corp.contoso.com** na caixa de texto. Clique em **OK**.
6. No diálogo popup do **Windows Security,** especifique as credenciais para a conta de administrador de domínio predefinido **(CORP\DomainAdmin)** e a palavra-passe **(Contoso!0000).**
7. Quando vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique **em OK**.
8. Clique **em Fechar**e, em seguida, clique em Reiniciar **Agora** no diálogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicione o Corp\Instale o utilizador como administrador em cada VM de cluster

Depois de cada máquina virtual reiniciar como membro do domínio, adicione **CORP\Install** como membro do grupo de administradores locais.

1. Aguarde até que o VM seja reiniciado e, em seguida, volte a lançar o ficheiro RDP a partir do controlador de domínio primário para iniciar **súpton-0** utilizando a conta **CORP\DomainAdmin.**
   >[!TIP]
   >Certifique-se de que faz sê-lo com a conta do administrador de domínio. Nos passos anteriores, estava a utilizar a conta de administrador incorporada. Agora que o servidor está no domínio, use a conta de domínio. Na sessão de PDR, especifique o nome de utilizador *DO DOMÍNIO* \\ *username*.

2. No **Gestor do Servidor**, selecione **Ferramentas**e, em seguida, clique em **Gestão de Computadores**.
3. Na janela **de Gestão de Computadores,** expanda **os Utilizadores e Grupos Locais**e, em seguida, selecione **Grupos**.
4. Clique **duas vezes** no grupo de administradores.
5. No diálogo **Propriedades dos Administradores,** clique no botão **Adicionar.**
6. Introduza o utilizador **CORP\Installe**e clique **em OK**.
7. Clique **em OK** para fechar o diálogo **Dedesemo administradores.**
8. Repita os passos anteriores no **sqlserver-1** e **no cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Definir as contas de serviço do SQL Server

Em cada SQL Server VM, desajuste a conta de serviço SQL Server. Utilize as contas que criou quando configurar as contas de domínio.

1. Abra o **Gestor de Configuração do SQL Server**.
2. Clique com o botão direito no serviço SQL Server e, em seguida, clique em **Propriedades**.
3. Desa cos um pouco de conta e senha.
4. Repita estes passos no outro SQL Server VM.  

Para os grupos de disponibilidade do SQL Server, cada SQL Server VM tem de funcionar como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Crie um sinal de inserção em cada SQL Server VM para a conta de instalação

Utilize a conta de instalação (CORP\install) para configurar o grupo de disponibilidade. Esta conta tem de ser um membro da função de servidor fixo **sysadmin** em cada SQL Server VM. Os seguintes passos criam uma inscrição para a conta de instalação:

1. Conecte-se ao servidor através do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando a conta * \<MachineName\> \DomainAdmin.*

1. Abra o SQL Server Management Studio e ligue-se à instância local do SQL Server.

1. No **Object Explorer,** clique em **Segurança**.

1. Clique no **botão direito Logins**. Clique em **Novo Início de Sessão**.

1. Em **Iniciar sessão - Novo,** clique em **Procurar.**

1. Clique **em Locais**.

1. Introduza as credenciais de rede de administrador de domínio.

1. Utilize a conta de instalação.

1. Descreva o sinal para ser membro da função de servidor fixo **sysadmin.**

1. Clique em **OK**.

Repita os passos anteriores no outro SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicionar funcionalidades de Clustering failover a ambos os VMs do servidor SQL

Para adicionar funcionalidades de Clustering Failover, faça os seguintes passos em ambos os VMs do servidor SQL:

1. Ligue-se à máquina virtual SQL Server através do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando a conta *CORP\install.* Abrir **o painel do gestor do servidor**.
2. Clique nas **funções Add e link de funcionalidades** no painel de instrumentos.

    ![Gestor de servidores - Adicionar funções](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
3. Selecione **Seguinte** até chegar à secção funcionalidades do **servidor.**
4. Em **Funcionalidades**, selecione **Failover Clustering**.
5. Adicione quaisquer funcionalidades adicionais necessárias.
6. Clique **em Instalar** para adicionar as funcionalidades.

Repita os passos no outro SQL Server VM.

  >[!NOTE]
  > Este passo, juntamente com a união dos VMs do SQL Server ao cluster failover, pode agora ser automatizado com modelos [Azure SQL VM CLI](availability-group-az-cli-configure.md) e [Azure Quickstart](availability-group-quickstart-template-configure.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Configure a firewall em cada SQL Server VM

A solução requer que as seguintes portas TCP sejam abertas na firewall:

- **SQL Server VM**:<br/>
   Porta 1433 para uma instância padrão do SQL Server.
- **Sonda equilibrador de carga Azure:**<br/>
   Qualquer porto disponível. Exemplos usam frequentemente o 59999.
- **Ponto final espelhante da base de dados:** <br/>
   Qualquer porto disponível. Exemplos usam frequentemente o 5022.

As portas de firewall precisam de estar abertas em ambos os VMs do SQL Server.

O método de abertura das portas depende da solução de firewall que utiliza. A secção seguinte explica como abrir as portas no Windows Firewall. Abra as portas necessárias em cada um dos seus VMs do seu SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abra uma porta TCP na firewall

1. No primeiro ecrã **de arranque** do SQL Server, lance **o Windows Firewall com Segurança Avançada**.
2. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra.**
3. Para **o Tipo de Regra,** escolha a **Porta.**
4. Para a porta, especifique **o TCP** e escreva os números de porta apropriados. Veja o seguinte exemplo:

   ![Firewall SQL](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Clique em **Seguinte**.
6. Na página **Ação,** mantenha **a ligação** selecionada e, em seguida, clique em **Seguinte**.
7. Na página **'Perfil',** aceite as definições predefinidos e, em seguida, clique em **Seguinte**.
8. Na página **Nome,** especifique um nome de regra (como **sonda Azure LB)** na caixa de texto **Name** e, em seguida, clique em **Terminar**.

Repita estes passos no segundo SQL Server VM.

## <a name="configure-system-account-permissions"></a>Configure permissões de conta do sistema

Para criar uma conta para a conta do sistema e conceder permissões apropriadas, complete os seguintes passos em cada instância do SQL Server:

1. Crie uma conta `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server. O seguinte script cria esta conta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda as seguintes permissões `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   O seguinte script concede estas permissões:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Passos seguintes

* [Crie um sql servidor sempre no grupo de disponibilidade em máquinas virtuais Azure](availability-group-manually-configure-tutorial.md)
