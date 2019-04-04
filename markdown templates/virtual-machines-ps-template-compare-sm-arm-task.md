---
ms.openlocfilehash: aec6282daadc61b4e1bcf6bbaf1266d9bc98cdac
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895803"
---
<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>Utilizar o Azure PowerShell para [tarefa]
Este artigo mostra como [tarefas], utilizar comandos do módulo do Azure e o módulo do Azure Resource Manager. Isso se destina a ajudar a aprender os novos comandos, bem como migrar os scripts existentes para os novos comandos.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>Pré-requisito: Instalar uma versão recente do PowerShell do Azure
Se ainda não fez isso, instale, pelo menos, a versão [número da versão] do Azure PowerShell no seu computador local. Se utilizar uma versão anterior, ele não terá de cmdlets do Azure Resource Manager descritos neste artigo. Para obter mais detalhes, veja:

* [Como instalar e configurar o Azure PowerShell](install-configure-powershell.md) para obter instruções sobre como configurar o Azure PowerShell.
* [Com o Windows PowerShell com o Resource Manager](powershell-azure-resource-manager.md) para Noções básicas sobre como utilizar o Gestor de recursos.

> [!NOTE]
> A maioria das tarefas necessitam que utilize uma linha de comandos do PowerShell do Azure de nível de administrador.
> 
> 

## <a name="command-comparison"></a>Comparação de comando
Isso [tabela | Secção] mostra a sintaxe de comando.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

Estes exemplos de comandos, utilize as seguintes variáveis:

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in GitHub or published to Sandbox.-->

| Gestão de Serviços | Resource Manager |
| --- | --- |
| `syntax` |`syntax` |

<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->

[Frase curta introdução sobre o comando. Omita se realmente nada a dizer. Mas se utilizar a essas abordagens um pipeline, explique que]:

    [command string]

## <a name="script-examples"></a>Exemplos de script
Eis um exemplo que usa [nomes de cmdlet)] para [tarefas]. Ele inclui comandos que:

* [verbo curto, utilizações, tem, é, etc]
* [verbo curto seguinte] 

<!--include this statement if it uses variables that weren't introduced earlier--> Ele inclui as seguintes variáveis:

* [variável 1]
* [variável 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>Recursos Adicionais
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[Gerir a disponibilidade](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](https://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[Documentação das máquinas virtuais](/previous-versions/azure/jj156003(v=azure.100))

