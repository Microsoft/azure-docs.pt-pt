---
title: Personalizar a instalação para o tempo de execução de integração do Azure-SSIS
description: Este artigo descreve como usar a interface de instalação personalizada para o tempo de execução de integração do Azure-SSIS para instalar componentes adicionais ou alterar configurações
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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497050"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personalizar a instalação para o tempo de execução de integração do Azure-SSIS

A interface de instalação personalizada para o Azure-SSIS Integration Runtime fornece uma interface para adicionar suas próprias etapas de configuração durante o provisionamento ou reconfiguração do seu Azure-SSIS IR. 

A instalação personalizada permite alterar a configuração operacional padrão ou o ambiente (por exemplo, para iniciar serviços adicionais do Windows ou manter as credenciais de acesso para compartilhamentos de arquivos) ou instalar componentes adicionais (por exemplo, assemblies, drivers ou extensões) em cada nó do seu Azure-SSIS IR.

Há duas maneiras de fazer configurações personalizadas em seu Azure-SSIS IR: configurações personalizadas expressas sem scripts e configurações personalizadas padrão com scripts.

Com as instalações personalizadas expressas, você pode executar algumas configurações comuns do sistema/comandos do Windows ou instalar alguns componentes adicionais populares/recomendados sem usar nenhum script.  

Com as configurações personalizadas padrão, você precisa preparar um script e seus arquivos associados e carregá-los juntos em um contêiner de BLOB em sua conta de armazenamento do Azure. Em seguida, você fornece uma SAS (assinatura de acesso compartilhado) Uniform Resource Identifier (URI) para seu contêiner ao provisionar ou reconfigurar seu Azure-SSIS IR. Cada nó do seu Azure-SSIS IR baixará o script e seus arquivos associados do seu contêiner e executará a instalação personalizada com privilégios elevados. Quando a instalação personalizada for concluída, cada nó carregará a saída padrão de execução e outros logs em seu contêiner.

