---
title: Configure um tempo de execução de integração auto-hospedado como um proxy para o SSIS
description: Aprenda a configurar um tempo de funcionação de integração auto-hospedado como procuração para um Runtime de Integração Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605501"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configure um IR auto-hospedado como procuração para um IR Azure-SSIS na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como executar pacotes de Serviços de Integração de Servidores SQL (SSIS) num Runtime de Integração Azure-SSIS (Azure-SSIS IR) na Azure Data Factory com um tempo de funcionação de integração auto-hospedado (IR auto-hospedado) configurado como um proxy. 

Com esta funcionalidade, pode aceder a dados no local sem ter de [aderir ao seu IR Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). A funcionalidade é útil quando a sua rede corporativa tem uma configuração demasiado complexa ou uma política demasiado restritiva para injetar o seu Ir Azure-SSIS na si.

Esta funcionalidade divide qualquer tarefa de fluxo de dados do SSIS que tenha uma fonte de dados no local em duas tarefas de encenação: 
* A primeira tarefa, que funciona no seu IR auto-hospedado, transfere primeiro dados da fonte de dados no local para uma área de preparação no seu armazenamento Azure Blob.
* A segunda tarefa, que funciona no seu IR Azure-SSIS, transfere os dados da área de paragem para o destino de dados pretendido.

Outros benefícios e capacidades desta funcionalidade permitem- lhe, por exemplo, configurar o seu IR auto-hospedado em regiões que ainda não são suportadas por um IR Azure-SSIS, e permitir o endereço IP estático público do seu IR auto-hospedado na firewall das suas fontes de dados.

## <a name="prepare-the-self-hosted-ir"></a>Prepare o IR auto-hospedado

