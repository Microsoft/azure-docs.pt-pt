---
title: Integrar o Azure DevTest Labs no seu pipeline de integração e entrega contínua Pipelines do Azure | Documentos da Microsoft
description: Saiba como integrar o Azure DevTest Labs no seu pipeline de entrega de integração Pipelines do Azure e
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 20ba297d22e26aa8c7e20db300173f12582d257e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224474"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrar o Azure DevTest Labs ao seu pipeline Azure Pipelines CI/CD

Você pode usar a extensão de *tarefas de Azure DevTest Labs* para integrar Azure pipelines seus pipelines de compilação e versão de CI/CD (integração contínua e entrega contínua) com Azure DevTest Labs. A extensão instala várias tarefas, incluindo: 

- Criar uma VM (máquina virtual)
- Criar uma imagem personalizada a partir de uma VM
- Eliminar uma VM 

Essas tarefas facilitam, por exemplo, a implantação rápida de uma VM de *imagem Golden* para uma tarefa de teste específica e, em seguida, a exclusão da VM quando o teste é concluído.

Este artigo mostra como usar Azure DevTest Labs tarefas para criar e implantar uma VM, criar uma imagem personalizada e, em seguida, excluir a VM, tudo como um pipeline de liberação. Normalmente, você executaria as tarefas individualmente em seus próprios pipelines personalizados de compilação, teste e implantação.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Registre-se ou faça logon em sua organização [DevOps do Azure](https://dev.azure.com) e [crie um projeto](/vsts/organizations/projects/create-project) na organização. 
  
- Instale a extensão de tarefas de Azure DevTest Labs de Visual Studio Marketplace.
  
  1. Aceda a [tarefas de laboratórios DevTest do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selecione **obter gratuitamente**.
  1. Selecione sua organização do Azure DevOps na lista suspensa e selecione **instalar**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Criar o modelo para compilar uma VM do Azure 

Esta seção descreve como criar o modelo de Azure Resource Manager que você usa para criar uma VM do Azure sob demanda.

1. Para criar um modelo do Resource Manager em sua assinatura, siga o procedimento em [usar um modelo do Resource Manager](devtest-lab-use-resource-manager-template.md).
   
1. Antes de gerar o modelo do Resource Manager, adicione a [artefacto de WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação da VM. Tarefas de implantação como a *cópia de arquivos do Azure* e o *PowerShell em computadores de destino precisam de* acesso do WinRM. O artefato do WinRM requer um nome de host como um parâmetro, que deve ser o FQDN (nomes de domínio totalmente qualificado) da VM. 
   
   > [!NOTE]
   > Quando utiliza WinRM com um endereço IP partilhado, tem de adicionar uma regra NAT para mapear uma porta externa para a porta de WinRM. Você não precisará da regra NAT se criar a VM com um endereço IP público.
   
   
1. Salve o modelo em seu computador como um arquivo chamado *CreateVMTemplate. JSON*.
   
1. Faça check-in no modelo para o sistema de controle do código-fonte.

## <a name="create-a-script-to-get-vm-properties"></a>Criar um script para obter as propriedades da VM

Quando você executa etapas de tarefa como *cópia de arquivo do Azure* ou *PowerShell em computadores de destino* no pipeline de liberação, o script a seguir coleta os valores necessários para implantar um aplicativo em uma VM. Normalmente, você usaria essas tarefas para implantar seu aplicativo em uma VM do Azure. As tarefas exigem valores como o nome do grupo de recursos da VM, o endereço IP e o FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar o arquivo de script:

1. Abra um editor de texto e cole o seguinte script para o mesmo.
   
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

1. Salve o arquivo com um nome como *GetLabVMParams. ps1*e verifique-o em seu sistema de controle do código-fonte. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Criar um pipeline de versão no Azure Pipelines

Para criar um novo pipeline de lançamento:

1. Na página do projeto DevOps do Azure, selecione**versões** de **pipelines** > no painel de navegação esquerdo.
1. Selecione **novo pipeline**.
1. Em **selecionar um modelo**, role para baixo e selecione **trabalho vazio**e, em seguida, selecione **aplicar**.

### <a name="add-and-set-variables"></a>Adicionar e definir variáveis

As tarefas de pipeline usam os valores atribuídos à VM quando você criou o modelo do Resource Manager no portal do Azure. 

Para adicionar variáveis para os valores: 

1. Na página pipeline, selecione a guia **variáveis** .
   
1. Para cada variável, selecione **Adicionar** e insira o nome e o valor:
   
   |Name|Value|
   |---|---|
   |*vmName*|Nome da VM que você atribuiu no modelo do Resource Manager|
   |*Usu*|Nome de usuário para acessar a VM|
   |*password*|Senha para o nome de usuário. Selecione o ícone de cadeado para ocultar e proteger a senha.

### <a name="create-a-devtest-labs-vm"></a>Criar uma VM do DevTest Labs

A próxima etapa é criar a VM de imagem dourada a ser usada para implantações futuras. Você cria a VM dentro de sua instância de Azure DevTest Labs usando a tarefa *Azure DevTest Labs criar VM* .

1. Na guia **pipeline** de lançamento de pipeline, selecione o texto de hiperlink no **estágio 1** para **Exibir tarefas de estágio**e, em seguida, selecione **+** o sinal de adição ao lado de **trabalho do agente**. 
   
1. Em **adicionar tarefas**, selecione **Azure DevTest Labs criar VM**e selecione **Adicionar**. 
   
1. Selecione **criar Azure DEVTEST Labs VM** no painel esquerdo. 

1. No painel direito, preencha o formulário da seguinte maneira:
   
   |Campo|Value|
   |---|---|
   |**Assinatura do Azure RM**|Selecione uma conexão de serviço ou assinatura de **conexões de serviço do Azure disponíveis** ou **assinaturas do Azure disponíveis** no menu suspenso e selecione **autorizar** se necessário.<br /><br />**Nota:** Para obter informações sobre como criar uma conexão de permissões mais restritas para sua assinatura do Azure, consulte [Azure Resource Manager ponto de extremidade de serviço](/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual a VM do laboratório será criada.|
   |**Nome do modelo**|Insira o caminho completo e o nome do arquivo de modelo que você salvou em seu repositório de código-fonte. Você pode usar propriedades internas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parâmetros de modelo**|Insira os parâmetros para as variáveis que você definiu anteriormente:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   | > **ID da VM do laboratório** das variáveis de saída|Insira a variável para a ID da VM do laboratório criada. Se você usar o **labVMId**padrão, poderá consultar a variável nas tarefas subsequentes como *$ (labVMId)* .<br /><br />Você pode criar um nome diferente do padrão, mas lembre-se de usar o nome correto nas tarefas subsequentes. Você pode escrever a ID da VM do laboratório no seguinte formato:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Coletar os detalhes da VM do DevTest Labs

Execute o script que criou anteriormente para recolher os detalhes da VM de laboratórios DevTest. 

1. Na guia **pipeline** de lançamento de pipeline, selecione o texto de hiperlink no **estágio 1** para **Exibir tarefas de estágio**e, em seguida, selecione **+** o sinal de adição ao lado de **trabalho do agente**. 
   
1. Em **adicionar tarefas**, selecione **Azure PowerShell**e selecione **Adicionar**. 
   
1. Selecione **Azure PowerShell script: FilePath** no painel esquerdo. 
   
1. No painel direito, preencha o formulário da seguinte maneira:
   
   |Campo|Value|
   |---|---|
   |**Tipo de conexão do Azure**|Selecione **Azure Resource Manager**.|
   |**Subscrição do Azure**|Selecione sua conexão de serviço ou assinatura.| 
   |**Tipo de script**|Selecione o **caminho do arquivo de script**.|
   |**Caminho do script**|Insira o caminho completo e o nome do script do PowerShell que você salvou em seu repositório de código-fonte. Você pode usar propriedades internas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumentos de script**|Insira o nome da variável *labVmId* que foi populada pela tarefa anterior, por exemplo:<br /><br />`-labVmId '$(labVMId)'`|

O script coleta os valores necessários e os armazena em variáveis de ambiente dentro do pipeline de lançamento, para que você possa consultá-los facilmente nas etapas subsequentes.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Criar uma imagem de VM da VM do DevTest Labs

A próxima tarefa é criar uma imagem da VM implantada recentemente na sua instância do Azure DevTest Labs. Em seguida, pode utilizar a imagem para criar cópias da VM a pedido, sempre que pretender executar uma tarefa de desenvolvimento ou de executar alguns testes. 

1. Na guia **pipeline** de lançamento de pipeline, selecione o texto de hiperlink no **estágio 1** para **Exibir tarefas de estágio**e, em seguida, selecione **+** o sinal de adição ao lado de **trabalho do agente**. 
   
1. Em **adicionar tarefas**, selecione **Azure DevTest Labs criar imagem personalizada**e selecione **Adicionar**. 
   
1. Configure a tarefa da seguinte forma:
   
   |Campo|Value|
   |---|---|
   |**Assinatura do Azure RM**|Selecione sua conexão de serviço ou assinatura.|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual a imagem será criada.|
   |**Nome da imagem personalizada**|Insira um nome para a imagem personalizada.|
   |**Descrição** do adicional|Insira uma descrição para facilitar a seleção da imagem correta mais tarde.|
   | > **ID da VM** do laboratório de origem da VM do laboratório de origem|Se você alterou o nome padrão da variável LabVMId, insira-o aqui. O valor predefinido é **$(labVMId)** .|
   | > **ID da imagem personalizada** das variáveis de saída|Você pode editar o nome padrão da variável, se necessário.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Implantar seu aplicativo na VM do DevTest Labs (opcional)

Você pode adicionar tarefas para implantar seu aplicativo na nova VM do DevTest Labs. As tarefas que você geralmente usa para implantar o aplicativo são *cópia de arquivo do Azure* e *PowerShell em computadores de destino*.

As informações de VM necessárias para os parâmetros dessas tarefas são armazenadas em três variáveis de configuração chamadas **labVmRgName**, **labVMIpAddress**e **labVMFqdn** no pipeline de lançamento. Se apenas pretender experimentar com a criação de uma VM de laboratórios Dev/Test e de uma imagem personalizada, sem ter de implementar uma aplicação, pode ignorar este passo.

### <a name="delete-the-vm"></a>Elimine a VM

A tarefa final é excluir a VM implantada em sua instância de Azure DevTest Labs. Normalmente seria elimine a VM depois de executar as tarefas de desenvolvimento ou executar os testes que tem na VM implementada. 

1. Na guia **pipeline** de lançamento de pipeline, selecione o texto de hiperlink no **estágio 1** para **Exibir tarefas de estágio**e, em seguida, selecione **+** o sinal de adição ao lado de **trabalho do agente**. 
   
1. Em **adicionar tarefas**, selecione **Azure DevTest Labs excluir VM**e selecione **Adicionar**. 
   
1. Configure a tarefa da seguinte forma:
   
   - Em **assinatura do Azure RM**, selecione sua conexão de serviço ou assinatura. 
   - Para **ID da VM do laboratório**, se você alterou o nome padrão da variável LabVMId, insira-o aqui. O valor predefinido é **$(labVMId)** .
   
### <a name="save-the-release-pipeline"></a>Salvar o pipeline de liberação

Para salvar o novo pipeline de lançamento:

1. Selecione o nome **novo pipeline de liberação** na página pipeline de liberação e insira um novo nome para o pipeline. 
   
1. Selecione o ícone **salvar** no canto superior direito. 

## <a name="create-and-run-a-release"></a>Criar e executar uma versão

Para criar e executar uma versão usando o novo pipeline:

1. Selecione **criar versão** no canto superior direito na página do pipeline de liberação. 
   
1. Em **artefatos**, selecione a compilação mais recente e, em seguida, selecione **criar**.
   
1. Em cada estágio de lançamento, atualize a exibição da instância do DevTest Labs no portal do Azure para exibir a criação da VM, criação de imagem e exclusão de VM.

Você pode usar a imagem personalizada para criar VMs sempre que precisar delas.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar ambientes multi-VM com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
- Explore mais modelos do Resource Manager de início rápido para automatização de DevTest Labs dos [repositório público do GitHub de laboratórios DevTest](https://github.com/Azure/azure-quickstart-templates).
- Se necessário, consulte a página de [solução de problemas do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) .
 
