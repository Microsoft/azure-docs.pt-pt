---
title: Implemente um VM de Serviços Web da Amazon com um livro de execução da Automação Azure
description: Este artigo diz como automatizar a criação de um VM amazon Web Services.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 4dcc095648111348a6935225a6aa10798109f76e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832237"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Implemente um VM de Serviços Web da Amazon com um livro de execução

Neste artigo, aprende como pode aproveitar a Automatização Azure para fornecer uma máquina virtual na subscrição do Amazon Web Service (AWS) e dar a esse VM um nome específico – a que a AWS se refere como "marcação" do VM.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma conta Azure Automation e uma subscrição amazon Web Services (AWS). Para obter mais informações sobre a criação de uma conta Azure Automation e configurá-la com as suas credenciais de subscrição AWS, reveja a [Autenticação Configurada com os Serviços Web da Amazon](automation-config-aws-account.md). Esta conta deve ser criada ou atualizada com as suas credenciais de subscrição AWS antes de proceder, uma vez que refere esta conta nas secções abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implementar módulo PowerShell de serviços web da Amazon

O seu livro de execução de provisionamento VM utiliza o módulo AWS PowerShell para fazer o seu trabalho. Utilize os seguintes passos para adicionar o módulo à sua conta Automation que está configurado com as suas credenciais de subscrição AWS.  

1. Abra o seu navegador web e navegue para a [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no **botão Deploy to Azure Automation**.<br><br> ![Importação de módulo sPs AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você é levado para a página de login Azure e após a autenticação, você será encaminhado para o portal Azure e apresentado com a seguinte página:<br><br> ![Página do Módulo de Importação](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta Automation para utilizar e clique em **OK** para iniciar a implementação.

   > [!NOTE]
   > Quando a Azure Automation importa um módulo PowerShell, extrai os cmdlets. As atividades não aparecem até que a Automação termine completamente de importar o módulo e extrair os cmdlets. Este processo pode demorar alguns minutos.  
   > <br>

1. No portal do Azure, abra a sua conta da Automatização.
2. Clique no azulejo **Assets** e, no painel Assets, selecione **Módulos**.
3. Na página Módulos, vê-se o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar o livro de execução VM da AWS

Uma vez implementado o Módulo AWS PowerShell, pode agora ser autor de um livro de execução para automatizar o fornecimento de uma máquina virtual em AWS utilizando um script PowerShell. Os passos abaixo demonstram como usar o script nativo PowerShell na Automação Azure.  

> [!NOTE]
> Para mais opções e informações sobre este script, visite a [Galeria PowerShell.](https://www.powershellgallery.com/packages/New-AwsVM/)
> 

1. Descarregue o script PowerShell New-AwsVM da PowerShell Gallery abrindo uma sessão powerShell e digitando o seguinte comando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A partir do portal Azure, abra a sua conta de Automação e selecione **Runbooks** em Automação de **Processos**.  
3. A partir da página Runbooks, selecione **Adicionar um livro de execução**.
4. No painel Adicionar um livro de corridas, selecione **Quick Create** para criar um novo livro de corridas.
5. No painel de propriedades runbook, digite um nome para o seu livro de corridas.
6. A partir da lista de drop-down do **tipo Runbook,** selecione **PowerShell**, e, em seguida, clique **em Criar**.<br><br> ![Criar painel de livro de corridas](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Quando a página editPowerShell Runbook aparecer, copie e cole o script PowerShell na tela de autor do livro de execução.<br><br> ![Script PowerShell do livro de execução](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Note o seguinte ao trabalhar com o exemplo do script PowerShell:

    * O livro de execução contém uma série de valores de parâmetros padrão. Avalie todos os valores predefinidos e atualize sempre que necessário.
    * Se guardou as suas credenciais AWS como um ativo credencial nomeado de forma `AWScred` diferente, precisa de atualizar o script na linha 57 para corresponder em conformidade.  
    * Ao trabalhar com os comandos AWS CLI na PowerShell, especialmente com este livro de execução exemplo, deve especificar a região AWS. Caso contrário, os cmdlets falham. Consulte o tópico AWS [Especifique a região AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell para mais detalhes.  

8. Para obter uma lista de nomes de imagem da sua subscrição AWS, lance powerShell ISE e importe o Módulo AWS PowerShell. Autenticar contra a AWS substituindo `Get-AutomationPSCredential` no seu ambiente ISE por `AWScred = Get-Credential` . Esta declaração solicita as suas credenciais e pode fornecer o seu ID de chave de acesso para o nome do utilizador e a sua chave de acesso secreta para a palavra-passe. 

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
        
    A seguinte saída é devolvida:<br><br>
   ![Obtenha imagens AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Copiar e colar um dos nomes de imagem numa variável De automação como referenciado no livro de execução como `$InstanceType` . Uma vez que, neste exemplo, está a utilizar a subscrição gratuita de nível AWS, utiliza **t2.micro** para o seu exemplo de livro de execução.  
10. Guarde o livro de recortes e, em seguida, clique em **Publicar** para publicar o livro de execução e, em seguida, **sim** quando solicitado.

### <a name="test-the-aws-vm-runbook"></a>Teste o livro de execução AWS VM

1. Verifique se o livro de execução cria um ativo chamado `AWScred` para autenticação contra AWS ou atualize o script para fazer referência ao seu nome de ativo credencial.    
2. Verifique o seu novo livro de execução e certifique-se de que todos os valores dos parâmetros foram atualizados.
Certifique-se de que o módulo AWS PowerShell foi importado para a Automação Azure.  
3. Na Automatização Azure, detete **tete os registos de log verbose** e, opcionalmente, **registar registos** de progresso no âmbito da operação de caderneta **Logging e rastreio** para **On**.<br><br> ![Livro de execução Logging and Tracing ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. **Clique em Iniciar** o livro de execução e, em seguida, clique em **OK** quando o painel start Runbook abrir.
5. No painel Start Runbook, forneça um nome VM. Aceite os valores predefinidos para os outros parâmetros que preconfigurou no script. Clique **em OK** para iniciar o trabalho do livro de corridas.<br><br> ![Iniciar o livro de corridas New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Um painel de trabalho está aberto para o trabalho de livro que criou. Feche este painel.
7. Pode visualizar o progresso do trabalho e ver fluxos de saída selecionando **Todos os Registos** do painel de trabalho do livro de execução.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Para confirmar que o VM está a ser provisionado, inicie sessão na Consola de Gestão AWS se não estiver atualmente registado.<br><br> ![Consola AWS implantada VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos seguintes
 
* Para saber quais os livros de execução suportados, consulte os tipos de livro de[execução da Automação Azure](automation-runbook-types.md).
* Para trabalhar com livros de execução, consulte [Gerir livros de execução em Automação Azure.](manage-runbooks.md)
* Para mais detalhes sobre powerShell, consulte [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* Para suporte a script, consulte suporte de [script Native PowerShell em Automação Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).