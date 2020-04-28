---
title: Configurar alta disponibilidade para VMs do Gestor de Recursos Azure [ Microsoft Docs
description: Este tutorial mostra-lhe como criar um grupo sempre disponível com máquinas virtuais Azure no modo Azure Resource Manager.
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
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75374265"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configure sempre em grupos de disponibilidade em Máquinas Virtuais Azure automaticamente: Gestor de Recursos

Este tutorial mostra-lhe como criar um grupo de disponibilidade do SQL Server que utiliza máquinas virtuais do Gestor de Recursos Azure. O tutorial usa lâminas Azure para configurar um modelo. Pode rever as definições predefinidas, escrever as definições necessárias e atualizar as lâminas no portal à medida que passa por este tutorial.

O tutorial completo cria um grupo de disponibilidade do SQL Server em Máquinas Virtuais Azure que incluem os seguintes elementos:

* Uma rede virtual que tem várias subredes, incluindo uma frontend e uma subnet backend
* Dois controladores de domínio que têm um domínio de Diretório Ativo
* Duas máquinas virtuais que executam o Servidor SQL e são implantadas na subnet de backend e unidas ao domínio do Diretório Ativo
* Um cluster de três nós failover com o modelo quórum da maioria do nó
* Um grupo de disponibilidade que tem duas réplicas sincronizadas de uma base de dados de disponibilidade

A ilustração que se segue representa a solução completa.

![Testar arquitetura de laboratório para grupos de disponibilidade em Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos desta solução pertencem a um único grupo de recursos.

Antes de iniciar este tutorial, confirme o seguinte:

* Já tem uma conta Azure. Se não tiver uma, [inscreva-se numa conta](https://azure.microsoft.com/pricing/free-trial/)de julgamento.
* Já sabe como usar o GUI para fornecer uma máquina virtual SQL Server a partir da galeria de máquinas virtuais. Para mais informações, consulte [o provisionamento de uma máquina virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Você já tem uma compreensão sólida de grupos de disponibilidade. Para mais informações, consulte [Sempre em Grupos de Disponibilidade (Servidor SQL)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Se estiver interessado em utilizar grupos de disponibilidade com o SharePoint, consulte também o [Configure SQL Server 2012 Always On grupos de disponibilidade para o SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

Neste tutorial, utilize o portal Azure para:

* Escolha o modelo Sempre Em Vez do portal.
* Reveja as definições do modelo e atualize algumas definições de configuração para o seu ambiente.
* Monitore o Azure à medida que cria todo o ambiente.
* Ligue-se a um controlador de domínio e, em seguida, a um servidor que executa o Servidor SQL.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Fornecer o cluster da galeria
O Azure fornece uma imagem de galeria para toda a solução. Para localizar o modelo:

1. Inscreva-se no portal Azure utilizando a sua conta.
2. No portal Azure, clique em **Criar um recurso** para abrir o **novo** painel.
3. No **painel Novo,** procure **alwayson**.
   ![Encontrar modelo alwayson](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize o **Cluster AlwaysOn do Servidor SQL**.
   ![Modelo alwayson](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Em Selecionar um modelo de **implementação,** escolha **Gestor de Recursos**.

### <a name="basics"></a>Noções básicas
Clique em Básicos e configure as **seguintes** definições:

* **O nome** de utilizador do administrador é uma conta de utilizador que tem permissões de administrador de domínio e é membro da função de servidor fixo sysadmin a SQL Server em ambas as instâncias do Servidor SQL. Para este tutorial, utilize **o DomainAdmin**.
* **A palavra-passe** é a palavra-passe da conta do administrador de domínio. Use uma senha complexa. Confirme a palavra-passe.
* **A subscrição** é a subscrição que o Azure fatura para executar todos os recursos implantados para o grupo de disponibilidade. Se a sua conta tiver várias subscrições, pode especificar uma subscrição diferente.
* **O grupo de recursos** é o nome para o grupo a que pertencem todos os recursos Azure que são criados por este modelo. Para este tutorial, utilize **O SQL-HA-RG**. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **A localização** é a região de Azure onde o tutorial cria os recursos. Escolha uma região azure.

A seguinte imagem é uma lâmina **Basics** completada:

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Clique em **OK**.

### <a name="domain-and-network-settings"></a>Definições de domínio e rede
Este modelo de galeria Azure cria um domínio e controladores de domínio. Também cria uma rede e duas subnets. O modelo não pode criar servidores num domínio ou rede virtual existentes. O próximo passo configura as definições de domínio e rede.

Na lâmina de definições de **Domínio e rede,** reveja os valores predefinidos para as definições de domínio e rede:

* **O nome** do domínio da raiz da floresta é o nome de domínio para o domínio do Diretório Ativo que acolhe o cluster. Para o tutorial, use **contoso.com**.
* **O nome da Rede Virtual** é o nome da rede para a rede virtual Azure. Para o tutorial, utilize **autohaVNET**.
* O nome da **sub-rede do Controlador** de Domínio é o nome de uma parte da rede virtual que acolhe o controlador de domínio. Utilize **a sub-rede 1**. Esta sub-rede utiliza o prefixo de endereço **10.0.0.0.0/24**.
* O nome da **sub-rede SQL Server** é o nome de uma parte da rede virtual que acolhe os servidores que executam o Servidor SQL e a testemunha de partilha de ficheiros. Utilize **a sub-rede-2**. Esta sub-rede utiliza a prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre redes virtuais em Azure, consulte a [visão geral da rede Virtual.](../../../virtual-network/virtual-networks-overview.md)  

As **definições de Domínio e rede** devem parecer a seguinte imagem:

![Definições de domínio e rede](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.

Reveja as definições e, em seguida, clique **EM OK**.

### <a name="availability-group-settings"></a>Definições de grupo de disponibilidade
Nas **definições do grupo Disponibilidade,** reveja os valores predefinidos para o grupo de disponibilidade e para o ouvinte.

* **O nome** do grupo de disponibilidade é o nome de recursos agrupados para o grupo de disponibilidade. Para este tutorial, use **Contoso-ag.**
* **O nome** do ouvinte do grupo de disponibilidade é utilizado pelo cluster e pelo equilibrador interno de carga. Os clientes que se ligam ao SQL Server podem usar este nome para se ligarem à réplica apropriada da base de dados. Para este tutorial, utilize **Contoso-ouvinte.**
* **A porta** de escuta do grupo de disponibilidade especifica a porta TCP do ouvinte Do Servidor SQL. Para este tutorial, utilize a porta predefinida, **1433**.

Se necessário, pode alterar estes valores. Para este tutorial, utilize os valores predefinidos.  

![definições de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Clique em **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, configurações de armazenamento
No **tamanho vM, configurações**de armazenamento, escolha um tamanho de máquina virtual SQL Server e reveja as outras definições.

* O tamanho da **máquina virtual do SQL Server** é o tamanho de ambas as máquinas virtuais que executam o Servidor SQL. Escolha um tamanho de máquina virtual apropriado para a sua carga de trabalho. Se estiver a construir este ambiente para o tutorial, utilize **dS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que possa suportar a carga de trabalho. Muitas cargas de trabalho de produção requerem **DS4** ou maior. O modelo constrói duas máquinas virtuais deste tamanho e instala o SQL Server em cada uma delas. Para mais informações, consulte [Tamanhos para máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> O Azure instala a Edição Empresarial do Servidor SQL. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os custos atuais, consulte [os preços das máquinas virtuais.](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)
>
>

* **O tamanho** da máquina virtual do controlador de domínio é o tamanho da máquina virtual para os controladores de domínio. Para este tutorial use **D2**.
* **File Share O tamanho da máquina virtual da testemunha** é o tamanho virtual da máquina para a testemunha de partilha de ficheiros. Para este tutorial, utilize **a A1**.
* **A conta de armazenamento SQL** é o nome da conta de armazenamento que detém os dados do Servidor SQL e os discos do sistema operativo. Para este tutorial, use **sempreonsql01**.
* **Dc Conta de armazenamento** é o nome da conta de armazenamento para os controladores de domínio. Para este tutorial, use **sempreondc01**.
* O tamanho do disco de dados do **SQL Server** em TB é o tamanho do disco de dados do Servidor SQL em TB. Especifique um número de 1 a 4. Para este tutorial, utilize **1**.
* **A otimização do armazenamento** define definições específicas de configuração de armazenamento para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todas as máquinas virtuais do SQL Server neste cenário utilizam armazenamento premium com cache de hospedagem de disco Azure definido para apenas leitura. Além disso, pode otimizar as definições do Servidor SQL para a carga de trabalho, escolhendo uma destas três definições:

  * **A carga de trabalho geral** não define definições específicas de configuração.
  * **O processamento transacional** define a bandeira 1117 e 1118.
  * **O armazenamento** de dados define o trace flag 1117 e o 610.

Para este tutorial, utilize **a carga de trabalho geral.**

![Definições de armazenamento de tamanho VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Reveja as definições e, em seguida, clique **EM OK**.

#### <a name="a-note-about-storage"></a>Uma nota sobre armazenamento
Otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, o Azure adiciona um armazenamento adicional de 1 TB premium. Quando um servidor requer 2 TB ou mais, o modelo cria uma piscina de armazenamento em cada máquina virtual do SQL Server. Um pool de armazenamento é uma forma de virtualização de armazenamento onde vários discos são configurados para fornecer maior capacidade, resiliência e desempenho.  O modelo cria então um espaço de armazenamento no pool de armazenamento e apresenta um único disco de dados para o sistema operativo. O modelo designa este disco como o disco de dados para o Servidor SQL. O modelo ajusta a piscina de armazenamento para o Servidor SQL utilizando as seguintes definições:

* O tamanho da risca é a regulação do interleave para o disco virtual. As cargas de trabalho transacionais utilizam 64 KB. As cargas de trabalho de armazenamento de dados utilizam 256 KB.
* A resiliência é simples (sem resiliência).

> [!NOTE]
> O armazenamento premium Azure é localmente redundante e guarda três cópias dos dados numa única região, pelo que não é necessária uma resiliência adicional no armazém.
>
>

* A contagem de colunas é igual ao número de discos no depósito.

Para obter informações adicionais sobre espaço de armazenamento e piscinas de armazenamento, consulte:

* [Descrição Geral dos Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx)
* [Cópia de Segurança do Windows Server e Agrupamentos de Armazenamento](https://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre as melhores práticas de configuração do Servidor SQL, consulte [as melhores práticas de Desempenho para o SQL Server em máquinas virtuais Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Definições do SQL Server
Nas **definições do SQL Server,** reveja e modifique o prefixo de nome de máquina virtual do SQL Server, a versão SQL Server, a conta de serviço do Servidor SQL e a palavra-passe e o calendário de manutenção de correção automática SQL.

* O Prefixo de Nome do **Servidor SQL** é usado para criar um nome para cada máquina virtual do Servidor SQL. Para este tutorial, utilize **o sqlserver**. O modelo designa as máquinas virtuais SQL Server *sqlserver-0* e *sqlserver-1*.
* **A versão SQL Server** é a versão do SQL Server. Para este tutorial use **SQL Server 2014**. Também pode escolher **o SQL Server 2012** ou **o SQL Server 2016**.
* O nome do utilizador da conta de **serviço do SQL Server** é o nome da conta de domínio para o serviço SQL Server. Para este tutorial, utilize **o serviço de sqlservice**.
* **A palavra-passe** é a palavra-passe da conta de serviço do SQL Server.  Use uma senha complexa. Confirme a palavra-passe.
* O calendário de manutenção de **patching automático SQL** identifica o dia da semana em que o Azure remende automaticamente os Servidores SQL. Para este tutorial, escreva **domingo.**
* A hora de início da manutenção de **remendos automáticos SQL** é a hora do dia para a região de Azure quando começa a remendar automaticamente.

> [!NOTE]
> A janela de remendos para cada máquina virtual é escalonada por uma hora. Apenas uma máquina virtual é remendada de cada vez para evitar perturbações de serviços.
>
>

![Definições do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Reveja as definições e, em seguida, clique **EM OK**.

### <a name="summary"></a>Resumo
Na página resumo, o Azure valida as definições. Também pode descarregar o modelo. Reveja o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Esta lâmina final contém **termos de utilização**e política de **privacidade.** Reveja esta informação. Quando estiver pronto para o Azure começar a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, clique em **Criar**.

O portal Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorizar a implementação
Monitorize o progresso de implantação do portal Azure. Um ícone que representa a implantação é automaticamente fixado no painel do portal Azure.

![Painel de Instrumentos Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server
Os novos casos do SQL Server estão a ser recorridos em máquinas virtuais que têm endereços IP ligados à Internet. Pode remotamente desktop (RDP) diretamente para cada máquina virtual do Servidor SQL.

Para RDP para um Servidor SQL, siga estes passos:

1. A partir do painel do portal Azure, verifique se a implantação foi bem sucedida.
2. Clique em **Recursos**.
3. Na lâmina **Recursos,** clique em **sqlserver-0,** que é o nome do computador de uma das máquinas virtuais que está a executar o Servidor SQL.
4. Na lâmina para **sqlserver-0,** clique **em Ligar**. O seu navegador pergunta se pretende abrir ou salvar o objeto de ligação remota. Clique em **Open** (Abrir).
5. **A ligação remota** do ambiente de trabalho pode avisá-lo que o editor desta ligação remota não pode ser identificado. Clique em **Ligar**.
6. A segurança do Windows pede-lhe que introduza as suas credenciais para se ligar ao endereço IP do controlador de domínio primário. Clique **Em utilizar outra conta**. Para **nome de utilizador,** escreva **contoso\DomainAdmin**. Configurou esta conta quando definiu o nome de utilizador do administrador no modelo. Utilize a senha complexa que escolheu quando configurao o modelo.
7. **O ambiente** de trabalho remoto pode avisá-lo de que o computador remoto não poderia ser autenticado devido a problemas com o seu certificado de segurança. Mostra o nome da certidão de segurança. Se seguiu o tutorial, o nome é **sqlserver-0.contoso.com.** Clique **sim**.

Está agora ligado ao RDP à máquina virtual Do Servidor SQL. Pode abrir o Estúdio de Gestão de Servidores SQL, ligar-se à instância padrão do Servidor SQL e verificar se o grupo de disponibilidade está configurado.
