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
ms.date: 07/31/2019
ms.openlocfilehash: 1d8261d05f59c7f40ba6b1e2d59d2b15ad56de95
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424587"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes SSIS em Azure a partir de SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve a característica dos projetos de serviços de integração de servidores SQL (SSIS) ativados pelo Azure em Ferramentas de Dados do Servidor SQL (SSDT), que lhe permite executar pacotes no Tempo de Execução de Integração Azure-SSIS (IR) na Azure Data Factory (ADF).  Pode utilizar esta funcionalidade para testar os seus pacotes SSIS existentes antes de levantar & deslocar/migrar para Azure ou para desenvolver novos pacotes SSIS para funcionar em Azure.

Com esta funcionalidade, pode criar um novo Azure-SSIS IR ou anexar um existente a projetos SSIS e, em seguida, executar os seus pacotes nele.  Apoiamos a execução de pacotes a implementar no catálogo SSIS (SSISDB) no Modelo de Implementação de Projetos e aqueles que serão implantados em sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure no Modelo de Implementação de Pacotes. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar esta funcionalidade, descarregue e instale a mais recente extensão SSDT com SSIS Projects para Estúdio Visual a partir [daqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como instalador autónomo a partir [daqui.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Projetos SSIS habilitados para a Azure
### <a name="create-new-azure-enabled-ssis-projects"></a>Criar novos projetos SSIS habilitados para o Azure
No SSDT, pode criar novos projetos SSIS habilitados pelo Azure utilizando o modelo do **Projeto de Serviços de Integração (Azure-Enabled).**

   ![Novo Projeto SSIS habilitado para a Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Após a criação do projeto Azure, será solicitado que se conecte ao SSIS na Azure Data Factory.

   ![Ligue o pedido de ir azure-SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Se pretender ligar-se imediatamente ao seu IR Azure-SSIS, consulte-se para obter mais detalhes [sobre o Connect to Azure-SSIS IR.](#irconnect) Também pode ligar-se mais tarde clicando no nó do projeto no painel SSDT do Solution Explorer para aparecer um menu e, em seguida, selecionar o item do menu **Connect to SSIS in Azure Data Factory** no submenu **SSIS in Azure Data Factory.**

### <a name="azure-enable-existing-ssis-projects"></a>Azure-enable projetos SSIS existentes
Para os projetos SSIS existentes, pode a azure-os-capacitar seguindo estes passos:

1. Clique com o botão direito no nó do projeto no painel SSDT do Solution Explorer para aparecer um menu e, em seguida, selecionar o item do menu **Azure-Enabled Project** no **âmbito do SSIS in Azure Data Factory** submenu para lançar o Assistente de Projeto **Ativado pelo Azure.**

   ![Projeto SSIS ativo azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na página **Select Visual Studio Configuration,** selecione a configuração do Estúdio Visual para aplicar as definições de execução de pacotes em Azure. Uma boa prática é criar uma nova configuração do Visual Studio para cloud e ativar o seu projeto contra a configuração da nuvem. Com múltiplas configurações, pode atribuir valores diferentes aos seus parâmetros com base nos diferentes ambientes (localmente ou em Azure). Consulte [este exemplo](#example) para mais detalhes.

   ![Selecione configuração de estúdio visual](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. O Azure-enableing seus projetos SSIS existentes requer que você definir a sua versão de servidor alvo para ser o mais recente suportado pela Azure-SSIS IR, que é atualmente **SQL Server 2017**. Portanto, se ainda não o fez, tem de verificar se o seu pacote contém componentes adicionais que não são suportados no SQL Server 2017 e clicar no botão Seguinte para continuar se não houver preocupação.

   ![Versão do servidor do alvo do switch](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Consulte a [Connect to Azure-SSIS IR](#irconnect) para completar a ligação ao Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Ligue os projetos habilitados ao SSIS na Azure Data Factory

Ao ligar os seus projetos ativados pelo Azure ao SSIS em ADF, pode enviar os seus pacotes para ficheiros Azure e executá-los no Azure-SSIS IR. Para tal, siga estes passos:

1. Na página **SSIS na página ADF Introdução,** reveja a introdução e clique no botão **Seguinte** para continuar.

   ![SSIS na Introdução da ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na página **Select SSIS IR na página ADF,** selecione o seu ADF e Azure-SSIS IR existentes para executar pacotes ou criar novos caso não tenha nenhum.
   - Para selecionar o seu Azure-SSIS IR existente, selecione primeiro a subscrição Azure relevante e a ADF.
   - Se selecionar o seu ADF existente que não tenha qualquer Azure-SSIS IR, clique no botão **Create SSIS IR** para criar um novo no portal/app ADF.
   - Se selecionar a subscrição Azure existente que não tenha ADF, clique no botão **Create SSIS IR** para lançar o **Assistente de Criação de Tempo de Integração,** onde pode introduzir a localização e prefixo para criarmos automaticamente um novo Grupo de Recursos Azure, Data Factory e SSIS IR em seu nome, nomeado no seguinte padrão: **YourPrefix-RG/DF/IR-YourCreationTime**.

   ![Selecione SSIS IR em ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na página **Select Azure Storage,** selecione a sua conta de Armazenamento Azure existente para enviar pacotes em Ficheiros Azure ou criar um novo caso não tenha nenhum.
   - Para selecionar a sua conta de Armazenamento Azure existente, selecione primeiro a subscrição Azure relevante.
   - Se selecionar a mesma subscrição Azure que o seu Azure-SSIS IR que não tenha qualquer conta de Armazenamento Azure, clique no botão **De Armazenamento Create Azure** para criarmos automaticamente uma nova em seu nome no mesmo local que o seu Azure-SSIS IR, nomeado através da combinação de um prefixo do seu nome Azure-SSIS IR e da sua data de criação.
   - Se selecionar uma subscrição Azure diferente que não tenha qualquer conta de Armazenamento Azure, clique no botão **De Armazenamento Create Azure** para criar uma nova no portal Azure.

   ![Selecione o Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Clique no botão **Ligar** para completar a sua ligação.  Exibiremos a sua conta de Armazenamento Azure-SSIS IR e Azure selecionada no nó **De Recursos Azure Ligados** no painel SSDT do Solution Explorer.  Também refrescaremos o estado do seu Azure-SSIS IR, enquanto pode geri-lo clicando com o direito no seu nó para aparecer um menu e, em seguida, selecionar o item do menu **Start\Stop\Gerir** o que o leva ao portal/app ADF para o fazer.

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS em Azure
### <a name="azure-enabled-setting"></a>Definição ativada pelo Azure
Antes de executar as suas encomendas em Azure, pode optar por configurar as definições de execução. Se pretender ativar a autenticação do Windows para as suas embalagens SSIS, siga os passos abaixo:

1. Clique com o botão direito no nó do projeto no painel SSDT do Solution Explorer para aparecer um menu e, em seguida, selecionar o item do menu **Azure-Enabled Settings** no **submenu SSIS in Azure Data Factory.**

   ![Definições ativadas pelo Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Clique na lista de drop down **de autenticação do Windows Enable** e escolha **True**. Em seguida, clique no botão de edição para a opção **De autenticação** do Windows para introduzir as credenciais.

   ![Ativar a autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Fornecer credenciais no editor **de Credenciais de Autenticação** do Windows.

   ![Credenciais de autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Início das execuções de pacotes
Depois de ligar os seus projetos ao SSIS em ADF, pode executar pacotes no Azure-SSIS IR.  Tem duas opções para iniciar execuções de pacotes:
-  Clique no botão **Iniciar** na barra de ferramentas SSDT para deixar cair um menu e selecione o item do menu **Executar em Azure** 

   ![Executar em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Clique com o botão direito no nó de pacote no painel SSDT do Solution Explorer para aparecer um menu e selecionar o Pacote Executar no item do menu **Azure.**

   ![Execute pacote em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A execução dos seus pacotes em Azure requer que tenha um Azure-SSIS IR em execução, por isso, se o seu Azure-SSIS IR for interrompido, uma janela de diálogo aparecerá para o iniciar.  Excluindo qualquer tempo de configuração personalizado, este processo deve ser concluído dentro de 5 minutos, mas pode demorar aproximadamente 20 - 30 minutos para o Azure-SSIS IR juntar uma rede virtual.  Depois de executar as suas encomendas em Azure, pode parar o seu Azure-SSIS IR para gerir o seu custo de funcionamento clicando no seu nó no painel SSDT do Solution Explorer para aparecer um menu e, em seguida, selecionar o item do menu **Start\Stop\Gerir** o que o leva ao portal/app ADF para o fazer.

### <a name="checking-package-execution-logs"></a>Verificação de registos de execução de pacotes
Quando iniciar a execução do seu pacote, entraremos em formato e exibiremos o seu registo na janela Progress of SSDT.  Para um pacote de longa duração, atualizaremos periodicamente o seu registo nas atas.  Pode parar a execução da sua embalagem clicando no botão **Stop** na barra de ferramentas SSDT que o cancelará imediatamente.  Também pode encontrar temporariamente os dados brutos de registo no seu caminho da Convenção Universal de Nomeação (UNC): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` mas vamos limpá-lo após um dia.

### <a name="switching-package-protection-level"></a>Mudar o nível de proteção do pacote
A execução de pacotes SSIS no Azure não suporta os níveis de proteção **Encriptação ComuserKey.** / **EncryptAllWithUserKey**  Consequentemente, se os seus pacotes estiverem configurados com esses, vamos alterá-los temporariamente para **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, respectivamente, com senhas geradas aleatoriamente quando enviamos os seus pacotes para Ficheiros Azure para execução no seu IR Azure-SSIS.

> [!NOTE]
> Se as suas encomendas contiverem tarefas de pacote executem que se referem a outros pacotes configurados com os níveis de proteção **Encriptação ComUserKey** / **EncryptAllWithUserKey,** tem de reconfigurar manualmente esses outros pacotes para utilizar **encriptaçãoSensitiveWithPassword,** / **EncryptAllWithPassword**respectivamente, antes de executar os seus pacotes.

Se os seus pacotes já estiverem configurados com os níveis de proteção **EncriptaçãoSensitiveWithPassword,** / **EncryptAllWithPassword** vamos mantê-los inalterados, mas continuaremos a utilizar senhas geradas aleatoriamente quando enviarmos os seus pacotes para Ficheiros Azure para execução no seu Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Usando o ficheiro de configuração de pacote
Se utilizar ficheiros de configuração de pacotes no Modelo de Implementação de Pacotes para alterar valores variáveis no tempo de execução, enviaremos automaticamente esses ficheiros com os seus pacotes para ficheiros Azure para execução no seu Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Utilização de tarefa de pacote de execução
Se as suas embalagens contiverem tarefas de pacote execute que se referem a outras embalagens armazenadas em sistemas de ficheiros locais, tem de executar as seguintes configurações adicionais:

1. Faça o upload dos outros pacotes para os Ficheiros Azure na mesma conta de Armazenamento Azure ligada aos seus projetos e obtenha o seu novo caminho unc, por exemplo.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Substitua o caminho de ficheiro desses outros pacotes no Gestor de Conexão de Ficheiros de Executar Tarefas de Pacotes pelo seu novo caminho UNC
   - Se a sua máquina em funcionamento SSDT não conseguir aceder ao novo caminho do UNC, pode alterar o caminho de ficheiro no painel de propriedades do Gestor de Ligação de Ficheiros
   - Em alternativa, pode utilizar uma variável para o caminho do ficheiro para atribuir o valor certo no tempo de execução

Se as suas embalagens contiverem executações de pacotes que se referem a outras embalagens no mesmo projeto, não é necessária nenhuma configuração adicional.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Mudar ambientes de execução de pacotes com projetos habilitados para o Azure

Para mudar os ambientes de execução de pacotes com projetos ativados pelo Azure, pode criar múltiplas configurações do Visual Studio para aplicar diferentes valores para parâmetros específicos do ambiente. Por exemplo, existe um pacote SSIS simples com uma Tarefa do Sistema de **Ficheiros** que define atributos do ficheiro especificado, pode simplesmente migrar para a nuvem usando os seguintes passos:

1. Defina um **parâmetro FilePath** do tipo de corda, que é o caminho do ficheiro do ficheiro-alvo.

   ![Definir parâmetro de pacote](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Associar a **Ligação Origem** a este parâmetro. 

   ![Ligação de fonte de atualização](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Crie uma nova configuração do Estúdio Visual para nuvem no Visual Studio Configuration Manager.

4. Defina os valores para este parâmetro em cada configuração do Visual Studio.

   ![Valores de parâmetros de substituição](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Ativado pelo Azure este projeto SSIS contra a configuração do Estúdio Visual para nuvem.

6. Execute este pacote em Azure. Pode facilmente mudar o ambiente para o local, mudando a configuração atual do Estúdio Visual.

   ![Configuração do estúdio visual switch](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="next-steps"></a>Passos seguintes
Assim que estiver satisfeito com a execução dos seus pacotes em Azure a partir de SSDT, pode implantá-los e executá-los como atividades do Pacote SSIS executado em oleodutos ADF, consulte [pacotes Run SSIS como executar atividades do Pacote SSIS em oleodutos ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
