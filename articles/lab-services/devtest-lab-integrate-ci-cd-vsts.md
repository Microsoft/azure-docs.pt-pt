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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7806599c1a2f1396ff4b07d6f0538057654029d7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738526"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Integrar o Azure DevTest Labs no seu pipeline de entrega de integração do Azure DevOps e
Pode utilizar o *do Azure DevTest Labs tarefas* extensão facilmente instalado em DevOps do Azure para integrar o seu pipeline de compilação e versão de CI/CD com o Azure DevTest Labs. A extensão instala três tarefas: 
* Criar uma VM
* Criar uma imagem personalizada a partir de uma VM
* Eliminar uma VM 

O processo torna mais fácil para, por exemplo, implementar rapidamente uma "imagem dourada" para uma tarefa de teste específico e, em seguida, elimine-o quando o teste for concluído.

Este artigo mostra como criar e implementar uma VM, criar uma imagem personalizada e, em seguida, elimine a VM, todos como um pipeline completo. Normalmente executará cada tarefa individualmente em seu próprio pipeline de compilação-teste-implementação personalizada.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar
Pode integrar o seu pipeline CI/CD com o Azure DevTest Labs, tem de instalar a extensão do Visual Studio Marketplace.
1. Aceda a [tarefas de laboratórios DevTest do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Selecione **Instalar**.
1. Conclua o assistente.

## <a name="create-a-resource-manager-template"></a>Criar um modelo do Resource Manager
Esta secção descreve como criar o modelo Azure Resource Manager que utilizar para criar uma máquina virtual do Azure a pedido.

1. Para criar um modelo do Resource Manager na sua subscrição, conclua o procedimento [utilizar um modelo do Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Antes de gerar o modelo do Resource Manager, adicione a [artefacto de WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação da VM.

   É necessário acesso de WinRM para utilizar como tarefas de implantação *cópia de ficheiros do Azure* e *PowerShell em máquinas de destino*.

   > [!NOTE]
   > Quando utiliza WinRM com um endereço IP partilhado, tem de adicionar uma regra NAT para mapear uma porta externa para a porta de WinRM. Este passo não é necessário se criar a VM com um endereço IP público.
   >
   >

1. Guarde o modelo como um ficheiro no seu computador. Nomeie o arquivo **CreateVMTemplate.json**.
1. Verifique o modelo seu sistema de controle de origem.
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

1. Verifique o script seu sistema de controle de origem. Um nome semelhante **GetLabVMParams.ps1**.

   Quando executar esse script no agente como parte do pipeline de versões e se usar como passos de tarefas *cópia de ficheiros do Azure* ou *PowerShell em máquinas de destino*, o script recolhe os valores que precisa Implemente a sua aplicação para a VM. Usaria normalmente estas tarefas para implementar aplicações numa VM do Azure. As tarefas exigem valores, como o nome do grupo de recursos de VM, o endereço IP e o nome de domínio completamente qualificado (FQDN).

## <a name="create-a-release-pipeline-in-release-management"></a>Criar um pipeline de lançamento no gerenciamento de lançamentos
Para criar o pipeline de lançamento, faça o seguinte:

1. Sobre o **versões** separador da **compilação e versão** hub, selecione o botão do sinal de adição (+).
1. Na **Criar definição de versão** janela, selecione a **vazia** modelo e, em seguida, selecione **seguinte**.
1. Selecione **escolha mais tarde**e, em seguida, selecione **criar** para criar um novo pipeline de lançamento com o ambiente de um padrão e não ligados artefactos.
1. Para abrir o menu de atalho, no novo pipeline de lançamento, selecione as reticências (...) junto ao nome do ambiente e, em seguida, selecione **configurar variáveis**. 
1. Na **configurar - ambiente** janela, para as variáveis que utilize as tarefas de pipeline de lançamento, introduza os seguintes valores:

   a. Para **vmName**, introduza o nome que atribuiu à VM quando criou o modelo do Resource Manager no portal do Azure.

   b. Para **nome de utilizador**, introduza o nome de utilizador que atribuiu à VM quando criou o modelo do Resource Manager no portal do Azure.

   c. Para **palavra-passe**, introduza a palavra-passe que atribuiu à VM quando criou o modelo do Resource Manager no portal do Azure. Utilize o ícone de "cadeado" para ocultar e proteger a palavra-passe.

### <a name="create-a-vm"></a>Criar uma VM

A próxima fase da implementação é criar a VM para utilizar como a "imagem dourada" para implementações subsequentes. Criar a VM na sua instância do laboratório DevTest do Azure, utilizando a tarefa que é especialmente desenvolvida para esta finalidade. 

1. No pipeline de versões, selecione **adicionar tarefas**.
1. Sobre o **implementar** separador, adicione um *Azure DevTest Labs criar VM* tarefas. Configure a tarefa da seguinte forma:

   > [!NOTE]
   > Para criar a VM a utilizar para implementações subsequentes, veja [tarefas de Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **subscrição do Azure RM**, selecione uma ligação no **ligações de serviço do Azure disponíveis** listar ou criar uma ligação de permissões mais restrita a sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Para **nome de laboratório**, selecione o nome da instância que criou anteriormente.

   c. Para **nome do modelo**, introduza o caminho completo e nome do ficheiro de modelo que guardou para o seu repositório de código de origem. Pode utilizar as propriedades internas de gerenciamento de liberação para simplificar o caminho, por exemplo:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Para **parâmetros de modelo**, introduza os parâmetros para as variáveis que são definidas no modelo. Utilize os nomes das variáveis que definiu no ambiente, por exemplo:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Para **variáveis de saída - ID de VM de laboratório**, precisa do ID da VM criada recentemente para os passos subsequentes. Definir o nome predefinido da variável de ambiente que é preenchido automaticamente com esse ID na **variáveis de saída** secção. Pode editar a variável, se necessário, mas não se esqueça de utilizar o nome correto nas tarefas subseqüentes. O ID de VM de laboratório é escrito no seguinte formato:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. Execute o script que criou anteriormente para recolher os detalhes da VM de laboratórios DevTest. 
1. No pipeline de versões, selecione **adicionar tarefas** e, em seguida, no **Deploy** separador, adicione uma *Azure PowerShell* tarefas. Configure a tarefa da seguinte forma:

   > [!NOTE]
   > Para recolher os detalhes da VM de laboratórios Dev/Test, veja [implementar: O Azure PowerShell](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3) e execute o script.

   a. Para **tipo de ligação do Azure**, selecione **do Azure Resource Manager**.

   b. Para **subscrição do Azure RM**, selecione uma ligação a partir da lista sob **ligações de serviço do Azure disponíveis**, ou crie uma ligação de permissões mais restrita à sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   c. Para **tipo de Script**, selecione **ficheiro de Script**.
 
   d. Para **caminho do Script**, introduza o caminho completo e nome do script que guardou para o seu repositório de código de origem. Pode utilizar as propriedades internas de gerenciamento de liberação para simplificar o caminho, por exemplo:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Para **argumentos de Script**, introduza o nome da variável de ambiente que foi preenchido automaticamente com o ID do laboratório de VM pela tarefa anterior, por exemplo: 
      ```
      -labVmId '$(labVMId)'
      ```
    O script recolhe os valores necessários e armazena-os nas variáveis de ambiente no pipeline de versões para que possa facilmente consultá-las nos passos subsequentes.

1. Implemente a sua aplicação para a nova VM de laboratórios DevTest. As tarefas normalmente utiliza para implementar a aplicação são *cópia de ficheiros do Azure* e *PowerShell em máquinas de destino*.
   As informações sobre a VM precisa para os parâmetros destas tarefas são armazenadas em três variáveis de configuração com o nome **labVmRgName**, **labVMIpAddress**, e **labVMFqdn**dentro do pipeline de versões. Se apenas pretender experimentar com a criação de uma VM de laboratórios Dev/Test e de uma imagem personalizada, sem ter de implementar uma aplicação, pode ignorar este passo.

### <a name="create-an-image"></a>Criar uma imagem

A fase seguinte é criar uma imagem da VM recentemente implementada na sua instância do Azure DevTest Labs. Em seguida, pode utilizar a imagem para criar cópias da VM a pedido, sempre que pretender executar uma tarefa de desenvolvimento ou de executar alguns testes. 

1. No pipeline de versões, selecione **adicionar tarefas**.
1. Sobre o **Deploy** separador, adicione um **Azure DevTest Labs criar Custom Image** tarefas. Configure da seguinte forma:

   > [!NOTE]
   > Para criar a imagem, veja [tarefas de Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **subscrição do Azure RM**, na **ligações de serviço do Azure disponíveis** lista, selecione uma ligação a partir da lista ou criar uma ligação de permissões mais restrita a sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Para **nome de laboratório**, selecione o nome da instância que criou anteriormente.

   c. Para **nome de imagem personalizado**, introduza um nome para a imagem personalizada.

   d. (Opcional) Para **Descrição**, introduza uma descrição para facilitar selecionar a imagem correta mais tarde.

   e. Para **VM de laboratório de origem - ID de VM de laboratório de origem**, se tiver alterado o nome predefinido da variável de ambiente que foi preenchido automaticamente com o ID do laboratório VM por tarefa anterior, editá-lo aqui. O valor predefinido é **$(labVMId)**.

   f. Para **variáveis de saída - ID de imagem personalizada**, precisa do ID da imagem recém-criada quando pretender gerir ou eliminá-lo. O nome predefinido da variável de ambiente que é preenchido automaticamente com este ID está definido **variáveis de saída** secção. Pode editar a variável, se necessário.

### <a name="delete-the-vm"></a>Elimine a VM

A etapa final é eliminar a VM que tenha implementado na sua instância do Azure DevTest Labs. Normalmente seria elimine a VM depois de executar as tarefas de desenvolvimento ou executar os testes que tem na VM implementada. 

1. No pipeline de versões, selecione **adicionar tarefas** e, em seguida, no **Deploy** separador, adicione uma *Azure DevTest Labs eliminar VM* tarefas. Configure da seguinte forma:

      > [!NOTE]
      > Para eliminar a VM, veja [do Azure DevTest Labs tarefas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **subscrição do Azure RM**, selecione uma ligação no **ligações de serviço do Azure disponíveis** listar ou criar uma ligação de permissões mais restrita a sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).
 
   b. Para **ID da VM de laboratório**, se tiver alterado o nome predefinido da variável de ambiente que foi preenchido automaticamente com o ID do laboratório VM por tarefa anterior, editá-lo aqui. O valor predefinido é **$(labVMId)**.

1. Introduza um nome para o pipeline de lançamento e, em seguida, guardá-lo.
1. Criar uma nova versão, selecione a compilação mais recente e implementá-la para o ambiente único no pipeline.

Cada fase, atualize a vista da sua instância do DevTest Labs no portal do Azure para ver a VM e imagem que estão a ser criada e a VM que está a ser eliminada novamente.

Agora, pode utilizar a imagem personalizada para criar VMs quando eles forem necessários.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [criar ambientes multi-VM com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* Explore mais modelos do Resource Manager de início rápido para automatização de DevTest Labs dos [repositório público do GitHub de laboratórios DevTest](https://github.com/Azure/azure-quickstart-templates).
* Se necessário, consulte a [resolução de problemas de DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) página.
 
