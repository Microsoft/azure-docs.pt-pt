---
title: Configurar o grupo de disponibilidade (modelo de início rápido do Azure)
description: Use os modelos de início rápido do Azure para criar o cluster de failover do Windows, ingressar SQL Server VMs no cluster, criar o ouvinte e configurar o balanceador de carga interno no Azure.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022388"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Usar modelos de início rápido do Azure para configurar um grupo de disponibilidade para SQL Server em uma VM do Azure
Este artigo descreve como usar os modelos de início rápido do Azure para automatizar parcialmente a implantação de uma configuração Always On grupo de disponibilidade para SQL Server máquinas virtuais no Azure. Dois modelos de início rápido do Azure são usados neste processo: 

   | Modelo | Descrição |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o cluster de failover do Windows e une as VMs SQL Server a ele. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e configura o balanceador de carga interno. Este modelo só poderá ser usado se o cluster de failover do Windows tiver sido criado com o modelo **101-SQL-VM-AG-setup** . |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, como a criação do grupo de disponibilidade e a criação do balanceador de carga interno. Este artigo fornece a sequência de etapas automatizadas e manuais.
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a configuração de um grupo de disponibilidade Always On usando modelos de início rápido, você deve ter os seguintes pré-requisitos: 
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs ingressadas [no domínio no Azure que executam o SQL Server 2016 (ou posterior) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) que estão no mesmo conjunto de disponibilidade ou zona de disponibilidade e que foram [registradas com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dois endereços IP disponíveis (não usados por nenhuma entidade): um para o balanceador de carga interno e outro para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se um balanceador de carga existente estiver sendo usado, você precisará de apenas um endereço IP disponível.  

## <a name="permissions"></a>Permissões
As permissões a seguir são necessárias para configurar o grupo de disponibilidade Always On usando modelos de início rápido do Azure: 

- Uma conta de usuário de domínio existente que tenha a permissão **criar objeto de computador** no domínio.  Por exemplo, uma conta de administrador de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). _Essa conta também deve fazer parte do grupo de Administradores local em cada VM para criar o cluster._
- A conta de usuário de domínio que controla o serviço de SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Etapa 1: criar o cluster de failover e ingressar SQL Server VMs no cluster usando um modelo de início rápido 
Depois que as VMs do SQL Server tiverem sido registradas com o provedor de recursos da VM do SQL, você poderá ingressar suas VMs do SQL Server no *SqlVirtualMachineGroups*. Esse recurso define os metadados do cluster de failover do Windows. Os metadados incluem a versão, a edição, o nome de domínio totalmente qualificado, Active Directory contas para gerenciar o cluster e o serviço de SQL Server e a conta de armazenamento como a testemunha de nuvem. 

Adicionar SQL Server VMs ao grupo de recursos *SqlVirtualMachineGroups* Inicializa o serviço de cluster de failover do Windows para criar o cluster e une essas SQL Server VMs a esse cluster. Esta etapa é automatizada com o modelo de início rápido **101-SQL-VM-AG-setup** . Você pode implementá-lo usando as seguintes etapas:

