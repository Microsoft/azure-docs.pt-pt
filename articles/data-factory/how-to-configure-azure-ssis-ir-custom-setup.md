---
title: Personalize a configuração para um tempo de execução de integração Azure-SSIS
description: Este artigo descreve como usar a interface de configuração personalizada para um tempo de execução de integração Azure-SSIS para instalar componentes adicionais ou alterar definições
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387738"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalize a configuração para um tempo de execução de integração Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode personalizar o seu Serviço de Integração de Servidores Azure-SQL (SSIS) Integration Runtime (IR) na Azure Data Factory (ADF) através de configurações personalizadas. Permitem-lhe adicionar os seus próprios passos durante o provisionamento ou reconfiguração do seu Azure-SSIS IR. 

Ao utilizar configurações personalizadas, pode alterar a configuração ou ambiente de funcionamento predefinido do seu Azure-SSIS IR. Por exemplo, para iniciar serviços adicionais do Windows, persistir credenciais de acesso para partilha de ficheiros ou utilizar um forte protocolo de criptografia/rede mais seguro (TLS 1.2). Ou pode instalar componentes adicionais, tais como conjuntos, controladores ou extensões, em cada nó do seu Azure-SSIS IR. Podem ser feitos sob medida, Open Source ou componentes de 3ª parte. Para obter mais informações sobre componentes incorporados/pré-instalados, consulte [componentes incorporados/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Pode fazer configurações personalizadas no seu Azure-SSIS IR de duas formas: 
* **Configuração personalizada padrão com um script**: Prepare um script e seus ficheiros associados, e carre deixe-os juntos para um recipiente de bolhas na sua conta de armazenamento Azure. Em seguida, fornece um identificador de recursos uniforme (SAS) de assinatura de acesso partilhado (URI) para o seu recipiente quando configurar ou reconfigurar o seu Azure-SSIS IR. Cada nó do seu Azure-SSIS IR, em seguida, descarrega o script e os ficheiros associados do seu contentor e executa a sua configuração personalizada com permissões elevadas. Quando a sua configuração personalizada estiver terminada, cada nó carrega a saída padrão da execução e outros registos para o seu contentor.
* **Express custom setup without a script**: Executar algumas configurações comuns do sistema e comandos Windows ou instalar alguns componentes adicionais populares ou recomendados sem usar quaisquer scripts.

Pode instalar componentes gratuitos (não licenciados) e pagos (licenciados) com configurações personalizadas padrão e expressas. Se for um fornecedor independente de software (ISV), consulte [Desenvolver componentes pagos ou licenciados para a Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Para beneficiar de melhorias futuras, recomendamos a utilização de v3 ou séries posteriores de nós para o seu Azure-SSIS IR com configuração personalizada.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações aplicam-se apenas às configurações personalizadas padrão:

- Se pretender utilizar *gacutil.exe* no seu script para instalar conjuntos na cache de montagem global (GAC), tem de fornecer *gacutil.exe* como parte da sua configuração personalizada. Ou pode utilizar a cópia fornecida na pasta *amostra* do nosso recipiente de *pré-visualização pública,* consulte a secção **de amostras de configuração personalizada padrão** abaixo.

- Se pretender fazer referência a uma sub-dobradeira no seu script, *msiexec.exe* não suporta a `.\` notação para fazer referência à pasta raiz. Utilize um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- As ações administrativas, ou as ações ocultas de rede que são criadas automaticamente pelo Windows, não são atualmente suportadas no Azure-SSIS IR.

- O controlador IBM iSeries Access ODBC não é suportado no Azure-SSIS IR. Pode ver erros de instalação durante a sua configuração personalizada. Se o fizer, contacte o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o seu Azure-SSIS IR, precisa dos seguintes itens:

- [Uma subscrição do Azure](https://azure.microsoft.com/)

- [Provisionar o seu Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Uma conta de armazenamento Azure.](https://azure.microsoft.com/services/storage/) Não é necessário para configurações personalizadas expressas. Para configurações personalizadas padrão, faça o upload e guarde o seu script de configuração personalizado e os seus ficheiros associados num recipiente de bolhas. O processo de configuração personalizado também envia os seus registos de execução para o mesmo recipiente blob.

## <a name="instructions"></a>Instruções

Pode proviscionar ou reconfigurar o seu Azure-SSIS IR com configurações personalizadas na ADF UI. Se quiser fazer o mesmo utilizando o PowerShell, descarregue e instale [o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Configuração personalizada padrão

Para provisão ou reconfigure o seu Azure-SSIS IR com configurações personalizadas padrão na ADF UI, complete os seguintes passos.

1. Prepare o seu script de configuração personalizado e os seus ficheiros associados (por exemplo, .bat, .cmd, .exe, .dll, .msi ou ficheiros .ps1).

   * Você deve ter um ficheiro de script chamado *main.cmd*, que é o ponto de entrada da sua configuração personalizada.  
   * Para garantir que o script pode ser executado silenciosamente, deve testá-lo primeiro na sua máquina local.  
   * Se pretender que os registos adicionais gerados por outras ferramentas (por exemplo, *msiexec.exe*) sejam carregados para o seu contentor, especifique a variável ambiente predefinido, `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de registo nos seus scripts (por exemplo, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\instalar.log*).

1. Descarregue, instale e abra [o Azure Storage Explorer](https://storageexplorer.com/).

   a. Em **(Local e Anexo)**, clique à direita Contas de **Armazenamento** e, em seguida, selecione Connect **to Azure storage**.

      ![Ligar ao Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione **Utilize o nome e a chave da conta de armazenamento** e, em seguida, selecione **Seguinte**.

      ![Utilizar o nome e a chave de uma conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Introduza o nome e a chave da sua conta de armazenamento Azure, selecione **Seguinte** e, em seguida, selecione **Connect**.

      ![Fornecer nome e chave de conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Na sua conta de armazenamento Azure conectada, clique com o botão direito **Blob Containers**, selecione **Create Blob Container** e nomeie o novo recipiente.

      ![Criar um contentor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo recipiente e carre faça upload do seu script de configuração personalizado e dos seus ficheiros associados. Certifique-se de que carrega *main.cmd* no nível superior do seu recipiente, não em nenhuma pasta. O seu recipiente deve conter apenas os ficheiros de configuração personalizados necessários, por isso, transferi-los para o seu Azure-SSIS IR mais tarde não demorará muito tempo. A duração máxima de uma configuração personalizada é atualmente definida em 45 minutos antes do intervalo. Isto inclui o tempo para descarregar todos os ficheiros do seu recipiente e instalá-los no Azure-SSIS IR. Se a configuração necessitar de mais tempo, levante um bilhete de apoio.

      ![Faça upload de ficheiros para o recipiente blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique com o botão direito no recipiente e, em seguida, **selecione Obter Assinatura de Acesso Partilhado**.

      ![Obtenha a Assinatura de Acesso Partilhado para o recipiente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   exemplo, Crie o SAS URI para o seu recipiente com um tempo de validade suficientemente longo e com permissão de leitura/escrita/lista. Precisa do SAS URI para descarregar e executar o seu script de configuração personalizado e os seus ficheiros associados. Isto acontece sempre que qualquer nó do seu Azure-SSIS IR é remimagem ou reiniciado. Também precisa de escrever permissão para carregar registos de execução de configuração.

      > [!IMPORTANT]
      > Certifique-se de que o SAS URI não expira e os recursos de configuração personalizados estão sempre disponíveis durante todo o ciclo de vida do seu Azure-SSIS IR, desde a criação até à eliminação, especialmente se parar regularmente e iniciar o seu Azure-SSIS IR durante este período.

      ![Gere a Assinatura de Acesso Partilhado para o contentor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e guarde o SAS URI do seu recipiente.

      ![Copiar e guardar a Assinatura de Acesso Partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Selecione o **tempo de execução de integração Azure-SSIS com configurações de sistema/instalações adicionais** na página **de configurações avançadas** do painel de configuração do tempo de execução da **integração.** Em seguida, introduza o SAS URI do seu recipiente na caixa de texto **SAS URI do recipiente de configuração personalizada.**

   ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Depois de terminar a configuração personalizada padrão e o seu Azure-SSIS IR começar, pode encontrar todos os registos de configuração personalizados na pasta *principal.cmd.log* do seu recipiente. Incluem a saída padrão do *main.cmd* e outros registos de execução.

### <a name="express-custom-setup"></a>Configuração personalizada expressa

Para provisão ou reconfigure o seu Azure-SSIS IR com configurações personalizadas expressas na ADF UI, complete os seguintes passos.

1. Selecione o **tempo de execução de integração Azure-SSIS com configurações de sistema/instalações adicionais** na página **de configurações avançadas** do painel de configuração do tempo de execução da **integração.** 

1. Selecione **Novo** para abrir o painel **de configuração personalizado Add Express** e, em seguida, selecione um tipo na lista de drop-down do tipo de **configuração personalizada Express.** Atualmente oferecemos configurações personalizadas expressas para executar o comando cmdkey, adicionando variáveis ambientais, instalando Azure PowerShell e instalando componentes licenciados.

#### <a name="running-cmdkey-command"></a>Comando cmdkey

Se selecionar o tipo **de comando Run cmdkey** para a sua configuração personalizada expressa, pode executar o comando Cmdkey do Windows no seu Azure-SSIS IR. Para tal, insira o nome de computador ou nome de domínio, nome de utilizador ou nome de conta, e senha ou chave de conta nas caixas de texto **/Add**, **/User**, e **/Passe,** respectivamente. Isto permitir-lhe-á persistir credenciais de acesso para SQL Servers, ações de ficheiros ou Ficheiros Azure no seu Azure-SSIS IR. Por exemplo, para aceder aos Ficheiros Azure, pode introduzir `YourAzureStorageAccountName.file.core.windows.net` `azure\YourAzureStorageAccountName` , e para `YourAzureStorageAccountKey` **/Adicionar**, **/Utilizador,** e **/Pass,** respectivamente. Isto é semelhante ao funcionamento do comando [Windows cmdkey](/windows-server/administration/windows-commands/cmdkey) na sua máquina local. Por enquanto, apenas uma configuração personalizada expressa para executar o comando cmdkey é suportada. Para executar vários comandos cmdkey, utilize uma configuração personalizada padrão.

#### <a name="adding-environment-variables"></a>Adicionar variáveis ambientais

Se selecionar o tipo **de variável de ambiente Add** para a sua configuração personalizada expressa, pode adicionar uma variável ambiente Windows no seu Azure-SSIS IR. Para tal, insira o nome e valor variáveis do seu ambiente nas caixas de texto **de valor variável** e **de valor variável,** respectivamente. Isto permitir-lhe-á utilizar a variável ambiental nos seus pacotes que funcionam no Azure-SSIS IR, por exemplo, em Componentes/Tarefas de Script. Isto é semelhante ao comando de [conjunto](/windows-server/administration/windows-commands/set_1) do Windows na sua máquina local.

#### <a name="installing-azure-powershell"></a>Instalação Azure PowerShell

Se selecionar o tipo **Install Azure PowerShell** para a sua configuração personalizada expressa, pode instalar o módulo Az de PowerShell no seu Azure-SSIS IR. Para tal, introduza o número da versão do módulo Az (x.y.z) que pretende a partir de uma [lista de suportes](https://www.powershellgallery.com/packages/az). Isto permitir-lhe-á executar cmdlets/scripts Azure PowerShell nos seus pacotes para gerir os recursos da Azure, por [exemplo, serviços de análise Azure (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Instalação de componentes licenciados

Se selecionar o tipo **de componente licenciado instalar** para a sua configuração personalizada expressa, pode selecionar um componente integrado dos nossos parceiros ISV na lista de drop-down do nome **Componente:**

   * Se selecionar o componente **de Fábrica de Tarefas da SentryOne,** pode instalar o conjunto de componentes da Fábrica de [Tarefas](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) da SentryOne no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **2020.1.3**.

   * Se selecionar o **HEDDA do oh22. Componente IO,** pode instalar o [HEDDA. Componente](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) de qualidade/limpeza de dados IO a partir de oh22 no seu Azure-SSIS IR. Para tal, é necessário adquirir previamente o seu serviço. A versão integrada atual é **1.0.14**.

   * Se selecionar o componente **SQLPhonetics.NET do oh22,** pode instalar [o](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) SQLPhonetics.NET componente de qualidade/correspondência de dados a partir de oh22 no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **1.0.45**.

   * Se selecionar o componente **SSIS Integration Toolkit da KingswaySoft,** pode instalar o conjunto de conectores [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) para aplicações CRM/ERP/marketing/colaboração, tais como Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, etc. da KingswaySoft no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **2020.1**.

   * Se selecionar o componente **SSIS Productivity Pack da KingswaySoft,** pode instalar o conjunto de componentes [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) da KingswaySoft no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **20.1**.

   * Se selecionar o componente **Xtract IS do Theobald Software,** pode instalar o conjunto de conectores [Xtract IS](https://theobald-software.com/en/xtract-is/) para sistemas SAP (ERP, S/4HANA, BW) a partir do Software Theobald no seu Azure-SSIS IR. Para tal, arraste & largar/carregar o ficheiro de licença de produto que adquiriu previamente na caixa de entrada de **ficheiros License.** A versão integrada atual é **6.1.1.3**.

   * Se selecionar o componente **do Serviço de Integração da AecorSoft,** pode instalar o conjunto de conectores do Serviço de [Integração](https://www.aecorsoft.com/en/products/integrationservice) para sistemas SAP e Salesforce a partir da AecorSoft no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **3.0.00**.

   * Se selecionar o componente **SSIS Standard Package do CData,** pode instalar o conjunto [SSIS Standard Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) dos componentes mais populares do CData, como os conectores Microsoft SharePoint, no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **de 19.7354**.

   * Se selecionar o componente **SSIS Extended Package do CData,** pode instalar o pacote [SSIS Extended Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) de todos os componentes do CData, tais como conectores Business Central Microsoft Dynamics 365 e outros componentes no seu **Pacote Standard SSIS,** no seu Azure-SSIS IR. Para tal, introduza a chave de licença do produto que adquiriu previamente na caixa de texto **da chave da licença.** A versão integrada atual é **de 19.7354**. Devido ao seu grande tamanho, para evitar o tempo de instalação, certifique-se de que o seu Azure-SSIS IR tem pelo menos 4 núcleos de CPU por nó.

As configurações personalizadas expressas adicionadas aparecerão na página **de definições Avançadas.** Para removê-las, selecione as suas caixas de verificação e, em seguida, **selecione Delete**.

### <a name="azure-powershell"></a>Azure PowerShell

Para provisão ou reconfigure o seu Azure-SSIS IR com configurações personalizadas utilizando a Azure PowerShell, complete os seguintes passos.

1. Se o seu Azure-SSIS IR já estiver iniciado/em funcionamento, pare-o primeiro.

1. Em seguida, pode adicionar ou remover configurações personalizadas executando o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet antes de iniciar o seu Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Amostras de configuração personalizadas padrão

Para visualizar e reutilizar algumas amostras de configurações personalizadas padrão, complete os seguintes passos.

1. Ligue-se ao nosso recipiente de pré-visualização pública utilizando o Azure Storage Explorer.

   a. Em **(Local e Anexado)**, clique à direita **Contas de Armazenamento**, selecione Connect to **Azure storage**, selecione Use uma cadeia de **conexão ou uma assinatura de acesso partilhado URI**, e, em seguida, selecione **Seguinte**.

      ![Conecte-se ao armazenamento Azure com a Assinatura de Acesso Partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **Utilize um SAS URI** e, em seguida, na caixa de texto **URI,** introduza o seguinte SAS URI:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornecer a Assinatura de Acesso Partilhado para o recipiente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Seguinte** e, em seguida, selecione **Connect**.

   d. No painel esquerdo, selecione o recipiente **de visualização pública** ligado e, em seguida, clique duas vezes na pasta *CustomSetupScript.* Nesta pasta encontram-se os seguintes itens:

      * Uma pasta *de amostra,* que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu Azure-SSIS IR. A tarefa não faz nada além de dormir por alguns segundos. A pasta também contém uma pasta *gacutil,* cujo conteúdo completo (*gacutil.exe*, *gacutil.exe.config*, e *1033\gacutlrc.dll*) pode ser copiado como está no seu recipiente.

      * Uma pasta *UserScenarios,* que contém várias amostras de configuração personalizadas de cenários reais do utilizador. Se pretender instalar várias amostras no seu Azure-SSIS IR, pode combinar os seus ficheiros de configuração personalizado *(main.cmd)* num único e carregá-lo com todos os ficheiros associados no seu recipiente.

        ![Conteúdo do recipiente de pré-visualização público](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *UserScenarios* para encontrar os seguintes itens:

      * Uma pasta *.NET FRAMEWORK 3.5,* que contém um script de configuração personalizado *(main.cmd*) para instalar uma versão anterior do Quadro .NET em cada nó do seu Azure-SSIS IR. Esta versão pode ser requerida por alguns componentes personalizados.

      * Uma pasta *BCP,* que contém um script de configuração personalizado *(main.cmd*) para instalar utilitários de linha de comando SQL Server *(MsSqlCmdLnUtils.msi*) em cada nó do seu Azure-SSIS IR. Um desses utilitários é o programa de cópias a granel *(bcp).*

      * Uma pasta *DNS SUFFIX,* que contém um script de configuração personalizado *(main.cmd*) para anexar o seu próprio sufixo DNS (por *exemplo, test.com*) a qualquer nome de domínio de rótulo único não qualificado e transformá-lo num Nome de Domínio Totalmente Qualificado (FQDN) antes de o utilizar em consultas DNS do seu Azure-SSIS IR.

      * Uma pasta *EXCEL,* que contém um script de configuração personalizado *(main.cmd*) para instalar alguns conjuntos e bibliotecas C# em cada nó do seu Azure-SSIS IR. Pode usá-las em Script Tasks para ler e escrever ficheiros Excel de forma dinâmica. 
      
        Primeiro, faça o download [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), e depois carre deixe-os todos junto com *o main.cmd* para o seu recipiente. Em alternativa, se apenas pretender utilizar os conectores Excel padrão (Connection Manager, Source e Destination), o Access Redistributable que os contém já está pré-instalado no seu Azure-SSIS IR, pelo que não necessita de qualquer configuração personalizada.
      
      * Uma pasta *MYSQL ODBC,* que contém um script de configuração personalizado *(main.cmd*) para instalar os controladores MySQL ODBC em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar os conectores ODBC (Connection Manager, Source e Destination) para se ligar ao servidor MySQL. 
     
        Em primeiro lugar, [faça o download das versões mais recentes de 64 bits e 32 bits dos instaladores do controlador MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi),* e depois faça o upload de todos junto com *o main.cmd* para o seu contentor.

      * Uma pasta *ORACLE ENTERPRISE,* que contém um script de configuração personalizado *(main.cmd*) e ficheiro de configuração silenciosa *(client.rsp*) para instalar os conectores Oracle e o controlador OCI em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Oracle Connection Manager, Source e Destination para ligar ao servidor Oráculo. 
      
        Em primeiro lugar, baixe o Microsoft Connectors v5.0 para Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi)* do [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o mais recente cliente oracle (por exemplo, *winx64_12102_client.zip)* da [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Em seguida, faça o upload de todos junto com *main.cmd* e *client.rsp* para o seu recipiente. Se utilizar o TNS para ligar à Oracle, também precisa de baixar *tnsnames.ora,* editá-lo e carregá-lo para o seu recipiente. Desta forma, pode ser copiado para a pasta de instalação do Oráculo durante a instalação.

      * Uma pasta *ORACLE STANDARD ADO.NET,* que contém um script de configuração personalizado *(main.cmd*) para instalar o controlador Oracle ODP.NET em cada nó do seu Azure-SSIS IR. Esta configuração permite-lhe utilizar o ADO.NET Connection Manager, Source e Destination para ligar ao servidor Oráculo. 
      
        Primeiro, [faça o download do mais recente ODP.NET da Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *ODP.NET_Managed_ODAC122cR1.zip),* e depois carrere-o juntamente com *o main.cmd* para o seu contentor.
       
      * Uma pasta *ODBC STANDARD ORACLE,* que contém um script de configuração personalizado *(main.cmd*) para instalar o controlador Oracle ODBC em cada nó do seu Azure-SSIS IR. O script também configura o Nome de Fonte de Dados (DSN). Esta configuração permite-lhe utilizar o Gestor de Ligação ODBC, Fonte e Destino ou Gestor de Ligação de Ligação de Energia e Fonte com o tipo de fonte de dados ODBC para ligar ao servidor Oráculo. 
      
        Em primeiro lugar, faça o download do mais recente Oracle Instant Client (Pacote Básico ou Pacote Lite Básico) e pacote ODBC e, em seguida, carrede-os todos junto com *o main.cmd* para o seu recipiente:
        * [Descarregue pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Pacote Básico: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip;* Pacote Lite Básico: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip;* Pacote ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Descarregue pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Pacote Básico: *instantclient-basic-nt-18.3.0.0.0dbru.zip;* Pacote Lite Básico: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip;* Pacote ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Uma pasta *OLEDB STANDARD ORACLE,* que contém um script de configuração personalizado *(main.cmd*) para instalar o controlador OLEDB oracle em cada nó do seu Azure-SSIS IR. Esta configuração permite-lhe utilizar o OLEDB Connection Manager, Source e Destination para ligar ao servidor Oráculo. 
     
        Primeiro, [faça o download do mais recente controlador Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64.zip),* e depois carrere-o juntamente com *o main.cmd* para o seu contentor.

      * Uma pasta *POSTGRESQL ODBC,* que contém um script de configuração personalizado *(main.cmd*) para instalar os controladores PostgreSQL ODBC em cada nó do seu Azure-SSIS IR. Esta configuração permite-lhe utilizar o Gestor de Ligação ODBC, Fonte e Destino para ligar ao servidor PostgreSQL. 
     
        Em primeiro lugar, [faça o download das versões mais recentes de 64 bits e 32 bits dos instaladores do controlador PostgreSQL ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64.msi* e *psqlodbc_x86.msi),* e depois faça o upload de todos junto com *o main.cmd* para o seu contentor.

      * Uma pasta *SAP BW,* que contém um script de configuração personalizado *(main.cmd*) para instalar o conjunto de conector SAP .NET *(librfc32.dll*) em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Gestor de Ligação SAP BW, Fonte e Destino para ligar ao servidor SAP BW. 
      
        Em primeiro lugar, faça o upload da versão de 64 bits ou da versão de 32 bits de *librfc32.dll* da pasta de instalação SAP juntamente com *o main.cmd* para o seu recipiente. Em seguida, o guião copia o conjunto SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a configuração.

      * Uma pasta *DE ARMAZENAMENTO,* que contém um script de configuração personalizado *(main.cmd*) para instalar o Azure PowerShell em cada nó do seu Azure-SSIS IR. Esta configuração permite-lhe implementar e executar pacotes SSIS que executam [cmdlets/scripts Azure PowerShell para gerir o seu Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Copie *main.cmd*, uma amostra *AzurePowerShell.msi* (ou utilize a versão mais recente) e *storage.ps1* para o seu recipiente. Utilize *o PowerShell.dtsx* como modelo para os seus pacotes. O modelo de pacote combina uma [Tarefa de Download Azure Blob](/sql/integration-services/control-flow/azure-blob-download-task), que descarrega um script PowerShell modificável *(storage.ps1*- e uma [Tarefa de Processo de Execução](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), que executa o script em cada nó.

      * Uma pasta *TERADATA,* que contém um script de configuração personalizado *(main.cmd),* o seu ficheiro associado *(install.cmd)* e pacotes de instaladores *(.msi).* Estes ficheiros instalam os conectores Teradata, a API do Transporte Paralelo Teradata e o controlador ODBC em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Gestor de Ligação Teradata, Fonte e Destino para ligar ao servidor Teradata. 
      
        Em primeiro lugar, [faça o download do ficheiro Teradata Tools and Utilities 15.x zip](http://partnerintelligence.teradata.com) (por exemplo,  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* e depois carreve-o juntamente com os ficheiros *de .cmd* e *.msi* previamente mencionados para o seu contentor.

      * Uma pasta *TLS 1.2,* que contém um script de configuração personalizado *(main.cmd*) para utilizar uma criptografia forte e um protocolo de rede mais seguro (TLS 1.2) em cada nó do seu Azure-SSIS IR. O script também desativa as versões SSL/TLS mais antigas.

      * Uma pasta *ZULU OPENJDK,* que contém um script de configuração personalizado *(main.cmd*) e um ficheiro PowerShell *(install_openjdk.ps1*) para instalar o Zulu OpenJDK em cada nó do seu Azure-SSIS IR. Esta configuração permite-lhe utilizar a Azure Data Lake Store e os conectores de ficheiros flexíveis para processar ficheiros ORC e Parquet. Para mais informações, consulte [o Azure Feature Pack para serviços de integração.](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java) 
      
        Primeiro, [faça o download do mais recente Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* e depois carreje-o juntamente com *o main.cmd* e *install_openjdk.ps1* para o seu contentor.

        ![Pastas na pasta de cenários do utilizador](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para reutilizar estas amostras de configuração personalizadas padrão, copie o conteúdo da pasta selecionada para o seu recipiente.

1. Quando forja ou reconfigura o seu Azure-SSIS IR em ADF UI, selecione o **Tempo de Execução de Integração Azure-SSIS com configurações de sistema/instalações adicionais** de verificação na página **de configurações avançadas** do painel de configuração do tempo de execução da **integração.** Em seguida, introduza o SAS URI do seu recipiente na caixa de texto **SAS URI do recipiente de configuração personalizada.**
   
1. Quando forja ou reconfigura o seu Azure-SSIS IR utilizando o Azure PowerShell, pare-o se já estiver iniciado/em funcionamento, passe o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet com o SAS URI do seu recipiente como valor para o `SetupScriptContainerSasUri` parâmetro e, em seguida, inicie o seu Azure-SSIS IR.

1. Depois de terminar a configuração personalizada padrão e o seu Azure-SSIS IR começar, pode encontrar todos os registos de configuração personalizados na pasta *principal.cmd.log* do seu recipiente. Incluem a saída padrão do *main.cmd* e outros registos de execução.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a Edição Empresarial da Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolver componentes pagos ou licenciados para a Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
