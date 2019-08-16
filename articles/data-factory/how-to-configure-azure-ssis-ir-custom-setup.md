---
title: Personalizar a instalação para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Este artigo descreve como usar a interface de instalação personalizada para o tempo de execução de integração do Azure-SSIS para instalar componentes adicionais ou alterar configurações
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4962070d69af98d0c7b10dc6f931612766529dce
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515718"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personalizar a instalação para o tempo de execução de integração do Azure-SSIS

A interface de instalação personalizada para o Integration Runtime do Azure-SSIS fornece uma interface para adicionar suas próprias etapas de configuração durante o provisionamento ou reconfiguração do IR do Azure-SSIS. A instalação personalizada permite alterar a configuração operacional padrão ou o ambiente (por exemplo, para iniciar serviços adicionais do Windows ou manter as credenciais de acesso para compartilhamentos de arquivos) ou instalar componentes adicionais (por exemplo, assemblies, drivers ou extensões) em cada nó do IR do Azure-SSIS.

Você configura a configuração personalizada preparando um script e seus arquivos associados e carregando-os em um contêiner de BLOB em sua conta de armazenamento do Azure. Você fornece uma SAS (assinatura de acesso compartilhado) Uniform Resource Identifier (URI) para seu contêiner ao provisionar ou reconfigurar o IR do Azure-SSIS. Cada nó do IR do Azure-SSIS então baixa o script e seus arquivos associados do seu contêiner e executa a configuração personalizada com privilégios elevados. Quando a instalação personalizada for concluída, cada nó carregará a saída padrão de execução e outros logs em seu contêiner.

