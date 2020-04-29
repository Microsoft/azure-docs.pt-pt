---
title: Execute pacotes de Serviços de Integração de Servidores SQL (SSIS) com o utilitário DTexec ativado pelo Azure
description: Saiba como executar pacotes de Serviços de Integração de Servidores SQL (SSIS) com o utilitário DTexec ativado pelo Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: dce7fb87ee49aefdedf5653243fa5729eee34519
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414329"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Executar pacotes de Serviços de Integração de Servidores SQL com o utilitário DTexec ativado pelo Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve o utilitário de comando dtexec (AzureDTExec) ativado por AzureDTExec. É usado para executar pacotes SQL Server Integration Services (SSIS) no Tempo de Funcionano de Integração Azure-SSIS (IR) na Azure Data Factory.

O utilitário dtexec tradicional vem com o SQL Server. Para mais informações, consulte a [utilidade dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). É frequentemente invocado por orquestradores ou programadores de terceiros, como ActiveBatch e Control-M, para executar pacotes SSIS no local. 

O moderno utilitário AzureDTExec vem com uma ferramenta SQL Server Management Studio (SSMS). Também pode ser invocado por orquestradores ou programadores de terceiros para executar pacotes SSIS em Azure. Facilita o levantamento e deslocamento ou migração dos seus pacotes SSIS para a nuvem. Após a migração, se quiser continuar a usar orquestradores ou programadores de terceiros nas suas operações do dia-a-dia, eles podem agora invocar o AzureDTExec em vez de dtexec.

