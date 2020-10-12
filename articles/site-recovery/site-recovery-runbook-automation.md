---
title: Adicione os runbooks da Azure Automation aos planos de recuperação do local
description: Saiba como alargar os planos de recuperação com a Azure Automation para recuperação de desastres utilizando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424876"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de Automatização do Azure aos planos de recuperação

Este artigo descreve como integrar os runbooks da Azure Automation, para alargar os planos [de recuperação do local de Azure.](site-recovery-overview.md) Mostramos-lhe como automatizar tarefas básicas que de outra forma necessitariam de intervenção manual e como converter uma recuperação em várias etapas numa ação de um clique único.

## <a name="recovery-plans"></a>Planos de recuperação 

Pode utilizar planos de recuperação quando falhar com máquinas no local ou VMs Azure. Os planos de recuperação ajudam-no a definir um processo de recuperação sistemático que define como as máquinas falham e como começam e recuperam após o fracasso. 

A recuperação de grandes aplicações pode ser complexa. Os planos de recuperação ajudam a impor a ordem para que a recuperação seja consistentemente precisa, repetível e automatizada. Pode automatizar tarefas dentro de um plano de recuperação utilizando scripts, bem como livros de execução da Azure Automation. Exemplos típicos podem estar a configurar configurações num VM Azure após o failover ou a reconfigurar uma aplicação que está a ser executada no VM.

- [Saiba mais](recovery-plan-overview.md) sobre planos de recuperação.
- [Saiba mais](../automation/automation-runbook-types.md) sobre os runbooks da Azure Automation.



## <a name="runbooks-in-recovery-plans"></a>Runbooks em planos de recuperação

Adicione uma conta Esmundo Automation e runbooks a um plano de recuperação. O livro é invocado quando o plano de recuperação funciona.

- A conta Automation pode estar em qualquer região de Azure, e deve estar na mesma subscrição que o cofre de Recuperação do Site. 
- Um runbook pode funcionar num plano de recuperação durante o failover de um local primário para secundário, ou durante o recuo da localização secundária para a primária.
- Os livros de bordo de um plano de recuperação são executados em série, um após o outro, na ordem definida.
- Se os runbooks de um plano de recuperação configurar VMs para iniciar em diferentes grupos, o plano de recuperação só continuará quando a Azure reportar todos os VMs como funcionando.
- Os planos de recuperação continuam a decorrer, mesmo que um guião falhe.

### <a name="recovery-plan-context"></a>Contexto do plano de recuperação

Quando um script é executado, injeta um contexto de plano de recuperação no runbook. O contexto contém as variáveis resumidas na tabela.

| **Nome da variável** | **Descrição** |
| --- | --- |
| Nome do Plano de Recuperação |Nome do plano de recuperação. Usado em ações baseadas no nome. |
| Tipo de Failover |Especifica se é um teste ou um fracasso de produção. 
| FailoverDirection | Especifica se a recuperação é para um local primário ou secundário. |
| GrupoID |Identifica o número de grupo no plano de recuperação quando o plano está em execução. |
| VmMap |Uma série de todos os VMs do grupo. |
| Chave VMMap |Uma chave única (GUID) para cada VM. |
| SubscriptionId |O ID de assinatura Azure no qual o VM foi criado. |
| ResourceGroupName | Nome do grupo de recursos em que se encontra o VM.
| CloudServiceName |O nome de serviço em nuvem Azure sob o qual o VM foi criado. |
| RoleName |O nome do Azure VM. |
| RecoveryPointId|O tempo para a recuperação do VM. |

>[!Note]
>O valor para a variável 'FailoverDirection' será 'PrimaryToSecondary' em caso de failover e 'SecondaryToPrimary' em caso de falha.

O exemplo a seguir mostra uma variável de contexto:

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

Se pretender aceder a todos os VMs em VMMap num loop, pode utilizar o seguinte código:

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


