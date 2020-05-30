---
title: Configurar alta disponibilidade para máquinas virtuais Azure Resource Manager Microsoft Docs
description: Este tutorial mostra-lhe como criar um grupo de disponibilidade Always On com Máquinas Virtuais Azure no modo Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.openlocfilehash: d00d8245f14b042b5fa779697ed74edacaf397a6
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219605"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configure sempre em grupos de disponibilidade em Azure Virtual Machines automaticamente: Gestor de recursos

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial mostra-lhe como criar um grupo de disponibilidade de servidor SQL que utiliza máquinas virtuais Azure Resource Manager (VM). O tutorial usa lâminas Azure para configurar um modelo. Pode rever as definições predefinidas, escrever as definições necessárias e atualizar as lâminas no portal à medida que passa por este tutorial.

O tutorial completo cria um grupo de disponibilidade de servidor SQL em Máquinas Virtuais Azure que incluem os seguintes elementos:

* Uma rede virtual que tem várias sub-redes, incluindo uma linha frontal e uma sub-rede de backend
* Dois controladores de domínio que têm um domínio ative directory
* Duas máquinas virtuais que executam o SQL Server e são implantadas na sub-rede de backend e unidas ao domínio do Ative Directory
* Um cluster de failover de três nó com o modelo de quórum da maioria do nó
* Um grupo de disponibilidade que tem duas réplicas sincronizadas de uma base de dados de disponibilidade

A seguinte ilustração representa a solução completa.

