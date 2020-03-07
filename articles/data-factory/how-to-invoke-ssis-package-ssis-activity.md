---
title: Execute um pacote SSIS com a atividade do Pacote Executar SSIS
description: Este artigo descreve como executar um pacote sQL Server Integration Services (SSIS) num pipeline Azure Data Factory utilizando a atividade do Pacote Execute SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: 6027c2d94535ca2ef5c41e7027fe070c6ccb21a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388564"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Execute um pacote SSIS com a atividade do Pacote Execute SSIS na Fábrica de Dados Azure
Este artigo descreve como executar um pacote sQL Server Integration Services (SSIS) num pipeline Azure Data Factory utilizando a atividade do Pacote Execute SSIS. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um tempo de execução de integração Azure-SSIS (IR) se já não tiver um seguindo as instruções passo a passo no [Tutorial: Provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal Azure
Nesta secção, utiliza a interface de utilizador da Data Factory (UI) ou app para criar um pipeline data Factory com uma atividade do Pacote Execute SSIS que executa o seu pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade do Pacote SSIS execute
Neste passo, utiliza-se a UI ou app data Factory para criar um pipeline. Adicione uma atividade do Pacote Execute SSIS ao pipeline e configure-a para executar o seu pacote SSIS. 

1. Na sua visão geral da Data Factory ou na página inicial do portal Azure, selecione o azulejo **Author & Monitor** para iniciar a UI ou app data factory num separador separado. 

   ![Página inicial da Fábrica de Dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **Vamos começar**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na caixa de **ferramentas De Atividades,** expandir **general.** Em seguida, arraste uma atividade **do Pacote Execute SSIS** para a superfície do design do gasoduto. 

   ![Arraste uma atividade do Pacote SSIS execute para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. No separador **Geral** para a atividade do Pacote Executar SSIS, forneça um nome e descrição para a atividade. Definir valores opcionais de **timeout** e **retry.**

   ![Definir propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. No separador **Definições** para a atividade do Pacote Executar SSIS, selecione um IR Azure-SSIS onde pretende executar o seu pacote. Se o seu pacote utilizar a autenticação do Windows para aceder a lojas de dados (por exemplo, servidores SQL ou partilhas de ficheiros no local ou ficheiros Azure), selecione a caixa de verificação de **autenticação do Windows.** Introduza os valores das suas credenciais de execução do pacote nas caixas **De Domínio,** **Username**e **Password.** 

    Em alternativa, pode usar os segredos armazenados no seu cofre chave Azure como valores. Para isso, selecione a caixa de verificação **AZURE KEY VAULT** junto à credencial relevante. Selecione ou edite o seu serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome ou versão secreto para o seu valor credencial.

    Quando criar ou editar o seu serviço de ligação ao cofre de chaves, pode selecionar ou editar o seu cofre de chaves existente ou criar um novo. Certifique-se de conceder acesso de identidade gerido à Data Factory ao seu cofre chave se ainda não o fez. Também pode introduzir os seus segredos diretamente no seguinte formato: `<Key vault linked service name>/<secret name>/<secret version>`. Se o seu pacote necessitar de um tempo de execução de 32 bits para executar, selecione a caixa de verificação de tempo de execução de **32 bits.**

   Para **a localização do pacote**, selecione **SSISDB,** Sistema de **Ficheiros (Pacote)** , **Sistema de Ficheiros (Projeto)** , ou **pacote incorporado**. Se selecionar o **SSISDB** como a sua localização do pacote, que é automaticamente selecionado se o seu IR Azure-SSIS foi aprovisionado com o catálogo SSIS (SSISDB) hospedado por um servidor de base de dados Azure SQL ou instância gerida, especifique o seu pacote para executar que foi implantado no SSISDB. 

    Se o seu IR Azure-SSIS estiver em execução e a caixa de **verificação** de entradas Manual estiver limpa, navegue e selecione as suas pastas, projetos, pacotes ou ambientes existentes do SSISDB. Selecione **Refresh** para obter as suas pastas, projetos, pacotes ou ambientes recém-adicionados do SSISDB para que estejam disponíveis para navegação e seleção. Para navegar ou selecionar os ambientes para as suas execuções organizadas, deve configurar previamente os seus projetos para adicionar esses ambientes como referências das mesmas pastas no âmbito do SSISDB. Para mais informações, consulte [Criar e mapear ambientes SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Para **o nível de registo,** selecione um âmbito predefinido de exploração de madeira para a execução do seu pacote. Selecione a caixa de verificação **Personalizada** se quiser introduzir o seu nome de registo personalizado. 

   ![Definir propriedades no separador Definições - Automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se o seu IR Azure-SSIS não estiver em execução ou se estiver selecionada a caixa de verificação de **entradas Manual,** introduza diretamente nos seguintes formatos os seus caminhos de embalagem e ambiente a partir do SSISDB: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Definir propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se selecionar o Sistema de **Ficheiros (Pacote)** como a sua localização do pacote, que é automaticamente selecionado se o seu IR Azure-SSIS foi aprovisionado sem O SSISDB, especifique o seu pacote para executar, fornecendo um caminho de Convenção universal de nomeação (UNC) para o seu ficheiro de pacote (`.dtsx`) na caixa **de caminhos** pacote. Por exemplo, se armazenar o seu pacote em Ficheiros Azure, o seu pacote é `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Se configurar o seu pacote num ficheiro separado, também precisa de fornecer um caminho UNC para o seu ficheiro de configuração (`.dtsConfig`) na caixa de caminhos de **Configuração.** Por exemplo, se armazenar a sua configuração em Ficheiros Azure, o seu caminho de configuração é `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Definir propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se selecionar o Sistema de **Ficheiros (Project)** como localização do pacote, especifique o seu pacote para executar, fornecendo um caminho UNC para o seu ficheiro de projeto (`.ispac`) na caixa de **caminhos** do Projeto e um ficheiro de pacote (`.dtsx`) do seu projeto na caixa de **nome** sinuosa. Por exemplo, se armazenar o seu projeto em Ficheiros Azure, o seu percurso de projeto é `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Definir propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Em seguida, especifique as credenciais para aceder ao seu projeto, pacote ou ficheiros de configuração. Se inseriu previamente os valores das suas credenciais de execução do pacote (ver anterior), pode reutilizá-las selecionando a mesma caixa de **verificação de credenciais** de execução de pacotes. Caso contrário, introduza os valores das suas credenciais de acesso ao pacote nas caixas **De domínio,** **nome de utilizador**e **password.** Por exemplo, se armazenar o seu projeto, pacote ou configuração em Ficheiros Azure, o domínio é `Azure`, o nome de utilizador é `<storage account name>`, e a palavra-passe é `<storage account key>`. 

   Em alternativa, pode utilizar segredos armazenados no seu cofre chave como valores (ver anterior). Estas credenciais são usadas para aceder ao seu pacote e pacotes infantis na Executar Package Task, tudo a partir do seu próprio caminho ou do mesmo projeto, bem como configurações, que incluem as especificadas nos seus pacotes. 

   Se selecionar o **pacote Embedded** como localização do pacote, arraste e deixe cair o seu pacote para executar ou **enviar** a partir de uma pasta de ficheiros para a caixa fornecida. O seu pacote será automaticamente comprimido e incorporado na carga útil da atividade. Uma vez incorporado, pode **descarregar** o seu pacote mais tarde para edição. Também pode **parametrizar** o seu pacote incorporado atribuindo-o a um parâmetro de gasoduto que pode ser usado em várias atividades, otimizando assim o tamanho da carga útil do seu pipeline. Se o seu pacote incorporado não estiver todo encriptado e detetarmos a utilização da Executar Package Task nele, a caixa de verificação de tarefas de **execução** será automaticamente selecionada e os pacotes infantis relevantes com as referências do sistema de ficheiros serão automaticamente adicionados para que também os integre. Se não conseguirmos detetar a utilização da Executar Package Task, terá de selecionar manualmente a caixa de verificação de tarefas de **pacote** execute e adicionar as embalagens infantis relevantes com as referências do sistema de ficheiros uma a uma para que também as insera. Se as embalagens infantis utilizarem referências do Servidor SQL, certifique-se de que o Servidor SQL está acessível pelo seu IR Azure-SSIS.  Atualmente, a utilização de referências de projetos para pacotes infantis não é suportada.
   
   ![Definir propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Se utilizou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou o seu pacote através de Ferramentas de Dados do Servidor SQL, introduza o valor da sua palavra-passe na caixa de **passwords de encriptação.** Em alternativa, pode utilizar um segredo armazenado no seu cofre chave como valor (ver anterior). Se utilizou o nível de proteção **EncryptSensitiveWithUserKey,** reintroduza os seus valores sensíveis em ficheiros de configuração ou nos **parâmetros SSIS,** Gestores de **Ligação**ou separadores de **sobreposições** de propriedade (ver mais tarde). 

   Se utilizou o nível de proteção **EncryptAllWithUserKey,** não suporta do suporte. É necessário reconfigurar o seu pacote para utilizar outro nível de proteção através das Ferramentas de Dados do Servidor SQL ou do utilitário de linha de comando `dtutil`. 
   
   Para **o nível de registo,** selecione um âmbito predefinido de exploração de madeira para a execução do seu pacote. Selecione a caixa de verificação **Personalizada** se quiser introduzir o seu nome de registo personalizado. Se pretender registar as suas execuções para além da utilização dos fornecedores de registo padrão que podem ser especificados no seu pacote, especifique a sua pasta de registo fornecendo o seu caminho UNC na caixa de **caminhos de registo.** Por exemplo, se guardar os seus registos em Ficheiros Azure, o seu caminho de registo é `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Uma subpasta é criada neste caminho para cada execução de pacotes individuais e nomeada após o ID de execução da atividade do Pacote SSIS execute, no qual os ficheiros de registo são gerados a cada cinco minutos. 
   
   Por fim, especifique as credenciais para aceder à sua pasta de registo. Se inseriu previamente os valores das suas credenciais de acesso ao pacote (ver anterior), pode reutilizá-las selecionando a mesma caixa de **verificação de credenciais** de acesso ao pacote. Caso contrário, introduza os valores para as suas credenciais de acesso ao registo nas caixas **De domínio,** **nome de utilizador**e **password.** Por exemplo, se armazenar os seus registos em Ficheiros Azure, o domínio é `Azure`, o nome de utilizador é `<storage account name>`, e a palavra-passe é `<storage account key>`. 

    Em alternativa, pode utilizar segredos armazenados no seu cofre chave como valores (ver anterior). Estas credenciais são usadas para armazenar os seus registos. 
   
   Para todos os caminhos do CNU anteriormente mencionados, o nome de ficheiro totalmente qualificado deve ser inferior a 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

1. No separador **SSIS Parameters** para a atividade do Pacote SSIS execute, se o seu IR Azure-SSIS estiver em execução, o **SSISDB** é selecionado como a sua localização do pacote, e a caixa de verificação de **entradas Manual** no separador **Definições** está limpa, os parâmetros SSIS existentes no seu projeto ou pacote selecionado sisDB são apresentados para que você atribua valores para eles. Caso contrário, pode inscrevê-los um a um para lhes atribuir valores manualmente. Certifique-se de que existem e que estão corretamente inscritos para que a sua execução do pacote tenha sucesso. 
   
   Se utilizou o nível de proteção **EncryptSensitiveWithUserKey** quando criou o seu pacote através de Ferramentas de Dados do Servidor SQL e sistema de **ficheiros (Pacote)** ou Sistema de **Ficheiros (Project)** é selecionado como a localização do seu pacote, também precisa de reintroduzir os seus parâmetros sensíveis para atribuir valores aos mesmos em ficheiros de configuração ou neste separador. 
   
   Ao atribuir valores aos seus parâmetros, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory. Em alternativa, pode utilizar segredos armazenados no seu cofre chave como valores (ver anterior).

   ![Definir propriedades no separador Parâmetros SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. No separador Gestores de **Ligação** para a atividade do Pacote Executar SSIS, se o seu IR Azure-SSIS estiver em execução, o **SSISDB** é selecionado como a sua localização do pacote, e a caixa de verificação de **entradas Manual** no separador **Definições** é apurada, os gestores de ligação existentes no seu projeto ou pacote selecionados do SSISDB são apresentados para que você atribua valores às suas propriedades. Caso contrário, pode inscrevê-los um a um para atribuir valores às suas propriedades manualmente. Certifique-se de que existem e que estão corretamente inscritos para que a sua execução do pacote tenha sucesso. 
   
   Se utilizou o nível de proteção **EncryptSensitiveWithUserKey** quando criou o seu pacote através de Ferramentas de Dados do Servidor SQL e sistema de **ficheiros (Pacote)** ou Sistema de **Ficheiros (Project)** é selecionado como a localização do seu pacote, também precisa de reintroduzir as propriedades do seu gestor de ligação sensível para atribuir valores aos mesmos em ficheiros de configuração ou neste separador. 
   
   Ao atribuir valores às propriedades do seu gestor de ligação, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory. Em alternativa, pode utilizar segredos armazenados no seu cofre chave como valores (ver anterior).

   ![Definir propriedades no separador Gestores de Ligação](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. No separador **Desobresões** de Propriedade para a atividade do Pacote Executar SSIS, introduza os caminhos das propriedades existentes no seu pacote selecionado um a um para atribuir valores manualmente aos mesmos. Certifique-se de que existem e que estão corretamente inscritos para que a sua execução do pacote tenha sucesso. Por exemplo, para anular o valor da variável do utilizador, insira o seu caminho no seguinte formato: `\Package.Variables[User::<variable name>].Value`. 
   
   Se utilizou o nível de proteção **EncryptSensitiveWithUserKey** quando criou o seu pacote através de Ferramentas de Dados do Servidor SQL e sistema de **ficheiros (Pacote)** ou Sistema de **Ficheiros (Project)** é selecionado como a sua localização do pacote, também precisa de reintroduzir as suas propriedades sensíveis para atribuir valores aos mesmos em ficheiros de configuração ou neste separador. 
   
   Ao atribuir valores às suas propriedades, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory.

   ![Definir propriedades no separador Sobrerides de Propriedade](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Os valores atribuídos nos ficheiros de configuração e no separador **SSIS Parâmetros** podem ser ultrapassados utilizando os separadores De Sobreposições de **Ligação** ou Sobreposições de **Propriedade.** Os valores atribuídos no separador Gestores de **Ligação** também podem ser ultrapassados utilizando o separador **Sobrepordetes** de Propriedade.

1. Para validar a configuração do gasoduto, selecione **Validar** na barra de ferramentas. Para fechar o Relatório de Validação do **Gasoduto,** selecione **>>** .

1. Para publicar o pipeline na Data Factory, **selecione Publicar Tudo**. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Neste passo, desencadeas uma corrida de gasodutos. 

1. Para acionar uma execução de gasoduto, selecione **Trigger** na barra de ferramentas e selecione **'Gatilho' agora**. 

   ![Acionar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Você vê o gasoduto executado e o seu estado juntamente com outras informações, tais como o tempo de início de **corrida.** Para atualizar a vista, selecione **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecione a ligação **Ver Execuções de Atividades** na coluna **Ações**. Só se vê uma atividade a funcionar porque o oleoduto tem apenas uma atividade. É a atividade do Pacote Execute SSIS.

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Execute a seguinte consulta contra a base de dados SSISDB no seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar execuções de pacotes](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Também pode obter o ID de execução SSISDB a partir da saída da execução da atividade do pipeline e usar o ID para verificar registos de execução mais abrangentes e mensagens de erro no Estúdio de Gestão de Servidores SQL.

   ![Pegue a identidade de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agende o oleoduto com um gatilho

Também pode criar um gatilho programado para o seu oleoduto para que o gasoduto seja executado num horário, como por hora ou diariamente. Por exemplo, consulte Criar uma fábrica de [dados - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com powerShell
Nesta secção, utiliza o Azure PowerShell para criar um pipeline data Factory com uma atividade do Pacote Execute SSIS que executa o seu pacote SSIS. 

Instale os mais recentes módulos Azure PowerShell seguindo as instruções passo a passo em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Criar uma fábrica de dados com o IR Azure-SSIS
Pode utilizar uma fábrica de dados existente que já tenha o Ir Azure-SSIS aprovisionado ou criar uma nova fábrica de dados com o IR Azure-SSIS. Siga as instruções passo a passo no [Tutorial: Desloque as embalagens SSIS para Azure via PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade do Pacote SSIS execute 
Neste passo, cria-se um pipeline com uma atividade do Pacote Execute SSIS. A atividade executa o seu pacote SSIS. 

1. Crie um ficheiro JSON chamado *RunSSISPackagePipeline.json* na pasta *C:\ADF\RunSSISPackage* com conteúdo semelhante ao seguinte exemplo.

   > [!IMPORTANT]
   > Substitua os nomes, descrições e caminhos, valores de propriedade ou parâmetro, palavras-passe e outros valores variáveis antes de guardar o ficheiro. 
    
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

   Para executar pacotes armazenados em sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure, introduza os valores para as propriedades de localização de pacote e log da seguinte forma:

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

   Para executar pacotes dentro de projetos armazenados em sistemas de ficheiros, ações de ficheiros ou Ficheiros Azure, insira os valores para a sua propriedade de localização de pacote da seguinte forma:

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

   Para executar pacotes incorporados, insira os valores para a sua propriedade de localização de pacote da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. No Azure PowerShell, mude para a pasta *C:\ADF\RunSSISPackage.*

3. Para criar o pipeline **RunSSISPackagePipeline,** execute o **cmdlet Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Aqui está a saída da amostra:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Utilize o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o gasoduto. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie ou cole o seguinte script na janela PowerShell e selecione Enter. 

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

Também pode monitorizar o gasoduto utilizando o portal Azure. Para obter instruções passo a passo, consulte [Monitorize o gasoduto](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agende o oleoduto com um gatilho
No passo anterior, dirigiu o oleoduto a pedido. Também pode criar um gatilho de horário para executar o oleoduto em horário, como por hora ou diariamente.

1. Crie um ficheiro JSON chamado *MyTrigger.json* na pasta *C:\ADF\RunSSISPackage* com o seguinte conteúdo: 
        
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
    
1. No Azure PowerShell, mude para a pasta *C:\ADF\RunSSISPackage.*
1. Executar o **set-AzDataFactoryV2Trigger** cmdlet, que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Por defeito, o gatilho está em estado de paragem. Inicie o gatilho executando o **cmdlet Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Confirme que o gatilho é iniciado com o funcionamento do cmdlet **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Executar o seguinte comando após a próxima hora. Por exemplo, se o tempo atual for 15:25 UTC, dirija o comando às 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Execute a seguinte consulta contra a base de dados SSISDB no seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte publicação no blog:
- [Modernizar e alargar os seus fluxos de trabalho ETL/ELT com atividades SSIS em pipelines azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