Você pode instalar componentes gratuitos/não licenciados e pagos/licenciados com configurações personalizadas expressas/padrão. Se você for um ISV, consulte nossa documentação sobre [como desenvolver componentes pagos ou licenciados para Azure-SSIS ir](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Os nós da série v2 de Azure-SSIS IR não são adequados para a instalação personalizada, portanto, use os nós da série V3 em vez disso.  Se você já usa os nós da série v2, altere para usar os nós da série V3 assim que possível.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam somente a configurações personalizadas padrão:

- Se você quiser usar `gacutil.exe` em seu script para instalar assemblies no GAC (cache de assembly global), será necessário fornecer `gacutil.exe` como parte da configuração personalizada ou usar a cópia fornecida em nosso contêiner de visualização pública abaixo.

- Se você quiser fazer referência a uma subpasta em seu script, `msiexec.exe` não oferece suporte à notação de `.\` para fazer referência à pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Os compartilhamentos administrativos, ou seja, compartilhamentos de rede ocultos criados automaticamente pelo Windows, atualmente não têm suporte no Azure-SSIS IR.

- Não há suporte para o driver ODBC para acesso IBM iSeries no Azure-SSIS IR. Você poderá ver erros de instalação durante a instalação personalizada. Entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar sua Azure-SSIS IR, você precisará das seguintes coisas:

- [Subscrição do Azure](https://azure.microsoft.com/)

- [Provisionando seu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Não é necessário para configurações personalizadas expressas. Para configurações personalizadas padrão, você carrega e armazena seu script de instalação personalizada e seus arquivos associados em um contêiner de BLOB. O processo de instalação personalizada também carrega seus logs de execução no mesmo contêiner de BLOBs.

## <a name="instructions"></a>Instruções

1. Se você quiser provisionar ou reconfigurar seu Azure-SSIS IR com o PowerShell, baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps). Para as configurações personalizadas expressas, pule para a etapa 4.

1. Prepare o script de instalação personalizada e seus arquivos associados (por exemplo, arquivos. bat,. cmd,. exe,. dll,. msi ou. ps1).

   1. Você deve ter um arquivo de script chamado `main.cmd`, que é o ponto de entrada da configuração personalizada.

   1. Você precisa verificar se o script pode ser executado silenciosamente, é recomendável testar o script no computador local primeiro.

   1. Se desejar que logs adicionais gerados por outras ferramentas (por exemplo, `msiexec.exe`) sejam carregados em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de log em seus scripts (por exemplo, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Baixe, instale e inicie o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).

   1. Em **(local e anexado)** , selecione contas de **armazenamento** com o botão direito do mouse e selecione **conectar ao armazenamento do Azure**.

      ![Ligar ao armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Selecione **usar um nome de conta de armazenamento e uma chave** e selecione **Avançar**.

      ![Utilizar o nome e a chave de uma conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Insira o nome e a chave da conta de armazenamento do Azure, selecione **Avançar**e, em seguida, selecione **conectar**.

      ![Forneça o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Em sua conta de armazenamento do Azure conectada, clique com o botão direito do mouse em **contêineres de blob**, selecione **criar contêiner de blob**e nomeie o novo contêiner.

      ![Criar um contentor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Selecione o novo contêiner e carregue seu script de instalação personalizada e seus arquivos associados. Certifique-se de carregar `main.cmd` no nível superior do seu contêiner, não em nenhuma pasta. Verifique também se o contêiner contém apenas os arquivos de instalação personalizados necessários, então baixá-los em seu Azure-SSIS IR mais tarde não levará muito tempo. O período máximo para a instalação personalizada está definido atualmente em 45 minutos antes de expirar e isso inclui o tempo para baixar todos os arquivos do contêiner e instalá-los em Azure-SSIS IR. Se um período mais longo for necessário, gere um tíquete de suporte.

      ![Carregar arquivos para o contêiner de BLOBs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**.

      ![Obter a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Crie o URI de SAS para seu contêiner com uma hora de expiração suficientemente longa e com permissões de leitura + gravação + lista. Você precisa do URI de SAS para baixar e executar o script de instalação personalizada e seus arquivos associados sempre que qualquer nó de sua Azure-SSIS IR for refeita a imagem/reiniciado. Você precisa de permissão de gravação para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Verifique se o URI SAS não expira e se os recursos de instalação personalizados estão sempre disponíveis durante todo o ciclo de vida do seu Azure-SSIS IR, desde a criação até a exclusão, especialmente se você parar e iniciar regularmente o Azure-SSIS IR durante esse período.

      ![Gerar a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Copie e salve o URI de SAS do seu contêiner.

      ![Copiar e salvar a assinatura de acesso compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Ao provisionar ou reconfigurar seu Azure-SSIS IR com a interface do usuário do Data Factory, você pode adicionar/remover configurações personalizadas marcando a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com as definições de sistema/instalações de componentes adicionais** na seção **Configurações avançadas** do painel de instalação do Integration Runtime. 

   Se você quiser adicionar configurações personalizadas padrão, insira o URI de SAS do seu contêiner no campo **URI de SAS do contêiner de instalação personalizada** . 
   
   Se você quiser adicionar configurações personalizadas expressas, selecione **novo** para abrir o painel **Adicionar configuração personalizada expressa** e, em seguida, selecione qualquer tipo no menu suspenso **tipo de instalação personalizada expressa** :

   1. Se você selecionar o tipo de **comando ' executar cmdkey** ', poderá manter as credenciais de acesso para seus arquivos compartilhamentos/do Azure no Azure-SSIS ir inserindo seu nome de computador/domínio de destino, nome da conta/nome de usuário e chave de conta/senha nos campos **/Add**, **/User**e **/Pass** , respectivamente. Isso é semelhante à execução do comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) do Windows no computador local.
   
   1. Se você selecionar o tipo de **variável adicionar ambiente** , poderá adicionar variáveis de ambiente do Windows para usar em seus pacotes executados no Azure-SSIS ir inserindo o nome e o valor da variável de ambiente nos campos **nome da variável** e **valor da variável** , respectivamente. Isso é semelhante à execução do comando [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) do Windows no computador local.

   1. Se você selecionar o tipo de **componente instalar licenciado** , poderá selecionar todos os componentes integrados de nossos parceiros ISV no menu suspenso **nome do componente** :

      1. Se você selecionar o componente de **fábrica de tarefas do SentryOne** , poderá instalar o pacote de [tarefas da fábrica](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de componentes do SentryOne no seu Azure-SSIS ir inserindo a chave de licença do produto que você comprou a partir deles no campo chave de **licença** . A versão integrada atual é **2019.4.3**.

      1. Se você selecionar o **OH22'S HEDDA. Componente de e/s** , você pode instalar o [HEDDA. ](https://hedda.io/ssis-component/)Componente de qualidade de dados de es/limpeza do oh22 no seu Azure-SSIS ir depois de comprar seu serviço. A versão integrada atual é **1.0.13**.

      1. Se você selecionar o componente **oh22's SQLPhonetics.net** , poderá instalar o componente de correspondência/qualidade de dados do [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) do oh22 no seu Azure-SSIS ir inserindo a chave de licença do produto que você comprou a partir deles no campo **chave de licença** . A versão integrada atual é **1.0.43**.
   
   Suas configurações personalizadas expressas adicionadas aparecerão na seção **Advanced Settings** . Para removê-los, você pode selecionar suas caixas de seleção e, em seguida, selecionar **excluir**.

   ![Configurações avançadas com instalações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   Ao provisionar ou reconfigurar seu Azure-SSIS IR com o PowerShell, você pode adicionar/remover configurações personalizadas executando o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` antes de iniciar o Azure-SSIS IR.
   
   Para configurações personalizadas padrão, você pode fornecer o URI de SAS do seu contêiner como o valor para `SetupScriptContainerSasUri` parâmetro. Por exemplo:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   Depois que a instalação personalizada padrão for concluída e o Azure-SSIS IR for iniciado, você poderá encontrar a saída padrão de `main.cmd` e outros logs de execução na pasta `main.cmd.log` do seu contêiner de armazenamento.

1. Para ver alguns exemplos de configurações personalizadas padrão, conecte-se ao nosso contêiner de visualização pública com Gerenciador de Armazenamento do Azure.

   1. Em **(local e anexado)** , clique com o botão direito do mouse em **contas de armazenamento**, selecione **conectar ao armazenamento do Azure**, selecione **usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado**e, em seguida, selecione **Avançar**.

      ![Conectar-se ao armazenamento do Azure com a assinatura de acesso compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Selecione **usar um URI de SAS** e insira o seguinte URI de SAS para o contêiner de visualização pública. Selecione **Avançar**e, em seguida, selecione **conectar**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fornecer a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Selecione o contêiner de visualização pública conectada e clique duas vezes na pasta `CustomSetupScript`. Nesta pasta estão os seguintes itens:

      1. Uma pasta `Sample`, que contém uma instalação personalizada para instalar uma tarefa básica em cada nó do seu Azure-SSIS IR. A tarefa não faz nada, mas está em suspensão por alguns segundos. A pasta também contém uma pasta `gacutil`, todo o conteúdo do qual (`gacutil.exe`, `gacutil.exe.config`e `1033\gacutlrc.dll`) pode ser copiado como está em seu contêiner.

      1. Uma pasta `UserScenarios`, que contém vários exemplos de instalação personalizada de cenários de usuário reais.

      ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Clique duas vezes na pasta `UserScenarios` para localizar os seguintes itens:

      1. Uma pasta `.NET FRAMEWORK 3.5`, que contém uma instalação personalizada para instalar uma versão anterior do .NET Framework que pode ser necessária para componentes personalizados em cada nó do seu Azure-SSIS IR.

      1. Uma pasta `BCP`, que contém uma instalação personalizada para instalar SQL Server utilitários de linha de comando (`MsSqlCmdLnUtils.msi`), incluindo o programa de cópia em massa (`bcp`), em cada nó do seu Azure-SSIS IR.

      1. Uma pasta `EXCEL`, que contém um script de instalação personalizada (`main.cmd`) para C# instalar assemblies/bibliotecas que você pode usar em tarefas de script para ler/gravar dinamicamente arquivos do Excel em cada nó do seu Azure-SSIS ir. Primeiro, baixe `ExcelDataReader.dll` [aqui](https://www.nuget.org/packages/ExcelDataReader/) e `DocumentFormat.OpenXml.dll` [aqui](https://www.nuget.org/packages/DocumentFormat.OpenXml/)e, em seguida, carregue-os junto com `main.cmd` em seu contêiner. Como alternativa, se você quiser apenas usar o Gerenciador de conexões padrão do Excel/origem/destino, o acesso redistribuível necessário já está pré-instalado no seu Azure-SSIS IR, portanto, você não precisa de nenhuma configuração personalizada.
      
      1. Uma pasta `MYSQL ODBC`, que contém um script de instalação personalizada (`main.cmd`) para instalar os drivers ODBC do MySQL em cada nó de seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões ODBC/origem/destino para se conectar ao servidor MySQL. Primeiro, baixe as versões mais recentes de 64 bits e 32 bits dos instaladores de driver ODBC do MySQL-por exemplo, `mysql-connector-odbc-8.0.13-winx64.msi` e `mysql-connector-odbc-8.0.13-win32.msi`-do [MySQL](https://dev.mysql.com/downloads/connector/odbc/)e, em seguida, carregue-as junto com `main.cmd` em seu contêiner.

      1. Uma pasta `ORACLE ENTERPRISE`, que contém um script de instalação personalizada (`main.cmd`) e o arquivo de configuração de instalação silenciosa (`client.rsp`) para instalar os conectores Oracle e o driver de OCI em cada nó do seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões Oracle/origem/destino para se conectar ao servidor Oracle. Primeiro, baixe o Microsoft Connectors v 5.0 para Oracle (`AttunitySSISOraAdaptersSetup.msi` e `AttunitySSISOraAdaptersSetup64.msi`) do [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais recente, por exemplo, `winx64_12102_client.zip`-da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)e, em seguida, carregue-os juntos com `main.cmd` e `client.rsp` em seu contêiner. Se você usar TNS para se conectar ao Oracle, também precisará baixar `tnsnames.ora`, editá-lo e carregá-lo em seu contêiner, para que ele possa ser copiado para a pasta de instalação do Oracle durante a instalação.

      1. Uma pasta `ORACLE STANDARD ADO.NET`, que contém um script de instalação personalizada (`main.cmd`) para instalar o Driver Oracle ODP.NET em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões do ADO.NET/origem/destino para se conectar ao servidor Oracle. Primeiro, baixe o driver do Oracle ODP.NET mais recente, por exemplo, `ODP.NET_Managed_ODAC122cR1.zip`-do [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)e, em seguida, carregue-o junto com `main.cmd` em seu contêiner.
       
      1. Uma pasta `ORACLE STANDARD ODBC`, que contém um script de instalação personalizada (`main.cmd`) para instalar o driver ODBC Oracle e configurar o DSN em cada nó do seu Azure-SSIS IR. Essa configuração permite usar o Gerenciador de conexões ODBC/origem/destino ou Power Query Gerenciador de conexões/origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. Primeiro, baixe o pacote mais recente do Oracle Instant Client (pacote básico ou Basic Lite) e ODBC, por exemplo, os pacotes de 64 bits [aqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacote básico: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, pacote Basic Lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pacote ODBC: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) ou os pacotes de 32 bits [aqui](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacote básico: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, pacote Basic Lite: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pacote ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) e, em seguida, carregue-os juntos com `main.cmd` em seu contêiner.

      1. Uma pasta `ORACLE STANDARD OLEDB`, que contém um script de instalação personalizada (`main.cmd`) para instalar o Driver Oracle OLEDB em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões OLEDB/origem/destino para se conectar ao servidor Oracle. Primeiro, baixe o Driver Oracle OLEDB mais recente, por exemplo, `ODAC122010Xcopy_x64.zip`-do [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), em seguida, carregue-o junto com `main.cmd` em seu contêiner.

      1. Uma pasta `POSTGRESQL ODBC`, que contém um script de instalação personalizada (`main.cmd`) para instalar os drivers ODBC do PostgreSQL em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões ODBC/origem/destino para se conectar ao servidor PostgreSQL. Primeiro, baixe as versões mais recentes de 64 bits e 32 bits dos instaladores de driver ODBC do PostgreSQL-por exemplo, `psqlodbc_x64.msi` e `psqlodbc_x86.msi`-do [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/)e, em seguida, carregue-as junto com `main.cmd` em seu contêiner.

      1. Uma pasta `SAP BW`, que contém um script de instalação personalizada (`main.cmd`) para instalar o assembly do conector do SAP .NET (`librfc32.dll`) em cada nó do seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões SAP BW/origem/destino para se conectar ao servidor SAP BW. Primeiro, carregue a versão de 64 bits ou 32 bits do `librfc32.dll` da pasta de instalação do SAP junto com `main.cmd` em seu contêiner. Em seguida, o script copia o assembly SAP para a pasta `%windir%\SysWow64` ou `%windir%\System32` durante a instalação.

      1. Uma pasta `STORAGE`, que contém uma instalação personalizada para instalar Azure PowerShell em cada nó do seu Azure-SSIS IR. Essa configuração permite implantar e executar pacotes do SSIS que executam [scripts do PowerShell para manipular sua conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copie `main.cmd`, um `AzurePowerShell.msi` de exemplo (ou use a versão mais recente) e `storage.ps1` ao seu contêiner. Use o PowerShell. dtsx como um modelo para seus pacotes. O modelo de pacote combina uma [tarefa de download de blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa `storage.ps1` como um script do PowerShell modificável e uma [tarefa Executar processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que executa o script em cada nó.

      1. Uma pasta `TERADATA`, que contém um script de instalação personalizada (`main.cmd`), seu arquivo associado (`install.cmd`) e os pacotes do instalador (`.msi`). Esses arquivos instalam os conectores do Teradata, a API do TPT e o driver ODBC em cada nó do seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões do Teradata/origem/destino para se conectar ao servidor Teradata. Primeiro, baixe o arquivo zip de ferramentas e utilitários do Teradata 15. x (por exemplo, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) do [Teradata](http://partnerintelligence.teradata.com), em seguida, carregue-o junto com os `.cmd` e `.msi` arquivos acima em seu contêiner.

      1. Uma pasta `ZULU OPENJDK`, que contém um script de instalação personalizado (`main.cmd`) e um arquivo do PowerShell (`install_openjdk.ps1`) para instalar o Zulu OpenJDK em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use conectores de arquivo Azure Data Lake Store/flexível para processar arquivos ORC/parquet, consulte [aqui](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) para obter mais informações. Primeiro, baixe a mais recente Zulu OpenJDK – por exemplo, `zulu8.33.0.1-jdk8.0.192-win_x64.zip`- [daqui, carregue](https://www.azul.com/downloads/zulu/zulu-windows/)-a junto com `main.cmd` e `install_openjdk.ps1` em seu contêiner.

      ![Pastas na pasta cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Para testar esses exemplos de instalação personalizada, copie e cole o conteúdo da pasta selecionada em seu contêiner.
   
      Ao provisionar ou reconfigurar seu Azure-SSIS IR com a interface do usuário do Data Factory, marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na seção **Configurações avançadas** e insira o URI de SAS do seu contêiner no campo **URI SAS do contêiner de instalação personalizada** .
   
      Ao provisionar ou reconfigurar o Azure-SSIS IR com o PowerShell, execute o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` com o URI SAS do seu contêiner como o valor do parâmetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Passos seguintes

-   [Enterprise Edition do Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
