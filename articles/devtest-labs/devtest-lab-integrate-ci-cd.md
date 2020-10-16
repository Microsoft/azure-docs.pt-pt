---
title: Integre a Azure DevTest Labs nos seus Pipelines Azure
description: Saiba como integrar a Azure DevTest Labs no seu pipelineS Azure e gasoduto de entrega
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288925"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integre a Azure DevTest Labs no seu pipeline Azure Pipelines CI/CD

Pode utilizar a extensão *Azure DevTest Labs Tasks* para integrar os seus gasodutos Azure e a entrega contínua (CI/CD) a construir e libertar oleodutos com a Azure DevTest Labs. A extensão instala várias tarefas, incluindo: 

- Criar uma VM (máquina virtual)
- Criar uma imagem personalizada a partir de uma VM
- Eliminar uma VM 

Estas tarefas facilitam, por exemplo, a implantação rápida de um VM *de imagem dourada* para uma tarefa específica de teste e, em seguida, apagam o VM quando o teste estiver terminado.

Este artigo mostra como usar Azure DevTest Labs Tasks para criar e implementar um VM, criar uma imagem personalizada e, em seguida, apagar o VM, tudo como um pipeline de lançamento. Normalmente, executaria as tarefas individualmente na sua própria construção, teste e implementação de gasodutos.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Registe-se ou inicie sessão na sua organização [Azure DevOps](https://dev.azure.com) e [crie um projeto](/vsts/organizations/projects/create-project) na organização. 
  
- Instale a extensão de tarefas Azure DevTest Labs do Visual Studio Marketplace.
  
  1. Ir para [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. **Selecione Get it free**.
  1. Selecione a sua organização Azure DevOps a partir do dropdown e selecione **Instalar**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Crie o modelo para construir um VM Azure 

Esta secção descreve como criar o modelo Azure Resource Manager que utiliza para criar um VM Azure a pedido.

1. Para criar um modelo de Gestor de Recursos na sua subscrição, siga o procedimento no [Modelo de Gestor de Recursos](devtest-lab-use-resource-manager-template.md).
   
1. Antes de gerar o modelo de Gestor de Recursos, adicione o [artefacto WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação do VM. Tarefas de implementação como *Azure File Copy* e *PowerShell em Máquinas-Alvo* precisam de acesso WinRM. O artefacto WinRM requer um nome de hospedeiro como parâmetro, que deve ser o nome de domínio totalmente qualificado (FQDN) do VM. 
   
   > [!NOTE]
   > Quando utilizar o WinRM com um endereço IP partilhado, tem de adicionar uma regra NAT para mapear uma porta externa para a porta WinRM. Não precisa da regra NAT se criar o VM com um endereço IP público.
   
   
1. Guarde o modelo para o seu computador como um ficheiro nomeado *CreateVMTemplate.jsem*.
   
1. Consulte o modelo do seu sistema de controlo de origem.

## <a name="create-a-script-to-get-vm-properties"></a>Criar um script para obter propriedades VM

Quando executa etapas de tarefa como *Azure File Copy* ou *PowerShell em Máquinas-Alvo* no pipeline de lançamento, o seguinte script recolhe os valores necessários para implementar uma aplicação num VM. Normalmente, utilizaria estas tarefas para implementar a sua aplicação num VM Azure. As tarefas requerem valores como o nome do grupo de recursos VM, endereço IP e FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar o ficheiro de script:

1. Abra um editor de texto e cole-o o seguinte script.
   
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

1. Guarde o ficheiro com um nome como *GetLabVMParams.ps1*e faça o check-in no seu sistema de controlo de fontes. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Criar um pipeline de versão no Azure Pipelines

Para criar um novo oleoduto de lançamento:

1. Na sua página de projeto Azure DevOps, selecione **Pipelines**  >  **Releases** a partir da navegação à esquerda.
1. Selecione **Novo Pipeline**.
1. Em **Selecione um modelo,** desloque-se para baixo e selecione **Trabalho Vazio**e, em seguida, selecione **Aplicar**.

### <a name="add-and-set-variables"></a>Adicionar e definir as variáveis

As tarefas do pipeline utilizam os valores atribuídos ao VM quando criou o modelo de Gestor de Recursos no portal Azure. 

Para adicionar variáveis para os valores: 

1. Na página do pipeline, selecione o **separador Variáveis.**
   
1. Para cada variável, **selecione Adicionar** e insira o nome e o valor:
   
   |Nome|Valor|
   |---|---|
   |*vmName*|Nome VM atribuído no modelo de Gestor de Recursos|
   |*nome de utilizador*|Nome de utilizador para aceder ao VM|
   |*palavra-passe*|Palavra-passe para o nome de utilizador. Selecione o ícone de bloqueio para ocultar e fixar a palavra-passe.

### <a name="create-a-devtest-labs-vm"></a>Criar um DevTest Labs VM

O próximo passo é criar a imagem dourada VM para usar para futuras implementações. Cria o VM dentro da sua instância Azure DevTest Labs utilizando a tarefa *Azure DevTest Labs Create VM.*

1. No separador **pipeline de** desbloqueio, selecione o texto hiperligado no fase **1** para ver as **tarefas de fase**e, em seguida, selecione o sinal de mais ao lado do trabalho do **+** **Agente**. 
   
1. Em **Tarefas adicionar**, selecione **Azure DevTest Labs Create VM**, e selecione **Add**. 
   
1. **Selecione Create Azure DevTest Labs VM** no painel esquerdo. 

1. No painel do lado direito, preencha o formulário da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione uma ligação de serviço ou subscrição a partir de Conexões de **Serviço Azure disponíveis** ou **Subscrições Azure disponíveis** no dropdown, e selecione **Authorize** se necessário.<br /><br />**Nota:** Para obter informações sobre a criação de uma ligação de permissões mais restritas à sua subscrição Azure, consulte [o ponto final do serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual o VM do laboratório será criado.|
   |**Nome do modelo**|Introduza o caminho e o nome completos do ficheiro de modelo que guardou para o seu repositório de código fonte. Pode utilizar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parâmetros do modelo**|Introduza os parâmetros para as variáveis que definiu anteriormente:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Variáveis de**  >  saída **ID do laboratório VM**|Introduza a variável para o ID VM de laboratório criado. Se utilizar o **laboratório**padrão, pode consultar a variável em tarefas subsequentes como *$(labVMId)*.<br /><br />Pode criar um nome diferente do padrão, mas lembre-se de usar o nome correto em tarefas subsequentes. Pode escrever o ID do VM lab no seguinte formulário:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Recolha os detalhes do DevTest Labs VM

Execute o script que criou anteriormente para recolher os detalhes do VM de DevTest Labs. 

1. No separador **pipeline de** desbloqueio, selecione o texto hiperligado no fase **1** para ver as **tarefas de fase**e, em seguida, selecione o sinal de mais ao lado do trabalho do **+** **Agente**. 
   
1. Em **Tarefas adicionar**, selecione **Azure PowerShell**e selecione **Adicionar**. 
   
1. Selecione **O script Azure PowerShell: FilePath** no painel esquerdo. 
   
1. No painel do lado direito, preencha o formulário da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Tipo de conexão Azure**|Selecione **Azure Resource Manager**.|
   |**Subscrição do Azure**|Selecione a sua ligação de serviço ou subscrição.| 
   |**Tipo de script**|Selecione **o caminho do ficheiro do script**.|
   |**Caminho do roteiro**|Introduza o caminho e o nome completos do script PowerShell que guardou no seu repositório de código fonte. Pode utilizar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumentos de script**|Insira o nome da variável *labVmId* que foi povoada pela tarefa anterior, por exemplo:<br /><br />`-labVmId '$(labVMId)'`|

O script recolhe os valores necessários e armazena-os em variáveis ambientais dentro do pipeline de libertação, para que possa facilmente referir-se a eles em etapas posteriores.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Criar uma imagem VM a partir do DevTest Labs VM

A próxima tarefa é criar uma imagem do VM recém-implantado na sua instância Azure DevTest Labs. Em seguida, pode utilizar a imagem para criar cópias do VM a pedido sempre que quiser executar uma tarefa dev ou realizar alguns testes. 

1. No separador **pipeline de** desbloqueio, selecione o texto hiperligado no fase **1** para ver as **tarefas de fase**e, em seguida, selecione o sinal de mais ao lado do trabalho do **+** **Agente**. 
   
1. Em **Tarefas adicionar**, selecione **Azure DevTest Labs Create Custom Image**, e selecione **Add**. 
   
1. Configure a tarefa da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione a sua ligação de serviço ou subscrição.|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual a imagem será criada.|
   |**Nome de imagem personalizado**|Insira um nome para a imagem personalizada.|
   |**Descrição** (opcional)|Introduza uma descrição para facilitar a seleção da imagem correta mais tarde.|
   |**Laboratório de Origem VM**  >  **Source Lab VM ID**|Se alterar o nome predefinido da variável LabVMId, insira-o aqui. O valor predefinido é **de $(labVMId)**.|
   |**Variáveis de**  >  saída **ID de imagem personalizada**|Pode editar o nome predefinido da variável, se necessário.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Implemente a sua aplicação no DevTest Labs VM (opcional)

Pode adicionar tarefas para implementar a sua aplicação no novo VM da DevTest Labs. As tarefas que normalmente utiliza para implementar a aplicação são *Azure File Copy* e *PowerShell em Máquinas-Alvo*.

A informação VM que necessita para os parâmetros destas tarefas é armazenada em três variáveis de configuração chamadas **labVmRgName**, **labVMIpAddress**e **labVMFqdn** dentro do pipeline de libertação. Se quiser apenas experimentar a criação de um VM DevTest Labs e uma imagem personalizada, sem implementar uma aplicação para a presente, pode saltar este passo.

### <a name="delete-the-vm"></a>Elimine a VM

A tarefa final é eliminar o VM que implementou na sua instância Azure DevTest Labs. Normalmente, apagaria o VM depois de executar as tarefas de dev ou executar os testes de que necessita no VM implantado. 

1. No separador **pipeline de** desbloqueio, selecione o texto hiperligado no fase **1** para ver as **tarefas de fase**e, em seguida, selecione o sinal de mais ao lado do trabalho do **+** **Agente**. 
   
1. Em **Tarefas adicionar**, selecione **Azure DevTest Labs Delete VM**, e selecione **Add**. 
   
1. Configure a tarefa da seguinte forma:
   
   - Sob **Subscrição Azure RM,** selecione a sua ligação de serviço ou subscrição. 
   - Para **o ID de VM do laboratório,** se alterar o nome padrão da variável LabVMId, insira-o aqui. O valor predefinido é **de $(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Salvar o gasoduto de libertação

Para salvar o novo oleoduto de lançamento:

1. Selecione o nome **Novo pipeline de lançamento** na página do pipeline de lançamento e introduza um novo nome para o pipeline. 
   
1. Selecione o ícone **Guardar** na parte superior direita. 

## <a name="create-and-run-a-release"></a>Criar e executar um lançamento

Para criar e executar um desbloqueio utilizando o novo oleoduto:

1. Selecione **Criar desbloqueio** na parte superior direita na página do pipeline de lançamento. 
   
1. Sob **Artefactos,** selecione a construção mais recente e, em seguida, **selecione Criar**.
   
1. Em cada fase de lançamento, refresque a visão da sua instância DevTest Labs no portal Azure para ver a criação de VM, criação de imagem e eliminação de VM.

Pode utilizar a imagem personalizada para criar VMs sempre que precisar.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar ambientes multi-VM com modelos de Gestor de Recursos.](devtest-lab-create-environment-from-arm.md)
- Explore modelos mais rápidos de Gestor de Recursos para automação de DevTest Labs a partir do [repo de DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- Se necessário, consulte a página [de resolução de problemas do Azure DevOps.](/azure/devops/pipelines/troubleshooting)
 
