---
title: Adicionar runbooks de automatização do Azure aos planos de recuperação do Site Recovery | Documentos da Microsoft
description: Saiba como estender os planos de recuperação com a automatização do Azure para recuperação após desastre com o Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 26c3466080cb356ca3610d42eaaf5ee4975d3731
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471949"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de automatização do Azure aos planos de recuperação
Neste artigo, descrevemos como o Azure Site Recovery se integra com a automatização do Azure para ajudar a expandir seus planos de recuperação. Planos de recuperação podem orquestrar a recuperação de VMs que são protegidas com o Site Recovery. Planos de recuperação funcionam tanto para a replicação para uma nuvem secundária e para replicação no Azure. Planos de recuperação também ajudam a tornar a recuperação **consistentemente preciso**, **repetíveis**, e **automatizada**. Se efetuar a ativação pós-falha suas VMs do Azure, integração com a automatização do Azure expande a seus planos de recuperação. Pode usá-lo para executar runbooks, que oferecem as tarefas de automação poderoso.

Se estiver familiarizado com a automatização do Azure, pode [Inscreva-se](https://azure.microsoft.com/services/automation/) e [transfira scripts de exemplo](https://azure.microsoft.com/documentation/scripts/). Para obter mais informações e para saber como orquestrar a recuperação para o Azure, utilizando [planos de recuperação](./site-recovery-create-recovery-plans.md), consulte [do Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Neste artigo, descrevemos como integrar os runbooks de automatização do Azure nos seus planos de recuperação. Exemplos são utilizadas para automatizar tarefas básicas que anteriormente exigiam uma intervenção manual. Também descreveremos como converter uma recuperação de vários passo para uma ação de recuperação de clique único.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação
1. Vá para o **recuperação de Site** painel de recursos do plano de recuperação. Neste exemplo, o plano de recuperação tem duas VMs adicionadas a ele, para a recuperação. Para começar a adicionar um runbook, clique nas **personalizar** separador.

    ![Clique no botão Personalizar](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Com o botão direito **grupo 1: Inicie**e, em seguida, selecione **adicionar ação posterior**.

    ![Atalho grupo 1: Iniciar e adicionar a ação de postagem](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Clique em **escolher um script**.

4. Sobre o **ação de atualização** painel, o nome do script **Hello World**.

    ![O painel de ação de atualização](media/site-recovery-runbook-automation-new/update-rp.png)

5. Introduza um nome de conta de automatização.
    >[!NOTE]
    > A conta de automatização pode estar em qualquer região do Azure. A conta de automatização deve estar na mesma subscrição que o cofre do Azure Site Recovery.

6. Na sua conta de automatização, selecione um runbook. Este runbook é o script que é executado durante a execução do plano de recuperação, após a recuperação do primeiro grupo.

7. Para guardar o script, clique em **OK**. O script é adicionado ao **grupo 1: Pós-passos**.

    ![Grupo de pós-ação 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Considerações sobre a adição de um script

* Para obter as opções para **eliminar um passo** ou **atualizar o script**, com o botão direito do script.
* Um script pode executar no Azure durante a ativação pós-falha, a partir de uma máquina no local para o Azure. Ele também pode executar no Azure como um script de site principal antes do encerramento, durante a reativação pós-falha do Azure para uma máquina no local.
* Quando um script é executado, ele injeta um contexto de plano de recuperação. O exemplo seguinte mostra uma variável de contexto:

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    A tabela seguinte lista o nome e descrição de cada variável no contexto.

    | **Nome da variável** | **Descrição** |
    | --- | --- |
    | RecoveryPlanName |O nome do plano a ser executado. Esta variável ajuda-o a efetuar ações diferentes com base no nome do plano de recuperação. Também pode reutilizar o script. |
    | FailoverType |Especifica se a ativação pós-falha é um teste, planeada ou não planeada. |
    | FailoverDirection |Especifica se a recuperação é para um site primário ou secundário. |
    | GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução. |
    | VmMap |Uma matriz de todas as VMs no grupo. |
    | Chave de VMMap |Uma chave exclusiva (GUID) para cada VM. É o mesmo que o ID de Azure Virtual Machine Manager (VMM) da VM, quando aplicável. |
    | SubscriptionId |O ID de subscrição do Azure na qual a VM foi criada. |
    | RoleName |O nome da VM do Azure que está a ser recuperada. |
    | CloudServiceName |O nome do serviço cloud do Azure sob a qual a VM foi criada. |
    | ResourceGroupName|O nome do grupo de recursos do Azure sob a qual a VM foi criada. |
    | RecoveryPointId|O carimbo de hora para quando a VM é recuperada. |

* Certifique-se de que a conta de automatização tem os seguintes módulos:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Todos os módulos devem ter as versões compatíveis. Uma forma fácil de garantir que todos os módulos são compatíveis é usar as versões mais recentes de todos os módulos.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Aceder a todas as VMs de VMMap num loop
Utilize o seguinte código para fazer o loop em todas as VMs de VMMap da Microsoft:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> Os recurso nome e a função de nome valores de grupo estão vazios quando o script é uma pré-ação de a um grupo de arranque. Os valores são preenchidos apenas se for concluída com êxito a VM desse grupo de ativação pós-falha. O script é uma ação posterior do grupo de arranque.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Utilizar o mesmo runbook de automatização nos vários planos de recuperação

Pode utilizar um script único nos vários planos de recuperação utilizando variáveis externas. Pode usar [variáveis de automatização do Azure](../automation/automation-variables.md) para armazenar os parâmetros que pode passar para uma execução de plano de recuperação. Ao adicionar o nome do plano de recuperação como um prefixo para a variável, pode criar variáveis individuais para cada plano de recuperação. Em seguida, utilize as variáveis como parâmetros. Pode alterar um parâmetro sem alterar o script, mas ainda alterar a forma como o script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Utilizar uma variável de cadeia de caracteres simples num script do runbook

Neste exemplo, um script assume a entrada de um grupo de segurança de rede (NSG) e o aplica as VMs de um plano de recuperação.

Para o script detetar a recuperação está a executar o plano, utilize o contexto de plano de recuperação:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Para aplicar um NSG existente, tem de saber o nome do NSG e o nome de grupo de recursos NSG. Utilize estas variáveis como entradas para os scripts de plano de recuperação. Para tal, crie duas variáveis em recursos da conta de automatização. Adicione o nome do plano de recuperação que está a criar os parâmetros para como um prefixo ao nome da variável.

1. Crie uma variável para armazenar o nome do NSG. Adicione um prefixo ao nome da variável com o nome do plano de recuperação.

    ![Criar uma variável de nome do NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Crie uma variável para armazenar o nome do grupo de recursos do NSG. Adicione um prefixo ao nome da variável com o nome do plano de recuperação.

    ![Criar um nome de grupo de recursos do NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  No script, utilize o seguinte código de referência para obter os valores das variáveis:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Utilize as variáveis no runbook para aplicar o NSG à interface de rede da VM com ativação pós-falha:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

Para cada plano de recuperação, crie variáveis independentes, para que possa reutilizar o script. Adicione um prefixo com o nome do plano de recuperação. Para obter um script completado, ponto-a-ponto para este cenário, consulte [adicionar um IP público e do NSG a VMs durante a ativação pós-falha de teste de um plano de recuperação do Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Utilizar uma variável de complexo para armazenar a obter mais informações

Considere um cenário no qual pretende que um único script para ativar a um IP público em VMs específicas. Em outro cenário, pode querer aplicar diferentes NSGs em VMs diferentes (não em todas as VMs). Pode fazer um script que é reutilizável para qualquer plano de recuperação. Cada plano de recuperação pode ter um número variável de VMs. Por exemplo, uma recuperação do SharePoint tem dois front-ends. Um aplicativo básico linha de negócio (LOB) tem apenas um front-end. Não é possível criar variáveis separadas para cada plano de recuperação.

No exemplo a seguir, usamos uma nova técnica e criar um [complexo variável](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) nos ativos de conta de automatização do Azure. Pode fazê-lo ao especificar vários valores. Tem de utilizar o Azure PowerShell para concluir os passos seguintes:

1. No PowerShell, inicie sessão sua subscrição do Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Para armazenar os parâmetros, crie a variável de complexos usando o nome do plano de recuperação:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Nesta variável de complexo **VMDetails** é o ID de VM para a VM protegida. Para obter o ID de VM, no portal do Azure, consulte as propriedades VM. Captura de ecrã seguinte mostra uma variável que armazena os detalhes de duas VMs:

    ![Utilizar o ID da VM como o GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Utilize esta variável no runbook. Se não for encontrado o GUID de VM indicado no contexto de plano de recuperação, aplicam-se o NSG na VM:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. No runbook, um loop as VMs do contexto de plano de recuperação. Verifique se a VM existe na **$VMDetailsObj**. Se existir, acessar as propriedades da variável para aplicar o NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Pode utilizar o mesmo script para planos de recuperação diferente. Introduza os parâmetros diferentes ao armazenar o valor que corresponde a um plano de recuperação em diferentes variáveis.

## <a name="sample-scripts"></a>Scripts de exemplo

Para implementar scripts de exemplo para a sua conta de automatização, clique a **implementar no Azure** botão.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Por outro exemplo, veja o vídeo seguinte. Ele demonstra como recuperar uma aplicação do WordPress de duas camadas no Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Recursos adicionais
* [O Azure Automation Run As conta de serviço](../automation/automation-create-runas-account.md)
* [Descrição geral de automatização do Azure](https://msdn.microsoft.com/library/azure/dn643629.aspx "descrição geral da automatização do Azure")
* [Scripts de exemplo de automatização do Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "scripts de exemplo da automatização do Azure")

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais](site-recovery-failover.md) sobre como executar as ativações pós-falha.
