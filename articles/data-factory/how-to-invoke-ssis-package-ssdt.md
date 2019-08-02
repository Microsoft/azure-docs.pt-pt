---
title: Executar pacotes do SSIS no Azure Data Factory do SSDT | Microsoft Docs
description: Saiba como executar pacotes do SSIS no Azure do SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ddf427d7d749dff1af45b3f6f83d20a89e1aae0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678409"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes do SSIS no Azure do SSDT
Este artigo descreve o recurso de projetos SQL Server Integration Services (SSIS) habilitados para o Azure em SQL Server Data Tools (SSDT), que permite que você execute pacotes no Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF).  Você pode usar esse recurso para testar seus pacotes SSIS existentes antes de levantar & Shift/migrá-los para o Azure ou para desenvolver novos pacotes do SSIS para serem executados no Azure.

Com esse recurso, você pode criar um novo IR do Azure-SSIS ou anexar um existente a projetos do SSIS e, em seguida, executar os pacotes nele.  Damos suporte à execução de pacotes a serem implantados no catálogo do SSIS (SSISDB) no modelo de implantação do projeto e aqueles a serem implantados em sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure no modelo de implantação de pacote 

## <a name="prerequisites"></a>Pré-requisitos
Para usar esse recurso, baixe e instale o SSDT mais recente com a extensão de projetos do SSIS para Visual Studio [aqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como um instalador autônomo [aqui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure – habilitar projetos do SSIS
No SSDT, você pode criar novos projetos do SSIS habilitados para o Azure usando o modelo de **projeto Integration Services (habilitado para o Azure)** .

![Novo projeto SSIS habilitado para Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Como alternativa, você pode habilitar o Azure em seus projetos do SSIS existentes clicando com o botão direito do mouse no nó do projeto no painel de Gerenciador de Soluções de SSDT para exibir um menu e selecionar o item de menu **habilitado para Azure** .

![Azure-habilitar projeto SSIS existente](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

A habilitação do Azure para seus projetos SSIS existentes exige que você defina a versão do servidor de destino como a mais recente com suporte do IR do Azure-SSIS, que atualmente é **SQL Server 2017**, portanto, se você ainda não tiver feito isso, uma janela da caixa de diálogo será exibida para fazer isso.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Conectar projetos habilitados para o Azure ao SSIS no Azure Data Factory
Conectando seus projetos habilitados para o Azure ao SSIS no ADF, você pode carregar seus pacotes em arquivos do Azure e executá-los no IR do Azure-SSIS.  Para fazer isso, siga estas etapas:

1. Clique com o botão direito do mouse no nó projeto ou **recursos vinculados do Azure** no painel Gerenciador de soluções de SSDT para exibir um menu e selecione o item **conectar ao SSIS no Azure data Factory** menu para iniciar o **Assistente de conexão do SSIS no ADF**.

   ![Conectar-se ao SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na página de **introdução do SSIS no ADF** , examine a introdução e clique no botão **Avançar** para continuar.

   ![Introdução ao SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na página **selecionar ir do SSIS no ADF** , selecione o AAD existente e o ir do Azure-SSIS para executar pacotes ou criar novos, se você não tiver nenhum.
   - Para selecionar o IR do Azure-SSIS existente, selecione primeiro a assinatura relevante do Azure e o ADF.
   - Se você selecionar o ADF existente que não tem nenhum IR do Azure-SSIS, clique no botão **criar ir do SSIS** para criar um novo no portal do ADF/aplicativo.
   - Se você selecionar sua assinatura do Azure existente que não tem nenhum ADF, clique no botão **criar ir do SSIS** para iniciar o **Assistente de criação de Integration Runtime**, onde você pode inserir o local e o prefixo para que possamos criar automaticamente um novo Azure Grupo de recursos, Data Factory e IR do SSIS em seu nome, nomeados no seguinte padrão: **YourPrefix-RG/DF/ir-YourCreationTime**.
   
   ![Selecione o IR do SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na página **selecionar armazenamento do Azure** , selecione sua conta de armazenamento do Azure existente para carregar pacotes em arquivos do Azure ou crie um novo se você não tiver nenhum.
   - Para selecionar sua conta de armazenamento do Azure existente, selecione a assinatura relevante do Azure primeiro.
   - Se você selecionar a mesma assinatura do Azure que o IR do Azure-SSIS que não tem nenhuma conta de armazenamento do Azure, clique no botão **criar armazenamento do Azure** para que possamos criar automaticamente um novo em seu nome no mesmo local que o ir do Azure-SSIS, nomeado por combinando um prefixo de seu nome de IR do Azure-SSIS e sua data de criação.
   - Se você selecionar uma assinatura diferente do Azure que não tenha nenhuma conta de armazenamento do Azure, clique no botão **criar armazenamento do Azure** para criar uma nova em portal do Azure.
   
   ![Selecione o Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Clique no botão **conectar** para concluir a conexão.  Vamos exibir o IR do Azure-SSIS selecionado e a conta de armazenamento do Azure no nó **recursos vinculados do Azure** no painel de Gerenciador de soluções do SSDT.  Também atualizaremos o status do IR do Azure-SSIS, enquanto você pode gerenciá-lo clicando com o botão direito do mouse em seu nó para exibir um menu e, em seguida, selecionando o item de menu **Start\Stop\Manage** que leva você ao portal/aplicativo do ADF para fazer isso.

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS no Azure
### <a name="starting-package-executions"></a>Iniciando execuções de pacote
Depois de conectar seus projetos ao SSIS no ADF, você pode executar pacotes no IR do Azure-SSIS.  Você tem duas opções para iniciar as execuções de pacote:
-  Clique no botão **Iniciar** na barra de ferramentas SSDT para listar um menu e selecionar o item de menu **executar no Azure** 

   ![Executar no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Clique com o botão direito do mouse no nó do pacote no painel de Gerenciador de Soluções de SSDT para exibir um menu e selecionar o item de menu **executar pacote no Azure** .

   ![Executar pacote no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Executar seus pacotes no Azure exige que você tenha um IR do Azure-SSIS em execução, portanto, se o IR do Azure-SSIS for interrompido, uma janela de caixa de diálogo será exibida para iniciá-lo.  Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos, mas pode levar aproximadamente 20-30 minutos para o IR do Azure-SSIS ingressando em uma rede virtual.  Depois de executar seus pacotes no Azure, você pode parar o IR do Azure-SSIS para gerenciar seu custo de execução clicando com o botão direito do mouse em seu nó no painel de Gerenciador de Soluções de SSDT para exibir um menu e selecionar o item de menu **Start\Stop\Manage** que o leva para o portal do ADF/ aplicativo para fazer isso.

### <a name="checking-package-execution-logs"></a>Verificando os logs de execução do pacote
Quando você iniciar a execução do pacote, iremos Formatar e exibir seu log na janela de progresso do SSDT.  Para um pacote de longa execução, atualizaremos periodicamente seu log em minutos.  Você pode parar a execução do pacote clicando no botão **parar** na barra de ferramentas SSDT que o cancelará imediatamente.  Você também pode localizar temporariamente os dados brutos de log em seu caminho UNC (Convenção de nomenclatura `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`universal):, mas nós o limparemos após um dia.

### <a name="switching-package-protection-level"></a>Alterando o nível de proteção do pacote
A execução de pacotes do SSIS no Azure não dá suporte aos níveis de proteção do **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** .  Consequentemente, se os pacotes estiverem configurados com eles, iremos alterná-los temporariamente para **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respectivamente, com senhas geradas aleatoriamente quando carregarmos seu pacotes em arquivos do Azure para execução em seu IR do Azure-SSIS.

> [!NOTE]
> Se seus pacotes contiverem tarefas executar pacote que se referem a outros pacotes configurados com os níveis de proteção do **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** , você precisará reconfigurar manualmente esses outros pacotes para Use **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respectivamente, antes de executar seus pacotes.

Se seus pacotes já estiverem configurados com os níveis de proteção do **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** , nós os manteremos inalterados, mas ainda usarão senhas geradas aleatoriamente quando carregarmos seu pacotes em arquivos do Azure para execução em seu IR do Azure-SSIS.

### <a name="using-package-configuration-file"></a>Usando o arquivo de configuração do pacote
Se você usar arquivos de configuração de pacote no modelo de implantação de pacote para alterar valores de variáveis em tempo de execução, nós carregaremos automaticamente esses arquivos com seus pacotes em arquivos do Azure para execução em seu IR do Azure-SSIS.

### <a name="using-execute-package-task"></a>Usando a tarefa Executar Pacote
Se seus pacotes contiverem tarefas executar pacote que se referem a outros pacotes armazenados em sistemas de arquivos locais, você precisará executar as seguintes configurações adicionais:

1. Carregue os outros pacotes em arquivos do Azure na mesma conta de armazenamento do Azure conectada aos seus projetos e obtenha seu novo caminho UNC, por exemplo,`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Substitua o caminho do arquivo desses outros pacotes no Gerenciador de conexões de arquivos das tarefas executar pacote com seu novo caminho UNC
   - Se o computador que executa o SSDT não puder acessar o novo caminho UNC, você poderá alterar o caminho do arquivo no painel de propriedades do Gerenciador de conexões de arquivos
   - Como alternativa, você pode usar uma variável para o caminho do arquivo para atribuir o valor correto em tempo de execução

Se seus pacotes contiverem tarefas executar pacote que se referem a outros pacotes no mesmo projeto, nenhuma configuração adicional será necessária.

## <a name="next-steps"></a>Passos Seguintes
Quando estiver satisfeito com a execução de seus pacotes no Azure do SSDT, você poderá implantá-los e executá-los como executar atividades de pacote SSIS em pipelines do ADF, ver [executar pacotes do SSIS como executar atividades de pacote do SSIS em pipelines do ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
