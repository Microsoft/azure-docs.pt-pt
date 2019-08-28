---
title: Configurar a alta disponibilidade para VMs Azure Resource Manager | Microsoft Docs
description: Este tutorial mostra como criar um grupo de disponibilidade Always On com máquinas virtuais do Azure no modo Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a06ea59af0776fe3decb0b56a3ef886f08b2dfda
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100718"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configurar grupos de disponibilidade Always On em máquinas virtuais do Azure automaticamente: Resource Manager

Este tutorial mostra como criar um grupo de disponibilidade SQL Server que usa Azure Resource Manager máquinas virtuais. O tutorial usa as folhas do Azure para configurar um modelo. Você pode examinar as configurações padrão, digitar as configurações necessárias e atualizar as folhas no portal conforme percorre este tutorial.

O tutorial completo cria um grupo de disponibilidade SQL Server em máquinas virtuais do Azure que incluem os seguintes elementos:

* Uma rede virtual que tem várias sub-redes, incluindo um frontend e uma sub-rede de back-end
* Dois controladores de domínio que têm um domínio Active Directory
* Duas máquinas virtuais que executam SQL Server e são implantadas na sub-rede de back-end e ingressadas no domínio de Active Directory
* Um cluster de failover de três nós com o modelo de quorum maioria de nós
* Um grupo de disponibilidade que tem duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A ilustração a seguir representa a solução completa.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um único grupo de recursos.

Antes de iniciar este tutorial, confirme o seguinte:

