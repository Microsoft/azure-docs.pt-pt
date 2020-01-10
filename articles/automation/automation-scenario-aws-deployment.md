---
title: Automatizando a implantação de uma VM no Amazon Web Services
description: Este artigo demonstra como usar a automação do Azure para automatizar a criação de uma VM do Amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b149a21ef60b22c2e549c91007265ce99babdbe4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420930"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de automação do Azure-provisionar uma máquina virtual AWS
Neste artigo, você aprende como é possível aproveitar a automação do Azure para provisionar uma máquina virtual em sua assinatura do AWS (Amazon Web Service) e dar a essa VM um nome específico – que AWS se refere à "marcação" da VM.

## <a name="prerequisites"></a>Pré-requisitos
Para os fins deste artigo, você precisa ter uma conta de automação do Azure e uma assinatura do AWS. Para obter mais informações sobre como configurar uma conta de automação do Azure e configurá-la com suas credenciais de assinatura do AWS, examine [Configurar a autenticação com o Amazon Web Services](automation-config-aws-account.md). Essa conta deve ser criada ou atualizada com suas credenciais de assinatura do AWS antes de continuar, conforme você faz referência a essa conta nas etapas abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implantar Amazon Web Services módulo do PowerShell
Seu runbook de provisionamento de VM aproveita o módulo do PowerShell do AWS para fazer seu trabalho. Execute as etapas a seguir para adicionar o módulo à sua conta de automação configurada com suas credenciais de assinatura do AWS.  

1. Abra o navegador da Web e navegue até a [Galeria do PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no **botão implantar na automação do Azure**.<br><br> ![](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png) de importação do módulo AWS PS
2. Você é levado para a página de logon do Azure e, após a autenticação, será encaminhado para a portal do Azure e apresentada na página a seguir:<br><br> ![Página Importar módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta de automação a ser usada e clique em **OK** para iniciar a implantação.

   > [!NOTE]
   > Ao importar um módulo do PowerShell para a automação do Azure, ele também está extraindo os cmdlets e essas atividades não aparecem até que o módulo tenha concluído a importação e extração completa dos cmdlets. Esse processo pode levar alguns minutos.  
   > <br>

1. Na portal do Azure, abra sua conta de automação referenciada na etapa 3.
2. Clique no bloco **ativos** e, no painel **ativos** , selecione o bloco **módulos** .
3. Na página **módulos** , você vê o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar AWS implantar runbook de VM
Depois que o módulo do AWS PowerShell tiver sido implantado, agora você poderá criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS usando um script do PowerShell. As etapas a seguir demonstram como aproveitar o script nativo do PowerShell na automação do Azure.  

> [!NOTE]
> Para obter mais opções e informações sobre esse script, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Baixe o script New-AwsVM do PowerShell no Galeria do PowerShell abrindo uma sessão do PowerShell e digitando o seguinte:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. No portal do Azure, abra sua conta de automação e selecione **Runbooks** na seção **automação do processo** à esquerda.  
3. Na página **Runbooks** , selecione **Adicionar um runbook**.
4. No painel **Adicionar um runbook** , selecione **criação rápida** (criar um novo runbook).
5. No painel Propriedades do **runbook** , digite um nome na caixa nome do runbook e, na lista suspensa **tipo de runbook** , selecione **PowerShell**e, em seguida, clique em **criar**.<br><br> ![criar o painel de runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando a página Editar runbook do PowerShell for exibida, copie e cole o script do PowerShell na tela de criação do runbook.<br><br> ![Script do PowerShell do runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observe o seguinte ao trabalhar com o script do PowerShell de exemplo:
    > 
    > * O runbook contém vários valores de parâmetro padrão. Avalie todos os valores padrão e atualize quando necessário.
    > * Se você armazenou suas credenciais do AWS como um ativo de credencial chamado de forma diferente de **AWScred**, precisará atualizar o script na linha 57 para que ele seja correspondente.  
    > * Ao trabalhar com os comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, você deve especificar a região AWS. Caso contrário, os cmdlets falharão. Exibir tópico AWS [especifique a região AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell para obter mais detalhes.  
    >

7. Para recuperar uma lista de nomes de imagem de sua assinatura do AWS, inicie o ISE do PowerShell e importe o módulo AWS PowerShell. Autentique no AWS substituindo **Get-AutomationPSCredential** no ambiente do ISE por **AWScred = Get-Credential**. Isso solicitará suas credenciais e você poderá fornecer sua ID de **chave de acesso** para o nome de usuário e a **chave de acesso de segredo** para a senha. Veja o exemplo abaixo:  

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
        
    A seguinte saída é retornada:<br><br>
   ![obter imagens AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copie e cole um dos nomes de imagem em uma variável de automação como referenciado no runbook como **$InstanceType**. Como neste exemplo você está usando a assinatura em camadas gratuita do AWS, use **T2. micro** para seu exemplo de runbook.  
9. Salve o runbook e clique em **publicar** para publicar o runbook e, em seguida, em **Sim** quando solicitado.

### <a name="testing-the-aws-vm-runbook"></a>Testando o runbook de VM AWS
Antes de prosseguir com o teste do runbook, você precisa verificar algumas coisas. Especificamente:  

* Um ativo para autenticação no AWS foi criado chamado **AWScred** ou o script foi atualizado para referenciar o nome do seu ativo de credencial.    
* O módulo do PowerShell do AWS foi importado na automação do Azure  
* Um novo runbook foi criado e os valores de parâmetro foram verificados e atualizados quando necessário  
* **Registros detalhados de log** e, opcionalmente, **registros de progresso de log** na configuração do runbook **log e rastreamento** foram definidos como **on**.<br><br> ![log e rastreamento de runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Você deseja iniciar o runbook, portanto, clique em **Iniciar** e, em seguida, clique em **OK** quando o painel Iniciar runbook for aberto.
2. No painel Iniciar runbook, forneça um **VMname**. Aceite os valores padrão para os outros parâmetros pré-configurados anteriormente no script. Clique em **OK** para iniciar o trabalho de runbook.<br><br> ![iniciar o runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Um painel de trabalho é aberto para o trabalho de runbook que você criou. Feche este painel.
4. Você pode exibir o progresso do trabalho e exibir os **fluxos** de saída selecionando o bloco **todos os logs** na página de trabalho do runbook.<br><br> ](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png) de saída do fluxo de ![
5. Para confirmar se a VM está sendo provisionada, faça logon no console de gerenciamento do AWS se você não estiver conectado no momento.<br><br> ![VM implantada pelo console do AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


