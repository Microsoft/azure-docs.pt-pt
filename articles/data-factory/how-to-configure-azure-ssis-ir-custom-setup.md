---
title: Personalizar a configuração para o runtime de integração de SSIS do Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar a interface de configuração personalizada para o runtime de integração Azure-SSIS para instalar componentes adicionais ou alterar as definições
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
ms.openlocfilehash: 5ade16bbd45203892cd9a3117dd52471884cf700
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455824"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personalizar a configuração para o runtime de integração Azure-SSIS

A interface de configuração personalizada para o Runtime de integração Azure-SSIS fornece uma interface para adicionar suas próprias etapas de configuração durante o aprovisionamento ou a reconfiguração de sua ir Azure-SSIS. Configuração personalizada permite-lhe alterar a predefinição de operativo ambiente (por exemplo, para iniciar serviços adicionais do Windows ou manter as credenciais de acesso para partilhas de ficheiros) ou de configuração ou instalar componentes adicionais (por exemplo, assemblies, drivers ou extensões) em cada nó do seu ir Azure-SSIS.

Configurar o seu programa de configuração personalizado ao preparar um script e os ficheiros associados e carregá-los para um contentor de BLOBs na sua conta de armazenamento do Azure. Fornecer um identificador de assinatura de acesso partilhado (SAS) recurso uniforme (URI) para o seu contentor, quando aprovisionar ou reconfigurar o ir Azure-SSIS. Cada nó do Runtime de integração Azure-SSIS, em seguida, transfere o script e os ficheiros associados a partir do seu contentor e executa a sua configuração personalizada com privilégios elevados. Quando a configuração personalizada é concluída, cada nó carrega a saída padrão de execução e outros registos para o seu contentor.