* Você já tem uma conta do Azure. Se você não tiver uma, [Inscreva-se para uma conta de avaliação](https://azure.microsoft.com/pricing/free-trial/).
* Você já sabe como usar a GUI para provisionar uma máquina virtual SQL Server da Galeria de máquinas virtuais. Para obter mais informações, consulte Provisionando [uma máquina virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Você já tem uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, consulte [grupos de disponibilidade Always on (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Se você estiver interessado em usar grupos de disponibilidade com o SharePoint, consulte também [configurar SQL Server 2012 grupos de disponibilidade Always on para SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

Neste tutorial, use o portal do Azure para:

* Escolha o modelo de Always On no Portal.
* Examine as configurações do modelo e atualize algumas definições de configuração para o seu ambiente.
* Monitore o Azure à medida que cria todo o ambiente.
* Conecte-se a um controlador de domínio e, em seguida, a um servidor que executa o SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Provisionar o cluster da Galeria
O Azure fornece uma imagem da galeria para toda a solução. Para localizar o modelo:

1. Entre no portal do Azure usando sua conta.
2. No portal do Azure, clique em **criar um recurso** para abrir o **novo** painel.
3. No painel **novo** , procure **AlwaysOn**.
   ![Localizar modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize **SQL Server Cluster AlwaysOn**.
   ![Modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Em **selecionar um modelo de implantação**, escolha **Gerenciador de recursos**.

### <a name="basics"></a>Noções Básicas
Clique em **noções básicas** e defina as seguintes configurações:

* O **nome de usuário administrador** é uma conta de usuário que tem permissões de administrador de domínio e é membro da função de servidor fixa sysadmin SQL Server em ambas as instâncias do SQL Server. Para este tutorial, use **DomainAdmin**.
* **Senha** é a senha para a conta de administrador de domínio. Use uma senha complexa. Confirme a palavra-passe.
* **Assinatura** é a assinatura que o Azure cobra para executar todos os recursos implantados para o grupo de disponibilidade. Se sua conta tiver várias assinaturas, você poderá especificar uma assinatura diferente.
* **Grupo de recursos** é o nome do grupo ao qual todos os recursos do Azure criados por este modelo pertencem. Para este tutorial, use **SQL-ha-RG**. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Local** é a região do Azure em que o tutorial cria os recursos. Escolha uma região do Azure.

A captura de tela a seguir é uma folha **básica** completa:

![Noções Básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Clique em **OK**.

### <a name="domain-and-network-settings"></a>Configurações de rede e de domínio
Este modelo da galeria do Azure cria um domínio e controladores de domínio. Ele também cria uma rede e duas sub-redes. O modelo não pode criar servidores em um domínio ou rede virtual existente. A próxima etapa define as configurações de domínio e rede.

Na folha **configurações de domínio e rede** , examine os valores predefinidos para as configurações de rede e de domínio:

* **Nome de domínio raiz da floresta** é o nome de domínio para o Active Directory domínio que hospeda o cluster. Para o tutorial, use **contoso.com**.
* **Nome da rede virtual** é o nome da rede para a rede virtual do Azure. Para o tutorial, use **autohaVNET**.
* **Nome da sub-rede do controlador de domínio** é o nome de uma parte da rede virtual que hospeda o controlador de domínio. Use **Subnet-1**. Essa sub-rede usa o prefixo de endereço **10.0.0.0/24**.
* **SQL Server nome da sub-rede** é o nome de uma parte da rede virtual que hospeda os servidores que executam SQL Server e a testemunha de compartilhamento de arquivos. Use **Subnet-2**. Essa sub-rede usa o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre redes virtuais no Azure, confira [visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md).  

As **configurações de domínio e rede** devem ser semelhantes à captura de tela a seguir:

![Configurações de rede e de domínio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, você pode alterar esses valores. Para este tutorial, use os valores predefinidos.

Examine as configurações e clique em **OK**.

### <a name="availability-group-settings"></a>Configurações do grupo de disponibilidade
Em **configurações do grupo de disponibilidade**, examine os valores predefinidos para o grupo de disponibilidade e o ouvinte.

* **Nome do grupo de disponibilidade** é o nome do recurso clusterizado para o grupo de disponibilidade. Para este tutorial, use **contoso-AG**.
* O nome do ouvinte do **grupo de disponibilidade** é usado pelo cluster e pelo balanceador de carga interno. Clientes que se conectam ao SQL Server podem usar esse nome para se conectar à réplica apropriada do banco de dados. Para este tutorial, use **contoso-Listener**.
* **Porta** do ouvinte do grupo de disponibilidade especifica a porta TCP do ouvinte de SQL Server. Para este tutorial, use a porta padrão, **1433**.

Se necessário, você pode alterar esses valores. Para este tutorial, use os valores predefinidos.  

![configurações do grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Clique em **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, configurações de armazenamento
Em **tamanho da VM, configurações de armazenamento**, escolha um SQL Server tamanho da máquina virtual e examine as outras configurações.

* **SQL Server tamanho da máquina virtual** é o tamanho de ambas as máquinas virtuais que executam SQL Server. Escolha um tamanho de máquina virtual apropriado para sua carga de trabalho. Se você estiver criando este ambiente para o tutorial, use **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que possa dar suporte à carga de trabalho. Muitas cargas de trabalho de produção exigem **DS4** ou mais. O modelo cria duas máquinas virtuais desse tamanho e instala SQL Server em cada uma delas. Para obter mais informações, consulte [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> O Azure instala a Enterprise Edition do SQL Server. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os custos atuais, consulte [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Tamanho da máquina virtual do controlador de domínio** é o tamanho da máquina virtual para os controladores de domínio. Para este tutorial, use **D2**.
* **Tamanho da máquina virtual de testemunha de compartilhamento de arquivos** é o tamanho da máquina virtual para a testemunha de compartilhamento de arquivos. Para este tutorial, use **a1**.
* **Conta de armazenamento do SQL** é o nome da conta de armazenamento que contém os dados de SQL Server e os discos do sistema operacional. Para este tutorial, use **alwaysonsql01**.
* **Conta de armazenamento de DC** é o nome da conta de armazenamento para os controladores de domínio. Para este tutorial, use **alwaysondc01**.
* **SQL Server tamanho do disco de dados** em TB é o tamanho do disco de dados de SQL Server em TB. Especifique um número de 1 a 4. Para este tutorial, use **1**.
* A **otimização de armazenamento** define definições de configuração de armazenamento específicas para as SQL Server máquinas virtuais com base no tipo de carga de trabalho. Todas as máquinas virtuais SQL Server nesse cenário usam o armazenamento Premium com o cache do host de disco do Azure definido como somente leitura. Além disso, você pode otimizar SQL Server configurações para a carga de trabalho escolhendo uma dessas três configurações:

  * A **carga de trabalho geral** não define definições de configuração específicas.
  * O **processamento** transacional define o sinalizador de rastreamento 1117 e 1118.
  * O **data warehouse** define o sinalizador de rastreamento 1117 e 610.

Para este tutorial, use a **carga de trabalho geral**.

![Configurações de armazenamento de tamanho da VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Examine as configurações e clique em **OK**.

#### <a name="a-note-about-storage"></a>Uma observação sobre armazenamento
Otimizações adicionais dependem do tamanho do SQL Server discos de dados. Para cada terabyte de disco de dados, o Azure adiciona um armazenamento Premium adicional de 1 TB. Quando um servidor requer 2 TB ou mais, o modelo cria um pool de armazenamento em cada máquina virtual SQL Server. Um pool de armazenamento é uma forma de virtualização de armazenamento em que vários discos são configurados para fornecer maior capacidade, resiliência e desempenho.  Em seguida, o modelo cria um espaço de armazenamento no pool de armazenamento e apresenta um único disco de dados para o sistema operacional. O modelo designa esse disco como o disco de dados para SQL Server. O modelo ajusta o pool de armazenamento para SQL Server usando as seguintes configurações:

* Tamanho da distribuição é a configuração de intercalação para o disco virtual. As cargas de trabalho transacionais usam 64 KB. As cargas de trabalho de data warehousing usam 256 KB.
* A resiliência é simples (sem resiliência).

> [!NOTE]
> O armazenamento Premium do Azure é localmente redundante e mantém três cópias dos dados em uma única região, portanto, a resiliência adicional no pool de armazenamento não é necessária.
>
>

* A contagem de colunas é igual ao número de discos no pool de armazenamento.

Para obter informações adicionais sobre o espaço de armazenamento e pools de armazenamento, consulte:

* [Visão geral dos espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx)
* [Backup do Windows Server e pools de armazenamento](https://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre as práticas recomendadas de configuração do SQL Server, consulte [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Definições de SQL Server
Em **SQL Server configurações**, examine e modifique o prefixo do nome da máquina virtual SQL Server, SQL Server versão, SQL Server conta de serviço e senha e a agenda de manutenção de aplicação de patch automática do SQL.

* **SQL Server prefixo de nome** é usado para criar um nome para cada máquina virtual SQL Server. Para este tutorial, use o **SqlServer**. O modelo nomeia o SQL Server máquinas virtuais *SqlServer-0* e *SqlServer-1*.
* **SQL Server versão** é a versão do SQL Server. Para este tutorial, use **SQL Server 2014**. Você também pode escolher **SQL Server 2012** ou **SQL Server 2016**.
* **SQL Server nome de usuário da conta de serviço** é o nome da conta de domínio para o serviço de SQL Server. Para este tutorial, use **SqlService**.
* **Senha** é a senha para a conta de serviço do SQL Server.  Use uma senha complexa. Confirme a palavra-passe.
* O **agendamento de manutenção de aplicação de patch automática do SQL** identifica o dia da semana em que o Azure corrige automaticamente os SQL Servers. Para este tutorial, digite **domingo**.
* A **hora de início da manutenção da aplicação de patch automática do SQL** é a hora do dia da região do Azure quando o patch automático começa.

> [!NOTE]
> A janela de aplicação de patch para cada máquina virtual é escalonada em uma hora. Apenas uma máquina virtual é corrigida por vez para evitar a interrupção dos serviços.
>
>

![Definições de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Examine as configurações e clique em **OK**.

### <a name="summary"></a>Resumo
Na página Resumo, o Azure valida as configurações. Você também pode baixar o modelo. Reveja o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Essa folha final contém os **termos de uso**e a **política de privacidade**. Examine essas informações. Quando estiver pronto para o Azure começar a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, clique em **criar**.

O portal do Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorar a implantação
Monitore o progresso da implantação no portal do Azure. Um ícone que representa a implantação é fixado automaticamente no painel portal do Azure.

![Painel do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server
As novas instâncias do SQL Server estão em execução em máquinas virtuais que têm endereços IP conectados à Internet. Você pode fazer a área de trabalho remota (RDP) diretamente para cada máquina virtual SQL Server.

Para o RDP para um SQL Server, siga estas etapas:

1. No painel portal do Azure, verifique se a implantação foi bem-sucedida.
2. Clique em **recursos**.
3. Na folha **recursos** , clique em **SqlServer-0**, que é o nome do computador de uma das máquinas virtuais em execução SQL Server.
4. Na folha do **SqlServer-0**, clique em **conectar**. Seu navegador pergunta se você deseja abrir ou salvar o objeto de conexão remota. Clique em **aberto**.
5. A **conexão de área de trabalho remota** pode avisá-lo que o editor dessa conexão remota não pode ser identificado. Clique em **Ligar**.
6. A segurança do Windows solicita que você insira suas credenciais para se conectar ao endereço IP do controlador de domínio primário. Clique em **usar outra conta**. Para **nome de usuário**, digite **contoso\DomainAdmin**. Você configurou essa conta ao definir o nome de usuário do administrador no modelo. Use a senha complexa que você escolheu ao configurar o modelo.
7. A **área de trabalho remota** pode avisá-lo que o computador remoto não pôde ser autenticado devido a problemas com seu certificado de segurança. Ele mostra o nome do certificado de segurança. Se você seguiu o tutorial, o nome é **SqlServer-0.contoso.com**. Clique em **Sim**.

Agora você está conectado ao RDP para a máquina virtual SQL Server. Você pode abrir SQL Server Management Studio, conectar-se à instância padrão do SQL Server e verificar se o grupo de disponibilidade está configurado.
