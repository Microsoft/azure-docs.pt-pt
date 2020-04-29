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
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399433"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes SSIS em Azure a partir do SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve a funcionalidade dos projetos de Serviços de Integração de Servidores SQL (SSIS) ativados pelo Azure na SQL Server Data Tools (SSDT), que lhe permite executar pacotes em Runtime de Integração Azure-SSIS (IR) na Azure Data Factory (ADF).  Pode utilizar esta funcionalidade para testar os seus pacotes SSIS existentes antes de levantar & de sossetrá-los para o Azure ou desenvolver novos pacotes SSIS para funcionar em Azure.

Com esta funcionalidade, pode criar um novo IR Azure-SSIS ou anexar um existente aos projetos SSIS e, em seguida, executar os seus pacotes nele.  Apoiamos pacotes de execução a serem implantados no catálogo SSIS (SSISDB) no Modelo de Implementação de Projetos e aqueles a serem implantados em sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure no Modelo de Implementação de Pacotes. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar esta funcionalidade, por favor, descarregue e instale o mais recente SSDT com extensão de Projetos SSIS para Estúdio Visual a partir [daqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como instalador autónomo a partir [daqui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Projetos SSIS com habilitação Azure
No SSDT, pode criar novos projetos SSIS ativados pelo Azure utilizando o modelo de **Projeto de Serviços de Integração (Azure-Enabled).**

![Novo Projeto SSIS habilitado para OI](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Em alternativa, pode ativar os seus projetos SSIS existentes clicando no nó do projeto no painel Solution Explorer do SSDT para aparecer num menu e, em seguida, selecionar o item do menu **Azure-Enabled.**

![Projeto SSIS existente via Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

O Azure habilitar os seus projetos SSIS existentes requer que você detetete a sua versão de servidor alvo para ser o mais recente suportado pelo Azure-SSIS IR, que atualmente é **O Servidor SQL 2017**, por isso, se ainda não o fez, surgirá uma janela de diálogo para o fazer.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Connect Azure projetos habilitados para o SSIS na Fábrica de Dados Azure
Ao ligar os seus projetos ativados pelo Azure ao SSIS em ADF, pode fazer o upload dos seus pacotes para Ficheiros Azure e executá-los no IR Azure-SSIS.  Para isso, siga estes passos:

1. Clique no projeto ou no nó **linked Azure Resources** no painel Solution Explorer do SSDT para aparecer um menu e selecionar o item do menu **Connect To SSIS em Azure Data Factory** para lançar o SSIS no Assistente de **Ligação ADF**.

   ![Ligue-se ao SSIS em ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. No **SSIS na página de Introdução ADF,** reveja a introdução e clique no botão **Seguinte** para continuar.

   ![SSIS em Introdução ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na página **Select SSIS IR na página ADF,** selecione o seu ADF e o IR Azure-SSIS existentes para executar pacotes ou criar novos se não tiver nenhum.
   - Para selecionar o seu IR Azure-SSIS existente, selecione primeiro a subscrição Azure relevante e a ADF.
   - Se selecionar o seu ADF existente que não tenha nenhum IR Azure-SSIS, clique no botão **Create SSIS IR** para criar um novo no portal/app ADF.
   - Se selecionar a subscrição azure existente que não tenha nenhum ADF, clique no botão **Create SSIS IR** para lançar o Assistente de Criação de Prazo de **Integração,** onde pode introduzir o local e prefixar para que criemos automaticamente um novo Grupo de Recursos Azure, Data Factory e SSIS IR em seu nome, nomeado no seguinte padrão: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Selecione SSIS IR em ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na página **de Armazenamento Select Azure,** selecione a sua conta de Armazenamento Azure existente para fazer upload de pacotes em Ficheiros Azure ou criar uma nova se não tiver nenhuma.
   - Para selecionar a sua conta de Armazenamento Azure existente, selecione primeiro a subscrição Azure relevante.
   - Se selecionar a mesma subscrição Azure que o seu IR Azure-SSIS que não tenha qualquer conta de Armazenamento Azure, clique no botão **Create Azure Storage** para que criemos automaticamente um novo em seu nome no mesmo local que o seu IR Azure-SSIS, nomeado combinando um prefixo do seu nome Azure-SSIS IR e a sua data de criação.
   - Se selecionar uma subscrição Azure diferente que não tenha qualquer conta de Armazenamento Azure, clique no botão **Create Azure Storage** para criar uma nova no portal Azure.
   
   ![Selecione o Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Clique no botão **Ligar** para completar a ligação.  Apresentaremos a sua conta de Armazenamento Azure-SSIS IR e Azure selecionada sob o nó **linked Azure Resources** no painel Solution Explorer da SSDT.  Também atualizaremos o estado do seu IR Azure-SSIS, enquanto você pode geri-lo clicando no seu nó para aparecer um menu e, em seguida, selecionar o item do menu **Start\Stop\Gerir** o item do menu que o leva ao portal/app ADF para o fazer.

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS em Azure
### <a name="starting-package-executions"></a>Iniciando execuções de pacotes
Depois de ligar os seus projetos ao SSIS em ADF, pode executar pacotes no Ir Azure-SSIS.  Tem duas opções para iniciar execuções de pacotes:
-  Clique no botão **Iniciar** na barra de ferramentas SSDT para deixar cair um menu e selecionar o artigo do menu **Execute no Menu Azure** 

   ![Executar em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Clique no nó da embalagem no painel Solution Explorer do SSDT para aparecer um menu e selecionar o Pacote Executar no item do menu **Azure.**

   ![Executar pacote em Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Executar os seus pacotes em Azure requer que tenha um IR Azure-SSIS em execução, por isso, se o seu IR Azure-SSIS for parado, uma janela de diálogo surgirá para o iniciar.  Excluindo qualquer tempo de configuração personalizado, este processo deve ser concluído dentro de 5 minutos, mas pode demorar aproximadamente 20 a 30 minutos para que o Azure-SSIS IR se junte a uma rede virtual.  Depois de executar os seus pacotes em Azure, pode parar o seu IR Azure-SSIS para gerir o seu custo de funcionamento clicando no seu nó no painel Solution Explorer da SSDT para aparecer um menu e, em seguida, selecionar o item do menu **Start\Stop\Gerir** o item de menu que o leva ao portal/app ADF para o fazer.

### <a name="checking-package-execution-logs"></a>Verificação de registos de execução de pacotes
Quando iniciar a execução do seu pacote, formamos e exibiremos o seu log na janela Progress do SSDT.  Para um pacote de longa duração, atualizaremos periodicamente o seu registo pelas atas.  Pode impedir a execução do pacote clicando no botão **Stop** na barra de ferramentas SSDT que o cancelará imediatamente.  Também pode encontrar temporariamente os dados brutos de registo no `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`seu caminho da Convenção Universal de Nomeação (UNC): , mas vamos limpá-lo após um dia.

### <a name="switching-package-protection-level"></a>Mudar o nível de proteção do pacote
A execução de pacotes SSIS em Azure não suporta níveis de proteção **EncryptSensitive WithUserKey.**/**EncryptAllWithUserKey**  Consequentemente, se os seus pacotes estiverem configurados com esses, iremos trocá-los temporariamente para **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respectivamente, com palavras-passe geradas aleatoriamente quando enviarmos os seus pacotes para Ficheiros Azure para execução no seu IR Azure-SSIS.

> [!NOTE]
> Se os seus pacotes contiverem Executar Tarefas de Pacote que se referem a outros pacotes configurados com níveis de proteção **EncryptSensitiveWithUserKey**/**AllWithUserKey,** é necessário reconfigurar manualmente esses outros pacotes para utilizar **encryptSensitiveWithPassword**/**EncryptAllWithPassword**, respectivamente, antes de executar os seus pacotes.

Se os seus pacotes já estiverem configurados com os níveis de proteção **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** mantemo-los inalterados, mas continuaremos a usar senhas geradas aleatoriamente quando enviarmos os seus pacotes para Ficheiros Azure para execução no seu IR Azure-SSIS.

### <a name="using-package-configuration-file"></a>Usando o ficheiro de configuração do pacote
Se utilizar ficheiros de configuração de pacotes no Modelo de Implementação de Pacotes para alterar valores variáveis no tempo de execução, enviaremos automaticamente esses ficheiros com os seus pacotes para Ficheiros Azure para execução no seu IR Azure-SSIS.

### <a name="using-execute-package-task"></a>Usando executar tarefa de pacote
Se os seus pacotes contiverem Executar Tarefas de Pacote que se referem a outros pacotes armazenados em sistemas de ficheiros locais, terá de realizar as seguintes configurações adicionais:

1. Faça o upload dos outros pacotes para Ficheiros Azure sob a mesma conta de Armazenamento Azure ligada aos seus projetos e obtenha o seu novo caminho unc, por exemplo.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Substitua o caminho de ficheiro supor os outros pacotes no Gestor de Ligação de Ficheiros de Executar Tarefas de Pacotes por o seu novo caminho unc
   - Se a sua máquina que executa o SSDT não conseguir aceder ao novo caminho unc, pode alterar o caminho de ficheiro no painel Properties do File Connection Manager
   - Alternativamente, pode usar uma variável para o caminho do ficheiro para atribuir o valor certo no tempo de execução

Se os seus pacotes contiverem Executar Tarefas de Pacote que se referem a outros pacotes no mesmo projeto, não é necessária nenhuma configuração adicional.

## <a name="next-steps"></a>Passos seguintes
Uma vez satisfeito com a execução dos seus pacotes em Azure a partir do SSDT, pode implantá-los e executá-los como executar atividades do Pacote SSIS em pipelines ADF, consulte os [pacotes Run SSIS como executar atividades do Pacote SSIS em pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
