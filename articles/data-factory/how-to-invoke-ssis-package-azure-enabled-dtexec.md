---
title: Executar pacotes do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure | Microsoft Docs
description: Saiba como executar pacotes do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249022"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Executar pacotes do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para o Azure
Este artigo descreve o utilitário de prompt de comando **dtexec** (**AzureDTExec**) habilitado para Azure.  Ele é usado para executar pacotes do SSIS em Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF).

O utilitário **dtexec** tradicional vem com SQL Server, consulte a documentação do [Utilitário dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) para obter mais informações.  Geralmente, ele é invocado por modeladores/agendadores de terceiros, como o lote ativo, controle-M, etc., para executar pacotes do SSIS localmente.  O utilitário **AzureDTExec** moderno vem com a ferramenta SSMS (SQL Server Management Studio).  Ele também pode ser invocado por outros orquestradores/agendadores de terceiros para executar pacotes do SSIS no Azure.  Ele facilita o levantamento & a mudança/migração de seus pacotes do SSIS para a nuvem.  Após a migração, se você quiser continuar usando os orquestradores/agendadores de terceiros em suas operações diárias, eles agora poderão invocar **AzureDTExec** em vez de **dtexec**.

**AzureDTExec** executará seus pacotes como atividades de execução de pacote SSIS em pipelines do ADF, consulte o artigo [executar pacotes do SSIS como atividades do ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) para obter mais informações.  Ele pode ser configurado por meio do SSMS para usar um aplicativo Azure Active Directory (AAD) que gera pipelines no ADF.  Ele também pode ser configurado para acessar sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure em que você armazena seus pacotes.  Com base nos valores que você fornece para suas opções de invocação, o **AzureDTExec** gerará e executará um pipeline do ADF exclusivo com a atividade executar pacote SSIS nele.  Invocar **AzureDTExec** com os mesmos valores para suas opções executará novamente o pipeline existente.