Pode instalar componentes gratuitos ou não licenciados e componentes pagas ou licenciadas. Se for um ISV, consulte [como desenvolver pago ou licenciado componentes para o IR Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Os nós de série a v2 de IR Azure-SSIS não são adequados para a configuração personalizada, para que utilize em vez disso, os nós de série v3.  Se já utiliza os nós de série dv2, mude para utilizar os nós de série v3 logo que possível.

## <a name="current-limitations"></a>Limitações atuais

-   Se quiser usar `gacutil.exe` para instalar assemblies no Cache de Assembly Global (GAC), tem de fornecer `gacutil.exe` como parte da sua configuração personalizada ou utilize a cópia fornecida no contentor de pré-visualização pública.

-   Se deseja fazer referência uma subpasta no seu script `msiexec.exe` não suporta o `.\` notação para referenciar a pasta de raiz. Utilize um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Se tiver de associar o runtime de integração Azure-SSIS com a configuração personalizada a uma rede virtual, é suportada apenas do Azure Resource Manager rede virtual. Rede virtual clássica não é suportada.

-   Partilha administrativa não é atualmente suportada no ir Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o runtime de integração Azure-SSIS, precisa do seguinte:

-   [Subscrição do Azure](https://azure.microsoft.com/)

-   [Um servidor de base de dados do Azure SQL ou à instância gerida](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Aprovisionar o runtime de integração Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Para a configuração personalizada, carregar e armazenar o seu script de configuração personalizada e os ficheiros associados num contentor de Blobs. O processo de configuração personalizada também carrega seus registos de execução ao mesmo contentor de Blobs.

## <a name="instructions"></a>Instruções

1.  Transfira e instale [do Azure PowerShell](/powershell/azure/install-az-ps).

1.  Prepare o seu script de configuração personalizada e os ficheiros associados (por exemplo, ficheiros. bat,. cmd, .exe,. dll,. msi ou. ps1).

    1.  Tem de ter um ficheiro de script com o nome `main.cmd`, que é o ponto de entrada do seu programa de configuração personalizado.

    1.  Se pretender que os registos adicionais gerados por outras ferramentas (por exemplo, `msiexec.exe`) para ser carregadas no seu contentor, especifique a variável de ambiente predefinida `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de log em seus scripts (por exemplo, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1.  Transferir, instalar e iniciar [Explorador de armazenamento do Azure](http://storageexplorer.com/).

    1.  Sob **(Local e ligado)**, selecione direito **contas de armazenamento** e selecione **ligar ao armazenamento do Azure**.

       ![Ligar ao armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    1.  Selecione **utilize um nome de conta de armazenamento e a chave** e selecione **próxima**.

       ![Utilizar o nome e a chave de uma conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    1.  Introduza o seu nome de conta de armazenamento do Azure e a chave, selecione **próxima**e, em seguida, selecione **Connect**.

       ![Forneça o nome da conta de armazenamento e a chave](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    1.  Na sua conta de armazenamento do Azure ligada, faça duplo clique em **contentores de BLOBs**, selecione **criar contentor de BLOBs**e dê o nome do novo contentor.

       ![Criar um contentor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    1.  Selecione o contentor novo e carregue o seu script de configuração personalizada e os ficheiros associados. Certifique-se de que carregar `main.cmd` no nível superior do seu contentor, não em qualquer pasta. Verifique também se que o seu contentor contém apenas os ficheiros de configuração personalizados necessários, portanto, transferi-los para o runtime de integração Azure-SSIS mais tarde não levará muito tempo.

       ![Carregar ficheiros para o contentor de BLOBs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    1.  O contentor com o botão direito e selecione **obter assinatura de acesso partilhado**.

       ![Obter a assinatura de acesso partilhado do contentor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    1.  Criar o URI de SAS para o contentor com um tempo de expiração suficientemente longo e leitura + escrita + lista de permissões. É necessário o URI de SAS para transferir e executar o script de configuração personalizada e os ficheiros associados, sempre que qualquer nó do Runtime de integração Azure-SSIS está a recriar a imagem/reiniciado. Precisa de permissão de escrita para carregar os registos de execução do programa de configuração.

        > [!IMPORTANT]
        > Certifique-se de que o URI de SAS não expire e recursos de configuração personalizada estão sempre disponíveis durante o ciclo de vida completo do Runtime de integração Azure-SSIS, desde a criação até à eliminação, especialmente se regularmente de parar e iniciar o runtime de integração Azure-SSIS durante este período.

       ![Gerar a assinatura de acesso partilhado do contentor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    1.  Copie e guarde o URI de SAS do seu contentor.

       ![Copie e guarde a assinatura de acesso partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    1.  Quando aprovisionar ou reconfigurar o runtime de integração Azure-SSIS com a IU do Data Factory, antes de iniciar o runtime de integração Azure-SSIS, introduza o URI de SAS do seu contentor no campo adequado **definições avançadas** painel:

       ![Introduza a assinatura de acesso partilhado](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

       Quando aprovisionar ou reconfigurar o runtime de integração Azure-SSIS com o PowerShell, antes de iniciar o runtime de integração Azure-SSIS, execute o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet com o URI de SAS do seu contentor, como o valor para a nova `SetupScriptContainerSasUri` parâmetro. Por exemplo:

       ```powershell
       Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    1.  Após a conclusão da sua configuração personalizada e o runtime de integração Azure-SSIS é iniciado, é possível encontrar a saída padrão da `main.cmd` e outra execução inicia sessão a `main.cmd.log` pasta do seu contentor de armazenamento.

1.  Para ver outros exemplos de configuração personalizada, ligue para o contentor de pré-visualização pública com o Explorador de armazenamento do Azure.

    a.  Sob **(Local e ligado)**, clique com botão direito **contas de armazenamento**, selecione **ligar ao armazenamento do Azure**, selecione **utilizar uma cadeia de ligação ou um acesso partilhado URI de assinatura**e, em seguida, selecione **próxima**.

       ![Ligar ao armazenamento do Azure com a assinatura de acesso partilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Selecione **utilizar um URI de SAS** e introduza o seguinte URI de SAS do contentor de pré-visualização pública. Selecione **próxima**e selecione **Connect**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Fornecer a assinatura de acesso partilhado do contentor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Selecione o contentor de pré-visualização pública conectado e faça duplo clique o `CustomSetupScript` pasta. Nesta pasta são os seguintes itens:

       1. A `Sample` pasta, que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu ir Azure-SSIS. A tarefa não faz nada, mas o modo de suspensão por alguns segundos. A pasta também contém um `gacutil` pasta, todo o conteúdo das quais (`gacutil.exe`, `gacutil.exe.config`, e `1033\gacutlrc.dll`) podem ser copiados porque está no seu contentor. Além disso, `main.cmd` contém os seus comentários para manter as credenciais de acesso para partilhas de ficheiros.

       1. A `UserScenarios` pasta, que contém várias configurações personalizadas para cenários de utilizador real.

    ![Conteúdo do contêiner de pré-visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Clique duas vezes o `UserScenarios` pasta. Nesta pasta são os seguintes itens:

       1. A `.NET FRAMEWORK 3.5` pasta, que contém uma configuração personalizada para instalar uma versão anterior do .NET Framework que poderão ser necessárias para componentes personalizados em cada nó do seu ir Azure-SSIS.

       1. R `BCP` pasta, que contém uma configuração personalizada para instalar os utilitários da linha de comandos do SQL Server (`MsSqlCmdLnUtils.msi`), incluindo o programa de cópia em massa (`bcp`), em cada nó do seu ir Azure-SSIS.

       1. Uma `EXCEL` pasta, que contém uma configuração personalizada para instalar os assemblies de código-fonte aberto (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, e `ExcelDataReader.dll`) em cada nó do seu ir Azure-SSIS.

       1. Uma `ORACLE ENTERPRISE` pasta, que contém um script de configuração personalizada (`main.cmd`) e o ficheiro de configuração de instalação silenciosa (`client.rsp`) para instalar os conectores de Oracle e o controlador OCI em cada nó do seu Azure-SSIS IR Enterprise Edition. Esta configuração permite-lhe utilizar o Gestor de ligações do Oracle, a origem e destino. Em primeiro lugar, Baixe o Microsoft Connectors v5.0 para Oracle (`AttunitySSISOraAdaptersSetup.msi` e `AttunitySSISOraAdaptersSetup64.msi`) da [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente mais recente Oracle - por exemplo, `winx64_12102_client.zip` – na [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), em seguida, carregá-los tudo em conjunto com `main.cmd` e `client.rsp` ao contentor. Se usar TNS para ligar a Oracle, também tem de transferir `tnsnames.ora`, editá-lo e carregá-lo para o contentor, para que possam ser copiado para a pasta de instalação do Oracle durante a configuração.

       1. Uma `ORACLE STANDARD ADO.NET` pasta, que contém um script de configuração personalizada (`main.cmd`) para instalar o controlador ODP.NET do Oracle em cada nó do seu ir Azure-SSIS. Esta configuração permite-lhe utilizar o Gestor de ligações ADO.NET, a origem e destino. Em primeiro lugar, por exemplo, a transferir o controlador Oracle ODP.NET mais recentes - `ODP.NET_Managed_ODAC122cR1.zip` - a partir de [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)e, em seguida, carregue-o em conjunto com `main.cmd` ao contentor.
       
       1. Uma `ORACLE STANDARD ODBC` pasta, que contém um script de configuração personalizada (`main.cmd`) para instalar o controlador ODBC do Oracle e configurar o DSN em cada nó do seu ir Azure-SSIS. Esta configuração permite-lhe utilizar o Gestor de ligação de ODBC/origem/destino ou a ligação de consulta Power Manager/origem com o tipo de origem de dados ODBC para ligar ao servidor Oracle. Em primeiro lugar, transferir o cliente instantâneas Oracle (pacote básico ou pacote Lite básico) e ODBC pacote - por exemplo, os pacotes de 64 bits do mais recente [aqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacote básico: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, pacote Lite básico: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pacote de ODBC : `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) ou os pacotes de 32 bits a partir de [aqui](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacote básico: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, pacote Lite básico: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, o pacote de ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) e, em seguida, carregá-los em conjunto com `main.cmd` em o contentor.

       1. Uma `SAP BW` pasta, que contém um script de configuração personalizada (`main.cmd`) para instalar a assemblagem de conector de SAP .NET (`librfc32.dll`) em cada nó do seu Azure-SSIS IR Enterprise Edition. Esta configuração permite-lhe utilizar o Gestor de ligações do SAP BW, a origem e destino. Em primeiro lugar, carregue a 64 bits ou a versão de 32 bits do `librfc32.dll` da pasta de instalação SAP para o seu contentor, em conjunto com `main.cmd`. O script, em seguida, copia o assembly SAP para o `%windir%\SysWow64` ou `%windir%\System32` pasta durante a configuração.

       1. A `STORAGE` pasta, que contém uma configuração personalizada para instalar o Azure PowerShell em cada nó do seu ir Azure-SSIS. Esta configuração permite-lhe implementar e pacotes de SSIS execução que são executados [scripts do PowerShell para manipular a sua conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Cópia `main.cmd`, uma amostra `AzurePowerShell.msi` (ou instalar a versão mais recente), e `storage.ps1` para o seu contentor. Utilize PowerShell.dtsx como um modelo para os pacotes. O modelo de pacote combina uma [a tarefa de transferir o Blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), os downloads `storage.ps1` como um script do PowerShell modificável e uma [executar tarefa de processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que executa o script em cada nó.

       1. R `TERADATA` pasta, que contém um script de configuração personalizada (`main.cmd`), seu arquivo associado (`install.cmd`) e pacotes de instalador (`.msi`). Estes ficheiros instale conectores Teradata, a API de TPT e o controlador ODBC em cada nó do seu Azure-SSIS IR Enterprise Edition. Esta configuração permite-lhe utilizar o Gestor de ligações de Teradata, a origem e destino. Em primeiro lugar, transfira o ficheiro de zip de 15.x de Teradata ferramentas e utilitários (TTU) (por exemplo, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) da [Teradata](http://partnerintelligence.teradata.com)e, em seguida, carregue-o em conjunto com o acima `.cmd` e `.msi` ficheiros para o seu contentor.

    ![Pastas na pasta de cenários de utilizador](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Para experimentar esses exemplos de configuração personalizada, copie e cole o conteúdo da pasta selecionada no seu contentor. Quando aprovisionar ou reconfigurar o runtime de integração Azure-SSIS com o PowerShell, execute o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet com o URI de SAS do seu contentor, como o valor para a nova `SetupScriptContainerSasUri` parâmetro.

## <a name="next-steps"></a>Passos Seguintes

-   [Edição Enterprise do Runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Como desenvolver pago ou licenciado componentes personalizados para o runtime de integração Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
