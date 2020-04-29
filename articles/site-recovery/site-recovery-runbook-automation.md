---
title: Adicione os livros de execução da Automação Azure aos planos de recuperação do site
description: Saiba como alargar os planos de recuperação com a Azure Automation para recuperação de desastres utilizando a Recuperação do Site Azure.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257488"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de Automatização do Azure aos planos de recuperação

Este artigo descreve como integrar os livros de execução da Azure Automation, para alargar os planos de recuperação do [site azure.](site-recovery-overview.md) Mostramos-lhe como automatizar tarefas básicas que de outra forma necessitariam de intervenção manual e como converter uma recuperação em várias etapas numa ação de um clique.

## <a name="recovery-plans"></a>Planos de recuperação 

Pode utilizar planos de recuperação quando falhar nas máquinas no local ou nos VMs Azure. Os planos de recuperação ajudam-no a definir um processo de recuperação sistemático que define como as máquinas falham e como começam e recuperam após a falha. 

A recuperação de grandes aplicações pode ser complexa. Os planos de recuperação ajudam a impor a ordem para que a recuperação seja consistentemente precisa, repetível e automatizada. Pode automatizar tarefas dentro de um plano de recuperação utilizando scripts, bem como livros de execução da Automação Azure. Exemplos típicos podem estar a configurar definições num VM Azure após a falha, ou a reconfigurar uma aplicação que está a funcionar no VM.

