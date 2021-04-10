---
title: Configure grupo de disponibilidade (modelo de arranque rápido Azure)
description: Utilize modelos de arranque rápido Azure para criar o Cluster de Failover do Windows, junte VMs do SERVIDOR SQL ao cluster, crie o ouvinte e configuure o balançador de carga interno em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359919"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Utilize modelos de arranque rápido Azure para configurar um grupo de disponibilidade para O SQL Server em Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como utilizar os modelos de arranque rápido do Azure para automatizar parcialmente a implementação de uma configuração de grupo de disponibilidade Always On para máquinas virtuais SQL Server (VMs) em Azure. Neste processo são utilizados dois modelos de arranque rápido Azure: 

   | Modelo | Description |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o cluster de failover do Windows e junta-lhe os VMs do SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e configura o equilibrador de carga interno. Este modelo só pode ser utilizado se o cluster de failover do Windows tiver sido criado com o modelo **de configuração de 101 m2-vm-ag.** |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, tais como a criação do grupo de disponibilidade e a criação do equilibrador de carga interno. Este artigo fornece a sequência de passos automatizados e manuais.

Embora este artigo utilize os modelos Azure Quickstart para configurar o ambiente de grupo de disponibilidade, também é possível fazê-lo utilizando o [portal Azure](availability-group-azure-portal-configure.md), [PowerShell ou o Azure CLI,](availability-group-az-commandline-configure.md)ou [manualmente](availability-group-manually-configure-tutorial.md) também. 
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a configuração de um grupo de disponibilidade Always On utilizando modelos de arranque rápido, tem de ter os seguintes pré-requisitos: 
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs de domínio [em Azure executando SQL Server 2016 (ou mais tarde) Edição empresarial](./create-sql-vm-portal.md) que estão na mesma zona de disponibilidade ou disponibilidade e que foram [registadas com a extensão do Agente IAAS SQL.](sql-agent-extension-manually-register-single-vm.md)  
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade): um para o balançador de carga interno e outro para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede que o grupo de disponibilidade. Se um balanceador de carga existente estiver a ser utilizado, só precisa de um endereço IP disponível.  

## <a name="permissions"></a>Permissões
As seguintes permissões são necessárias para configurar o grupo de disponibilidade Always On utilizando modelos de arranque rápido Azure: 

- Uma conta de utilizador de domínio existente que tem a permissão **de Objeto de Computador** criar no domínio.  Por exemplo, uma conta de administração de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com ). _Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o SQL Server. 


## <a name="create-cluster"></a>Criar cluster
Depois de os VMs do seu SQL Server terem sido registados com a extensão sql IaaS Agent, pode juntar-se aos VMs do seu SQL Server ao *SqlVirtualMachineGroups*. Este recurso define os metadados do cluster de falha do Windows. Os metadados incluem a versão, edição, nome de domínio totalmente qualificado, contas de Ative Directory para gerir tanto o cluster como o SQL Server, e a conta de armazenamento como testemunha de nuvem. 

A adição de VMs de servidor SQL ao grupo de recursos *SqlVirtualMachineGroups,* o Windows Failover Cluster Service para criar o cluster e, em seguida, junta os VMs do SqL Server a esse cluster. Este passo é automatizado com o modelo de arranque rápido **de 101-vm-ag-ag-setup.** Pode implementá-lo utilizando os seguintes passos:

