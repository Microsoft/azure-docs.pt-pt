---
title: Automatizando a implantação de uma VM nos Amazon Web Services
description: Este artigo demonstra como utilizar a automatização do Azure para automatizar a criação de uma VM do Amazon Web Service
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 348c28f6a2d72048e34f117e802abf243597b458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737642"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de automatização do Azure – aprovisionar uma máquina virtual do AWS
Neste artigo, saiba como pode aproveitar a automatização do Azure para Aprovisionar uma máquina virtual na sua subscrição do serviço de Web da Amazon (AWS) e dê um nome específico – que AWS se refere a como "marcação" a VM a essa VM.

## <a name="prerequisites"></a>Pré-requisitos
Para os fins deste artigo, tem de ter uma conta de automatização do Azure e uma subscrição do AWS. Para obter mais informações sobre como configurar uma conta de automatização do Azure e configurá-lo com as credenciais da sua subscrição do AWS, reveja [configurar a autenticação com os Amazon Web Services](automation-config-aws-account.md). Esta conta deve ser criada ou atualizada com as suas credenciais de subscrição do AWS antes de continuar, à medida que fazer referência a esta conta nos passos abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implementar o módulo PowerShell do Amazon Web Services
A runbook de aprovisionamento da VM utiliza o módulo do PowerShell de AWS para realizar seu trabalho. Execute os seguintes passos para adicionar o módulo para a sua conta de automatização que esteja configurada com as credenciais da sua subscrição do AWS.  

1. Abra o browser e navegue para o [galeria do PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique nas **implementar no botão de automatização do Azure**.<br><br> ![Importação do módulo de PS de AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. É direcionado para a página de início de sessão do Azure e depois de autenticar, será encaminhado para o portal do Azure e apresentada a página seguinte:<br><br> ![Página de módulo de importação](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta de automatização para utilizar e clique em **OK** para iniciar a implementação.

   > [!NOTE]
   > Ao importar um módulo do PowerShell na automatização do Azure, ele é também a extrair os cmdlets e estas atividades não são apresentados até que o módulo completamente tenha terminado a importar e extrair os cmdlets. Este processo pode demorar alguns minutos.  
   > <br>

1. No portal do Azure, abra a sua conta de automatização referenciada no passo 3.
2. Clique nas **ativos** mosaico e, no **ativos** painel, selecione o **módulos** mosaico.
3. Sobre o **módulos** página, verá o **AWSPowerShell** módulo na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar AWS implementar runbook VM
Depois de ter sido implementado o módulo do PowerShell do AWS, pode agora criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS através de um script do PowerShell. Os passos abaixo demonstram como tirar partido do script do PowerShell nativo na automatização do Azure.  

> [!NOTE]
> Para obter mais opções e informações sobre este script, visite o [galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Transfira o script do PowerShell New-AwsVM a partir da galeria do PowerShell ao abrir uma sessão do PowerShell e digitar o seguinte:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. A partir do portal do Azure, abra sua conta de automatização e selecione **Runbooks** na secção **automatização de processos** à esquerda.  
3. Partir do **Runbooks** página, selecione **adicionar um runbook**.
4. Sobre o **adicionar um runbook** painel, selecione **criação rápida** (criar um runbook novo).
5. Sobre o **Runbook** painel de propriedades, escreva um nome na caixa de nome para o runbook e do **tipo de Runbook** selecione na lista pendente **PowerShell**e, em seguida, clique em **Criar**.<br><br> ![Criar o painel do runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando for apresentada a página Editar Runbook do PowerShell, copie e cole o script do PowerShell num runbook tela de criação.<br><br> ![Script do PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Ao trabalhar com o exemplo de script do PowerShell, tenha em atenção o seguinte:
    > 
    > * O runbook contém um número de valores de parâmetro predefinidos. Avalie todos os valores predefinidos e atualizar sempre que necessário.
    > * Se armazenou suas credenciais do AWS como um recurso de credencial com o nome diferente do que **AWScred**, tem de atualizar o script na linha 57 para corresponder da mesma forma.  
    > * Ao trabalhar com os comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, tem de especificar a região do AWS. Caso contrário, os cmdlets falhar. Tópico do modo de exibição AWS [especificar região de AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) nas ferramentas do AWS para o documento de PowerShell para obter mais detalhes.  
    >

7. Para obter uma lista de nomes de imagem da sua subscrição do AWS, inicie o ISE do PowerShell e importe o módulo PowerShell do AWS. Autenticar no AWS, substituindo **Get-AutomationPSCredential** no seu ambiente de ISE com **AWScred = Get-Credential**. Isso pede-lhe as suas credenciais e pode fornecer seus **ID de chave de acesso** para o nome de utilizador e **chave de acesso secreta** a palavra-passe. Veja o exemplo abaixo:  

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
        
    É devolvido o resultado seguinte:<br><br>
   ![Obter imagens AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copie e cole um dos nomes de imagens numa variável de automatização conforme referenciado no runbook como **$InstanceType**. Uma vez que neste exemplo, usar o AWS gratuito em camadas a subscrição, utilize **t2.micro** para seu exemplo de runbook.  
9. Guarde o runbook, em seguida, clique em **Publish** para publicar o runbook e, em seguida **Sim** quando lhe for pedido.

### <a name="testing-the-aws-vm-runbook"></a>Testar o runbook de VM do AWS
Antes de continuar a testar o runbook, tem de verificar algumas coisas. Especificamente:  

* Um recurso de autenticação relativamente aos AWS tiver sido criado, chamado **AWScred** ou o script foi atualizado para referenciar o nome do seu recurso de credencial.    
* O módulo do PowerShell do AWS foi importado na automatização do Azure  
* Foi criado um novo runbook e valores de parâmetros foram verificados e atualizados sempre que necessário  
* **Criar registos verbosos** e, opcionalmente **registos de progressos** sob a definição de runbook **registo e rastreio** foram definidos para **no**.<br><br> ![Registo do Runbook e o rastreio](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Pretende iniciar o runbook, por isso, clique em **começar** e, em seguida, clique em **OK** quando o Painel Iniciar Runbook for aberto.
2. No Painel Iniciar Runbook, forneça uma **VMname**. Aceite os valores predefinidos para os outros parâmetros que pré-configuradas anteriormente no script. Clique em **OK** para iniciar a tarefa de runbook.<br><br> ![Iniciar AwsVM novo runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. É aberto um painel de tarefas para a tarefa de runbook que criou. Feche este painel.
4. Pode ver o progresso da tarefa e a vista de saída **fluxos** ao selecionar o **todos os registos** mosaico a partir da página de tarefa de runbook.<br><br> ![Saída do Stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que a VM está a ser aprovisionada, inicie a sessão na consola de gestão do AWS se não estiver atualmente conectado.<br><br> ![Consola do AWS implementada a VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos Seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


