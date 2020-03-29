---
title: Integre os Laboratórios Azure DevTest nos seus Oleodutos Azure
description: Saiba como integrar o Azure DevTest Labs no seu pipelines Azure de integração contínua e gasoduto de entrega
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293220"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integre o Azure DevTest Labs no seu pipeline CI/CD de Pipelines Azure

Pode utilizar a extensão de *Tarefas DevTest Labs Azure* para integrar os seus oleodutos Azure de integração contínua e construção contínua (CI/CD) e oleodutos de lançamento com a Azure DevTest Labs. A extensão instala várias tarefas, incluindo: 

- Criar uma VM (máquina virtual)
- Criar uma imagem personalizada a partir de uma VM
- Eliminar uma VM 

Estas tarefas facilitam, por exemplo, a implementação rápida de um VM de *imagem dourada* para uma tarefa de teste específica e, em seguida, eliminar o VM quando o teste estiver terminado.

Este artigo mostra como usar as Tarefas Azure DevTest Labs para criar e implementar um VM, criar uma imagem personalizada e, em seguida, eliminar o VM, tudo como um oleoduto de lançamento. Normalmente, executaria as tarefas individualmente na sua própria construção, teste e implantação de oleodutos.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Registe-se ou inicie sessão na sua organização [Azure DevOps](https://dev.azure.com) e [crie um projeto](/vsts/organizations/projects/create-project) na organização. 
  
- Instale a extensão de tarefas do Azure DevTest Labs do Visual Studio Marketplace.
  
  1. Vá a [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selecione **Get it free**.
  1. Selecione a sua organização Azure DevOps a partir do dropdown e selecione **Instalar**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Crie o modelo para construir um VM Azure 

Esta secção descreve como criar o modelo de Gestor de Recursos Azure que utiliza para criar um VM Azure a pedido.

1. Para criar um modelo de Gestor de Recursos na sua subscrição, siga o procedimento em [Utilizar um modelo](devtest-lab-use-resource-manager-template.md)de Gestor de Recursos .
   
1. Antes de gerar o modelo de Gestor de Recursos, adicione o [artefacto WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação do VM. Tarefas de implementação como *Azure File Copy* e *PowerShell em Target Machines* precisam de acesso WinRM. O artefacto WinRM requer um nome de anfitrião como parâmetro, que deve ser o nome de domínio totalmente qualificado (FQDN) do VM. 
   
   > [!NOTE]
   > Quando utilizar o WinRM com um endereço IP partilhado, deve adicionar uma regra NAT para mapear uma porta externa à porta WinRM. Não precisa da regra NAT se criar o VM com um endereço IP público.
   
   
1. Guarde o modelo para o seu computador como um ficheiro chamado *CreateVMTemplate.json*.
   
1. Verifique o modelo no seu sistema de controlo de origem.

## <a name="create-a-script-to-get-vm-properties"></a>Crie um script para obter propriedades VM

Quando executa passos de tarefa como O Cópia de *Ficheiros Azure* ou *PowerShell em Máquinas-Alvo* no pipeline de lançamento, o seguinte script recolhe os valores que precisa para implementar uma aplicação para um VM. Normalmente utilizaria estas tarefas para implementar a sua aplicação num VM Azure. As tarefas requerem valores como o nome do grupo de recursos VM, endereço IP e FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar o ficheiro script:

1. Abra um editor de texto e cola o seguinte script nele.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Guarde o ficheiro com um nome como *GetLabVMParams.ps1*e faça o check-in no seu sistema de controlo de origem. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Criar um pipeline de versão no Azure Pipelines

Para criar um novo oleoduto de lançamento:

1. A partir da sua página de projeto Azure DevOps, selecione **Pipelines** > **Desbloqueios** da navegação esquerda.
1. Selecione **Novo Pipeline**.
1. Em **selecione um modelo,** desloque-se para baixo e selecione **trabalho vazio,** e depois selecione **Aplicar**.

### <a name="add-and-set-variables"></a>Adicionar e definir as variáveis

As tarefas do pipeline utilizam os valores que atribuiu ao VM quando criou o modelo de Gestor de Recursos no portal Azure. 

Para adicionar variáveis para os valores: 

1. Na página do pipeline, selecione o separador **Variáveis.**
   
1. Para cada variável, selecione **Adicionar** e introduzir o nome e o valor:
   
   |Nome|Valor|
   |---|---|
   |*vmName*|Nome VM atribuído no modelo de Gestor de Recursos|
   |*userName*|Nome de utilizador para aceder ao VM|
   |*palavra-passe*|Palavra-passe para o nome de utilizador. Selecione o ícone de bloqueio para ocultar e proteger a palavra-passe.

### <a name="create-a-devtest-labs-vm"></a>Criar um VM DevTest Labs

O próximo passo é criar a imagem dourada VM para usar para futuras implementações. Cria o VM dentro da sua instância Azure DevTest Labs utilizando a tarefa *Azure DevTest Labs Create VM.*

1. No separador **pipeline de** lançamento, selecione o texto hiperligado na fase **+** **1** para **ver tarefas**de fase 1 e, em seguida, selecione o sinal de mais ao lado da **função do Agente**. 
   
1. Em **funções adicionais,** selecione **Azure DevTest Labs Create VM**, e selecione **Adicionar**. 
   
1. Selecione **Create Azure DevTest Labs VM** no painel esquerdo. 

1. No painel do lado direito, preencha o formulário da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione uma ligação de serviço ou subscrição das **ligações de serviço azure disponíveis** ou **subscrições disponíveis** do Azure no dropdown, e selecione **Autorizar** se necessário.<br /><br />**Nota:** Para obter informações sobre a criação de uma ligação de permissões mais restrita à sua subscrição Azure, consulte o ponto final do [serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual o laboratório VM será criado.|
   |**Nome do modelo**|Introduza o caminho completo e o nome do ficheiro do modelo que guardou no seu repositório de código fonte. Pode utilizar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parâmetros de modelo**|Introduza os parâmetros para as variáveis que definiu anteriormente:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Variáveis** > de saída**Lab VM ID**|Introduza a variável para o ID VM do laboratório criado. Se utilizar o **labVMId**predefinido, pode referir-se à variável em tarefas subsequentes como *$(labVMId)*.<br /><br />Pode criar um nome diferente do predefinido, mas lembre-se de usar o nome correto em tarefas posteriores. Pode escrever o ID VM do Laboratório no seguinte formulário:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Recolher os detalhes do VM de Laboratórios DevTest

Execute o guião que criou anteriormente para recolher os detalhes do VM de Laboratórios DevTest. 

1. No separador **pipeline de** lançamento, selecione o texto hiperligado na fase **+** **1** para **ver tarefas**de fase 1 e, em seguida, selecione o sinal de mais ao lado da **função do Agente**. 
   
1. Em **funções de Adicionar,** selecione **Azure PowerShell**, e selecione **Adicionar**. 
   
1. Selecione **script Azure PowerShell: FilePath** no painel esquerdo. 
   
1. No painel do lado direito, preencha o formulário da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Tipo de ligação azure**|Selecione **Gestor de Recursos Azure**.|
   |**Assinatura Azure**|Selecione a sua ligação de serviço ou subscrição.| 
   |**Tipo de script**|Selecione **Script File Path**.|
   |**Caminho do Script**|Introduza o caminho completo e o nome do script PowerShell que guardou no seu repositório de código fonte. Pode utilizar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumentos do Script**|Introduza o nome da variável *labVmId* que foi povoada pela tarefa anterior, por exemplo:<br /><br />`-labVmId '$(labVMId)'`|

O script recolhe os valores necessários e armazena-os em variáveis ambientais dentro do pipeline de lançamento, para que possa facilmente encaminhá-los em etapas posteriores.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Crie uma imagem VM do VM DevTest Labs

A próxima tarefa é criar uma imagem do VM recém-implantado no seu caso Azure DevTest Labs. Em seguida, pode utilizar a imagem para criar cópias do VM a pedido sempre que quiser executar uma tarefa de dev ou executar alguns testes. 

1. No separador **pipeline de** lançamento, selecione o texto hiperligado na fase **+** **1** para **ver tarefas**de fase 1 e, em seguida, selecione o sinal de mais ao lado da **função do Agente**. 
   
1. Em funções **adicionais,** **selecione Azure DevTest Labs Criar Imagem Personalizada**, e selecione **Adicionar**. 
   
1. Configure a tarefa da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione a sua ligação de serviço ou subscrição.|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual a imagem será criada.|
   |**Nome de imagem personalizado**|Introduza um nome para a imagem personalizada.|
   |**Descrição** (opcional)|Introduza uma descrição para facilitar a seleção da imagem correta mais tarde.|
   |**Source Lab VM** > **Source Lab VM ID**|Se alterar o nome padrão da variável LabVMId, insira-o aqui. O valor predefinido é **$(labVMId)**.|
   |**Id** > **de imagem personalizado** variáveis de saída|Pode editar o nome padrão da variável, se necessário.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Implemente a sua aplicação para o VM DevTest Labs (opcional)

Pode adicionar tarefas para implementar a sua aplicação no novo VM DevTest Labs. As tarefas que normalmente utiliza para implementar a aplicação são *a Azure File Copy* e a *PowerShell em Target Machines*.

A informação VM de que necessita para os parâmetros destas tarefas é armazenada em três variáveis de configuração chamadas **labVmRgName,** **labVMIpAddress**e **labVMFqdn** dentro do pipeline de libertação. Se quiser experimentar apenas a criação de um VM DevTest Labs e uma imagem personalizada, sem implementar uma aplicação para ele, pode saltar este passo.

### <a name="delete-the-vm"></a>Elimine a VM

A tarefa final é eliminar o VM que implementou na sua instância Azure DevTest Labs. Normalmente, eliminaria o VM depois de executar as tarefas de dev ou executar os testes necessários no VM implantado. 

1. No separador **pipeline de** lançamento, selecione o texto hiperligado na fase **+** **1** para **ver tarefas**de fase 1 e, em seguida, selecione o sinal de mais ao lado da **função do Agente**. 
   
1. Em **funções adicionais,** selecione **Azure DevTest Labs Delete VM**, e selecione **Adicionar**. 
   
1. Configure a tarefa da seguinte forma:
   
   - No âmbito da **subscrição Azure RM,** selecione a sua ligação de serviço ou subscrição. 
   - Para **o ID VM do Laboratório,** se alterar o nome padrão da variável LabVMId, insira-o aqui. O valor predefinido é **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Salve o gasoduto de libertação

Para salvar o novo oleoduto de lançamento:

1. Selecione o nome **Novo oleoduto** de lançamento na página do gasoduto de lançamento e introduza um novo nome para o pipeline. 
   
1. Selecione o ícone **Guardar** na parte superior direita. 

## <a name="create-and-run-a-release"></a>Criar e executar um lançamento

Para criar e executar uma libertação usando o novo oleoduto:

1. Selecione **Criar a libertação** na parte superior direita na página do gasoduto de lançamento. 
   
1. Em **Artefactos,** selecione a última construção e, em seguida, selecione **Criar**.
   
1. Em cada fase de lançamento, refresque a vista do seu devTest Labs no portal Azure para ver a criação vM, criação de imagem e eliminação vM.

Pode utilizar a imagem personalizada para criar VMs sempre que precisar.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar ambientes multi-VM com modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos .
- Explore modelos mais rápidos de Gestor de Recursos para automatização de DevTest Labs do [público DevTest Labs GitHub repo](https://github.com/Azure/azure-quickstart-templates).
- Se necessário, consulte a página de resolução de [problemas do Azure DevOps.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
