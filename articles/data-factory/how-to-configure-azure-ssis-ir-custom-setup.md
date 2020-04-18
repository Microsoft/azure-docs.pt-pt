---
title: Personalize a configuração para um Tempo de Integração Azure-SSIS
description: Este artigo descreve como usar a interface de configuração personalizada para um Tempo de Funcionamento de Integração Azure-SSIS para instalar componentes adicionais ou alterar definições
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606210"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalize a configuração para um Tempo de Integração Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A configuração personalizada para um Runtime de Integração de Serviços de Integração de Servidores Azure-SQL (Azure-SSIS IR) fornece uma interface para adicionar os seus próprios passos durante a configuração ou reconfiguração do seu IR Azure-SSIS. 

Ao utilizar a configuração personalizada, pode alterar a configuração ou ambiente de funcionamento predefinido para, por exemplo, iniciar serviços windows adicionais, persistir credenciais de acesso para partilhas de ficheiros ou utilizar um protocolo de rede de encriptação/rede mais seguro (TLS 1.2). Ou pode instalar componentes adicionais, tais como conjuntos, controladores ou extensões, em cada nó do seu IR Azure-SSIS.

Pode fazer configurações personalizadas no seu IR Azure-SSIS de duas formas: 
* **Express configuração personalizada sem script**: Executar algumas configurações comuns do sistema e comandos windows ou instalar alguns componentes adicionais populares ou recomendados sem usar quaisquer scripts.
* **Configuração personalizada padrão com um script**: Prepare um script e os seus ficheiros associados e carregue-os todos juntos para um recipiente blob na sua conta de armazenamento Azure. Em seguida, fornece um identificador de recursos uniformes (SAS) de assinatura de acesso partilhado (URI) para o seu recipiente quando configurar ou reconfigurar o seu IR Azure-SSIS. Cada nó do seu IR Azure-SSIS descarrega o script e os seus ficheiros associados a partir do seu contentor e executa a sua configuração personalizada com permissões elevadas. Quando a configuração personalizada estiver terminada, cada nó envia a saída padrão de execução e outros registos para o seu recipiente.