1. Vá para o modelo de arranque rápido [**de 101 m2-vm-ag-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Em seguida, **selecione Implementar para Azure** para abrir o modelo de arranque rápido no portal Azure.
1. Preencha os campos necessários para configurar os metadados para o cluster de falha do Windows. Pode deixar os campos opcionais em branco.

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A subscrição onde existem os seus VMs do servidor SQL. |
   |**Grupo de recursos** | O grupo de recursos onde residem os seus VMs do SQL Server. | 
   |**Nome do cluster failover** | O nome que deseja para o seu novo cluster de falha do Windows. |
   | **Lista Vm existente** | Os VMs do SQL Server que pretende participar no grupo de disponibilidade e fazer parte deste novo cluster. Separe estes valores com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2).* |
   | **Versão do servidor SQL** | A versão SQL Server dos seus VMs do SQL Server. Selecione-o na lista de drop-down. Atualmente, apenas as imagens SQL Server 2016 e SQL Server 2017 são suportadas. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio em que residem os VMs do seu SQL Server. |
   | **Conta de Domínio existente** | Uma conta de utilizador de domínio existente que tem a permissão **de Objeto de Computador** criar no domínio à medida que o [CNO](/windows-server/failover-clustering/prestage-cluster-adds) é criado durante a implementação do modelo. Por exemplo, uma conta de administração de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com ). *Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster.*| 
   | **Senha de conta de domínio** | A palavra-passe para a conta de utilizador de domínio anteriormente mencionada. | 
   | **Conta de Serviço Sql existente** | A conta de utilizador de domínio que controla o [serviço SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante a implementação do grupo de disponibilidade (por exemplo: account@domain.com ). |
   | **Senha de serviço sql** | A palavra-passe utilizada pela conta de utilizador de domínio que controla o SQL Server. |
   | **Nome de testemunha em nuvem** | Uma nova conta de armazenamento Azure que será criada e usada para a testemunha em nuvem. Pode modificar este nome. |
   | **\_Localização de artefactos** | Este campo é definido por defeito e não deve ser modificado. |
   | **\_artefactos Localização SaS Token** | Este campo é intencionalmente deixado em branco. |
   | &nbsp; | &nbsp; |

1. Se concordar com os termos e condições, selecione o **I Concorda com os termos e condições indicados acima** da caixa de verificação. Em seguida, **selecione Comprar** para terminar a implementação do modelo de arranque rápido. 
1. Para monitorizar a sua implementação, selecione a implementação do ícone do sino **de Notificações** no banner de navegação superior ou vá para o **Grupo de Recursos** no portal Azure. Selecione **Implementações** em **Definições** e escolha a implementação do **Modelo Microsoft..** 

>[!NOTE]
> As credenciais fornecidas durante a colocação do modelo são armazenadas apenas para o comprimento da implantação. Após a colocação terminada, estas palavras-passe são removidas. Pedir-lhe-á que os forneça novamente se adicionar mais VMs do SQL Server ao cluster. 



## <a name="validate-cluster"></a>Validar o cluster 

Para que um cluster de failover seja suportado pela Microsoft, tem de passar na validação do cluster. Ligue-se ao VM utilizando o seu método preferido, como o Remote Desktop Protocol (RDP) e valide que o seu cluster passa a validação antes de prosseguir. Se não o fizer, deixa o seu aglomerado num estado não apoiado. 

Pode validar o cluster utilizando o Failover Cluster Manager (FCM) ou o seguinte comando PowerShell:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>Criar grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade como normalmente faria, utilizando [o SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento, porque o modelo de arranque rápido de configuração de **101-vm-aglistener** faz isso automaticamente no passo 4. 

## <a name="create-load-balancer"></a>Criar um balanceador de carga

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

O ouvinte do grupo Always On está disponível requer uma instância interna do Balançador de Carga Azure. O equilibrador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite uma maior falha e reconexão. Se os VMs do Servidor SQL de um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, pode utilizar um equilibrador de carga Básico. Caso contrário, é necessário utilizar um equilibrador de carga Standard. 

> [!IMPORTANT]
> O balançador de carga interno deve estar na mesma rede virtual que as instâncias VM do SQL Server. 

Só precisas de criar o equilibrador interno de carga. No passo 4, o modelo de arranque rápido **de 101 m2-vm-aglistener-configuração** lida com o resto da configuração (como a piscina de backend, a sonda de saúde e as regras de equilíbrio de carga). 

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais SQL Server. 
2. No grupo de recursos, **selecione Adicionar**.
3. Procure o **equilibrador de carga.** Nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.
4. Na lâmina do **balançador de carga,** selecione **Criar**.
5. Na caixa de diálogo do balançador de **carga Create,** configurar o balançador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Introduza um nome de texto que represente o equilibrador de carga. Por exemplo, **insira sqlLB**. |
   | **Tipo** |**Interna**: A maioria das implementações utiliza um equilibrador de carga interno, que permite que aplicações dentro da mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: Permite que as aplicações se conectem ao grupo de disponibilidade através de uma ligação pública à Internet. |
   | **Rede virtual** | Selecione a rede virtual em que se encontram as instâncias do SQL Server. |
   | **Sub-rede** | Selecione a sub-rede em que se encontram as instâncias do SQL Server. |
   | **Atribuição de endereços IP** |**Estático** |
   | **Endereço IP privado** | Especifique um endereço IP disponível a partir da sub-rede. |
   | **Subscrição** |Se tiver várias subscrições, este campo pode aparecer. Selecione a subscrição que pretende associar a este recurso. Normalmente é a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que se encontram as instâncias do SQL Server. |
   | **Localização** |Selecione a localização Azure em que se encontram as instâncias do SQL Server. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


>[!IMPORTANT]
> O recurso IP público para cada SQL Server VM deve ter um SKU Standard para ser compatível com o balanceador de carga Standard. Para determinar o SKU do recurso IP público do seu VM, vá ao **Grupo de Recursos,** selecione o seu recurso **de endereço IP público** para o SQL Server VM e localize o valor sob **SKU** no painel **de visão geral.** 

## <a name="create-listener"></a>Criar ouvinte 

Crie o ouvinte do grupo de disponibilidade e configuure automaticamente o balançador de carga interno utilizando o modelo de arranque rápido **de configuração de 101 m2-vm-aglistener.** O modelo prevê o recurso Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. O modelo de arranque rápido  **de 101 m2-vm-aglistener,** através da extensão SQL IaaS Agent, faz as seguintes ações:

- Cria um novo recurso IP frontend (baseado no valor do endereço IP fornecido durante a implementação) para o ouvinte. 
- Configura as definições de rede para o cluster e o balançador de carga interno. 
- Configura o pool de backend para o equilibrador de carga interno, a sonda de saúde e as regras de equilíbrio de carga.
- Cria o ouvinte do grupo de disponibilidade com o endereço ip e nome dados.
 
>[!NOTE]
> Só pode utilizar **a configuração de 101 m2-vm-aglistener** se o cluster de failover do Windows tiver sido criado com o modelo **de configuração de 101 vm-ag.**
   
   
Para configurar o balançador de carga interno e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Vá ao modelo de arranque rápido [de 101 m2-vm-aglistener e](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) selecione **Implementar para Azure** para iniciar o modelo de arranque rápido no portal Azure.
1. Preencha os campos necessários para configurar o balançador de carga interno e criar o ouvinte do grupo de disponibilidade. Pode deixar os campos opcionais em branco. 

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos onde existem os VMs do seu SQL Server e o grupo de disponibilidade. | 
   |**Nome do cluster de failover existente** | O nome do cluster a que os seus VMs do sql server estão unidos. |
   | **Grupo de Disponibilidade Sql existente**| O nome do grupo de disponibilidade em que os seus VMs do SqL Server fazem parte. |
   | **Lista Vm existente** | Os nomes dos VMs do Servidor SQL que fazem parte do grupo de disponibilidade anteriormente mencionado. Separe os nomes com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Serviço de Escuta** | O nome DNS que pretende atribuir ao ouvinte. Por padrão, este modelo especifica o nome "aglistener", mas pode alterá-lo. O nome não deve exceder 15 caracteres. |
   | **Porto de Ouvintes** | A porta que quer que o ouvinte use. Normalmente, esta porta deve ser o padrão de 1433. Este é o número da porta que o modelo especifica. Mas se a porta predefinida tiver sido alterada, a porta de escuta deve utilizar esse valor. | 
   | **IP ouvinte** | O endereço IP que deseja que o ouvinte utilize. Este endereço será criado durante a implementação do modelo, por isso forneça um que ainda não esteja em uso.  |
   | **Sub-rede existente** | O nome da sub-rede interna dos VMs do seu servidor SQL (por exemplo: *padrão).* Pode determinar este valor indo para **o Grupo de Recursos,** selecionando a sua rede virtual, selecionando **sub-redes** no painel **de Definições** e copiando o valor em **Nome**. |
   | **Equilibrador de Carga Interna existente** | O nome do equilibrador de carga interno que criou no passo 3. |
   | **Porto de Sonda** | A porta de sonda que pretende que o equilibrador interno utilize. O modelo utiliza 59999 por padrão, mas pode alterar este valor. |
   | &nbsp; | &nbsp; |

1. Se concordar com os termos e condições, selecione o **I Concorda com os termos e condições indicados acima** da caixa de verificação. Selecione **Comprar** para terminar a implementação do modelo de arranque rápido. 
1. Para monitorizar a sua implementação, selecione a implementação do ícone do sino **de Notificações** no banner de navegação superior ou vá para o **Grupo de Recursos** no portal Azure. Selecione **Implementações** em **Definições** e escolha a implementação do **Modelo Microsoft..** 

>[!NOTE]
>Se a sua implementação falhar a meio, terá de remover manualmente [o ouvinte recém-criado](#remove-listener) utilizando o PowerShell antes de recolocar o modelo de arranque rápido **de 101 m2-vm-aglistener..** 

## <a name="remove-listener"></a>Remover ouvinte
Se mais tarde precisar de remover o ouvinte do grupo de disponibilidade que o gabarito configurado, deve passar pela extensão do Agente IAAS SQL. Como o ouvinte está registado através da extensão sql IaaS Agent, apenas a sua eliminação através do SQL Server Management Studio é insuficiente. 

O melhor método é eliminá-lo através da extensão sql IaaS Agent utilizando o seguinte corte de código em PowerShell. Ao fazê-lo, remove os metadados do grupo de disponibilidade da extensão do Agente IAAS SQL. Também elimina fisicamente o ouvinte do grupo de disponibilidade. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erros comuns
Esta secção aborda algumas questões conhecidas e a sua possível resolução. 

**O ouvinte do grupo de disponibilidade para o grupo de disponibilidade \<AG-Name> ' ' já existe** O grupo de disponibilidade selecionado utilizado no modelo de arranque rápido Azure para o ouvinte do grupo de disponibilidade já contém um ouvinte. Ou está fisicamente dentro do grupo de disponibilidade, ou os seus metadados permanecem dentro da extensão do Agente IAAS SQL. Retire o ouvinte utilizando o [PowerShell](#remove-listener) antes de recolocar o modelo de arranque rápido **de 101 m2-vm-aglistener..** 

**A ligação só funciona a partir de réplica primária** Este comportamento é provável de uma implementação falhada do modelo **de configuração de 101-vm-aglistener** que deixou a configuração do equilibrador de carga interno num estado inconsistente. Verifique se o pool de backend lista o conjunto de disponibilidade, e que existem regras para a sonda de saúde e para as regras de equilíbrio de carga. Se faltar alguma coisa, a configuração do equilibrador de carga interno é um estado inconsistente. 

Para resolver este comportamento, remova o ouvinte utilizando o [PowerShell,](#remove-listener)elimine o equilibrador de carga interno através do portal Azure e comece novamente no passo 3. 

**BadRequest - Apenas a lista de máquinas virtuais SQL pode ser atualizada** Este erro pode ocorrer quando estiver a implementar o modelo **de configuração de 101 m2-vm-aglistener se** o ouvinte foi eliminado através do SQL Server Management Studio (SSMS), mas não foi eliminado da extensão do Agente IAAS SQL. A eliminação do ouvinte através de SSMS não remove os metadados do ouvinte da extensão sql IaaS Agent. O ouvinte deve ser eliminado do fornecedor de recursos através do [PowerShell](#remove-listener). 

**A conta de domínio não existe** Este erro pode ter duas causas. Ou a conta de domínio especificada não existe, ou falta os dados do Nome Principal do [Utilizador (UPN).](/windows/desktop/ad/naming-properties#userprincipalname) O modelo **de configuração de 101 m2-vm-ag-ag** espera uma conta de domínio no formulário UPN (isto é, user@domain.com ), mas algumas contas de domínio podem estar faltando-lhe. Isto acontece normalmente quando um utilizador local foi migrado para ser a primeira conta de administrador de domínio quando o servidor foi promovido a um controlador de domínio, ou quando um utilizador foi criado através do PowerShell. 

Verifique se a conta existe. Se acontecer, podes estar a deter-te na segunda situação. Para resolvê-lo, faça o seguinte:

1. No controlador de domínio, abra a janela **Ative Directory Users and Computers** a partir da opção **Ferramentas** no **Gestor do Servidor**. 
2. Aceda à conta selecionando **Utilizadores** no painel esquerdo.
3. Clique com o botão direito na conta e selecione **Propriedades.**
4. Selecione o separador **Conta.** Se a caixa **de nome do utilizador** estiver em branco, esta é a causa do seu erro. 

    ![Conta de utilizador em branco indica falta de UPN](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. Preencha a caixa de **nome do utilizador** para corresponder ao nome do utilizador e selecione o domínio adequado da lista de drop-down. 
6. **Selecione Aplicar** para guardar as suas alterações e fechar a caixa de diálogo selecionando **OK**. 

Depois de estoiste estas alterações, tente implementar novamente o modelo de arranque rápido do Azure. 


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral dos VMs do servidor SQL](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para VMs do servidor SQL](frequently-asked-questions-faq.md)
* [Orientação de preços para VMs do servidor SQL](pricing-guidance.md)
* [Notas de lançamento para VMs do servidor SQL](../../database/doc-changes-updates-release-notes.md)
* [Mudar modelos de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)