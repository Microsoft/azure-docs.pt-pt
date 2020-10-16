---
title: Executar pacotes SSIS a partir de SSDT
description: Saiba como executar pacotes SSIS em Azure a partir de SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: fb5b5cb0ac4a9ace7b5de5e92308da58fd2b1fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504949"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes SSIS em Azure a partir de SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve a característica dos projetos de serviços de integração de servidores SQL (SSIS) ativados pelo Azure em Ferramentas de Dados do Servidor SQL (SSDT). Permite-lhe avaliar a compatibilidade em nuvem dos seus pacotes SSIS e executá-los em Tempo de Execução de Integração Azure-SSIS (IR) na Azure Data Factory (ADF). Pode utilizar esta funcionalidade para testar as suas embalagens existentes antes de levantar & deslocar/migrar para Azure ou para desenvolver novos pacotes para funcionar em Azure.

Com esta funcionalidade, pode anexar um novo e já existente Azure-SSIS IR a projetos SSIS e, em seguida, executar os seus pacotes nele.  Apoiamos pacotes de execução a serem implantados no catálogo SSIS (SSISDB) hospedados pelo seu servidor de base de dados Azure SQL ou por exemplo gerido no Modelo de Implementação de Projetos. Também apoiamos pacotes de execução a serem implantados no sistema de ficheiros/Azure Files/SQL Server database (MSDB) hospedados pelo seu exemplo gerido Azure SQL no Modelo de Implementação de Pacotes. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, descarregue e instale a mais recente extensão SSDT com SSIS Projects para Visual Studio (VS) a partir [daqui.](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) Em alternativa, também pode descarregar e instalar o mais recente SSDT como instalador autónomo a partir [daqui.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Projetos SSIS habilitados para a Azure

### <a name="creating-new-azure-enabled-ssis-projects"></a>Criação de novos projetos SSIS habilitados para o Azure

No SSDT, pode criar novos projetos SSIS habilitados pelo Azure utilizando o modelo do **Projeto de Serviços de Integração (Azure-Enabled).**

   ![Novo projeto SSIS habilitado para a Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Após a criação do projeto Azure, será solicitado que se conecte ao SSIS na Azure Data Factory.

   ![Ligue o pedido ir-SSIS Azure-SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Se quiser ligar-se imediatamente ao seu Azure-SSIS IR, consulte [Connecting to Azure-SSIS IR](#connectssisir) para obter mais detalhes. Também pode ligar-se mais tarde clicando no nó do projeto na janela SSDT do Solution Explorer para aparecer um menu. Em seguida, selecione o **connect to SSIS em Azure Data Factory** item em SSIS no submenu **Azure Data Factory.**

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Projetos SSIS que permitem o Azure

Para os projetos SSIS existentes, pode a azure-os-capacitar seguindo estes passos:

1. Clique com o botão direito no nó do projeto na janela Solution Explorer do SSDT para aparecer um menu. Em seguida, selecione o item **do Projeto Azure-Enabled** em SSIS no submenu **Azure Data Factory** para lançar o **Azure-Enabled Project Wizard**.

   ![Projeto SSIS com ativação ativa do Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na página **De Configuração do Estúdio Visual Select,** selecione a configuração VS existente para aplicar as definições de execução de pacotes em Azure. Também pode criar um novo caso ainda não o tenha feito, consulte [criar uma nova configuração VS.](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019) Recomendamos que tenha pelo menos duas configurações VS diferentes para execuções de pacotes nos ambientes locais e em nuvem, para que possa ativar o seu projeto contra a configuração da nuvem. Desta forma, se tiver parametrizado o seu projeto ou pacotes, pode atribuir diferentes valores ao seu projeto ou parâmetros de pacote em tempo de execução com base nos diferentes ambientes de execução (seja na sua máquina local ou em Azure). Por exemplo, consulte [os ambientes de execução do pacote de comutação](#switchenvironment).

   ![Selecione configuração do Estúdio Visual](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. O Azure-enableing seus projetos SSIS existentes requer que você definir a sua versão de servidor alvo para ser o mais recente suportado pela Azure-SSIS IR. A Azure-SSIS IR é atualmente baseada no **SQL Server 2017**. Certifique-se de que as suas embalagens não contêm componentes adicionais que não sejam suportados no SQL Server 2017. Certifique-se também de que todos os componentes adicionais compatíveis também foram instalados no seu Azure-SSIS IR através de configurações personalizadas, consulte [Personalizar o seu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). Selecione o botão **Seguinte** para continuar.

   ![Verlige-se ao servidor alvo](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Consulte [a Ligação ao Azure-SSIS IR](#connectssisir) para concluir a ligação do seu projeto à Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Ligue os projetos habilitados ao SSIS na Azure Data Factory

Ao ligar os seus projetos ativados pelo Azure ao SSIS em ADF, pode enviar os seus pacotes para ficheiros Azure e executá-los no Azure-SSIS IR. Pode fazê-lo seguindo estes passos:

1. Na página **SSIS na página de Introdução do ADF,** reveja a introdução e selecione o botão **Seguinte** para continuar.

   ![SSIS na introdução da ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na página **Select SSIS IR na página ADF,** selecione o seu ADF e Azure-SSIS IR existentes para executar pacotes. Também pode criar novos se não tiver.
   - Para selecionar o seu Azure-SSIS IR existente, selecione primeiro a subscrição Azure relevante e a ADF.
   - Se selecionar o seu ADF existente que não tenha qualquer Azure-SSIS IR, selecione o botão **Create SSIS IR** para criar um novo no portal ADF. Uma vez criado, pode voltar a esta página para selecionar o seu novo Azure-SSIS IR.
   - Se selecionar a subscrição Azure existente que não tenha ADF, selecione o botão **Create SSIS IR** para lançar o Assistente de **Criação de Tempo de Execução**de Integração . No assistente, pode introduzir o seu local designado e prefixo para que criemos automaticamente um novo Grupo de Recursos Azure, Data Factory e SSIS IR em seu nome, nomeado no seguinte padrão: **YourPrefix-RG/DF/IR-YourCreationTime**. Uma vez criado, pode voltar a esta página para selecionar os seus novos ADF e Azure-SSIS IR.

   ![Selecione SSIS IR em ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na página **Select Azure Storage,** selecione a sua conta de Armazenamento Azure existente para enviar pacotes em Ficheiros Azure. Também pode criar um novo se não tiver nenhum.
   - Para selecionar a sua conta de Armazenamento Azure existente, selecione primeiro a subscrição Azure relevante.
   - Se selecionar a mesma subscrição Azure que o seu Azure-SSIS IR que não tenha qualquer conta de Armazenamento Azure, selecione o botão **De Armazenamento Create Azure.** Criaremos automaticamente um novo em seu nome no mesmo local que o seu Azure-SSIS IR, nomeado através da combinação de um prefixo do seu nome IR Azure-SSIS e da sua data de criação. Uma vez criado, pode voltar a esta página para selecionar a sua nova conta de Armazenamento Azure.
   - Se selecionar uma subscrição Azure diferente que não tenha qualquer conta de Armazenamento Azure, selecione o botão **De Armazenamento Create Azure** para criar uma nova no portal Azure. Uma vez criado, pode voltar a esta página para selecionar a sua nova conta de Armazenamento Azure.

   ![Selecione o Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Selecione o botão **Ligar** para completar a ligação do seu projeto ao Azure-SSIS IR. Exibiremos a sua conta de Armazenamento Azure-SSIS IR e Azure selecionada sob o nó **linked Azure Resources** na janela SSDT do Solution Explorer. Também atualizaremos regularmente e exibiremos o estado do seu Azure-SSIS IR lá. Pode gerir o seu Azure-SSIS IR clicando no seu nó para aparecer um menu e, em seguida, selecionar o item **Start\Stop\Manage** que o leva ao portal ADF para o fazer.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Avaliar o projeto SSIS\pacotes para execuções em Azure

### <a name="assessing-single-or-multiple-packages"></a>Avaliação de pacotes individuais ou múltiplos

Antes de executar os seus pacotes em Azure, pode avaliá-los para emergir quaisquer potenciais problemas de compatibilidade na nuvem. Estes incluem bloqueadores de migração e informações adicionais que você deve estar ciente. 
-  Tem as opções para avaliar pacotes individuais, um a um ou todos os pacotes ao mesmo tempo no âmbito do seu projeto.

   ![Avaliar pacote](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Avaliar projeto](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  Na janela do Relatório de **Avaliação** do SSDT, pode encontrar todos os potenciais problemas de compatibilidade na nuvem que são surtidos, cada um com a sua própria descrição e recomendação detalhadas. Também pode exportar o relatório de avaliação para um ficheiro CSV que pode ser partilhado com qualquer pessoa que deva atenuar estas questões. 

   ![Relatório de avaliação](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Suprimir regras de avaliação

Uma vez que tenha certeza de que alguns potenciais problemas de compatibilidade em nuvem não são aplicáveis ou foram devidamente atenuados nos seus pacotes, você pode suprimir as regras de avaliação relevantes que os surfaceam. Isto reduzirá o ruído nos seus relatórios de avaliação subsequentes.
-  Selecione o link de supressão da regra de **avaliação de configuração** na janela do Relatório de **Avaliação** de SSDT para aparecer na janela **Definições de Supressão de Regras de Avaliação,** onde pode selecionar as regras de avaliação para suprimir.

   ![Definições de supressão de regras de avaliação](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Em alternativa, clique com o botão direito no nó do projeto na janela Solution Explorer do SSDT para aparecer um menu. Selecione o item **definições ativadas pelo Azure** no **submenu SSIS no** submenu da Azure Data Factory para abrir uma janela contendo as páginas de propriedade do seu projeto. Selecione a propriedade **de IDs de regra de avaliação suprimida** na secção **Definições Ativadas pelo Azure.** Por fim, selecione o botão elipse **(...**) para abrir a janela **'Definições de Supressão da Regra de Avaliação',** onde pode selecionar as regras de avaliação para suprimir.

   ![Definições ativadas pelo Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Definições de supressão de regras de avaliação através de definições ativadas pelo Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS em Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Configurar configurações ativadas pelo Azure

Antes de executar as suas encomendas em Azure, pode configurar as suas definições ativadas pelo Azure para as suas definições. Por exemplo, pode permitir que a autenticação do Windows no seu Azure-SSIS IR aceda às lojas de dados no local/cloud seguindo estes passos:

1. Clique com o botão direito no nó do projeto na janela Solution Explorer do SSDT para aparecer um menu. Em seguida, selecione o item **definições ativadas pelo Azure** no **submenu SSIS no** submenu da Azure Data Factory para criar uma janela contendo as páginas de propriedade do seu projeto.

   ![Definições ativadas pelo Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Selecione a propriedade **De autenticação ativação do Windows** na secção **Definições Ativadas pelo Azure** e, em seguida, selecione **True** no seu menu suspenso. Em seguida, selecione a propriedade **Credenciais de Autenticação** do Windows e, em seguida, selecione o botão elipse **(...**) para aparecer na janela **De credenciais de autenticação** do Windows.

   ![Ativar a autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Introduza as suas credenciais de autenticação do Windows. Por exemplo, para aceder aos Ficheiros Azure, pode introduzir `Azure` `YourStorageAccountName` , e para `YourStorageAccountKey` **Domínio**, Nome **de Utilizador**e **Password,** respectivamente.

   ![Credenciais de autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Início das execuções de pacotes

Depois de ligar os seus projetos ativados pelo Azure ao SSIS em ADF, avaliando a sua compatibilidade em nuvem e atenuando potenciais problemas, pode executar/testar os seus pacotes no Azure-SSIS IR.
-  Selecione o botão **Iniciar** na barra de ferramentas SSDT para deixar cair um menu. Em seguida, selecione o **artigo Executar em Azure.**

   ![Executar em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Em alternativa, clique com o botão direito no nó do pacote na janela Solution Explorer do SSDT para aparecer um menu. Em seguida, selecione o **Pacote Executar em Azure** item.

   ![Execute o pacote em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A execução dos seus pacotes em Azure requer que tenha um Azure-SSIS IR em execução, por isso, se o seu Azure-SSIS IR for interrompido, uma janela de diálogo aparecerá para o iniciar. Excluindo qualquer tempo de configuração personalizado, este processo deve ser concluído dentro de 5 minutos, mas pode demorar aproximadamente 20 - 30 minutos para o Azure-SSIS IR juntar uma rede virtual. Depois de executar as suas encomendas em Azure, pode parar o seu Azure-SSIS IR para gerir o seu custo de funcionamento clicando no seu nó na janela SSDT do Solution Explorer para aparecer um menu e, em seguida, selecionar o item **Start\Stop\Manage** que o leva ao portal ADF para o fazer.

### <a name="using-execute-package-task"></a>Utilização de tarefa de pacote de execução

Se as suas embalagens contiverem tarefas de pacote execute que se referem a pacotes infantis armazenados em sistemas de ficheiros locais, siga estes passos adicionais:

1. Faça o upload dos pacotes para crianças em Ficheiros Azure sob a mesma conta de Armazenamento Azure ligada aos seus projetos e obtenha o seu novo caminho da Convenção Universal de Nomeação (UNC), por exemplo. `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Substitua o percurso de ficheiros desses pacotes infantis no Gestor de Conexão de Ficheiros de Executar Tarefas de Pacotes pelo seu novo caminho UNC
   - Se a sua máquina local que executa o SSDT não puder aceder ao novo caminho do UNC, pode inseri-lo no painel de propriedades do Gestor de Ligação de Ficheiros
   - Em alternativa, pode utilizar uma variável para o caminho do ficheiro para atribuir o valor certo no tempo de execução

Se as suas embalagens contiverem tarefas de pacote execute que se referem a pacotes infantis no mesmo projeto, não é necessário qualquer passo adicional.

### <a name="switching-package-protection-level"></a>Mudar o nível de proteção do pacote

A execução de pacotes SSIS em Azure não suporta os níveis de proteção **Encriptação ComuserKey.** / **EncryptAllWithUserKey** Consequentemente, se os seus pacotes estiverem configurados para os utilizar, convertemo-los temporariamente em níveis de proteção **EncriptaçãoSensitiveWithPassword** / **EncryptAllWithPassword,** respectivamente. Também geraremos aleatoriamente senhas de encriptação quando enviarmos os seus pacotes para ficheiros Azure para execuções no seu Azure-SSIS IR.

> [!NOTE]
> Se as suas encomendas contiverem tarefas de pacote executem que se referem a pacotes infantis configurados para utilizar os níveis de proteção **EncriptaçãoSensitiveWithUserKey** / **EncryptAllWithUserKey,** tem de reconfigurar manualmente esses pacotes para utilização **de encriptaçãoSensitiveWithPassword** / **EncryptAllWithPassword,** respectivamente, antes de executar os seus pacotes.

Se os seus pacotes já estiverem configurados para utilizar os níveis de proteção **EncriptaçãoSensitiveWithPassword** / **EncryptAllWithPassword,** vamos mantê-los inalterados. Continuaremos a gerar aleatoriamente senhas de encriptação quando enviarmos os seus pacotes para ficheiros Azure para execuções no seu Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Mudar ambientes de execução de pacotes

Se parametrizar o seu projeto/pacotes no Modelo de Implementação de Projetos, pode criar várias configurações VS para mudar os ambientes de execução do pacote. Desta forma, pode atribuir valores específicos do ambiente aos parâmetros do seu projeto/pacote no tempo de execução. Recomendamos que tenha pelo menos duas configurações VS diferentes para execuções de pacotes nos ambientes locais e em nuvem, para que possa ativar os seus projetos contra a configuração da nuvem. Aqui está um exemplo passo-a-passo de mudar ambientes de execução de pacotes entre a sua máquina local e Azure:

1. Digamos que o seu pacote contém uma Tarefa do Sistema de Ficheiros que define os atributos de um ficheiro. Quando o executa na sua máquina local, define os atributos de um ficheiro armazenado no seu sistema de ficheiros local. Quando o executou no seu Azure-SSIS IR, pretende que ele desconfiem dos atributos de um ficheiro armazenado em Ficheiros Azure. Em primeiro lugar, crie um parâmetro de pacote do tipo de corda e nomeie-o **FilePath** para manter o valor do caminho do ficheiro alvo.

   ![Criar parâmetro de pacote](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Em seguida, na página **geral** da janela Do Editor de **Tarefas do Sistema** de Ficheiros, parametrize a propriedade **SourceVariable** na secção **De Ligação Origem** com o parâmetro do pacote **FilePath.** 

   ![Parametrizar a ligação de origem](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Por padrão, tem uma configuração VS existente para execuções de pacotes no ambiente local chamado **Desenvolvimento**. Crie uma nova configuração VS para execuções de pacotes no ambiente de nuvem chamado **Azure**, ver [Criar uma nova configuração VS](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019), se ainda não o fez.

4. Ao visualizar os parâmetros do seu pacote, selecione o botão **Adicionar Parâmetros às Configurações** para abrir a janela **Valores de Parâmetros de Gestão** para o seu pacote. Em seguida, atribua diferentes valores do caminho do ficheiro-alvo para o parâmetro do pacote **FilePath** nas configurações **de Desenvolvimento** e **Azure.**

   ![Atribuir valores de parâmetros](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Ativar o seu projeto contra a configuração da nuvem, ver [projetos SSIS que permitem o Azure,](#azureenableproject)se ainda não o fez. Em seguida, configurar as definições ativadas pelo Azure para permitir a autenticação do Windows para o seu Azure-SSIS IR aceder a Ficheiros Azure, ver [configurar configurações ativadas pelo Azure](#azureenabledsettings), se ainda não o fez.

6. Execute o seu pacote em Azure. Pode mudar o ambiente de execução do pacote de volta para a sua máquina local selecionando a configuração **de Desenvolvimento.**

   ![Configuração do estúdio visual switch](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Usando o ficheiro de configuração de pacote

Se utilizar ficheiros de configuração de pacotes no Modelo de Implementação de Pacotes, pode atribuir valores específicos do ambiente às propriedades do seu pacote no tempo de execução. Enviaremos automaticamente esses ficheiros com os seus pacotes para ficheiros Azure para execuções no seu Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Verificação de registos de execução de pacotes

Depois de iniciar a execução do seu pacote, vamos formatar e exibir os seus registos na janela **Progress** de SSDT. Para um pacote de longa duração, atualizaremos periodicamente os seus registos nos minutos. Pode cancelar imediatamente a execução do seu pacote selecionando o botão **Stop** na barra de ferramentas SSDT. Também pode encontrar temporariamente os dados brutos dos seus registos no seguinte caminho da UNC: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , mas vamos limpá-lo após um dia.

## <a name="current-limitations"></a>Limitações atuais

-  O SSDT habilitado para o Azure apoia apenas regiões de nuvem comercial/global e não apoia regiões de nuvem governamentais/nacionais por enquanto.

## <a name="next-steps"></a>Passos seguintes

Assim que estiver satisfeito com a execução dos seus pacotes em Azure a partir de SSDT, pode implantá-los e executá-los como atividades do Pacote SSIS executado em oleodutos ADF, consulte [os pacotes Running SSIS como executar atividades do Pacote SSIS em oleodutos ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
