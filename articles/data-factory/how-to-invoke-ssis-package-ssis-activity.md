---
title: Executar pacote do SSIS com a atividade executar pacote SSIS-Azure | Microsoft Docs
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) no pipeline Azure Data Factory usando a atividade executar pacote do SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d2fe8da1c4d49f5b57f907a5940ec9c445d0d1f7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984323"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote do SSIS com a atividade executar pacote do SSIS no Azure Data Factory
Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) no pipeline Azure Data Factory (ADF) usando a atividade executar pacote do SSIS. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um Azure-SSIS Integration Runtime (ir) se você ainda não tiver um, seguindo as instruções passo a passo no [tutorial: Provisionando Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal do Azure
Nesta seção, você usa a interface do usuário do ADF/app para criar um pipeline do ADF com a atividade executar pacote SSIS que executa o pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade executar pacote do SSIS
Nesta etapa, você usa a interface do usuário do ADF/aplicativo para criar um pipeline. Você adiciona uma atividade executar pacote do SSIS ao pipeline e a configura para executar seu pacote SSIS. 

1. Em sua visão geral/home page do ADF em portal do Azure, clique no bloco **criar & monitorar** para iniciar a interface do usuário/aplicativo do ADF em uma guia separada. 

   ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **vamos** começar, clique em **criar pipeline**: 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Na caixa de ferramentas **atividades** , expanda **geral**e arraste & descartar uma atividade **Executar Pacote SSIS** para a superfície do designer de pipeline. 

   ![Arraste uma atividade executar pacote do SSIS para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na guia **geral** da atividade executar pacote SSIS, forneça um nome e uma descrição para a atividade. Defina valores de tempo limite e de repetição opcionais.

   ![Definir propriedades na guia geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na guia **configurações** da atividade executar pacote SSIS, selecione um Azure-SSIS ir em que você deseja executar o pacote. Se o pacote usar a autenticação do Windows para acessar armazenamentos de dados, por exemplo, servidores SQL/compartilhamentos de arquivos locais, arquivos do Azure, etc., marque a caixa de seleção **autenticação do Windows** e insira os valores para suas credenciais de execução de pacote (**domínio** Senha de **nome de usuário**/). / Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault (AKV) como seus valores. Para fazer isso, clique na caixa de seleção **Azure Key Vault** ao lado da credencial relevante, selecione/edite seu serviço vinculado akv existente ou crie um novo e, em seguida, selecione o nome/versão do segredo para seu valor de credencial.  Ao criar/editar seu serviço vinculado do AKV, você pode selecionar/editar seu AKV existente ou criar um novo, mas conceda acesso de identidade gerenciada do ADF ao seu AKV se você ainda não tiver feito isso. Você também pode inserir seus segredos diretamente no seguinte formato: `<AKV linked service name>/<secret name>/<secret version>`. Se o pacote precisar de um tempo de execução de 32 bits para ser executado, marque a caixa de seleção de **tempo de execução de bit 32** . 

   Para **local do pacote**, **selecione SSISDB**, **sistema de arquivos (pacote)** ou **sistema de arquivos (projeto)** . Se você selecionar **SSISDB** como o local do pacote, que será selecionado automaticamente se o Azure-SSIS ir tiver sido provisionado com o catálogo do SSIS (SSISDB) hospedado pelo servidor/instância gerenciada do banco de dados SQL do Azure, será necessário especificar o pacote a ser executado implantado no SSISDB. Se o Azure-SSIS IR estiver em execução e a caixa de seleção **entradas manuais** estiver desmarcada, você poderá procurar e selecionar suas pastas/projetos/pacotes/ambientes existentes do SSISDB. Clique no botão **Atualizar** para buscar pastas/projetos/pacotes/ambientes recém-adicionados do SSISDB, para que eles fiquem disponíveis para navegação e seleção. Para procurar/selecionar os ambientes para suas execuções de pacote, você deve configurar seus projetos com antecedência para adicionar esses ambientes como referências das mesmas pastas em SSISDB. Para obter mais informações, consulte [criando/mapeando ambientes SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Para **nível de log**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizada** , se você quiser inserir seu nome de log personalizado em vez disso. 

   ![Definir propriedades na guia Configurações – automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se o Azure-SSIS ir não estiver em execução ou se a caixa de seleção **entradas manuais** estiver marcada, você poderá inserir os caminhos do pacote e do ambiente do SSISDB `<folder name>/<project name>/<package name>.dtsx` diretamente `<folder name>/<environment name>`nos seguintes formatos: e.

   ![Defina as propriedades na guia Configurações – manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se você selecionar **sistema de arquivos (pacote)** como o local do pacote, que será selecionado automaticamente se o Azure-SSIS ir tiver sido provisionado sem o SSISDB, será necessário especificar o pacote a ser executado fornecendo um caminho UNC (Convenção de nomenclatura universal) para seu arquivo de pacote`.dtsx`() no **caminho do pacote**. Por exemplo, se você armazenar o pacote em arquivos do Azure, o caminho do pacote `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`será. 
   
   Se você configurar o pacote em um arquivo separado, também precisará fornecer um caminho UNC para o arquivo de configuração (`.dtsConfig`) no **caminho de configuração**. Por exemplo, se você armazenar sua configuração em arquivos do Azure, seu caminho de configuração `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`será.

   ![Defina as propriedades na guia Configurações – manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se você selecionar **sistema de arquivos (projeto)** como o local do pacote, precisará especificar o pacote a ser executado fornecendo um caminho UNC para o arquivo de`.ispac`projeto () no **caminho do projeto** e um arquivo`.dtsx`de pacote () do seu projeto no  **Nome do pacote**. Por exemplo, se você armazenar seu projeto em arquivos do Azure, seu caminho de projeto `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`será.

   ![Defina as propriedades na guia Configurações – manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Em seguida, você precisa especificar as credenciais para acessar seus arquivos de projeto/pacote/configuração. Se você tiver inserido anteriormente os valores para suas credenciais de execução de pacote (veja acima), poderá reutilizá-los marcando a caixa de seleção **igual às credenciais de execução de pacote** . Caso contrário, você precisa inserir os valores para suas credenciais de acesso de pacote (**senha**de**nome de usuário**/do**domínio**/). Por exemplo, se você armazenar seu projeto/pacote/configuração nos arquivos do Azure, o domínio `Azure`será; o nome `<storage account name>`de **usuário** será; e `<storage account key>`a **senha** será. Como alternativa, você pode usar os segredos armazenados em seu AKV como seus valores (veja acima). Essas credenciais serão usadas para acessar seu pacote e pacotes filho na tarefa Executar Pacote, tudo a partir de seu próprio caminho/o mesmo projeto, bem como configurações, incluindo aqueles especificados em seus pacotes. 
   
   Se você tiver usado o nível de proteção **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** ao criar seu pacote via SQL Server Data Tools (SSDT), precisará inserir o valor para sua senha na **criptografia senha**. Como alternativa, você pode usar um segredo armazenado em seu AKV como seu valor (veja acima). Se você tiver usado o nível de proteção **EncryptSensitiveWithUserKey** , precisará inserir novamente os valores confidenciais nos arquivos de configuração ou na propriedade**gerenciadores**/de conexões dos **parâmetros SSIS**/ **Substitui** as guias (veja abaixo). Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte, portanto, você precisa reconfigurar o pacote para usar outro nível de proteção por meio de `dtutil` SSDT ou utilitário de linha de comando. 
   
   Para **nível de log**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizada** , se você quiser inserir seu nome de log personalizado em vez disso. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, será necessário especificar a pasta de log fornecendo seu caminho UNC no **caminho de log**. Por exemplo, se você armazenar os logs nos arquivos do Azure, seu caminho de log `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`será. Uma subpasta será criada nesse caminho para cada execução de pacote individual e nomeada após a ID de execução de atividade do pacote SSIS, na qual os arquivos de log serão gerados a cada cinco minutos. 
   
   Por fim, você também precisa especificar as credenciais para acessar a pasta de log. Se você tiver inserido anteriormente os valores para as credenciais de acesso ao pacote (veja acima), poderá reutilizá-los marcando a caixa de seleção o **mesmo que as credenciais de acesso ao pacote** . Caso contrário, você precisará inserir os valores para suas credenciais de acesso de log (**senha**de**nome de usuário**/do**domínio**/). Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio `Azure`será; o nome `<storage account name>`de **usuário** será; e `<storage account key>`a **senha** será. Como alternativa, você pode usar os segredos armazenados em seu AKV como seus valores (veja acima). Essas credenciais serão usadas para armazenar seus logs. 
   
   Para todos os caminhos UNC mencionados acima, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres e o nome do diretório deve ter menos de 248 caracteres.

5. Na guia **parâmetros do SSIS** para executar a atividade de pacote SSIS, se o Azure-SSIS ir estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, o SSIS existente os parâmetros no projeto/pacote selecionado do SSISDB serão exibidos para você atribuir valores a eles. Caso contrário, você pode inseri-las uma a uma para atribuir valores a elas manualmente – Verifique se elas existem e foram inseridas corretamente para que a execução do pacote seja bem sucedido. 
   
   Se você tiver usado o nível de proteção **EncryptSensitiveWithUserKey** ao criar seu pacote por meio do SSDT e o sistema de arquivos (/pacote) de arquivos (**projeto)** estiver selecionado como o local do pacote, você também precisará inserir novamente seus parâmetros confidenciais para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores aos parâmetros, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis de sistema do ADF e variáveis/parâmetros de pipeline do ADF. Como alternativa, você pode usar os segredos armazenados em seu AKV como seus valores (veja acima).

   ![Definir propriedades na guia parâmetros do SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na guia **gerenciadores de conexões** para executar a atividade de pacote SSIS, se o Azure-SSIS ir estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, o existente os gerenciadores de conexões no projeto/pacote selecionado do SSISDB serão exibidos para você atribuir valores às suas propriedades. Caso contrário, você pode inseri-las uma a uma para atribuir valores às suas propriedades manualmente – Verifique se elas existem e foram inseridas corretamente para que a execução do pacote seja bem sucedido. 
   
   Se você tiver usado o nível de proteção **EncryptSensitiveWithUserKey** ao criar seu pacote por meio do SSDT e o sistema de arquivos (/pacote) de arquivos (**projeto)** estiver selecionado como o local do pacote, você também precisará inserir novamente suas propriedades confidenciais do Gerenciador de conexões para atribuir valores a elas em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores às propriedades do Gerenciador de conexões, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis de sistema do ADF e variáveis/parâmetros de pipeline do ADF. Como alternativa, você pode usar os segredos armazenados em seu AKV como seus valores (veja acima).

   ![Definir propriedades na guia gerenciadores de conexões](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Na guia **substituições de propriedade** para executar atividade de pacote SSIS, você pode inserir os caminhos das propriedades existentes no pacote selecionado um a um para atribuir valores a elas manualmente – Verifique se elas existem e foram inseridas corretamente para o pacote execução para obter sucesso, por exemplo, para substituir o valor da sua variável de usuário, insira seu caminho no seguinte `\Package.Variables[User::<variable name>].Value`formato:. 
   
   Se você tiver usado o nível de proteção **EncryptSensitiveWithUserKey** ao criar seu pacote por meio do SSDT e o sistema de arquivos (/pacote) de arquivos (**projeto)** estiver selecionado como o local do pacote, você também precisará inserir novamente suas propriedades confidenciais para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores às suas propriedades, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis de sistema do ADF e variáveis/parâmetros de pipeline do ADF.

   ![Definir propriedades na guia substituições de propriedade](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Os valores atribuídos nos arquivos de configuração e na *guia parâmetros do SSIS* podem ser substituídos usando a propriedade **gerenciadores**/de conexões**substitui** guias, enquanto aqueles atribuídos na guia **gerenciadores de conexões** podem também será substituído usando a guia **substituições de propriedade** .

8. Para validar a configuração do pipeline, clique em **validar** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>** .

9. Publique o pipeline no ADF clicando no botão **publicar tudo** . 

### <a name="run-the-pipeline"></a>Executar o pipeline
Nesta etapa, você dispara uma execução de pipeline. 

1. Para disparar uma execução de pipeline, clique em **gatilho** na barra de ferramentas e clique em **disparar agora**. 

   ![Acionar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Você vê a execução do pipeline e seu status junto com outras informações (como a hora de início da execução). Para atualizar a vista, clique em **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Você vê apenas uma execução de atividade, pois o pipeline tem apenas uma atividade (a atividade executar pacote SSIS).

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Você pode executar a **consulta** a seguir no banco de dados SSISDB no servidor SQL do Azure para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar execuções de pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Você também pode obter a ID de execução do SSISDB da saída da execução da atividade do pipeline e usar a ID para verificar logs de execução mais abrangentes e mensagens de erro no SQL Server Management Studio (SSMS).

   ![Obtenha a ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agendar o pipeline com um gatilho

Você também pode criar um gatilho agendado para o pipeline para que o pipeline seja executado em um agendamento (por hora, diariamente, etc.). Para obter um exemplo, consulte [criar uma interface do usuário de data factory data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com o PowerShell
Nesta seção, você usará Azure PowerShell para criar um pipeline do ADF com a atividade executar pacote do SSIS que executa seu pacote SSIS. 

Instale os módulos de Azure PowerShell mais recentes seguindo as instruções passo a passo em [como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Criar um ADF com Azure-SSIS IR
Você pode usar um ADF existente que já tenha Azure-SSIS ir provisionado ou criar um novo ADF com Azure-SSIS ir seguindo as instruções passo a passo no [tutorial: Implante pacotes do SSIS no Azure por](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)meio do PowerShell.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade executar pacote do SSIS 
Nesta etapa, você cria um pipeline com uma atividade executar pacote do SSIS. A atividade executa o pacote SSIS. 

1. Crie um arquivo JSON chamado **RunSSISPackagePipeline. JSON** na pasta **C:\ADF\RunSSISPackage** com conteúdo semelhante ao exemplo a seguir:

   > [!IMPORTANT]
   > Substitua nomes de objeto/descrições/caminhos, valores de propriedade/parâmetro, senhas e outros valores de variáveis antes de salvar o arquivo. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

Para executar pacotes armazenados em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure, você pode inserir os valores para as propriedades de local do pacote/log da seguinte maneira.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

Para executar pacotes em projetos armazenados em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure, você pode inserir os valores para a propriedade local do pacote da seguinte maneira.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Em Azure PowerShell, alterne para a `C:\ADF\RunSSISPackage` pasta.

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o cmdlet **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Segue-se o resultado do exemplo:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Use o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie/cole o seguinte script na janela do PowerShell e prima ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Você também pode monitorar o pipeline usando o portal do Azure. Para obter as instruções passo a passo, consulte [monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agendar o pipeline com um gatilho
Na etapa anterior, você executou o pipeline sob demanda. Você também pode criar um gatilho de agendamento para executar o pipeline em um agendamento (por hora, diariamente, etc.).

1. Crie um arquivo JSON chamado **mytrigger. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. Em **Azure PowerShell**, alterne para a pasta **C:\ADF\RunSSISPackage** .
3. Execute o cmdlet **set-AzDataFactoryV2Trigger** , que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Por padrão, o gatilho está no estado parado. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Execute o comando a seguir após a próxima hora. Por exemplo, se a hora atual for 3:25 PM UTC, execute o comando às 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Você pode executar a consulta a seguir no banco de dados SSISDB no servidor SQL do Azure para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passos Seguintes
Consulte a seguinte postagem no blog:
-   [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines do ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