Você pode instalar componentes gratuitos ou não licenciados e componentes pagos ou licenciados. Se você for um ISV, consulte [como desenvolver componentes pagos ou licenciados para o ir do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Os nós da série v2 do IR do Azure-SSIS não são adequados para a instalação personalizada, portanto, use os nós da série V3 em vez disso.  Se você já usa os nós da série v2, altere para usar os nós da série V3 assim que possível.

## <a name="current-limitations"></a>Limitações atuais

-   Se você quiser usar `gacutil.exe` o para instalar assemblies no GAC (cache de assembly global), será necessário fornecer `gacutil.exe` como parte da configuração personalizada ou usar a cópia fornecida no contêiner de visualização pública.

-   Se você quiser fazer referência a uma subpasta em seu `msiexec.exe` script, o `.\` não oferecerá suporte à notação para fazer referência à pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`de.

-   Se você precisar unir seu IR do Azure-SSIS com a instalação personalizada a uma rede virtual, somente Azure Resource Manager rede virtual tem suporte. Não há suporte para a rede virtual clássica.

-   Atualmente, não há suporte para o compartilhamento administrativo no IR do Azure-SSIS.

-   Não há suporte para o driver ODBC para acesso IBM iSeries no Azure-SSIS IR. Você poderá ver o erro de instalação durante a instalação personalizada. Entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o IR do Azure-SSIS, você precisará dos seguintes itens:

-   [Subscrição do Azure](https://azure.microsoft.com/)

-   [Um banco de dados SQL do Azure ou um servidor Instância Gerenciada](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Provisionar seu IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Para a instalação personalizada, você carrega e armazena seu script de instalação personalizada e seus arquivos associados em um contêiner de BLOB. O processo de instalação personalizada também carrega seus logs de execução no mesmo contêiner de BLOBs.

## <a name="instructions"></a>Instruções

1. Baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps).

1. Prepare o script de instalação personalizada e seus arquivos associados (por exemplo, arquivos. bat,. cmd,. exe,. dll,. msi ou. ps1).

   1.  Você deve ter um arquivo de script `main.cmd`chamado, que é o ponto de entrada da configuração personalizada.

   1.  Se você quiser que logs adicionais gerados por outras ferramentas (por exemplo `msiexec.exe`,) sejam carregados em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de log em seus scripts ( `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`por exemplo,).

1. Baixe, instale e inicie o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).

   1. Em **(local e anexado)** , selecione contas de **armazenamento** com o botão direito do mouse e selecione **conectar ao armazenamento do Azure**.

      ![Ligar ao armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Selecione **usar um nome de conta de armazenamento e uma chave** e selecione **Avançar**.

      ![Utilizar o nome e a chave de uma conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Insira o nome e a chave da conta de armazenamento do Azure, selecione **Avançar**e, em seguida, selecione **conectar**.

      ![Forneça o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Em sua conta de armazenamento do Azure conectada, clique com o botão direito do mouse em contêineres de **blob**, selecione **criar contêiner de blob**e nomeie o novo contêiner.

      ![Criar um contentor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Selecione o novo contêiner e carregue seu script de instalação personalizada e seus arquivos associados. Certifique-se de carregar `main.cmd` no nível superior do seu contêiner, não em nenhuma pasta. Verifique também se o contêiner contém apenas os arquivos de instalação personalizados necessários, portanto, baixá-los em seu IR do Azure-SSIS posteriormente não levará muito tempo. O período máximo para a instalação personalizada está definido atualmente em 45 minutos antes de expirar e isso inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no IR do Azure-SSIS. Se um período mais longo for necessário, gere um tíquete de suporte.

      ![Carregar arquivos para o contêiner de BLOBs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**.

      ![Obter a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Crie o URI de SAS para seu contêiner com uma hora de expiração suficientemente longa e com permissões de leitura + gravação + lista. Você precisa do URI de SAS para baixar e executar o script de instalação personalizada e seus arquivos associados sempre que qualquer nó do seu IR do Azure-SSIS for refeita a imagem/reiniciado. Você precisa de permissão de gravação para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Verifique se o URI SAS não expira e se os recursos de instalação personalizados estão sempre disponíveis durante todo o ciclo de vida do IR do Azure-SSIS, de criação a exclusão, especialmente se você parar e iniciar regularmente o IR do Azure-SSIS durante esse período.

      ![Gerar a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Copie e salve o URI de SAS do seu contêiner.

      ![Copiar e salvar a assinatura de acesso compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Quando você provisiona ou reconfigura seu IR do Azure-SSIS com a interface do usuário do Data Factory, antes de iniciar o IR do Azure-SSIS, insira o URI de SAS do seu contêiner no campo apropriado no painel de **Configurações avançadas** :

      ![Insira a assinatura de acesso compartilhado](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Ao provisionar ou reconfigurar o ir do Azure-SSIS com o PowerShell, antes de iniciar o ir do Azure- `Set-AzDataFactoryV2IntegrationRuntime` SSIS, execute o cmdlet com o URI de SAS do seu contêiner `SetupScriptContainerSasUri` como o valor para o novo parâmetro. Por exemplo:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Depois que a instalação personalizada for concluída e o ir do Azure-SSIS for iniciado, você poderá encontrar `main.cmd` a saída padrão do e outros `main.cmd.log` logs de execução na pasta do seu contêiner de armazenamento.

1. Para ver outros exemplos de instalação personalizada, conecte-se ao contêiner de visualização pública com Gerenciador de Armazenamento do Azure.

   a.  Em **(local e anexado)** , clique com o botão direito do mouse em **contas de armazenamento**, selecione **conectar ao armazenamento do Azure**, selecione **usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado**e, em seguida, selecione **Avançar**.

      ![Conectar-se ao armazenamento do Azure com a assinatura de acesso compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Selecione **usar um URI de SAS** e insira o seguinte URI de SAS para o contêiner de visualização pública. Selecione **Avançar**e selecione **conectar**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fornecer a assinatura de acesso compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione o contêiner de visualização pública conectada e clique duas `CustomSetupScript` vezes na pasta. Nesta pasta estão os seguintes itens:

      1. Uma `Sample` pasta, que contém uma instalação personalizada para instalar uma tarefa básica em cada nó do seu ir do Azure-SSIS. A tarefa não faz nada, mas está em suspensão por alguns segundos. A pasta também contém uma `gacutil` pasta, todo o conteúdo de que (`gacutil.exe`, `gacutil.exe.config`e `1033\gacutlrc.dll`) pode ser copiado como está em seu contêiner. Além disso `main.cmd` , o contém comentários para manter as credenciais de acesso para compartilhamentos de arquivos.

      1. Uma `UserScenarios` pasta, que contém várias configurações personalizadas para cenários de usuário reais.

   ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Clique duas vezes na `UserScenarios` pasta. Nesta pasta estão os seguintes itens:

      1. Uma `.NET FRAMEWORK 3.5` pasta, que contém uma instalação personalizada para instalar uma versão anterior do .NET Framework que pode ser necessária para componentes personalizados em cada nó do ir do Azure-SSIS.

      1. Uma `BCP` pasta, que contém uma instalação personalizada para instalar SQL Server utilitários de linha de comando`MsSqlCmdLnUtils.msi`(), incluindo o programa de cópia`bcp`em massa (), em cada nó do ir do Azure-SSIS.

      1. Uma `EXCEL` pasta, que contém uma instalação personalizada para instalar assemblies de código-fonte`DocumentFormat.OpenXml.dll`aberto `ExcelDataReader.DataSet.dll`(, `ExcelDataReader.dll`e) em cada nó do ir do Azure-SSIS.

      1. Uma `ORACLE ENTERPRISE` pasta, que contém um script de instalação personalizado`main.cmd`() e o arquivo de configuração`client.rsp`de instalação silenciosa () para instalar os conectores Oracle e o driver de OCI em cada nó do seu Azure-SSIS ir Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões Oracle, a origem e o destino. Primeiro, baixe o Microsoft Connectors v 5.0 para`AttunitySSISOraAdaptersSetup.msi` Oracle `AttunitySSISOraAdaptersSetup64.msi`(e) do [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais `winx64_12102_client.zip` recente, por exemplo,-da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)e, em seguida, carregue-os juntos com `main.cmd` e`client.rsp` em seu contêiner. Se você usar TNS para se conectar ao Oracle, também precisará baixá `tnsnames.ora`-lo, editá-lo e carregá-lo em seu contêiner, para que ele possa ser copiado para a pasta de instalação do Oracle durante a instalação.

      1. Uma `ORACLE STANDARD ADO.NET` pasta, que contém um script de instalação personalizado`main.cmd`() para instalar o Driver Oracle odp.net em cada nó do ir do Azure-SSIS. Essa configuração permite que você use o Gerenciador de conexões do ADO.NET, a origem e o destino. Primeiro, baixe o driver do Oracle odp.net mais recente, por `ODP.NET_Managed_ODAC122cR1.zip` exemplo,-do [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), e carregue-o `main.cmd` junto com o seu contêiner.
       
      1. Uma `ORACLE STANDARD ODBC` pasta, que contém um script de instalação personalizado`main.cmd`() para instalar o driver ODBC do Oracle e configurar o DSN em cada nó do ir do Azure-SSIS. Essa configuração permite usar o Gerenciador de conexões ODBC/origem/destino ou Power Query Gerenciador de conexões/origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. Primeiro, baixe o pacote mais recente do Oracle Instant Client (pacote básico ou Basic Lite) e ODBC, por exemplo, os pacotes de 64 bits [aqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacote básico: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, pacote Basic Lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pacote ODBC: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) ou os pacotes de 32 bits [aqui](https://www.oracle.com/technetwork/topics/winsoft-085727.html) ( `instantclient-basic-nt-18.3.0.0.0dbru.zip`pacote básico:, pacote Basic Lite: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pacote ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) e, em seguida, carregá-los `main.cmd` em seu contêiner.

      1. Uma `SAP BW` pasta, que contém um script de instalação personalizado`main.cmd`() para instalar o assembly do conector do`librfc32.dll`SAP .net () em cada nó do seu Azure-SSIS ir Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões SAP BW, a origem e o destino. Primeiro, carregue a versão de 64 bits ou de 32 bits do `librfc32.dll` da pasta de instalação do SAP em seu contêiner, junto com. `main.cmd` Em seguida, o script copia o assembly SAP `%windir%\SysWow64` para `%windir%\System32` a pasta ou durante a instalação.

      1. Uma `STORAGE` pasta, que contém uma instalação personalizada para instalar Azure PowerShell em cada nó do ir do Azure-SSIS. Essa configuração permite implantar e executar pacotes do SSIS que executam [scripts do PowerShell para manipular sua conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copie `main.cmd`, um exemplo `AzurePowerShell.msi` (ou instale a versão mais recente) e `storage.ps1` para o contêiner. Use o PowerShell. dtsx como um modelo para seus pacotes. O modelo de pacote combina uma [tarefa de download de blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que é baixada `storage.ps1` como um script do PowerShell modificável e uma [tarefa Executar processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que executa o script em cada nó.

      1. Uma `TERADATA` pasta, que contém um script de instalação personalizado`main.cmd`(), seu arquivo associado`install.cmd`() e pacotes do instalador`.msi`(). Esses arquivos instalam os conectores Teradata, a API TPT e o driver ODBC em cada nó do Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões do Teradata, a origem e o destino. Primeiro, baixe o arquivo zip de ferramentas e utilitários do Teradata (TTU) 15. x (por `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`exemplo,) de [Teradata](http://partnerintelligence.teradata.com)e, em seguida, carregue- `.cmd` o `.msi` junto com os arquivos acima e em seu contêiner.

   ![Pastas na pasta cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Para testar esses exemplos de instalação personalizada, copie e cole o conteúdo da pasta selecionada em seu contêiner. Quando você provisiona ou reconfigura seu ir do Azure-SSIS com o PowerShell, `Set-AzDataFactoryV2IntegrationRuntime` execute o cmdlet com o URI de SAS do seu contêiner como o `SetupScriptContainerSasUri` valor para o novo parâmetro.

## <a name="next-steps"></a>Passos Seguintes

-   [Enterprise Edition do Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