Para utilizar esta funcionalidade, cria-se primeiro uma fábrica de dados e cria-se um IR Azure-SSIS. Se ainda não o fez, siga as instruções em [Configurar um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Em seguida, configura o seu IR auto-hospedado na mesma fábrica de dados onde o seu IR Azure-SSIS está configurado. Para tal, consulte [Criar um IR auto-hospedado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Por fim, descarrega e instala a versão mais recente do IR auto-hospedado, bem como os controladores adicionais e o tempo de execução, na sua máquina no local ou na máquina virtual Azure (VM), da seguinte forma:
- Descarregue e instale a versão mais recente do [IR auto-hospedado.](https://www.microsoft.com/download/details.aspx?id=39717)
- Se utilizar conectores de base de dados de ligação e incorporação de objetos (OLEDB) nas suas embalagens, descarregue e instale os controladores OLEDB relevantes na mesma máquina onde o seu IR auto-hospedado está instalado, se ainda não o tiver feito.  

  Se utilizar a versão anterior do controlador OLEDB para o SQL Server (SQL Server Client Native Client [SQLNCLI]), [faça o download da versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se utilizar a versão mais recente do controlador OLEDB para o SQL Server (MSOLEDBSQL), [faça o download da versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se utilizar os controladores OLEDB para outros sistemas de base de dados, tais como PostgreSQL, MySQL, Oracle, e assim por diante, pode descarregar as versões de 64 bits dos seus websites.
- Se ainda não o fez, [descarregue e instale a versão de 64 bits do tempo de execução Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na mesma máquina onde o seu IR auto-hospedado está instalado.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Prepare o serviço ligado ao armazenamento Azure Blob para a encenação

Se ainda não o fez, crie um serviço ligado ao armazenamento Azure Blob na mesma fábrica de dados onde o seu IR Azure-SSIS está configurado. Para isso, consulte [Criar um serviço ligado à fábrica de dados Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Certifique-se de que faz o seguinte:
- Para **data store,** selecione **Armazenamento De Blob Azure**.  
- Para connect via tempo de execução de **integração,** selecione **AutoResolveIntegrationRuntime** (não o seu IR Azure-SSIS nem o seu IR auto-hospedado), porque usamos o Iv-Fi padrão para obter credenciais de acesso para o seu Armazenamento Blob Azure.
- Para **o método de autenticação,** selecione chave **conta,** **SAS URI,** ou **Diretor de Serviço**.  

    >[!TIP]
    >Se selecionar o método Principal de **Serviço,** conceda ao seu diretor de serviço pelo menos uma função de Colaborador de *Dados blob*de armazenamento. Para mais informações, consulte o [conector de armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

![Prepare o serviço ligado ao armazenamento Azure Blob para a encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configure um IR Azure-SSIS com o seu IR auto-hospedado como procuração

Depois de ter preparado o seu serviço ligado ao armazenamento ir e azure blob auto-hospedado para encenação, pode agora configurar o seu novo ou existente IR Azure-SSIS com o IR auto-hospedado como procuração no portal ou app da sua fábrica de dados. Antes de o fazer, porém, se o seu IR Azure-SSIS existente já estiver em execução, pare-o e reinicie-o.

1. No painel de configuração do tempo de **integração,** passe as **definições gerais** e as **definições SQL** selecionando **A Seguinte**. 

1. Na secção **definições Avançadas,** faça o seguinte:

   1. Selecione o tempo de execução de integração auto-hospedado como procuração para a sua caixa de verificação de tempo de **integração Azure-SSIS.** 

   1. Na lista de drop-down de **integração auto-hospedada,** selecione o seu IR auto-hospedado existente como um proxy para o IR Azure-SSIS.

   1. Na lista de drop-down do **serviço de armazenamento de armazenamento,** selecione o seu serviço ligado ao armazenamento Azure Blob existente ou crie um novo para encenação.

   1. Na caixa de **percurso de preparação,** especifique um recipiente de bolha na sua conta de armazenamento Azure Blob selecionada ou deixe-o vazio para utilizar um predefinido para a encenação.

   1. Selecione **Continuar**.

   ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Também pode configurar o seu novo ou existente IR Azure-SSIS com o IR auto-hospedado como procuração utilizando o PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Ativar os pacotes SSIS para ligar por procuração

Ao utilizar a mais recente extensão sSDT com Projetos SSIS para Estúdio `ConnectByProxy` Visual ou um instalador autónomo, pode encontrar uma nova propriedade que tenha sido adicionada nos gestores de conexão OLEDB ou Flat File.
* [Descarregue a extensão SSDT com Projetos SSIS para Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Descarregue o instalador autónomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando desenha novos pacotes que contenham tarefas de fluxo de dados com fontes OLEDB ou Flat File, que lhe permitem aceder a bases de dados ou ficheiros no local, pode ativar esta propriedade definindo-a como *True* no painel **Properties** dos gestores de conexão relevantes.

![Ativar a propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Também pode ativar esta propriedade quando executa os pacotes existentes, sem ter que troque manualmente um a um.  Existem duas opções:
- **Opção A**: Abrir, reconstruir e reimplantar o projeto contendo esses pacotes com o mais recente SSDT a funcionar no seu IR Azure-SSIS. Em seguida, pode ativar a propriedade definindo-a como *True* para os gestores de conexão relevantes. Quando estão a executar pacotes de SSMS, estes gestores de ligação aparecem no separador Gestores de **Ligação** da janela pop-up **do Pacote executa.**

  ![Ativar a propriedade ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Também pode ativar a propriedade definindo-a como *True* para os gestores de conexão relevantes que aparecem no separador **Gestores** de Ligação da [atividade do Pacote Execute SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando estão a executar pacotes em pipelines data Factory.
  
  ![Ativar a propriedade ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opção B:** Reutilizar o projeto contendo esses pacotes para executar no seu SSIS IR. Você pode então ativar a propriedade `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`fornecendo o seu caminho de propriedade, e definindo-o *True* como uma propriedade sobreposição no separador **Avançado** da janela pop-up do Pacote **Executar** quando você está executando pacotes de SSMS.

  ![Ativar a propriedade ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Você também pode ativar a propriedade fornecendo seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e definindo-o como um overover de propriedade no separador **Desobreposição** de Propriedade da [atividade do Pacote Execute SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando você está executando pacotes em pipelines data Factory. *True*
  
  ![Ativar a propriedade ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Depurar as primeira e segunda tarefas de encenação

No seu IR auto-hospedado, pode encontrar os registos de tempo de execução na pasta *C:\ProgramData\SSISTelemettry* e os registos de execução das primeiras tarefas de encenação na pasta *C:\ProgramData\SSISTelemettry\ExecutionLog.*  Pode encontrar os registos de execução de segundas tarefas de encenação no seu SSISDB ou caminhos de registo especificados, dependendo se armazena os seus pacotes no SSISDB ou sistema de ficheiros, partilhas de ficheiros ou Ficheiros Azure. Você também pode encontrar as iDs únicas das primeiras tarefas de encenação nos registos de execução de segundas tarefas de encenação. 

![Identificação única da primeira tarefa de encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Utilize a autenticação do Windows em tarefas de encenação

Se as tarefas de encenação no seu IR auto-hospedado necessitarem de autenticação do Windows, [configure os seus pacotes SSIS para utilizar a mesma autenticação do Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

As suas tarefas de encenação serão invocadas com a conta de serviço ir auto-hospedada *(NT SERVICE\DIAHostService*, por padrão), e as suas lojas de dados serão acedidas com a conta de autenticação do Windows. Ambas as contas exigem que sejam atribuídas a essas políticas de segurança. Na máquina de INFRAVERMELHOautos, vá à Missão de > **Direitos dos Utilizadores**das**Políticas Locais**de Política > de Segurança **Local,** e depois faça o seguinte:

1. Atribuir as quotas de *memória Ajustar para um processo* e substituir uma política de ficha de *nível de processo* na conta de serviço ir auto-hospedada. Isto deve ocorrer automaticamente quando instalar o seu IR auto-hospedado com a conta de serviço predefinida. Se não, atribua essas apólices manualmente. Se utilizar uma conta de serviço diferente, atribua-lhe as mesmas políticas.

1. Atribuir o *Registo como uma* política de serviço à conta de autenticação do Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faturação para as primeira e segunda tarefas de encenação

As primeiras tarefas de encenação que executam o seu IR auto-hospedado são faturadas separadamente, assim como quaisquer atividades de movimento de dados que funcionam em um IR auto-hospedado são cobrados. Isto é especificado no artigo de preços do pipeline de [dados da Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

As segundas tarefas de encenação que executam o seu IR Azure-SSIS não são faturadas separadamente, mas o seu ir Azure-SSIS em execução é faturado conforme especificado no artigo de [preços do Ir Azure-SSIS.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Habilitação TLS 1.2

Se necessitar de utilizar um protocolo de rede forte de criptografia/mais seguro (TLS 1.2) e desativar as versões SSL/TLS mais antigas no seu IV auto-hospedado, pode descarregar e executar o script *principal.cmd* que pode ser encontrado na pasta *CustomSetupScript/UserScenarios/TLS 1.2* do nosso recipiente de pré-visualização público.  Utilizando o [Azure Storage Explorer,](https://storageexplorer.com/)pode ligar-se ao nosso recipiente público de pré-visualização, entrando no seguinte SAS URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitações atuais

- Apenas são suportadas as tarefas de fluxo de dados com conectividade de base de dados aberta (ODBC)/OLEDB/Flat File ou o destino OLEDB. 
- Apenas os serviços ligados ao armazenamento Azure Blob que estão configurados com *chave de conta,* *Assinatura de Acesso Partilhado (SAS) URI,* ou autenticação principal de *serviço* são atualmente suportados.
- *O mapeado* de parâmetros na Fonte OLEDB ainda não é suportado. Como suposições, utilize o *Comando SQL from Variable* como modo de *acesso* e utilize a *Expressão* para inserir as suas variáveis/parâmetros num comando SQL. Como ilustração, consulte o pacote *ParameterMappingSample.dtsx* que pode ser encontrado na pasta *SelfHostedIRProxy/Limitações* do nosso recipiente de pré-visualização pública. Utilizando o Azure Storage Explorer, pode ligar-se ao nosso recipiente de pré-visualização público, entrando no SAS URI acima.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu IR auto-hospedado como procuração para o seu IR Azure-SSIS, pode implementar e executar os seus pacotes para aceder a dados no local como executar atividades do Pacote SSIS em pipelines de Data Factory. Para saber como, consulte os [pacotes Run SSIS como executar atividades do pacote SSIS em pipelines de fábrica](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)de dados .