![Testar arquitetura de laboratório para grupos de disponibilidade em Azure](./media/availability-group-azure-marketplace-template-configure/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um único grupo de recursos.

Antes de iniciar este tutorial, confirme o seguinte:

* Já tem uma conta Azure. Se não tiver uma, [inscreva-se para uma conta de julgamento.](https://azure.microsoft.com/pricing/free-trial/)
* Já sabe como utilizar o GUI para forneer uma máquina virtual SQL Server a partir da galeria de máquinas virtuais. Para obter mais informações, consulte [a provisionação de uma máquina virtual SQL Server no Azure](create-sql-vm-portal.md).
* Já tem uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, consulte [sempre os grupos de disponibilidade (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Se estiver interessado em utilizar grupos de disponibilidade com o SharePoint, consulte também o [Configure SQL Server 2012 Always On availability groups for SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>

Neste tutorial, utilize o portal Azure para:

* Escolha o modelo Always On a partir do portal.
* Reveja as definições do modelo e atualize algumas definições de configuração para o seu ambiente.
* Monitorar Azure à medida que cria todo o ambiente.
* Ligue-se a um controlador de domínio e, em seguida, a um servidor que executa o SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Provisionamento do cluster da galeria

A azure fornece uma imagem de galeria para toda a solução. Para localizar o modelo:

1. Inscreva-se no portal Azure utilizando a sua conta.
2. No portal Azure, selecione **Criar um recurso** para abrir o painel **Novo.**
3. No **painel New,** procure **pela AlwaysOn.**
  O Azure Marketplace mostra os resultados da pesquisa que incluem o cluster SQL Server. Esta funcionalidade está em pré-visualização.
4. Nos resultados da pesquisa, localize e clique no **SqL Server AlwaysOn Cluster (pré-visualização)**. Siga as instruções para criar a solução.

As seguintes secções explicam a entrada para a solução. 

### <a name="basics"></a>Noções básicas

Selecione **Basics** e configufique as seguintes definições:

* **O nome de utilizador do administrador** é uma conta de utilizador que tem permissões de administrador de domínio e é membro da função de servidor fixo sql Server em ambas as instâncias do SQL Server Server. Para este tutorial, utilize **o DomainAdmin**.
* **A palavra-passe** é a palavra-passe para a conta do administrador de domínio. Use uma senha complexa. Confirme a palavra-passe.
* **A subscrição** é a subscrição que a Azure fatura para executar todos os recursos implantados para o grupo de disponibilidade. Se a sua conta tiver várias subscrições, pode especificar uma subscrição diferente.
* **Grupo de recursos** é o nome do grupo ao qual todos os recursos Azure que são criados por este modelo pertencem. Para este tutorial, utilize **SQL-HA-RG**. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Localização** é a região de Azure onde o tutorial cria os recursos. Escolha uma região de Azure.

A imagem a seguir é uma lâmina **de base** completa:

![Noções básicas](./media/availability-group-azure-marketplace-template-configure/1-basics.png)

Selecione **OK**.

### <a name="domain-and-network-settings"></a>Definições de domínio e rede

Este modelo de galeria Azure cria um domínio e controladores de domínio. Também cria uma rede e duas sub-redes. O modelo não pode criar servidores num domínio ou rede virtual existente. O passo seguinte configura as definições de domínio e rede.

Na lâmina de **definição de domínio e de rede,** reveja os valores predefinidos para as definições de domínio e rede:

* **O nome de domínio da raiz da floresta** é o nome de domínio do domínio ative directory que acolhe o cluster. Para o tutorial, utilize **contoso.com**.
* **Nome de Rede Virtual** é o nome de rede para a rede virtual Azure. Para o tutorial, utilize **autohaVNET**.
* **Nome da sub-rede do controlador** de domínio é o nome de uma parte da rede virtual que acolhe o controlador de domínio. Utilize **a sub-rede 1**. Esta sub-rede utiliza o prefixo do endereço **10.0.0.0/24**.
* **Nome da sub-rede SQL Server** é o nome de uma parte da rede virtual que hospeda os servidores que executam o SQL Server e a testemunha de partilha de ficheiros. Utilize **a sub-rede 2**. Esta sub-rede utiliza o prefixo do endereço **10.0.1.0/26**.

Para saber mais sobre redes virtuais no Azure, consulte [a visão geral da rede Virtual.](../../../virtual-network/virtual-networks-overview.md)  

As **definições de Domínio e rede** devem parecer a seguinte imagem:

![Definições de domínio e rede](./media/availability-group-azure-marketplace-template-configure/2-domain.png)

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.

Reveja as definições e, em seguida, selecione **OK**.

### <a name="availability-group-settings"></a>Definições de grupo de disponibilidade

Nas **definições do grupo Disponibilidade,** reveja os valores predefinidos para o grupo de disponibilidade e para o ouvinte.

* **O nome do grupo disponibilidade** é o nome de recurso agrupado para o grupo de disponibilidade. Para este tutorial, use **Contoso-ag.**
* **O nome do ouvinte do grupo disponibilidade** é utilizado pelo cluster e pelo equilibrador de carga interno. Os clientes que se ligarem ao SQL Server podem usar este nome para se conectarem à réplica apropriada da base de dados. Para este tutorial, use **Contoso-ouvinte**.
* **A porta de ouvinte do grupo de** disponibilidade especifica a porta TCP do ouvinte do SQL Server. Para este tutorial, utilize a porta padrão, **1433**.

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.  

![definições de grupo de disponibilidade](./media/availability-group-azure-marketplace-template-configure/3-availabilitygroup.png)

Selecione **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, configurações de armazenamento

No **tamanho VM, definições de armazenamento,** escolha o tamanho da máquina virtual SQL Server e reveja as outras definições.

* **O tamanho da máquina virtual SQL Server** é o tamanho de ambas as máquinas virtuais que executam o SQL Server. Escolha um tamanho de máquina virtual apropriado para a sua carga de trabalho. 
   * Se estiver a construir este ambiente para o tutorial, use **o DS2**.
   * Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que possa suportar a carga de trabalho.
   * Muitas cargas de trabalho de produção requerem **DS4** ou maior. O modelo constrói duas máquinas virtuais deste tamanho e instala o SQL Server em cada uma delas.
   * Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../../../virtual-machines/windows/sizes.md)

   > [!NOTE]
   > O Azure instala a Enterprise Edition do SQL Server. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os [custos correntes, consulte os preços das máquinas virtuais.](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)
   >

* **O tamanho da máquina virtual do controlador de domínio** é o tamanho da máquina virtual para os controladores de domínio. Para esta utilização tutorial **D2**.
* **O tamanho da máquina virtual de Partilha de Ficheiros** é o tamanho da máquina virtual para a testemunha de partilha de ficheiros. Para este tutorial, utilize **a A1**.
* **A conta de armazenamento SQL** é o nome da conta de armazenamento que detém os dados do SQL Server e os discos do sistema operativo. Para este tutorial, use **sempre oonsql01**.
* **Conta de armazenamento DC** é o nome da conta de armazenamento para os controladores de domínio. Para este tutorial, use **alwaysondc01**.
* **O tamanho do disco de dados do SQL Server** em TB é o tamanho do disco de dados do SQL Server em TB. Especifique um número de 1 a 4. Para este tutorial, use **1**.
* **A otimização do** armazenamento define definições específicas de configuração de armazenamento para as máquinas virtuais SQL Server com base no tipo de carga de trabalho. Todas as máquinas virtuais sql Server neste cenário utilizam armazenamento premium com cache de anfitrião de disco Azure definido apenas para leitura. Além disso, pode otimizar as definições do SQL Server para a carga de trabalho, escolhendo uma destas três definições:

  * **A carga de trabalho geral** não define configurações específicas.
  * **O processamento transacional** define o traço da bandeira 1117 e 1118.
  * **O armazenamento de dados** define a bandeira 1117 e 610.

Para este tutorial, utilize **carga de trabalho geral.**

![Configurações de armazenamento de tamanho VM](./media/availability-group-azure-marketplace-template-configure/4-vm.png)

Reveja as definições e, em seguida, selecione **OK**.

#### <a name="a-note-about-storage"></a>Uma nota sobre armazenamento

As otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, a Azure adiciona um armazenamento adicional de 1 TB premium. Quando um servidor requer 2 TB ou mais, o modelo cria uma piscina de armazenamento em cada máquina virtual sql Server. Uma piscina de armazenamento é uma forma de virtualização de armazenamento onde vários discos são configurados para fornecer maior capacidade, resiliência e desempenho.  Em seguida, o modelo cria um espaço de armazenamento na piscina de armazenamento e apresenta um único disco de dados para o sistema operativo. O modelo designa este disco como o disco de dados para o SQL Server. O modelo afina a piscina de armazenamento para o SQL Server utilizando as seguintes definições:

* O tamanho da risca é a definição interleave para o disco virtual. Cargas de trabalho transacionais utilizam 64 KB. As cargas de trabalho de armazenamento de dados utilizam 256 KB.
* A resiliência é simples (sem resiliência).

   > [!NOTE]
   > O armazenamento azure premium é localmente redundante e mantém três cópias dos dados numa única região, pelo que não é necessária resiliência adicional no armazém.
   >

* A contagem de colunas é igual ao número de discos na piscina de armazenamento.

Para obter informações adicionais sobre o espaço de armazenamento e as piscinas de armazenamento, consulte:

* [Descrição Geral dos Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx)
* [Cópia de Segurança do Windows Server e Agrupamentos de Armazenamento](https://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre as melhores práticas de configuração do SQL Server, consulte [as melhores práticas de desempenho para SQL Server em máquinas virtuais Azure](performance-guidelines-best-practices.md).

### <a name="sql-server-settings"></a>Definições do SQL Server

Nas **definições do SQL Server**, reveja e modifique o prefixo do nome da máquina virtual SQL Server, a versão SQL Server, a conta de serviço do SQL Server e a palavra-passe do servidor SQL e o calendário de manutenção de remendação automática SQL.

* **O prefixo de nome do servidor SQL** é utilizado para criar um nome para cada máquina virtual do SQL Server. Para este tutorial, use **sqlserver.** O modelo dá nomes às máquinas virtuais SQL Server *sqlserver-0* e *sqlserver-1*.
* **A versão SQL Server** é a versão do SQL Server. Para este tutorial use **SQL Server 2014**. Também pode escolher **SQL Server 2012** ou **SQL Server 2016**.
* **O nome de utilizador da conta de serviço SQL Server** é o nome da conta de domínio do serviço SQL Server. Para este tutorial, utilize **sqlservice.**
* **A palavra-passe** é a palavra-passe da conta de serviço do SQL Server. Use uma senha complexa. Confirme a palavra-passe.
* **O calendário de manutenção do M2L Auto Patching** identifica o dia da semana que o Azure corrige automaticamente os Servidores SQL. Para este tutorial, escreva **domingo.**
* **SQL Auto Patching manutenção hora de início** é a hora do dia para a região de Azure quando o remendos automáticos começa.

> [!NOTE]
> A janela de remendação de cada máquina virtual é escalonada por uma hora. Apenas uma máquina virtual é remendada de cada vez para evitar perturbações nos serviços.
>

![Definições do SQL Server](./media/availability-group-azure-marketplace-template-configure/5-sql.png)

Reveja as definições e, em seguida, selecione **OK**.

### <a name="summary"></a>Resumo

Na página do resumo, o Azure valida as definições. Também pode descarregar o modelo. Reveja o resumo. Selecione **OK**.

### <a name="buy"></a>Comprar

Esta lâmina final contém **termos de utilização**e política de **privacidade.** Reveja esta informação. Quando estiver pronto para o Azure começar a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, selecione **Criar**.

O portal Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorizar a implementação

Monitorize o progresso da implantação a partir do portal Azure. Um ícone que representa a implementação é automaticamente fixado no painel do portal Azure.

![Painel Azure](./media/availability-group-azure-marketplace-template-configure/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

As novas instâncias do SQL Server estão a ser funcionadas em máquinas virtuais que têm endereços IP ligados à Internet. Pode ligar-se diretamente a cada máquina virtual sql Server com um ambiente de trabalho remoto (RDP).

Para utilizar o RDP para ligar a um SQL Server VM, siga estes passos:

1. A partir do painel do portal Azure, verifique se a implementação foi bem sucedida.
2. Selecione **Recursos**.
3. Na lâmina **Recursos,** selecione **sqlserver-0,** que é o nome de computador de uma das máquinas virtuais que está a executar o SQL Server.
4. Na lâmina para **sqlserver-0**, selecione **Connect**. O seu navegador pergunta se pretende abrir ou guardar o objeto de ligação remota. Selecione **Open** (Abrir).
5. **A ligação remota para o ambiente** de trabalho pode avisá-lo de que o editor desta ligação remota não pode ser identificado. Selecione **Ligar**.
6. A segurança do Windows solicita-lhe que introduza as suas credenciais para ligar ao endereço IP do controlador de domínio primário. **Selecione Utilize outra conta**. Para **o nome do utilizador,** **escreva contoso\DomainAdmin**. Configuraste esta conta quando definiste o nome de utilizador do administrador no modelo. Utilize a palavra-passe complexa que escolheu quando configurar o modelo.
7. **O ambiente de trabalho remoto** poderá avisá-lo de que o computador remoto não pode ser autenticado devido a problemas com o seu certificado de segurança. Mostra o nome do certificado de segurança. Se seguiu o tutorial, o nome é **sqlserver-0.contoso.com.** Selecione **Sim**.

Está agora ligado ao RDP à máquina virtual SQL Server. Pode abrir o SQL Server Management Studio, ligar-se à instância padrão do SQL Server e verificar se o grupo de disponibilidade está configurado.