1. Vá para o modelo de início rápido [**101-SQL-VM-AG-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) . Em seguida, selecione **implantar no Azure** para abrir o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar os metadados para o cluster de failover do Windows. Você pode deixar os campos opcionais em branco.

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Subscrição** |  A assinatura em que existem suas VMs SQL Server. |
   |**Grupo de recursos** | O grupo de recursos onde residem suas VMs SQL Server. | 
   |**Nome do cluster de failover** | O nome que você deseja para o novo cluster de failover do Windows. |
   | **Lista de VMs existentes** | As VMs SQL Server que você deseja participar do grupo de disponibilidade e fazem parte desse novo cluster. Separe esses valores com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Versão do SQL Server** | A versão SQL Server de suas VMs SQL Server. Selecione-o na lista suspensa. Atualmente, há suporte apenas para imagens SQL Server 2016 e SQL Server 2017. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio no qual o SQL Server VMs residem. |
   | **Conta de domínio existente** | Uma conta de usuário de domínio existente que tenha a permissão **Create Computer Object** no domínio como o [CNO](/windows-server/failover-clustering/prestage-cluster-adds) é criada durante a implantação do modelo. Por exemplo, uma conta de administrador de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). *Essa conta também deve fazer parte do grupo de Administradores local em cada VM para criar o cluster.*| 
   | **Senha da conta de domínio** | A senha da conta de usuário de domínio mencionada anteriormente. | 
   | **Conta de serviço do SQL existente** | A conta de usuário de domínio que controla o [serviço de SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante a implantação do grupo de disponibilidade (por exemplo: account@domain.com). |
   | **Senha do serviço SQL** | A senha usada pela conta de usuário do domínio que controla o serviço de SQL Server. |
   | **Nome da testemunha de nuvem** | Uma nova conta de armazenamento do Azure que será criada e usada para a testemunha de nuvem. Você pode modificar esse nome. |
   | **Localização de artefatos \_** | Esse campo é definido por padrão e não deve ser modificado. |
   | **Token SAS do local \_artefatos** | Esse campo é intencionalmente deixado em branco. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, marque a caixa de seleção **eu concordo com os termos e condições declarados acima** . Em seguida, selecione **comprar** para concluir a implantação do modelo de início rápido. 
1. Para monitorar sua implantação, selecione a implantação no ícone de Bell de **notificações** na faixa de navegação superior ou vá para o **grupo de recursos** na portal do Azure. Selecione **implantações** em **configurações**e escolha a implantação do **Microsoft. Template** . 

>[!NOTE]
> As credenciais fornecidas durante a implantação do modelo são armazenadas apenas para o comprimento da implantação. Após a conclusão da implantação, essas senhas são removidas. Você será solicitado a fornecê-las novamente se adicionar mais SQL Server VMs ao cluster. 


## <a name="step-2-manually-create-the-availability-group"></a>Etapa 2: criar manualmente o grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade como faria normalmente, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento, porque o modelo de início rápido **101-SQL-VM-aglistener-setup** faz isso automaticamente na etapa 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Etapa 3: criar manualmente o balanceador de carga interno
O ouvinte do grupo de disponibilidade Always On requer uma instância interna do Azure Load Balancer. O balanceador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite failover e reconexão mais rápidos. Se as SQL Server VMs em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga básico. Caso contrário, você precisa usar um balanceador de carga padrão. 

> [!IMPORTANT]
> O balanceador de carga interno deve estar na mesma rede virtual que as instâncias de VM SQL Server. 

Você só precisa criar o balanceador de carga interno. Na etapa 4, o modelo de início rápido **101-SQL-VM-aglistener-setup lida com** o restante da configuração (como o pool de back-end, investigação de integridade e regras de balanceamento de carga). 

1. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais SQL Server. 
2. No grupo de recursos, selecione **Adicionar**.
3. Pesquise o **balanceador de carga**. Nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.
4. Na folha **Load Balancer** , selecione **criar**.
5. Na caixa de diálogo **criar balanceador de carga** , configure o balanceador de carga da seguinte maneira:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Insira um nome de texto que represente o balanceador de carga. Por exemplo, digite **sqlLB**. |
   | **Tipo** |**Interno**: a maioria das implementações usa um balanceador de carga interno, que permite que os aplicativos na mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: permite que os aplicativos se conectem ao grupo de disponibilidade por meio de uma conexão de Internet pública. |
   | **Rede virtual** | Selecione a rede virtual na qual as instâncias do SQL Server estão. |
   | **Sub-rede** | Selecione a sub-rede na qual as instâncias de SQL Server estão. |
   | **Atribuição de endereço IP** |**Auto-estática** |
   | **Endereço IP privado** | Especifique um endereço IP disponível da sub-rede. |
   | **Subscrição** |Se você tiver várias assinaturas, esse campo poderá ser exibido. Selecione a assinatura que você deseja associar a este recurso. Normalmente, é a mesma assinatura que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual as instâncias do SQL Server estão. |
   | **Localização** |Selecione o local do Azure no qual as instâncias do SQL Server estão. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


>[!IMPORTANT]
> O recurso de IP público para cada VM SQL Server deve ter um SKU Standard para ser compatível com o Load Balancer padrão. Para determinar a SKU do recurso IP público da VM, vá para **grupo de recursos**, selecione o recurso de **endereço IP público** para a VM SQL Server e localize o valor em **SKU** no painel **visão geral** . 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Etapa 4: criar o ouvinte do grupo de disponibilidade e configurar o balanceador de carga interno usando o modelo de início rápido

Crie o ouvinte do grupo de disponibilidade e configure o balanceador de carga interno automaticamente usando o modelo de início rápido **101-SQL-VM-aglistener-setup** . O modelo provisiona o recurso Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. O modelo de início rápido **101-SQL-VM-aglistener-setup** , por meio do provedor de recursos de VM do SQL, executa as seguintes ações:

- Cria um novo recurso de IP de front-end (com base no valor de endereço IP fornecido durante a implantação) para o ouvinte. 
- Define as configurações de rede para o cluster e o balanceador de carga interno. 
- Configura o pool de back-end para o balanceador de carga interno, a investigação de integridade e as regras de balanceamento de carga.
- Cria o ouvinte do grupo de disponibilidade com o nome e endereço IP fornecidos.
 
>[!NOTE]
> Você pode usar **101-SQL-VM-aglistener-setup** somente se o cluster de failover do Windows tiver sido criado com o modelo **101-SQL-VM-AG-setup** .
   
   
Para configurar o balanceador de carga interno e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Vá para o modelo de início rápido [101-SQL-VM-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selecione **implantar no Azure** para iniciar o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar o balanceador de carga interno e crie o ouvinte do grupo de disponibilidade. Você pode deixar os campos opcionais em branco. 

   A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos no qual o SQL Server VMs e o grupo de disponibilidade existem. | 
   |**Nome do cluster de failover existente** | O nome do cluster ao qual suas VMs SQL Server estão unidas. |
   | **Grupo de disponibilidade do SQL existente**| O nome do grupo de disponibilidade para o qual suas VMs de SQL Server fazem parte. |
   | **Lista de VMs existentes** | Os nomes das VMs SQL Server que fazem parte do grupo de disponibilidade mencionado anteriormente. Separe os nomes com uma vírgula e um espaço (por exemplo: *SQLVM1, SQLVM2*). |
   | **Listener** | O nome DNS que você deseja atribuir ao ouvinte. Por padrão, esse modelo especifica o nome "aglistener", mas você pode alterá-lo. O nome não deve exceder 15 caracteres. |
   | **Porta do ouvinte** | A porta que você deseja que o ouvinte use. Normalmente, essa porta deve ser o padrão de 1433. Esse é o número da porta que o modelo especifica. Mas se a porta padrão tiver sido alterada, a porta do ouvinte deverá usar esse valor. | 
   | **IP do ouvinte** | O endereço IP que você deseja que o ouvinte use. Esse endereço será criado durante a implantação do modelo, portanto, forneça um que ainda não esteja em uso.  |
   | **Sub-rede existente** | O nome da sub-rede interna de suas VMs de SQL Server (por exemplo: *padrão*). Você pode determinar esse valor indo para o **grupo de recursos**, selecionando sua rede virtual, selecionando **sub-redes** no painel **configurações** e copiando o valor em **nome**. |
   | **Load Balancer internos existentes** | O nome do balanceador de carga interno que você criou na etapa 3. |
   | **Porta de investigação** | A porta de investigação que você deseja que o balanceador de carga interno use. O modelo usa 59999 por padrão, mas você pode alterar esse valor. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e condições, marque a caixa de seleção **eu concordo com os termos e condições declarados acima** . Selecione **comprar** para concluir a implantação do modelo de início rápido. 
1. Para monitorar sua implantação, selecione a implantação no ícone de Bell de **notificações** na faixa de navegação superior ou vá para o **grupo de recursos** na portal do Azure. Selecione **implantações** em **configurações**e escolha a implantação do **Microsoft. Template** . 

>[!NOTE]
>Se a sua implantação falhar no meio, você precisará [remover manualmente o ouvinte recém-criado usando o](#remove-the-availability-group-listener) PowerShell antes de reimplantar o modelo de início rápido **101-SQL-VM-aglistener-setup** . 

## <a name="remove-the-availability-group-listener"></a>Remover o ouvinte do grupo de disponibilidade
Se posteriormente você precisar remover o ouvinte do grupo de disponibilidade que o modelo configurou, deverá passar pelo provedor de recursos da VM do SQL. Como o ouvinte é registrado por meio do provedor de recursos de VM do SQL, apenas excluí-lo via SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo por meio do provedor de recursos da VM do SQL usando o trecho de código a seguir no PowerShell. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos da VM do SQL. Ele também exclui fisicamente o ouvinte do grupo de disponibilidade. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Erros comuns
Esta seção aborda alguns problemas conhecidos e sua possível resolução. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>O ouvinte do grupo de disponibilidade '\<AG-Name > ' já existe
O grupo de disponibilidade selecionado usado no modelo de início rápido do Azure para o ouvinte do grupo de disponibilidade já contém um ouvinte. Ele está fisicamente dentro do grupo de disponibilidade ou seus metadados permanecem dentro do provedor de recursos da VM do SQL. Remova o ouvinte usando o [PowerShell](#remove-the-availability-group-listener) antes de reimplantar o modelo de início rápido **101-SQL-VM-aglistener-setup** . 

### <a name="connection-only-works-from-primary-replica"></a>A conexão só funciona da réplica primária
Esse comportamento é provavelmente de uma implantação de modelo **101-SQL-VM-aglistener-setup** com falha que deixou a configuração do balanceador de carga interno em um estado inconsistente. Verifique se o pool de back-end lista o conjunto de disponibilidade e se existem regras para a investigação de integridade e para as regras de balanceamento de carga. Se algo estiver faltando, a configuração do balanceador de carga interno será um estado inconsistente. 

Para resolver esse comportamento, remova o ouvinte usando o [PowerShell](#remove-the-availability-group-listener), exclua o balanceador de carga interno por meio do portal do Azure e inicie novamente na etapa 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest-somente a lista de máquinas virtuais SQL pode ser atualizada
Esse erro pode ocorrer quando você estiver implantando o modelo **101-SQL-VM-aglistener-setup** se o ouvinte tiver sido excluído via SQL Server Management Studio (SSMS), mas não tiver sido excluído do provedor de recursos da VM do SQL. Excluir o ouvinte via SSMS não remove os metadados do ouvinte do provedor de recursos da VM do SQL. O ouvinte deve ser excluído do provedor de recursos por meio do [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>A conta de domínio não existe
Esse erro pode ter duas causas. A conta de domínio especificada não existe ou não tem os dados de [UPN (nome principal do usuário)](/windows/desktop/ad/naming-properties#userprincipalname) . O modelo **101-SQL-VM-AG-setup** espera uma conta de domínio no formato UPN (ou seja, *user@domain.com* ), mas algumas contas de domínio podem estar faltando. Isso normalmente acontece quando um usuário local foi migrado para ser a primeira conta de administrador de domínio quando o servidor foi promovido a um controlador de domínio, ou quando um usuário foi criado por meio do PowerShell. 

Verifique se a conta existe. Se isso ocorrer, você poderá estar executando a segunda situação. Para resolvê-lo, faça o seguinte:

1. No controlador de domínio, abra a janela **usuários e computadores Active Directory** da opção **ferramentas** em **Gerenciador do servidor**. 
2. Vá para a conta selecionando **usuários** no painel esquerdo.
3. Clique com o botão direito do mouse na conta e selecione **Propriedades**.
4. Selecione a guia **conta** . Se a caixa **nome de logon do usuário** estiver em branco, essa é a causa do seu erro. 

    ![A conta de usuário em branco indica um UPN ausente](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Preencha a caixa **nome de logon do usuário** para corresponder ao nome do usuário e selecione o domínio apropriado na lista suspensa. 
6. Selecione **aplicar** para salvar as alterações e feche a caixa de diálogo selecionando **OK**. 

Depois de fazer essas alterações, tente implantar o modelo de início rápido do Azure mais uma vez. 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral das VMs SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para VMs SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para VMs SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para VMs SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternando modelos de licenciamento para uma VM SQL Server](virtual-machines-windows-sql-ahb.md)



