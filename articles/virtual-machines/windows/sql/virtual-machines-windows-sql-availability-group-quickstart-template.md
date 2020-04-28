---
title: Configure grupo de disponibilidade (modelo de quickstart Azure)
description: Utilize modelos de quickstart Azure para criar o cluster de failover do Windows, junte-se a VMs do Servidor SQL para o cluster, crie o ouvinte e configure o equilíbrio interno de carga em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74022388"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Utilize modelos de quickstart Azure para configurar um grupo de disponibilidade para o SQL Server num VM Azure
Este artigo descreve como usar os modelos de quickstart Azure para automatizar parcialmente a implementação de uma configuração de grupo de disponibilidade Always On para máquinas virtuais SQL Server em Azure. Neste processo são utilizados dois modelos azure de arranque rápido: 

   | Modelo | Descrição |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o cluster de failover do Windows e junta-se aos VMs do Servidor SQL. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e confunde o equilibrador de carga interna. Este modelo só pode ser utilizado se o cluster failover do Windows ter sido criado com o modelo de configuração de **101 sql-vm-ag.** |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, tais como criar o grupo de disponibilidade e criar o equilibrador interno de carga. Este artigo fornece a sequência de passos automatizados e manuais.
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a configuração de um grupo Always On, utilizando modelos de arranque rápido, deve ter os seguintes pré-requisitos: 
- Uma [subscrição Azure.](https://azure.microsoft.com/free/)
- Um grupo de recursos com um controlador de domínio. 
- Um ou mais [VMs unidos em Azure executando SQL Server 2016 (ou mais tarde) Edição enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) que estão na mesma zona de disponibilidade ou de disponibilidade e que foram registadas com o fornecedor de recursos [SQL VM.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade) endereços IP: um para o equilibrista interno de carga e um para o ouvinte do grupo de disponibilidade dentro da mesma subnet que o grupo de disponibilidade. Se estiver a ser utilizado um equilibrador de carga existente, só precisa de um endereço IP disponível.  

## <a name="permissions"></a>Permissões
As seguintes permissões são necessárias para configurar o grupo Always On disponibilidade utilizando modelos de quickstart Azure: 

- Uma conta de utilizador de domínio existente que tem **a create computer object** permission in the domain.  Por exemplo, uma conta de administração de domínio account@domain.comnormalmente tem permissão suficiente (por exemplo: ). _Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o serviço SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Passo 1: Criar o cluster failover e juntar VMs do Servidor SQL ao cluster utilizando um modelo de arranque rápido 
Depois de os seus VMs do Servidor SQL terem sido registados no fornecedor de recursos SQL VM, pode juntar-se aos seus VMs do Servidor SQL para *SqlVirtualMachineGroups*. Este recurso define os metadados do cluster failover do Windows. Os metadados incluem a versão, edição, nome de domínio totalmente qualificado, contas De Diretório Ativo para gerir tanto o cluster como o serviço SQL Server, e a conta de armazenamento como testemunha na nuvem. 

Adicionar VMs de servidor SQL ao grupo de recursos *SqlVirtualMachineGroups* é o Serviço de Cluster Failover do Windows para criar o cluster e, em seguida, une esses VMs do Servidor SQL a esse cluster. Este passo é automatizado com o modelo de arranque rápido de **101 sql-vm-ag-configuração.** Pode implementá-lo utilizando os seguintes passos:

1. Vá ao modelo de arranque rápido de [**101 sql-vm-ag-set-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Em seguida, selecione **Deploy para Azure** para abrir o modelo de arranque rápido no portal Azure.
1. Preencha os campos necessários para configurar os metadados para o cluster failover do Windows. Pode deixar os campos opcionais em branco.

   A tabela que se segue mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde existem os seus VMs do Servidor SQL. |
   |**Grupo de recursos** | O grupo de recursos onde residem os seus VMs do Servidor SQL. | 
   |**Nome do cluster failover** | O nome que deseja para o seu novo cluster de falhas do Windows. |
   | **Lista Vm existente** | Os VMs do Servidor SQL que pretende participar no grupo de disponibilidade e fazer parte deste novo cluster. Separe estes valores com uma vírem e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Versão do Servidor SQL** | A versão SQL Server dos seus VMs do Servidor SQL. Selecione-o na lista de lançamentos. Atualmente, apenas as imagens SQL Server 2016 e SQL Server 2017 são suportadas. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio em que residem os seus VMs do Servidor SQL. |
   | **Conta de Domínio existente** | Uma conta de utilizador de domínio existente que tem **a create computer object** permission in the domain as the [CNO](/windows-server/failover-clustering/prestage-cluster-adds) é criado durante a implementação do modelo. Por exemplo, uma conta de administração de domínio account@domain.comnormalmente tem permissão suficiente (por exemplo: ). *Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster.*| 
   | **Palavra-passe da conta de domínio** | A palavra-passe para a conta de utilizador de domínio anteriormente mencionada. | 
   | **Conta de Serviço Sql existente** | A conta de utilizador de domínio que controla o serviço account@domain.com [SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante a implementação do grupo de disponibilidade (por exemplo: ). |
   | **Senha de serviço Sql** | A palavra-passe utilizada pela conta de utilizador de domínio que controla o serviço SQL Server. |
   | **Nome de testemunha de nuvem** | Uma nova conta de armazenamento Azure que será criada e usada para a testemunha de nuvem. Pode modificar este nome. |
   | **\_Localização de artefactos** | Este campo é definido por defeito e não deve ser modificado. |
   | **\_Artefactos Localização Sas Token** | Este campo é intencionalmente deixado em branco. |
   | &nbsp; | &nbsp; |

1. Se concordar com os termos e condições, selecione o **I Concordo com os termos e condições acima indicados.** Em seguida, **selecione Comprar** para terminar a implementação do modelo de arranque rápido. 
1. Para monitorizar a sua implementação, selecione a implementação do ícone do sino de **Notificações** no banner de navegação superior ou vá ao **Grupo de Recursos** no portal Azure. Selecione **Implementações** em **Definições**e escolha a implementação **Microsoft.Template.** 

>[!NOTE]
> As credenciais fornecidas durante a implantação do modelo são armazenadas apenas para o comprimento da implantação. Após os acabamentos de implantação, essas palavras-passe são removidas. Será-lhe pedido que os forneça novamente se adicionar mais VMs sQL server ao cluster. 


## <a name="step-2-manually-create-the-availability-group"></a>Passo 2: Criar manualmente o grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade como normalmente faria, utilizando o [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell,](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento, porque o modelo de arranque rápido de **101-sql-vm-aglistener** faz isso automaticamente no passo 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Passo 3: Criar manualmente o equilibrador de carga interna
O ouvinte do grupo Always On requer uma instância interna do Equilíbrio de Carga Azure. O equilibrador de carga interna fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite uma falha e reconexão mais rápidas. Se os VMs do Servidor SQL num grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, pode utilizar um equilíbrio de carga Basic. Caso contrário, tem de utilizar um equilibrador de carga Standard. 

> [!IMPORTANT]
> O equilibrante de carga interna deve estar na mesma rede virtual que as instâncias VM do Servidor SQL. 

Só precisas de criar o equilibrador interno de carga. No passo 4, o modelo de arranque rápido de **101 sql-vm-aglistener** manuseia o resto da configuração (como a piscina de backend, a sonda de saúde e as regras de equilíbrio de carga). 

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais Do Servidor SQL. 
2. No grupo de recursos, selecione **Adicionar**.
3. Procurar um **equilibrador**de carga . Nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.
4. Na lâmina **do balanceor de carga,** selecione **Criar**.
5. Na caixa de diálogo criar um **equilíbrio de carga,** configure o equilibrador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Introduza um nome de texto que represente o equilibrador de carga. Por exemplo, introduza **sqlLB**. |
   | **Tipo** |**Interna**: A maioria das implementações utiliza um equilibrador de carga interno, que permite que aplicações dentro da mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: Permite que as aplicações se conectem ao grupo de disponibilidade através de uma ligação à Internet pública. |
   | **Rede virtual** | Selecione a rede virtual em que os casos do Servidor SQL estão. |
   | **Sub-rede** | Selecione a sub-rede em que as instâncias do Servidor SQL estão dentro. |
   | **Atribuição de endereçoIP** |**Estático** |
   | **Endereço IP privado** | Especifique um endereço IP disponível a partir da sub-rede. |
   | **Subscrição** |Se tiver várias subscrições, este campo pode aparecer. Selecione a subscrição que pretende associar a este recurso. Normalmente é a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que os casos do Servidor SQL estão. |
   | **Localização** |Selecione a localização Azure onde os casos do Servidor SQL estão. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


>[!IMPORTANT]
> O recurso IP público para cada VM do Servidor SQL deve ter um SKU Padrão compatível com o equilíbrio de carga Standard. Para determinar o SKU do recurso IP público do seu VM, vá ao **Grupo de Recursos,** selecione o seu recurso **de Endereço IP Público** para o SQL Server VM e localize o valor em **SKU** no painel **de visão geral.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Passo 4: Criar o ouvinte do grupo de disponibilidade e configurar o equilíbrio interno de carga utilizando o modelo de arranque rápido

Crie o ouvinte do grupo de disponibilidade e configure automaticamente o equilibrador de carga interna utilizando o modelo de arranque rápido de **101-sql-vm-aglistener-setup.** O modelo prevê o recurso Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. O modelo de arranque rápido de **101 sql-vm-aglistener-setup,** através do fornecedor de recursos SQL VM, faz as seguintes ações:

- Cria um novo recurso IP frontend (com base no valor de endereço IP fornecido durante a implementação) para o ouvinte. 
- Configura as definições de rede para o cluster e o equilibrador de carga interno. 
- Configura o pool de backend para o equilibrador interno de carga, a sonda de saúde e as regras de equilíbrio de carga.
- Cria o ouvinte do grupo de disponibilidade com o endereço IP e nome dado.
 
>[!NOTE]
> Só é possível utilizar a configuração de **101-sql-vm-aglistener-setup** se o cluster de failover do Windows ter sido criado com o modelo de configuração de **101-sql-vm-ag..**
   
   
Para configurar o equilibrador de carga interna e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Vá ao modelo de arranque rápido de [101 sql-vm-aglistener](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selecione **Deploy para Azure** para iniciar o modelo de arranque rápido no portal Azure.
1. Preencha os campos necessários para configurar o equilibrador de carga interna e criar o ouvinte do grupo de disponibilidade. Pode deixar os campos opcionais em branco. 

   A tabela que se segue mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos onde existem os VMs do SQL Server e o grupo de disponibilidade. | 
   |**Nome de cluster de failover existente** | O nome do cluster a que os seus VMs do Servidor SQL estão unidos. |
   | **Grupo de Disponibilidade Sql existente**| O nome do grupo de disponibilidade do qual os seus VMs do Servidor SQL fazem parte. |
   | **Lista Vm existente** | Os nomes dos VMs do Servidor SQL que fazem parte do grupo de disponibilidade anteriormente mencionado. Separe os nomes com uma vírem e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Serviço de Escuta** | O nome DNS que pretende atribuir ao ouvinte. Por padrão, este modelo especifica o nome "aglistener", mas pode mudá-lo. O nome não deve exceder 15 caracteres. |
   | **Porto de escuta** | A porta que quer que o ouvinte use. Tipicamente, esta porta deve ser o padrão de 1433. Este é o número de porta que o modelo especifica. Mas se a sua porta padrão foi alterada, a porta do ouvinte deve utilizar esse valor. | 
   | **Escuta IP** | O endereço IP que pretende que o ouvinte utilize. Este endereço será criado durante a implementação do modelo, por isso forneça um que ainda não esteja em uso.  |
   | **Subnet existente** | O nome da sub-rede interna dos seus VMs do Servidor SQL (por exemplo: *predefinido*). Pode determinar este valor indo para o **Grupo de Recursos,** selecionando a sua rede virtual, selecionando **Subredes** no painel **definições** e copiando o valor em **nome**. |
   | **Balanceador de carga interna existente** | O nome do equilibrador de carga interna que criou no passo 3. |
   | **Porta sonda** | A porta da sonda que pretende utilizar o equilibrador de carga interna. O modelo utiliza 59999 por padrão, mas pode alterar este valor. |
   | &nbsp; | &nbsp; |

1. Se concordar com os termos e condições, selecione o **I Concordo com os termos e condições acima indicados.** **Selecione Comprar** para terminar a implementação do modelo de arranque rápido. 
1. Para monitorizar a sua implementação, selecione a implementação do ícone do sino de **Notificações** no banner de navegação superior ou vá ao **Grupo de Recursos** no portal Azure. Selecione **Implementações** em **Definições**e escolha a implementação **Microsoft.Template.** 

>[!NOTE]
>Se a sua implementação falhar a meio, terá de remover manualmente [o ouvinte recém-criado](#remove-the-availability-group-listener) utilizando o PowerShell antes de reimplantar o modelo de arranque rápido de **101 sql-vm-aglistener.** 

## <a name="remove-the-availability-group-listener"></a>Remova o ouvinte do grupo de disponibilidade
Se mais tarde precisar de remover o ouvinte do grupo de disponibilidade que o modelo configurado, deve passar pelo fornecedor de recursos SQL VM. Como o ouvinte está registado através do fornecedor de recursos SQL VM, apenas asua apagar através do SQL Server Management Studio é insuficiente. 

O melhor método é eliminá-lo através do fornecedor de recursos SQL VM utilizando o seguinte código snippet no PowerShell. Ao fazê-lo remove os metadados de ouvintes do grupo de disponibilidade do fornecedor de recursos SQL VM. Também elimina fisicamente o ouvinte do grupo de disponibilidade. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erros comuns
Esta secção discute algumas questões conhecidas e a sua possível resolução. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Já existe um ouvinte de\<grupo de disponibilidade para o grupo de disponibilidade ' AG-Name>'
O grupo de disponibilidade selecionado utilizado no modelo de quickstart Azure para o ouvinte do grupo de disponibilidade já contém um ouvinte. Ou está fisicamente dentro do grupo de disponibilidade, ou os seus metadados permanecem dentro do fornecedor de recursos SQL VM. Retire o ouvinte utilizando o [PowerShell](#remove-the-availability-group-listener) antes de recolocar o modelo de arranque rápido de **101-sql-vm-aglistener.** 

### <a name="connection-only-works-from-primary-replica"></a>A ligação só funciona a partir de réplica primária
Este comportamento é provavelmente de uma implementação falhada do modelo de configuração de **101-vm-aglistener** que deixou a configuração do equilibrista interno de carga num estado inconsistente. Verifique se o pool backend lista o conjunto de disponibilidade, e que existem regras para a sonda de saúde e para as regras de equilíbrio de carga. Se faltar alguma coisa, a configuração do equilibrista interno de carga é um estado inconsistente. 

Para resolver este comportamento, remova o ouvinte utilizando o [PowerShell,](#remove-the-availability-group-listener)elimine o equilibrador de carga interna através do portal Azure e reinicie no passo 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - Apenas a lista de máquinas virtuais SQL pode ser atualizada
Este erro pode ocorrer quando estiver a implementar o modelo de configuração de **101 sql-vm-aglistener** se o ouvinte foi eliminado através do SQL Server Management Studio (SSMS), mas não foi eliminado do fornecedor de recursos SQL VM. A eliminação do ouvinte via SSMS não remove os metadados do ouvinte do fornecedor de recursos SQL VM. O ouvinte deve ser eliminado do fornecedor de recursos através da [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>A conta de domínio não existe
Este erro pode ter duas causas. Ou a conta de domínio especificada não existe, ou falta os dados do Nome Principal do [Utilizador (UPN).](/windows/desktop/ad/naming-properties#userprincipalname) O modelo de configuração de **101 sql-vm-ag-ag-espera** uma *user@domain.com*conta de domínio na forma UPN (isto é, ), mas algumas contas de domínio podem estar faltando. Isto normalmente acontece quando um utilizador local foi migrado para ser a primeira conta de administrador de domínio quando o servidor foi promovido a um controlador de domínio, ou quando um utilizador foi criado através do PowerShell. 

Verifique se a conta existe. Se acontecer, podes estar a encontrar a segunda situação. Para resolvê-lo, faça o seguinte:

1. No controlador de domínio, abra a janela **Utilizadores e Computadores de Diretório Ativo** a partir da opção **Ferramentas** no **Gestor do Servidor**. 
2. Vá à conta selecionando **Utilizadores** no painel esquerdo.
3. Clique na conta e selecione **Propriedades**.
4. Selecione o separador **Conta.** Se a caixa de nomes de início de **sessão do Utilizador** estiver em branco, esta é a causa do seu erro. 

    ![Conta de utilizador em branco indica falta da UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Preencha a caixa de **nomes de início** de sessão do Utilizador para combinar com o nome do utilizador e selecione o domínio adequado da lista de lançamentos. 
6. Selecione **Aplicar** para guardar as alterações e fechar a caixa de diálogo selecionando **OK**. 

Depois de efazer estas alterações, tente implementar o modelo de arranque rápido azure mais uma vez. 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral dos VMs do Servidor SQL](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para VMs de servidor SQL](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para VMs do Servidor SQL](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para VMs do Servidor SQL](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Mudar modelos de licenciamento para um VM de servidor SQL](virtual-machines-windows-sql-ahb.md)