A AzureDTExec executa os seus pacotes como executar atividades do Pacote SSIS em pipelines de Fábrica de Dados. Para mais informações, consulte os [pacotes Run SSIS como atividades da Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 

O AzureDTExec pode ser configurado através do SSMS para utilizar uma aplicação Azure Ative Directory (Azure AD) que gere oleodutos na sua fábrica de dados. Também pode ser configurado para aceder a sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure onde armazena os seus pacotes. Com base nos valores que dá para as suas opções de invocação, o AzureDTExec gera e executa um pipeline exclusivo data factory com uma atividade do Pacote Execute SSIS nele. Invocar o AzureDTExec com os mesmos valores para as suas opções repete o gasoduto existente.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o AzureDTExec, faça o download e instale a versão mais recente do SSMS, que é a versão 18.3 ou posterior. Descarregue-o a partir [deste site](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Configure o utilitário AzureDTExec
Instalar SSMS na sua máquina local também instala o AzureDTExec. Para configurar as suas definições, inicie o SSMS com o Run como opção **de administrador.** Em seguida, selecione **Tools** > **Migrate para Azure** > **Configure Azure-enabled DTExec**.

![Configure menu dtexec ativado por Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Esta ação abre uma janela **AzureDTExecConfig** que precisa de ser aberta com privilégios administrativos para que escreva no ficheiro *de definições AzureDTExec..* Se não tiver executado o SSMS como administrador, abre-se uma janela de Controlo de Conta de Utilizador (UAC). Insira a sua senha de administração para elevar os seus privilégios.

![Configurar configurações dtexec ativadas por Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Na janela **AzureDTExecConfig,** introduza as definições de configuração da seguinte forma:

- **ApplicationId**: Introduza o identificador único da aplicação Azure AD que cria com as permissões certas para gerar oleodutos na sua fábrica de dados. Para mais informações, consulte [Create a Azure AD app e diretor](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço via portal Azure .
- **AutenticaçãoChave**: Introduza a tecla de autenticação para a sua aplicação Azure AD.
- **TenantId**: Introduza o identificador único do inquilino Azure AD, ao abrigo do qual a sua aplicação Azure AD é criada.
- **DataFactory**: Introduza o nome da sua fábrica de dados em que os gasodutos exclusivos com a atividade do Pacote Execute SSIS neles são gerados com base nos valores das opções fornecidas quando invoca o AzureDTExec.
- **NOME IRNome**: Introduza o nome do IR Azure-SSIS na sua fábrica de dados, no qual serão executados os pacotes especificados no seu caminho da Convenção Universal de Nomeação (UNC) quando invocar o AzureDTExec.
- **PipelineNameHashStrLen**: Introduza o comprimento das cordas de haxixe a gerar a partir dos valores das opções que fornece quando invoca o AzureDTExec. As cordas são usadas para formar nomes únicos para os oleodutos data Factory que executam os seus pacotes no IR Azure-SSIS. Normalmente, um comprimento de 32 caracteres é suficiente.
- **ResourceGroup**: Introduza o nome do grupo de recursos Azure no qual a sua fábrica de dados foi criada.
- **SubscriçãoId**: Introduza o identificador único da subscrição Azure, sob o qual a sua fábrica de dados foi criada.
- **LogAccessDomain**: Introduza a credencial de domínio para aceder à sua pasta de registo no seu caminho UNC quando escrever ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessPassword**: Introduza a credencial de senha para aceder à sua pasta de registo no seu caminho UNC quando escrever ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessUserName**: Introduza a credencial de nome de utilizador para aceder à sua pasta de registo no seu caminho UNC quando escrever ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogLevel**: Introduza o âmbito selecionado de exploração madeireira a partir de **nulas**predefinidos, **básicos,** **verboses**ou opções de **desempenho** para as suas execuções de pacotes no IR Azure-SSIS.
- **LogPath**: Introduza o caminho unc da pasta de registo, no qual são escritos ficheiros de registo das execuções do seu pacote no IR Azure-SSIS.
- **PackageAccessDomain**: Introduza a credencial de domínio para aceder aos seus pacotes no seu caminho UNC especificado quando invoca o AzureDTExec.
- **PackageAccessPassword**: Introduza a credencial de senha para aceder aos seus pacotes no seu caminho UNC especificado quando invocar o AzureDTExec.
- **PacoteAccessUserName**: Introduza a credencial de nome de utilizador para aceder aos seus pacotes no seu caminho UNC especificado quando invocar o AzureDTExec.

Para armazenar os seus pacotes e ficheiros de registo em sistemas de ficheiros ou file shares no local, junte-se ao seu IR Azure-SSIS a uma rede virtual ligada à sua rede no local para que possa ir buscar os seus pacotes e escrever os seus ficheiros de registo. Para mais informações, consulte [Join a Azure-SSIS IR a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Para evitar mostrar valores sensíveis escritos no ficheiro *de definições AzureDTExec.em* texto simples, codificamo-los em cadeias de codificação Base64. Quando invoca o AzureDTExec, todas as cordas codificadas pela Base64 são descodificadas de volta aos seus valores originais. Pode ainda fixar o ficheiro *de definições AzureDTExec.,* limitando as contas que podem aceder ao mesmo.

## <a name="invoke-the-azuredtexec-utility"></a>Invocar a utilidade AzureDTExec
Pode invocar o AzureDTExec na linha de comando e fornecer os valores relevantes para opções específicas no seu cenário de utilização.

O utilitário está `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`instalado a . Pode adicionar o seu caminho à variável ambiental 'PATH' para que seja invocada a partir de qualquer lugar.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Invocar o AzureDTExec oferece opções semelhantes à de invocar o dtexec. Para mais informações, consulte [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Aqui estão as opções que são atualmente suportadas:

- **/F[ile]**: Carrega uma embalagem que é armazenada no sistema de ficheiros, partilha de ficheiros ou Ficheiros Azure. Como valor para esta opção, pode especificar o caminho do CNU para o seu ficheiro de pacote no sistema de ficheiros, partilha de ficheiros ou Ficheiros Azure com a sua extensão .dtsx. Se o caminho do CNU especificado contiver algum espaço, coloque aspas em todo o caminho.
- **/Conf[igFile]**: Especifica um ficheiro de configuração para extrair valores de. Utilizando esta opção, pode definir uma configuração de tempo de execução para o seu pacote que difere da especificada no momento do design. Pode armazenar diferentes configurações num ficheiro de configuração XML e, em seguida, carregá-las antes da execução do pacote. Para mais informações, consulte [as configurações do pacote SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Para especificar o valor desta opção, utilize o caminho unC para o seu ficheiro de configuração no sistema de ficheiros, partilha de ficheiros ou Ficheiros Azure com a sua extensão dtsConfig. Se o caminho do CNU especificado contiver algum espaço, coloque aspas em todo o caminho.
- **/Conn[ection]**: Especifica as cordas de ligação para os gestores de ligação existentes no seu pacote. Utilizando esta opção, pode definir as cadeias de ligação de tempo de execução para os gestores de ligação existentes no seu pacote que diferem das especificadas no momento do design. Especifique o valor `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`desta opção da seguinte forma: .
- **/Conjunto**: Substitui a configuração de um parâmetro, variável, propriedade, contentor, fornecedor de registos, enumerador foreach ou ligação no seu pacote. Esta opção pode ser especificada várias vezes. Especifique o valor `property_path;value`desta opção da seguinte forma: . Por exemplo, `\package.variables[counter].Value;1` sobrepõe-se ao valor da `counter` variável como 1. Pode utilizar o assistente de **configuração** do pacote para `property_path` encontrar, copiar e colar o valor de itens no seu pacote cujo valor pretende anular. Para mais informações, consulte o [assistente de configuração](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)do pacote .
- **/De[cripta]**: Define a palavra-passe de desencriptação para o seu pacote configurado com o nível de proteção **EncryptAllWithPassword**/**EncryptSensitiveWithPassword.**

> [!NOTE]
> Invocar o AzureDTExec com novos valores para as suas opções gera um novo oleoduto, com exceção da opção **/De[cript]**.

## <a name="next-steps"></a>Passos seguintes

Depois de gasodutos únicos com a atividade do Pacote Execute SSIS neles serem gerados e executados quando invoca o AzureDTExec, podem ser monitorizados no portal data Factory. Também pode atribuir os gatilhos da Data Factory aos mesmos se pretender orquestrar/programar utilizando data factory. Para mais informações, consulte os [pacotes Run SSIS como atividades da Fábrica](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)de Dados.

> [!WARNING]
> Espera-se que o gasoduto gerado seja utilizado apenas pela AzureDTExec. As suas propriedades ou parâmetros podem mudar no futuro, por isso não as modifique ou reutilize para outros fins. As modificações podem quebrar o AzureDTExec. Se isto acontecer, apague o gasoduto. A AzureDTExec gera um novo oleoduto da próxima vez que for invocado.