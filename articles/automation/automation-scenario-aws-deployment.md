---
title: Implementar um VM de Serviços Web da Amazon com um runbook Azure Automation
description: Este artigo diz como automatizar a criação de um VM da Amazon Web Services.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 03f1f1659d120a2aa1b827063cb2bc84138f3655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896075"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Implementar um VM de Serviços Web da Amazon com um livro de bordo

Neste artigo, aprende-se como pode aproveitar a Azure Automation para fornecer uma máquina virtual na sua subscrição do Amazon Web Service (AWS) e dar a esse VM um nome específico – a que a AWS chama de "marcação" do VM.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de ter uma conta Azure Automation e uma subscrição da Amazon Web Services (AWS). Para obter mais informações sobre a criação de uma conta Azure Automation e configurá-la com as suas credenciais de subscrição AWS, [reveja a Autenticação de Configuração com a Amazon Web Services](automation-config-aws-account.md). Esta conta deve ser criada ou atualizada com as suas credenciais de subscrição AWS antes de prosseguir, como refere esta conta nas secções abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implementar módulo PowerShell de Serviços Web da Amazon

O seu manual de provisões VM utiliza o módulo AWS PowerShell para fazer o seu trabalho. Utilize os seguintes passos para adicionar o módulo à sua conta Automation que está configurada com as suas credenciais de subscrição AWS.  

1. Abra o seu navegador web e navegue na [Galeria PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no **botão Implementar para Azure Automation**.<br><br> ![Importação de módulos AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você é levado para a página de login do Azure e depois de autenticado, você será encaminhado para o portal Azure e apresentado com a seguinte página:<br><br> ![Página do Módulo de Importação](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta Dem automação para utilizar e clique **em OK** para iniciar a implementação.

   > [!NOTE]
   > Quando a Azure Automation importa um módulo PowerShell, extrai os cmdlets. As atividades não aparecem até que a Automação tenha terminado completamente de importar o módulo e extrair os cmdlets. Este processo pode demorar alguns minutos.  
   > <br>

1. No portal do Azure, abra a sua conta da Automatização.
2. Clique no azulejo **do Ativo** e, no painel 'Activos', selecione **Módulos**.
3. Na página Módulos, vê o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar o runbook VM implementar AWS

Uma vez implantado o Módulo AWS PowerShell, pode agora ser autoriado um livro de execução para automatizar o fornecimento de uma máquina virtual em AWS utilizando um script PowerShell. Os passos abaixo demonstram como utilizar o script nativo powerShell na Azure Automation.  

> [!NOTE]
> Para mais opções e informações sobre este script, visite a [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Descarregue o script PowerShell New-AwsVM da PowerShell Gallery abrindo uma sessão PowerShell e digitando o seguinte comando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A partir do portal Azure, abra a sua conta de Automação e selecione **Runbooks** em **Automação de Processos.**  
3. Na página Runbooks, **selecione Adicionar um livro de bordo**.
4. No painel De adicionar um painel de runbook, selecione **Quick Create** para criar um novo runbook.
5. No painel de propriedades runbook, escreva um nome para o seu runbook.
6. A partir da lista de drop-down **do tipo Runbook,** selecione **PowerShell** e, em seguida, clique em **Criar**.<br><br> ![Criar painel de runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Quando aparecer a página Editar PowerShell Runbook, copie e cole o script PowerShell na tela de autoria do livro de bordo.<br><br> ![Runbook PowerShell Script](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Note o seguinte ao trabalhar com o exemplo Do script PowerShell:

    * O livro de execução contém uma série de valores de parâmetros padrão. Avalie todos os valores predefinidos e atualização sempre que necessário.
    * Se tiver armazenado as suas credenciais AWS como um ativo credencial nomeado de forma `AWScred` diferente, tem de atualizar o script na linha 57 para corresponder em conformidade.  
    * Ao trabalhar com os comandos AWS CLI em PowerShell, especialmente com este livro de exemplo, deve especificar a região AWS. Caso contrário, os cmdlets falham. Ver tópico AWS [Especificar região AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell para mais detalhes.  

8. Para obter uma lista de nomes de imagem da sua assinatura AWS, lance o PowerShell ISE e importe o Módulo AWS PowerShell. Autenticar contra a AWS substituindo `Get-AutomationPSCredential` no seu ambiente ISE por `AWScred = Get-Credential` . Esta declaração solicita as suas credenciais e pode fornecer o seu ID da chave de acesso para o nome de utilizador e a sua chave de acesso secreta para a palavra-passe. 

      ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
      ```
        
    É devolvida a seguinte saída:<br><br>
   ![Obtenha imagens AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Copiar e colar um dos nomes de imagem de uma variável de Automação, tal como referenciado no livro de bordo `$InstanceType` como . Uma vez que, neste exemplo, está a utilizar a subscrição gratuita de nível AWS, utiliza **o t2.micro** para o seu exemplo de runbook.  
10. Guarde o livro de execução e, em seguida, clique em **Publicar** para publicar o livro de recortes e, em seguida, **Sim** quando solicitado.

### <a name="test-the-aws-vm-runbook"></a>Teste o livro de execução AWS VM

1. Verifique se o livro de execução cria um ativo solicitado `AWScred` para autenticação contra AWS, ou atualize o script para fazer referência ao nome do seu ativo credencial.    
2. Verifique o seu novo livro de execução e certifique-se de que todos os valores dos parâmetros foram atualizados.
Certifique-se de que o módulo AWS PowerShell foi importado para a Azure Automation.  
3. Na Azure Automation, estabeleça **registos verbosos de Log** e faça login opcionalmente **registos de progresso** no âmbito da operação **runbook Logging e tracing** to **On**.<br><br> ![Registo e rastreio de runbook ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. Clique **em Iniciar** o arranque do livro de execuções e, em seguida, clique em **OK** quando o painel start runbook abrir.
5. No painel Start Runbook, forneça um nome VM. Aceite os valores predefinidos para os outros parâmetros que pré-configurado no script. Clique **em OK** para iniciar o trabalho de runbook.<br><br> ![Comece New-AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Abre-se um painel job para o trabalho de runbook que criou. Feche este painel.
7. Pode visualizar o progresso do trabalho e visualizar os fluxos de saída selecionando **Todos os Registos** no painel de trabalho do runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Para confirmar que o VM está a ser a provisionado, inicie sessão na Consola de Gestão AWS se não estiver a fazer login.<br><br> ![Consola AWS implantada VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos seguintes
 
* Para saber quais os livros de execução suportados, consulte os[tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para trabalhar com runbooks, consulte [Gerir os runbooks na Azure Automation.](manage-runbooks.md)
* Para mais detalhes sobre o PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
* Para suporte ao script, consulte [o suporte para scripts Native PowerShell na Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
