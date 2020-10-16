---
title: Execute pacotes de Serviços de Integração de Servidores SQL (SSIS) com o utilitário dtexec habilitado para a Azure
description: Saiba como executar pacotes SQL Server Integration Services (SSIS) com o utilitário dtexec ativado pelo Azure.
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
ms.openlocfilehash: 94b581f677e370911a60db08276ff7dd0eb45486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927084"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Executar pacotes de Serviços de Integração de ServidorES SQL com o utilitário dtexec habilitado para a Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve o utilitário de pedido de aviso de comando dtexec (AzureDTExec) ativado pelo Azure.. É utilizado para executar pacotes sql server integration services (SSIS) no Tempo de Execução de Integração Azure-SSIS (IR) na Azure Data Factory.

O utilitário dtexec tradicional vem com o SQL Server. Para mais informações, consulte [a utilidade do dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). É frequentemente invocado por orquestradores ou programadores de terceiros, como ActiveBatch e Control-M, para executar pacotes SSIS no local. 

O utilitário AzureDTExec moderno vem com uma ferramenta SQL Server Management Studio (SSMS). Também pode ser invocado por orquestradores de terceiros ou programadores para executar pacotes SSIS em Azure. Facilita o levantamento e deslocação ou migração dos seus pacotes SSIS para a nuvem. Após a migração, se quiser continuar a usar orquestradores ou programadores de terceiros nas suas operações do dia-a-dia, podem agora invocar o AzureDTExec em vez de dtexec.