- [Saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.
- [Saiba mais](../automation/automation-runbook-types.md) sobre os livros de execução da Azure Automation.



## <a name="runbooks-in-recovery-plans"></a>Livros de execução em planos de recuperação

Adicione uma conta De Automação Azure e livros de execução a um plano de recuperação. O livro de execução é invocado quando o plano de recuperação funciona.

- A conta Automation pode estar em qualquer região do Azure, e deve estar na mesma subscrição que o cofre de Recuperação do Site. 
- Um livro de execução pode funcionar num plano de recuperação durante a falha de uma localização primária para secundária, ou durante o failback da localização secundária para a primária.
- Os livros de execução num plano de recuperação funcionam em série, um após o outro, na ordem definida.
- Se os livros de execução num plano de recuperação configurar em diferentes grupos, o plano de recuperação continuará apenas quando o Azure reportar todos os VMs como funcionando.
- Os planos de recuperação continuam a decorrer, mesmo que um guião falhe.

### <a name="recovery-plan-context"></a>Contexto do plano de recuperação

Quando um guião corre, injeta um contexto de plano de recuperação no livro de execução. O contexto contém as variáveis resumidas na tabela.

| **Nome da variável** | **Descrição** |
| --- | --- |
| Nome do Plano de Recuperação |Nome do plano de recuperação. Usado em ações baseadas no nome. |
| FailoverType |Especifica se é um teste ou falha de produção. 
| FailoverDirection | Especifica se a recuperação é para um local primário ou secundário. |
| GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução. |
| VmMap |Uma série de todos os VMs do grupo. |
| Chave VMMap |Uma chave única (GUID) para cada VM. |
| SubscriptionId |O ID de subscrição Azure no qual o VM foi criado. |
| ResourceGroupName | Nome do grupo de recursos em que se encontra o VM.
| CloudServiceName |O nome de serviço de nuvem Azure sob o qual o VM foi criado. |
| RoleName |O nome do VM Azure. |
| RecoveryPointid|A marca de tempo para a recuperação vm. |

O exemplo seguinte mostra uma variável de contexto:

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



## <a name="customize-the-recovery-plan"></a>Personalize o plano de recuperação

1. No cofre, selecione Planos de **Recuperação (Recuperação** do Local)
2. Para criar um plano de recuperação, clique em +Plano de **Recuperação**. [Saiba mais](site-recovery-create-recovery-plans.md). Se já tem um plano de recuperação, então selecione abri-lo.
3. Na página do plano de recuperação, clique em **Personalizar**.

    ![Clique no botão Personalizar](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Clique nas elipses (...) ao lado do **Grupo 1: Iniciar** > **adicionar a ação pós-**
3. Na **ação Insert,** verifique se o **Script** está selecionado e especifique um nome para o script **(Hello World**).
4. Especifique uma conta de automação e selecione um livro de execução. Para guardar o script, clique em **OK**. O guião é adicionado ao **Grupo 1: Pós-passos**.


## <a name="reuse-a-runbook-script"></a>Reutilizar um roteiro de livro de corridas

Você pode usar um único script de livro em vários planos de recuperação, usando variáveis externas. 

- Utiliza [variáveis Azure Automation](../automation/automation-variables.md) para armazenar parâmetros para executar um plano de recuperação.
- Ao adicionar o nome do plano de recuperação como prefixo à variável, pode criar variáveis individuais para cada plano de recuperação. Em seguida, use as variáveis como parâmetros.
- Pode alterar um parâmetro sem alterar o guião, mas ainda assim mudar a forma como o script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Use uma variável de cadeia simples em um script de livro de execução

Neste exemplo, um script pega na entrada de um Grupo de Segurança de Rede (NSG) e aplica-o aos VMs num plano de recuperação. 

1. Para que o script possa detetar que plano de recuperação está a decorrer, use este contexto de plano de recuperação:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Note o nome nsg e o grupo de recursos. Você usa estas variáveis como inputs para scripts de plano de recuperação. 
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

4.  Utilize as variáveis no livro de execução para aplicar o NSG à interface de rede do VM falhado:

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

Para um roteiro completo e de ponta a ponta para este cenário, reveja [este script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Use uma variável complexa para armazenar mais informações

Em alguns cenários, poderá não ser capaz de criar variáveis separadas para cada plano de recuperação. Considere um cenário em que deseja um único script para atribuir um endereço IP público em VMs específicos. Noutro cenário, é possível aplicar diferentes NSGs em diferentes VMs (não em todos os VMs). Tenha em atenção que:

- Podes fazer um guião reutilizável para qualquer plano de recuperação.
- Cada plano de recuperação pode ter um número variável de VMs.
- Por exemplo, uma recuperação do SharePoint tem duas extremidades dianteiras. Uma aplicação básica de linha de negócio (LOB) tem apenas uma extremidade frontal.
- Neste cenário não se pode criar variáveis separadas para cada plano de recuperação.

No exemplo seguinte, criamos uma [variável complexa](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) na conta Azure Automation.

Fazemos isto especificando vários valores, utilizando o Azure PowerShell.

1. Na PowerShell, inscreva-se na subscrição do Azure:

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

3. Nesta variável complexa, **vMDetails** é o ID VM para o VM protegido. Para obter o VM ID, no portal Azure, veja as propriedades VM. A imagem seguinte mostra uma variável que armazena os detalhes de dois VMs:

    ![Use o ID VM como O GUIA](media/site-recovery-runbook-automation-new/vmguid.png)

4. Use esta variável no seu livro de execução. Se o VM GUID indicado for encontrado no contexto do plano de recuperação, aplique o NSG no VM:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. No seu livro de execução, passe os VMs do contexto do plano de recuperação. Verifique se o VM existe em **$VMDetailsObj**. Se existir, aceda às propriedades da variável para aplicar o NSG:

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

Pode usar o mesmo guião para diferentes planos de recuperação. Introduza diferentes parâmetros armazenando o valor que corresponde a um plano de recuperação em diferentes variáveis.

## <a name="sample-scripts"></a>Scripts de exemplo

Para implementar scripts de amostra na sua conta Automation, clique no botão **Deploy para Azure.**

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Este vídeo dá outro exemplo. Demonstra como recuperar uma aplicação WordPress de dois níveis ao Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Passos seguintes

- Conheça uma execução de [automação Azure Como conta](../automation/automation-create-runas-account.md)
- Reveja os scripts da [amostra da Automação Azure.](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)
- [Saiba mais](site-recovery-failover.md) sobre correr falhas.