Pode instalar componentes gratuitos e não licenciados e componentes pagos e licenciados com configurações personalizadas expressas e standard. Se for um fornecedor de software independente (ISV), consulte [Desenvolver componentes pagos ou licenciados para um IR Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Para beneficiar de futuras melhorias, recomendamos a utilização de v3 ou séries posteriores de nós para o seu IR Azure-SSIS com configuração personalizada.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações aplicam-se apenas às configurações personalizadas padrão:

- Se quiser utilizar *gacutil.exe* no seu script para instalar conjuntos na cache de montagem global (GAC), precisa de fornecer *gacutil.exe* como parte da sua configuração personalizada. Ou pode utilizar a cópia fornecida no nosso recipiente de *Pré-visualização Pública,* discutida mais tarde na secção "Instruções".

- Se quiser fazer referência a uma subpasta no seu script, *msiexec.exe* não suporta a `.\` notação para fazer referência à pasta raiz. Utilize um comando `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` como `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`o de .

- As ações administrativas, ou ações ocultas de rede que são criadas automaticamente pelo Windows, não são atualmente suportadas no IR Azure-SSIS.

- O condutor da IBM iSeries Access ODBC não é suportado no IR Azure-SSIS. Pode ver erros de instalação durante a configuração personalizada. Se o fizer, contacte o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o seu IR Azure-SSIS, precisa dos seguintes itens:

- [Uma subscrição do Azure.](https://azure.microsoft.com/)

- [Fornecer o seu IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Uma conta de armazenamento Azure.](https://azure.microsoft.com/services/storage/) Não é necessário para configurações personalizadas expressas. Para configurações personalizadas padrão, faça o upload e guarde o seu script de configuração personalizado e os seus ficheiros associados num recipiente de blob. O processo de configuração personalizado também envia os seus registos de execução para o mesmo recipiente de bolhas.

## <a name="instructions"></a>Instruções

1. Se pretender configurar ou reconfigurar o seu IR Azure-SSIS com powerShell, descarregue e [instale o Azure PowerShell](/powershell/azure/install-az-ps). Para configurações personalizadas expressas, salte para o passo 4.

1. Prepare o seu script de configuração personalizado e os seus ficheiros associados (por exemplo, .bat, .cmd, .exe, .dll, .msi, ou .ps1 files).

   * Deve ter um ficheiro de script chamado *main.cmd,* que é o ponto de entrada da sua configuração personalizada.  
   * Para garantir que o script pode ser executado silenciosamente, recomendamos que o teste primeiro na sua máquina local.  
   * Se pretender que registos adicionais gerados por outras ferramentas (por exemplo, *msiexec.exe*) `CUSTOM_SETUP_SCRIPT_LOG_DIR`sejam enviados para o seu recipiente, especifique a variável ambiente predefinida, como a pasta de registo nos seus scripts (por exemplo, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\instalar.log*).

1. Descarregue, instale e abra o [Azure Storage Explorer.](https://storageexplorer.com/) Para tal:

   a. Em **(Local e Anexado)**, contas de **armazenamento**de clique soro direito, e, em seguida, selecione Connect **to Azure storage**.

      ![Ligar ao Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione Utilize o nome e a **chave da conta de armazenamento**e, em seguida, selecione **Next**.

      ![Utilizar o nome e a chave de uma conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Introduza o nome e a chave da sua conta de armazenamento Azure, selecione **Next**, e, em seguida, selecione **Connect**.

      ![Fornecer nome e chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Sob a sua conta de armazenamento Azure conectada, clique direito **em Recipientes Blob,** selecione **Create Blob Container**, e nomeie o novo recipiente.

      ![Criar um contentor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo recipiente e faça upload do script de configuração personalizado e dos seus ficheiros associados. Certifique-se de que faz o upload *principal.cmd* no nível superior do seu recipiente, e não em nenhuma pasta. Certifique-se também de que o seu recipiente contém apenas os ficheiros de configuração personalizados necessários, para que o seu IDesfir Azure-SSIS não deva demorar muito tempo. A duração máxima de uma configuração personalizada está atualmente definida em 45 minutos antes de sair. Isto inclui o tempo para descarregar todos os ficheiros do seu recipiente e instalá-los no IR Azure-SSIS. Se a configuração necessitar de mais tempo, levante um bilhete de apoio.

      ![Faça upload de ficheiros para o recipiente de bolha](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique no recipiente e, em seguida, selecione Obter Assinatura de **Acesso Partilhado**.

      ![Obtenha a Assinatura de Acesso Partilhado para o recipiente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Crie o SAS URI para o seu recipiente com um tempo de validade suficientemente longo e com permissão de leitura/escrita/lista. Você precisa do SAS URI para descarregar e executar o seu script de configuração personalizado e seus ficheiros associados sempre que qualquer nó do seu IR Azure-SSIS for reimagemdo ou reiniciado. Precisa de permissão para enviar registos de execução de configuração.

      > [!IMPORTANT]
      > Certifique-se de que o SAS URI não expira e os recursos de configuração personalizados estão sempre disponíveis durante todo o ciclo de vida do seu IR Azure-SSIS, desde a criação até à eliminação, especialmente se parar regularmente e iniciar o seu IR Azure-SSIS durante este período.

      ![Gerar a Assinatura de Acesso Partilhado para o recipiente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e guarde o SAS URI do seu recipiente.

      ![Copiar e guardar a Assinatura de Acesso Partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando configurar ou reconfigurar o seu IR Azure-SSIS com uma UI de fábrica de dados, pode adicionar ou remover configurações personalizadas selecionando o tempo de funcionamento de **integração Azure-SSIS com configurações/instalações** de componentes adicionais na secção **Definições Avançadas** da central de **integração.** 

   Se quiser adicionar configurações personalizadas padrão, introduza o SAS URI do seu recipiente na caixa SAS URI do **recipiente de configuração Personalizada.** 
   
   Se quiser adicionar configurações personalizadas expressas, selecione **New** para abrir o painel de **configuração personalizado Add** e, em seguida, selecione um tipo na lista de drop-down do tipo de **configuração personalizada Express:**

   * Se selecionar o tipo de **comando 'Executar cmdkey',** pode persistir credenciais de acesso para as suas ações de ficheiro ou ações do Azure-SSIS IR, introduzindo o nome ou nome de domínio do computador visado, nome de conta ou nome de utilizador, e chave de conta ou palavra-passe nas caixas **/Adicionar,** **/Utilizador**e **/Passar.** Isto é semelhante ao funcionamento do comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) windows na sua máquina local.
   
   * Se selecionar o tipo **variável ambiente Add,** pode adicionar variáveis ambientais do Windows para usar nos seus pacotes que funcionam no IR Azure-SSIS, inserindo o seu nome e valor variáveis no seu ambiente nas caixas de **nome variável** e **de valor variável.** Isto é semelhante ao funcionamento do comando windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) na sua máquina local.

   * Se selecionar o tipo de **componente licenciado Instalar,** pode então selecionar um componente integrado dos nossos parceiros ISV na lista de abandono do **nome Componente:**

     * Se selecionar o componente **da Task Factory da SentryOne,** pode instalar o conjunto de componentes da Task [Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) da SentryOne no seu Azure-SSIS IR introduzindo a chave de licença de produto que adquiriu na **caixa-chave da Licença.** A versão integrada atual é **2019.4.3**.

     * Se selecionar o **HEDDA do oh22. Componente IO,** pode instalar o [HEDDA. Componente](https://hedda.io/ssis-component/) de qualidade/limpeza de dados IO a partir de oh22 no seu IR Azure-SSIS após a compra do seu serviço. A versão integrada atual é **de 1.0.13**.

     * Se selecionar o componente **SQLPhonetics.NET do oh22,** pode instalar o [componente SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) qualidade/correspondência de dados a partir de oh22 no seu Ir Azure-SSIS, introduzindo a chave de licença de produto que adquiriu na **caixa-chave da Licença.** A versão integrada atual é **de 1.0.43**.

     * Se selecionar o componente de Toolkit de **Integração SSIS da KingswaySoft,** pode instalar o conjunto de ferramentas de [integração SSIS toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) de conectores para aplicações CRM/ERP/marketing/colaboração, tais como Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, etc. da KingswaySoft no seu Ir Azure-SSIS, introduzindo a chave de licença de produto que adquiriu na **caixa-chave** da Licença. A versão integrada atual é **2019.2.**

     * Se selecionar o componente **SSIS Productivity Pack da KingswaySoft,** pode instalar o conjunto de componentes [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) da KingswaySoft no seu Ir Azure-SSIS, introduzindo a chave de licença de produto que adquiriu na **caixa-chave da Licença.** A versão integrada atual é **de 10.0.**

     * Se selecionar o componente **Xtract IS do Theobald Software,** pode instalar o conjunto de conectores [Xtract IS](https://theobald-software.com/en/xtract-is/) para sistema SAP (ERP, S/4HANA, BW) do Software Theobald no seu IR Azure-SSIS arrastando & deixando cair/carregar o ficheiro de licença de produto que adquiriu deles na caixa de **ficheiros da Licença.** A versão integrada atual é **de 6.1.1.3**.

   As configurações personalizadas expressas adicionadas aparecerão na secção **Definições Avançadas.** Para removê-las, selecione as suas caixas de verificação e, em seguida, **selecione Delete**.

   ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando configurar ou reconfigurar o seu IR Azure-SSIS com powerShell, pode adicionar `Set-AzDataFactoryV2IntegrationRuntime` ou remover as configurações personalizadas executando o cmdlet antes de iniciar o seu IR Azure-SSIS.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   Após o início da configuração personalizada padrão e o seu IR Azure-SSIS, pode encontrar a saída padrão dos *registos principais* de execução e outros registos de execução na pasta *principal.cmd.log* do seu recipiente de armazenamento.

1. Para ver algumas amostras de configurações personalizadas padrão, ligue-se ao nosso recipiente de pré-visualização pública utilizando o Azure Storage Explorer.

   a. Em **(Local e Anexo)**, contas de **armazenamento**de clique soro direito, selecione Connect **to Azure storage,** selecione Use uma cadeia de ligação ou uma assinatura de acesso partilhado **URI,** e, em seguida, selecione **Next**.

      ![Ligue-se ao armazenamento Azure com a Assinatura de Acesso Partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **Utilize um SAS URI** e, em seguida, na caixa **URI,** introduza o seguinte SAS URI:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Forneça a Assinatura de Acesso Partilhado para o recipiente](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Next**, e, em seguida, selecione **Connect**.

   d. No painel esquerdo, selecione o recipiente de **visualização pública** conectado e, em seguida, clique duas vezes na pasta *CustomSetupScript.* Nesta pasta encontram-se os seguintes itens:

      * Uma pasta *Sample,* que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu IR Azure-SSIS. A tarefa não faz nada a não ser dormir por alguns segundos. A pasta também contém uma pasta *gacutil,* cujo conteúdo completo *(gacutil.exe*, *gacutil.exe.config*, e *1033\gacutlrc.dll*) pode ser copiado como está no seu recipiente.

      * Uma pasta *UserScenarios,* que contém várias amostras de configuração personalizadas de cenários reais do utilizador.

        ![Conteúdo do recipiente de pré-visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *UserScenarios* para encontrar os seguintes itens:

      * Uma pasta *.NET FRAMEWORK 3.5,* que contém um script de configuração personalizado *(main.cmd*) para instalar uma versão anterior do .NET Framework que pode ser necessário para componentes personalizados em cada nó do seu IR Azure-SSIS.

      * Uma pasta *BCP,* que contém um script de configuração personalizado *(principal.cmd*) para instalar utilitários de linha de comando SQL Server *(MsSqlCmdLnUtils.msi),* incluindo o programa de cópia a granel *(bcp),* em cada nó do seu IR Azure-SSIS.

      * Uma pasta *EXCEL,* que contém um script de configuração personalizado *(main.cmd*) para instalar conjuntos e bibliotecas C# que pode utilizar em tarefas de script para ler e escrever ficheiros Excel em cada nó do seu IR Azure-SSIS. 
      
        Primeiro, baixe [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), e depois carregue-os todos juntamente com *o principal.cmd* para o seu recipiente. Em alternativa, se pretender utilizar apenas o Excel Connection Manager, fonte excel e destino Excel, o acesso redistribuível necessário já está pré-instalado no seu IR Azure-SSIS, pelo que não necessita de configuração personalizada.
      
      * Uma pasta *MYSQL ODBC,* que contém um script de configuração personalizado *(main.cmd*) para instalar os controladores MySQL ODBC em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar o ODBC Connection Manager, fonte e destino para se ligar ao servidor MySQL. 
     
        Primeiro, [descarregue as versões mais recentes de 64 bits e 32 bits dos instaladores de controladorEs MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*), e depois carregue-as todas com *o principal.cmd* para o seu recipiente.

      * Uma pasta *ORACLE ENTERPRISE,* que contém um script de configuração personalizado *(main.cmd*) e ficheiro config de instalação silenciosa *(cliente.rsp*) para instalar os conectores Oracle e o controlador OCI em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Oracle Connection Manager, fonte e destino para se ligar ao servidor Oracle. 
      
        Primeiro, baixe os Conectores Microsoft v5.0 para oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) do [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o mais recente cliente Oracle (por exemplo, *winx64_12102_client.zip*) da [Oracle,](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)e depois carregue-os todos juntamente com *o principal.cmd* e *cliente.rsp* para o seu contentor. Se utilizar o TNS para se ligar à Oracle, também precisa de descarregar *tnsnames.ora*, editá-lo e carregá-lo para o seu recipiente, para que possa ser copiado para a pasta de instalação Oracle durante a configuração.

      * Uma pasta *ADO.NET ORACLE STANDARD,* que contém um script de configuração personalizado *(main.cmd*) para instalar o Oracle ODP.NET condutor em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar o ADO.NET Connection Manager, fonte e destino para se ligar ao servidor Oracle. 
      
        Primeiro, [baixe o mais recente oracle ODP.NET motorista](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *ODP.NET_Managed_ODAC122cR1.zip),* e depois carregue-o juntamente com o *principal.cmd* para o seu recipiente.
       
      * Uma pasta *ODBC PADRÃO ORACLE,* que contém um script de configuração personalizado *(main.cmd*) para instalar o controlador ODBC Oracle e configurar o nome de fonte de dados (DSN) em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar o ODBC Connection Manager, fonte e destino ou Power Query Connection Manager e fonte com o tipo de fonte de dados oDBC para se ligar ao servidor Oracle. 
      
        Primeiro, baixe o mais recente Cliente Instantâneo oracle (Pacote Básico ou Pacote Basic Lite) e pacote ODBC, e depois carregue-os todos juntamente com *o principal.cmd* para o seu recipiente:
        * [Baixar pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Pacote Básico: *instantclient-basic-windows.x64-18.0.0.0dbru.zip;* Pacote Lite Básico: *instantclient-basiclite-windows.x64-18.3.0.0.0.0dbru.zip;* Pacote ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0.0dbru.zip*) 
        * [Baixar pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Pacote Básico: *instantclient-basic-nt-18.3.0.0.0.0dbru.zip;* Pacote Lite Básico: *instantclient-basiclite-nt-18.3.0.0dbru.zip;* Pacote ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Uma pasta *OLEDB PADRÃO ORACLE,* que contém um script de configuração personalizado *(main.cmd*) para instalar o controlador OLEDB Oracle em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar o OLEDB Connection Manager, fonte e destino para se ligar ao servidor Oracle. 
     
        Primeiro, [baixe o mais recente controlador Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64.zip),* e depois carregue-o juntamente com *o principal.cmd* para o seu recipiente.

      * Uma pasta *PostGRESQL ODBC,* que contém um script de configuração personalizado *(main.cmd*) para instalar os controladores PostgreSQL ODBC em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar o ODBC Connection Manager, fonte e destino para se ligar ao servidor PostgreSQL. 
     
        Primeiro, [faça o download das versões mais recentes de 64 bits e 32 bits dos instaladores de controladores PostgreSQL ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64.msi* e *psqlodbc_x86.msi),* e depois carregue-as todas juntamente com *o principal.cmd* para o seu recipiente.

      * Uma pasta *SAP BW,* que contém um script de configuração personalizado *(main.cmd*) para instalar o conjunto de conector SAP .NET *(librfc32.dll*) em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Gestor de Conexão SAP Business Warehouse (BW) para ligar ao servidor SAP BW. 
      
        Em primeiro lugar, carregue a versão de 64 bits ou a versão de 32 bits de *librfc32.dll* da pasta de instalação SAP juntamente com *o principal.cmd* para o seu recipiente. O guião copia então o conjunto SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a configuração.

      * Uma pasta *DE ARMAZENAMENTO,* que contém um script de configuração personalizado *(main.cmd*) para instalar o Azure PowerShell em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe implementar e executar pacotes SSIS que executam [scripts PowerShell para manipular a sua conta](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)de armazenamento Azure . 
      
        Copie *o principal.cmd*, uma amostra *AzurePowerShell.msi* (ou use a versão mais recente) e *armazenamento.ps1* no seu recipiente. Use *powerShell.dtsx* como modelo para os seus pacotes. O modelo de pacote combina uma tarefa de [descarregamento De Azure Blob,](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)que *descarrega storage.ps1* como um roteiro modificável powerShell, e uma tarefa de [processo executar](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), que executa o script em cada nó.

      * Uma pasta *TERADATA,* que contém um script de configuração personalizado *(main.cmd),* o seu ficheiro associado *(install.cmd*) e pacotes de instaladores *(.msi).* Estes ficheiros instalam os conectores Teradata, a API do Transporte Paralelo Teradata (TPT) e o controlador ODBC em cada nó da sua Edição Empresarial Azure-SSIS IR. Esta configuração permite-lhe utilizar o Teradata Connection Manager, fonte e destino para se ligar ao servidor Teradata. 
      
        Primeiro, [descarregue o ficheiro Teradata Tools and Utilities 15.x zip](http://partnerintelligence.teradata.com) (por exemplo, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* e depois carregue-o juntamente com os ficheiros *.cmd* e *.msi* anteriormente mencionados no seu recipiente.

      * Uma pasta *TLS 1.2,* que contém um script de configuração personalizado *(main.cmd*) para utilizar um protocolo de rede forte de criptografia/mais seguro (TLS 1.2) e desativar as versões SSL/TLS mais antigas em cada nó do seu IR Azure-SSIS.

      * Uma pasta *ZULU OPENJDK,* que contém um script de configuração personalizado *(main.cmd*) e ficheiro PowerShell *(install_openjdk.ps1*) para instalar o Zulu OpenJDK em cada nó do seu IR Azure-SSIS. Esta configuração permite-lhe utilizar a Azure Data Lake Store e os conectores de ficheiros flexíveis para processar ficheiros ORC e Parquet. Para mais informações, consulte [o Pack de Recursos Azure para serviços de integração.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) 
      
        Primeiro, [baixe o mais recente Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* e depois carregue-o juntamente com *o principal.cmd* e *install_openjdk.ps1* para o seu recipiente.

        ![Pastas na pasta de cenários de utilizador](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para experimentar estas amostras de configuração personalizadas, copie o conteúdo da pasta selecionada para o seu recipiente.
   
      Quando configurar ou reconfigurar o seu IR Azure-SSIS utilizando o UI da Fábrica de Dados, selecione o Tempo de Funcionamento de **Integração Azure-SSIS com configurações/instalações de componentes adicionais,** verifique a caixa na secção **Definições Avançadas** e, em seguida, introduza o SAS URI do seu recipiente na caixa **De configuração Personalizada SAS URI.**
   
      Quando configurar ou reconfigurar o seu IR Azure-SSIS com powerShell, execute o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet com o SAS URI do seu recipiente como valor para `SetupScriptContainerSasUri` o parâmetro.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a Edição Empresarial do Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolver componentes personalizados pagos ou licenciados para o Runtime de Integração Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