O blog de Aman Sharma na [Harvesting Clouds](http://harvestingclouds.com) tem um exemplo útil de um roteiro de contexto de plano de [recuperação.](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)



## <a name="before-you-start"></a>Antes de começar

- Se é novo na Azure Automation, pode [inscrever-se](https://azure.microsoft.com/services/automation/) e [descarregar scripts de amostras.](https://azure.microsoft.com/documentation/scripts/)
- Certifique-se de que a conta Automation tem os seguintes módulos:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Todos os módulos devem ser de versões compatíveis. A forma mais simples é usar sempre as versões mais recentes de todos os módulos.



## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação

1. No cofre, selecione **Planos de Recuperação (Recuperação do Local)**
2. Para criar um plano de recuperação, clique em **+Plano de Recuperação.** [Saiba mais](site-recovery-create-recovery-plans.md). Se já tem um plano de recuperação, então selecione para abri-lo.
3. Na página do plano de recuperação, clique **em Personalizar**.

    ![Clique no botão Personalizar](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Clique nas elipses (...) ao lado **do Grupo 1: Iniciar**  >  **ação de publicação**.
3. In **Insert action**, verifique se o **Script** está selecionado e especifique um nome para o script **(Hello World).**
4. Especifique uma conta de automação e selecione um livro de contas. Para guardar o script, clique **em OK**. O guião é adicionado ao **Grupo 1: Pós-passos**.


## <a name="reuse-a-runbook-script"></a>Reutilizar um script de runbook

Você pode usar um único script runbook em planos de recuperação múltiplas, usando variáveis externas. 

- Utiliza [variáveis Azure Automation](../automation/shared-resources/variables.md) para armazenar parâmetros para executar um plano de recuperação.
- Ao adicionar o nome do plano de recuperação como prefixo à variável, pode criar variáveis individuais para cada plano de recuperação. Em seguida, use as variáveis como parâmetros.
- Pode alterar um parâmetro sem alterar o script, mas ainda assim alterar a forma como o script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Use uma variável de corda simples em um script de runbook

Neste exemplo, um script leva a entrada de um Grupo de Segurança de Rede (NSG) e aplica-o aos VMs num plano de recuperação. 

1. Para que o script possa detetar qual o plano de recuperação em execução, use este contexto de plano de recuperação:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Note o nome e o grupo de recursos NSG. Usa estas variáveis como entradas para scripts de plano de recuperação. 
1. Nos ativos da conta Automation. criar uma variável para armazenar o nome NSG. Adicione um prefixo ao nome variável com o nome do plano de recuperação.

    ![Criar uma variável de nome NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Crie uma variável para armazenar o nome do grupo de recursos para o recurso NSG. Adicione um prefixo ao nome variável com o nome do plano de recuperação.

    ![Criar um nome de grupo de recursos NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  No script, utilize este código de referência para obter os valores variáveis:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Utilize as variáveis no livro de aplicação do NSG à interface de rede do VM falhado:

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


Para cada plano de recuperação, crie variáveis independentes para que possa reutilizar o script. Adicione um prefixo utilizando o nome do plano de recuperação. 

Para um script completo e de ponta a ponta para este cenário, [reveja este script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Use uma variável complexa para armazenar mais informações

Em alguns cenários poderá não ser capaz de criar variáveis separadas para cada plano de recuperação. Considere um cenário em que pretende que um único script atribua um endereço IP público em VMs específicos. Noutro cenário, é melhor aplicar diferentes NSGs em VMs diferentes (não em todos os VMs). Tenha em atenção que:

- Podes fazer um guião reutilizável para qualquer plano de recuperação.
- Cada plano de recuperação pode ter um número variável de VMs.
- Por exemplo, uma recuperação do SharePoint tem duas extremidades dianteiras. Uma aplicação básica de linha de negócios (LOB) tem apenas uma extremidade frontal.
- Neste cenário não é possível criar variáveis separadas para cada plano de recuperação.

No exemplo seguinte, criamos uma [variável complexa](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) na conta Azure Automation.

Fazemos isso especificando vários valores, utilizando a Azure PowerShell.

1. Na PowerShell, inscreva-se na sua subscrição Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Para armazenar os parâmetros, crie a variável complexa utilizando o nome do plano de recuperação:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Nesta variável complexa, **vMDetails** é o VM ID para o VM protegido. Para obter o ID VM, no portal Azure, veja as propriedades VM. A imagem a seguir mostra uma variável que armazena os detalhes de dois VMs:

    ![Utilize o ID VM como GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Use esta variável no seu livro de bordo. Se o VM GUID indicado for encontrado no contexto do plano de recuperação, aplique o NSG no VM:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. No seu runbook, loop através dos VMs do contexto do plano de recuperação. Verifique se o VM existe em **$VMDetailsObj**. Se existir, aceda às propriedades da variável para aplicar o NSG:

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

Pode usar o mesmo script para diferentes planos de recuperação. Introduza diferentes parâmetros armazenando o valor que corresponde a um plano de recuperação em diferentes variáveis.

## <a name="sample-scripts"></a>Scripts de exemplo

Para implementar scripts de amostra na sua conta Dem automação, clique no botão **Implementar para Azure.**

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Este vídeo dá outro exemplo. Demonstra como recuperar uma aplicação WordPress de duas camadas para a Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre uma [Azure Automation Run As account](../automation/manage-runas-account.md)
- Reveja [os scripts da amostra da Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Saiba mais](site-recovery-failover.md) sobre execução de falhas.