## <a name="prerequisites"></a>Pré-requisitos
Para usar o **AzureDTExec**, baixe e instale o SSMS mais recente (versão 18,3 ou posterior) [aqui](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-azuredtexec-utility"></a>Configurar o utilitário AzureDTExec
A instalação do SSMS no computador local também instalará o **AzureDTExec**.  Para definir suas configurações, inicie o SSMS com a opção **Executar como administrador** e selecione o item de menu suspenso em cascata **Ferramentas-> migrar para o Azure-> Configurar dtexec habilitado para Azure**.

![Configurar o menu dtexec habilitado para Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Essa ação exibirá a janela **AzureDTExecConfig** que precisa ser aberta com privilégios administrativos para que ela seja gravada no arquivo **AzureDTExec. Settings** .  Se você não tiver executado o SSMS como administrador, uma janela de controle de conta de usuário (UAC) será exibida para que você insira sua senha de administrador para elevar seus privilégios.

![Definir configurações de dtexec habilitadas para o Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Na janela **AzureDTExecConfig** , você pode inserir as definições de configuração da seguinte maneira:

- **ApplicationId**: Insira o identificador exclusivo do aplicativo do AAD criado com as permissões corretas para gerar pipelines no ADF, consulte [criar um aplicativo do AAD e uma entidade de serviço por meio do portal do Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) artigo para obter mais informações.

- **AuthenticationKey**: Insira a chave de autenticação para seu aplicativo do AAD.

- **Tenantid**: Insira o identificador exclusivo do locatário do AAD, no qual seu aplicativo do AAD é criado.

- **SubscriptionId**: Insira o identificador exclusivo da assinatura do Azure, sob a qual o ADF foi criado.

- **Resourcegroup**: Insira o nome do grupo de recursos do Azure, no qual o ADF foi criado.

- **DataFactory**: Insira o nome do seu ADF, no qual pipelines exclusivos com atividade executar pacote SSIS neles são gerados com base nos valores das opções fornecidas ao invocar **AzureDTExec**.

- **IRName**: Insira o nome de Azure-SSIS ir no ADF, no qual os pacotes especificados em seu caminho UNC (Convenção de nomenclatura universal) ao invocar **AzureDTExec** serão executados.

- **PackageAccessDomain**: Insira a credencial de domínio para acessar seus pacotes em seu caminho UNC especificado ao invocar **AzureDTExec**.

- **PackageAccessUserName**: Insira a credencial de nome de usuário para acessar seus pacotes em seu caminho UNC especificado ao invocar **AzureDTExec**.

- **PackageAccessPassword**: Insira a credencial de senha para acessar seus pacotes em seu caminho UNC especificado ao invocar **AzureDTExec**.

- **LogPath**: Insira o caminho UNC da pasta de log, no qual os arquivos de log de suas execuções de pacote no Azure-SSIS ir serão gravados.

- **LogLevel**: Insira o escopo selecionado de registro em log das opções de**desempenho** **null**/**Basic**/**Verbose**/ detalhadas para suas execuções de pacote no Azure-SSIS ir.

- **LogAccessDomain**: Insira a credencial do domínio para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, necessário quando **logPath** é especificado e **logLevel** não é **nulo**.

- **LogAccessUserName**: Insira a credencial de nome de usuário para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, necessário quando **logPath** é especificado e **logLevel** não é **nulo**.

- **LogAccessPassword**: Insira a credencial de senha para acessar a pasta de log em seu caminho UNC ao gravar arquivos de log, necessário quando **logPath** é especificado e **logLevel** não é **nulo**.

- **PipelineNameHashStrLen**: Insira o comprimento das cadeias de caracteres de hash a serem geradas a partir dos valores das opções que você fornece ao invocar **AzureDTExec**.  As cadeias de caracteres serão usadas para formar nomes exclusivos para os pipelines do ADF que executam seus pacotes no Azure-SSIS IR.  Geralmente, um comprimento de 32 caracteres é suficiente.

Se você planeja armazenar seus pacotes e arquivos de log em sistemas de arquivos/compartilhamentos de arquivos locais, você deve ingressar seu Azure-SSIS IR em uma VNet conectada à sua rede local, para que possa buscar seus pacotes e gravar seus arquivos de log, confira [ingressar Azure-SSIS ir a uma VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) para obter mais informações.

Para evitar a exibição de valores confidenciais gravados no arquivo **AzureDTExec. Settings** em texto sem formatação, nós os codificaremos em cadeias de caracteres de codificação Base64.  Quando você invoca **AzureDTExec**, todas as cadeias de caracteres codificadas em base64 serão decodificadas de volta para seus valores originais.  Você pode proteger ainda mais o arquivo **AzureDTExec. Settings** limitando as contas que podem acessá-lo.

## <a name="invoke-azuredtexec-utility"></a>Invocar utilitário AzureDTExec
Você pode invocar **AzureDTExec** no prompt de linha de comando e fornecer os valores relevantes para as opções específicas em seu cenário de caso de uso.

O utilitário é instalado em `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. Você pode adicionar seu caminho à variável de ambiente ' PATH ' para que ele seja invocado de qualquer lugar.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Invocar o **AzureDTExec** oferece opções semelhantes como invocar **dtexec**, consulte a documentação do [Utilitário dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) para obter mais informações.  Aqui estão as opções com suporte no momento:

- **/F [ile]** : carrega um pacote que é armazenado no sistema de arquivos/compartilhamento de arquivos/arquivos do Azure.  Como o valor dessa opção, você pode especificar o caminho UNC para o arquivo de pacote no sistema de arquivos/compartilhamento de arquivos/arquivos do Azure com sua extensão dtsx.  Se o caminho UNC especificado contiver algum espaço, você deverá colocar o caminho inteiro entre aspas.

- **/Conf [igFile]** : especifica um arquivo de configuração do qual extrair valores.  Usando essa opção, você pode definir uma configuração de tempo de execução para o pacote que difere da especificada em tempo de design.  Você pode armazenar configurações diferentes em um arquivo de configuração XML e, em seguida, carregá-las antes da execução do pacote.  Consulte o artigo [configurações de pacote SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) para obter mais informações.  Como o valor dessa opção, você pode especificar o caminho UNC para o arquivo de configuração no sistema de arquivos/compartilhamento de arquivos/arquivos do Azure com sua extensão dtsConfig.  Se o caminho UNC especificado contiver algum espaço, você deverá colocar o caminho inteiro entre aspas.

- **/Conn [ection]** : especifica as cadeias de conexão para os gerenciadores de conexões existentes em seu pacote.  Usando essa opção, você pode definir cadeias de conexão de tempo de execução para os gerenciadores de conexões existentes em seu pacote que diferem daqueles especificados em tempo de design.  Como o valor dessa opção, você pode especificá-lo da seguinte maneira: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: substitui a configuração de um parâmetro, uma variável, uma propriedade, um contêiner, um provedor de log, um enumerador foreach ou uma conexão em seu pacote.  Essa opção pode ser especificada várias vezes.  Como o valor dessa opção, você pode especificá-lo da seguinte maneira: `property_path;value`, por exemplo, `\package.variables[counter].Value;1` substitui o valor da variável `counter` como 1.  Você pode usar o assistente de configuração de pacote para localizar, copiar e colar o valor de `property_path` para itens em seu pacote cujo valor você deseja substituir, consulte a documentação do [Assistente de configuração de pacote](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) para obter mais informações.

- **/De [criptu]** : define a senha de descriptografia para o pacote que está configurado com o nível de proteção **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** .

> [!NOTE]
> Invocar **AzureDTExec** com novos valores para suas opções irá gerar um novo pipeline, exceto para a opção **/de [cript]** .

## <a name="next-steps"></a>Passos seguintes

Depois que os pipelines exclusivos com atividade executar pacote SSIS são gerados e executados após a invocação de **AzureDTExec**, eles podem ser monitorados no portal do ADF. Consulte [o artigo executar pacotes do SSIS como atividades do ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) para obter mais informações.

> [!WARNING]
> Espera-se que o pipeline gerado seja usado somente por **AzureDTExec**. Suas propriedades/parâmetros podem mudar no futuro, portanto, você não deve modificá-los/reutilizá-los para outras finalidades, o que pode interromper o **AzureDTExec**. Caso isso aconteça, você sempre poderá excluir o pipeline e o **AzureDTExec** gerará um novo pipeline na próxima vez que for invocado.
