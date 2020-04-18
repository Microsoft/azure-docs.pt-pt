---
title: Automatização da implantação de um VM nos Serviços Web da Amazon
description: Este artigo demonstra como usar a Automação Azure para automatizar a criação de um VM amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604835"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de Automação Azure - fornecimento de uma máquina virtual AWS
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
6. Quando a página editPowerShell Runbook aparecer, copie e cole o script PowerShell na tela de autor do livro de execução.<br><br> ![Script PowerShell do livro de execução](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Note o seguinte ao trabalhar com o exemplo do script PowerShell:
    > 
    > * O livro de execução contém uma série de valores de parâmetros padrão. Avalie todos os valores predefinidos e atualize sempre que necessário.
    > * Se guardou as suas credenciais AWS como um `AWScred`ativo credencial nomeado de forma diferente, precisa de atualizar o script na linha 57 para corresponder em conformidade.  
    > * Ao trabalhar com os comandos AWS CLI na PowerShell, especialmente com este livro de execução exemplo, deve especificar a região AWS. Caso contrário, os cmdlets falham. Consulte o tópico AWS [Especifique a região AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell para mais detalhes.  
    >

7. Para obter uma lista de nomes de imagem da sua subscrição AWS, lance powerShell ISE e importe o Módulo AWS PowerShell. Autenticar contra a AWS `Get-AutomationPSCredential` substituindo no `AWScred = Get-Credential`seu ambiente ISE por . Esta declaração solicita as suas credenciais e pode fornecer o seu ID de chave de acesso para o nome do utilizador e a sua chave de acesso secreta para a palavra-passe. 

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
8. Copiar e colar um dos nomes de imagem numa variável `$InstanceType`De automação como referenciado no livro de execução como . Uma vez que, neste exemplo, está a utilizar a subscrição gratuita de nível AWS, utiliza **t2.micro** para o seu exemplo de livro de execução.  
9. Guarde o livro de recortes e, em seguida, clique em **Publicar** para publicar o livro de execução e, em seguida, **sim** quando solicitado.

### <a name="testing-the-aws-vm-runbook"></a>Testar o livro de execução DaWS VM
Antes de continuar a testar o livro de corridas, precisa de verificar algumas coisas:

* Foi criado `AWScred` um ativo chamado para autenticação contra a AWS, ou o script foi atualizado para fazer referência ao seu nome de ativo credencial.    
* O módulo AWS PowerShell foi importado na Automação Azure.  
* Foi criado um novo livro de ensaios e os valores dos parâmetros foram verificados e atualizados sempre que necessário.  
* **Registar registos verbosos** e, opcionalmente, **registar registos** de progresso no âmbito da operação do livro de **execução A exploração de registos de registos e** rastreios foram definidos para **On**.<br><br> ![Livro de execução](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)Logging and Tracing .  

1. **Clique em Iniciar** o livro de execução e, em seguida, clique em **OK** quando o painel start Runbook abrir.
2. No painel Start Runbook, forneça um nome VM. Aceite os valores predefinidos para os outros parâmetros que preconfigurou no script. Clique **em OK** para iniciar o trabalho do livro de corridas.<br><br> ![Iniciar o livro de corridas New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Um painel de trabalho está aberto para o trabalho de livro que criou. Feche este painel.
4. Pode visualizar o progresso do trabalho e ver fluxos de saída selecionando **Todos os Registos** do painel de trabalho do livro de execução.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que o VM está a ser provisionado, inicie sessão na Consola de Gestão AWS se não estiver atualmente registado.<br><br> ![Consola AWS implantada VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos seguintes
* Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](automation-first-runbook-graphical.md)
* Para começar com os livros de execução powerShell Workflow, consulte o meu primeiro livro de execução de fluxo de [trabalho PowerShell](automation-first-runbook-textual.md).
* Para saber mais sobre os tipos de livro de corridas e as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](automation-runbook-types.md).
* Para obter mais informações sobre o suporte ao script PowerShell, consulte o suporte do [script Native PowerShell em Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).