O AzureDTExec executa os seus pacotes como executar atividades do Pacote SSIS em oleodutos data factory. Para mais informações, consulte [os pacotes Run SSIS como atividades da Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

O AzureDTExec pode ser configurado através do SSMS para utilizar uma aplicação Azure Ative Directory (Azure AD) que gera oleodutos na sua fábrica de dados. Também pode ser configurado para aceder a sistemas de ficheiros, partilhas de ficheiros ou Ficheiros Azure onde armazena os seus pacotes. Com base nos valores que dá pelas suas opções de invocação, o AzureDTExec gera e executa um oleoduto exclusivo da Data Factory com uma atividade do Pacote SSIS executar nele. Invocar o AzureDTExec com os mesmos valores para as suas opções reencamina o pipeline existente.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o AzureDTExec, descarregue e instale a versão mais recente do SSMS, que é a versão 18.3 ou posterior. Descarregue-o a partir [deste site.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-the-azuredtexec-utility"></a>Configure a utilidade AzureDTExec
A instalação de SSMS na sua máquina local também instala o AzureDTExec. Para configurar as suas definições, inicie o SSMS com a **Opção executar como** administrador. Em seguida, selecione **Ferramentas**  >  **Migrar para Azure**  >  **Configure Azure-enabled DTExec**.

![Menu dtexec com ativação configure Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Esta ação abre uma janela **AzureDTExecConfig** que precisa de ser aberta com privilégios administrativos para que possa escrever no ficheiro *AzureDTExec.settings.* Se não tiver executado sSMS como administrador, abre-se uma janela do Controlo de Conta de Utilizador (UAC). Insira a sua senha de administração para elevar os seus privilégios.

![Configurar configurações dtexec ativadas pelo Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Na janela **AzureDTExecConfig,** insira as definições de configuração da seguinte forma:

- **ApplicationId**: Introduza o identificador único da aplicação Azure AD que cria com as permissões certas para gerar oleodutos na sua fábrica de dados. Para mais informações, consulte [Criar uma aplicação AD AZure e o principal de serviço através do portal Azure.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
- **AutenticaçãoKey**: Introduza a chave de autenticação para a sua aplicação AD AZure.
- **TenantId**: Introduza o identificador único do inquilino Azure AD, ao abrigo do qual é criada a sua app Azure AD.
- **DataFactory**: Introduza o nome da sua fábrica de dados em que os oleodutos exclusivos com a atividade do Pacote SSIS execute neles são gerados com base nos valores das opções fornecidas quando invoca o AzureDTExec.
- **IRName**: Introduza o nome do Azure-SSIS IR na sua fábrica de dados, sobre o qual os pacotes especificados no seu percurso da Convenção Universal de Nomeação (UNC) serão executados quando invocar o AzureDTExec.
- **PipelineNameHashStrLen**: Introduza o comprimento das cordas de haxixe a gerar a partir dos valores das opções que fornece quando invoca o AzureDTExec. As cordas são usadas para formar nomes únicos para os oleodutos data factory que executam os seus pacotes no Azure-SSIS IR. Normalmente, um comprimento de 32 caracteres é suficiente.
- **Grupo de Recursos**: Introduza o nome do grupo de recursos Azure no qual a sua fábrica de dados foi criada.
- **SubscriçãoId**: Introduza o identificador único da subscrição Azure, sob a qual a sua fábrica de dados foi criada.
- **LogAccessDomain**: Introduza a credencial de domínio para aceder à sua pasta de registo no seu caminho UNC quando escrever ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessPassword**: Introduza a credencial de palavra-passe para aceder à sua pasta de registo no seu caminho UNC quando escreve ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessUserName**: Introduza a credencial de nome de utilizador para aceder à sua pasta de registo no seu caminho UNC quando escrever ficheiros de registo, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogLevel**: Introduza o âmbito de registo selecionado a partir de **nulos**predefinidos, **básicos, verboses**ou opções **de desempenho** para as suas execuções de pacotes no Azure-SSIS IR. **Basic**
- **LogPath**: Introduza o caminho UNC da pasta de registo, no qual estão escritos ficheiros das execuções do seu pacote no Azure-SSIS IR.
- **PacoteAccessDomain**: Introduza a credencial de domínio para aceder aos seus pacotes no seu caminho UNC que é especificado quando invoca o AzureDTExec.
- **PackageAccessPassword**: Introduza a credencial de senha para aceder aos seus pacotes no seu caminho UNC que é especificado quando invoca o AzureDTExec.
- **PackageAccessUserName**: Introduza a credencial de nome de utilizador para aceder às suas embalagens no seu caminho UNC que é especificado quando invoca o AzureDTExec.

Para armazenar os seus pacotes e registar ficheiros em sistemas de ficheiros ou partilhas de ficheiros no local, junte-se ao seu Azure-SSIS IR a uma rede virtual ligada à sua rede no local para que possa ir buscar os seus pacotes e escrever os seus ficheiros de registo. Para obter mais informações, consulte [Junte-se a uma Azure-SSIS IR a uma rede virtual.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Para evitar a exibição de valores sensíveis escritos no ficheiro *AzureDTExec.settings* em texto simples, codificamo-los em cadeias de codificação Base64. Quando invoca o AzureDTExec, todas as cordas codificadas da Base64 são descodificadas de volta aos seus valores originais. Pode ainda proteger o ficheiro *AzureDTExec.settings* limitando as contas que podem aceder ao mesmo.

## <a name="invoke-the-azuredtexec-utility"></a>Invocar a utilidade AzureDTExec
Pode invocar o AzureDTExec na linha de comando e fornecer os valores relevantes para opções específicas no seu cenário de utilização.

O utilitário está instalado em `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Pode adicionar o seu caminho à variável ambiente 'PATH' para que seja invocada de qualquer lugar.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Invocar o AzureDTExec oferece opções semelhantes como invocar o dtexec. Para mais informações, consulte [a Dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Aqui estão as opções que são atualmente suportadas:

- **/F[ile]**: Carrega um pacote que é armazenado no sistema de ficheiros, partilha de ficheiros ou ficheiros Azure. Como valor para esta opção, pode especificar o caminho do UNC para o seu ficheiro de pacote no sistema de ficheiros, partilha de ficheiros ou Ficheiros Azure com a sua extensão .dtsx. Se o caminho da UNC especificado contiver algum espaço, coloque aspas em todo o caminho.
- **/Conf[igFile]**: Especifica um ficheiro de configuração para extrair valores de. Utilizando esta opção, pode definir uma configuração de tempo de execução para o seu pacote que difere da especificada no momento do design. Pode armazenar diferentes definições num ficheiro de configuração XML e carregá-las antes da execução do pacote. Para obter mais informações, consulte [as configurações do pacote SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Para especificar o valor desta opção, utilize o caminho UNC para o seu ficheiro de configuração no sistema de ficheiros, partilha de ficheiros ou Ficheiros Azure com a sua extensão dtsConfig. Se o caminho da UNC especificado contiver algum espaço, coloque aspas em todo o caminho.
- **/Conn[ection]**: Especifica as cadeias de ligação para os gestores de ligação existentes na sua embalagem. Utilizando esta opção, pode definir cadeias de ligação de tempo de execução para os gestores de ligação existentes no seu pacote que diferem dos especificados no momento do design. Especificar o valor desta opção da seguinte forma: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/set**: Substitui a configuração de um parâmetro, variável, propriedade, contentor, fornecedor de registo, enumerador de foreach ou ligação na sua embalagem. Esta opção pode ser especificada várias vezes. Especificar o valor desta opção da seguinte forma: `property_path;value` . Por exemplo, `\package.variables[counter].Value;1` sobrepõe-se ao valor da `counter` variável como 1. Pode utilizar o assistente **de Configuração de Pacotes** para encontrar, copiar e colar o valor de `property_path` itens no seu pacote cujo valor pretende sobrepor-se. Para obter mais informações, consulte [o assistente de configuração do pacote](https://docs.microsoft.com/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De[cript]**: Define a palavra-passe de desencriptação para o seu pacote configurado com o nível de proteção **Encriptação Encriptação Configuração DoPtusitiveWithPassword.** / **EncryptSensitiveWithPassword**

> [!NOTE]
> Invocar o AzureDTExec com novos valores para as suas opções gera um novo oleoduto, com exceção da opção **/De[cript]**.

## <a name="next-steps"></a>Passos seguintes

Após os gasodutos exclusivos com a atividade do Pacote SSIS executar neles gerados e executados quando invoca o AzureDTExec, eles podem ser monitorizados no portal data Factory. Também pode atribuir-lhes gatilhos data Factory se quiser orquestrar/agendar usando a Data Factory. Para mais informações, consulte [os pacotes Run SSIS como atividades da Data Factory.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

> [!WARNING]
> Espera-se que o gasoduto gerado seja utilizado apenas pelo AzureDTExec. As suas propriedades ou parâmetros podem mudar no futuro, por isso não as modifique nem reutilizá-las para outros fins. As modificações podem quebrar o AzureDTExec. Se isto acontecer, elimine o gasoduto. O AzureDTExec gera um novo oleoduto da próxima vez que for invocado.