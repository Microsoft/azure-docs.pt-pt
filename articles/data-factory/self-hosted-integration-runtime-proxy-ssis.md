---
title: Configure um tempo de integração auto-hospedado como procuração para ssis
description: Aprenda a configurar um tempo de integração auto-hospedado como procuração para um Tempo de Integração Azure-SSIS.
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
ms.date: 07/09/2020
ms.openlocfilehash: 1eac86e856840d5cb78313fb4d61751066d6886b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184009"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configure um IR auto-hospedado como representante de um Azure-SSIS IR na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar pacotes SQL Server Integration Services (SSIS) num Runtime de Integração Azure-SSIS (Azure-SSIS IR) na Azure Data Factory com um tempo de integração auto-hospedado (IR auto-hospedado) configurado como um proxy. 

Com esta funcionalidade, pode aceder aos dados no local sem ter de se juntar ao [seu Azure-SSIS IR a uma rede virtual.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) A funcionalidade é útil quando a sua rede corporativa tem uma configuração demasiado complexa ou uma política demasiado restritiva para que possa injetar o seu Azure-SSIS IR na sua.

Esta funcionalidade divide qualquer tarefa de fluxo de dados SSIS que tenha uma fonte de dados no local em duas tarefas de encenação: 
* A primeira tarefa, que funciona no seu IR auto-hospedado, move primeiro dados da fonte de dados no local para uma área de preparação no seu armazenamento Azure Blob.
* A segunda tarefa, que funciona no seu Azure-SSIS IR, em seguida, transfere os dados da área de preparação para o destino de dados pretendido.

Outros benefícios e capacidades desta funcionalidade permitem-lhe, por exemplo, configurar o seu IR auto-hospedado em regiões que ainda não são suportadas por um IR Azure-SSIS, e permitir o endereço IP estático público do seu IR auto-hospedado na firewall das suas fontes de dados.

## <a name="prepare-the-self-hosted-ir"></a>Prepare o IR auto-hospedado

Para utilizar esta funcionalidade, cria primeiro uma fábrica de dados e cria-se um IR Azure-SSIS na sua área. Se ainda não o fez, siga as instruções em [Configurar um Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Em seguida, instalou o seu IR auto-hospedado na mesma fábrica de dados onde está configurado o seu Azure-SSIS IR. Para tal, consulte [Criar um IR auto-hospedado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Por fim, descarrega e instala a versão mais recente do IR auto-hospedado, bem como os controladores e tempo de execução adicionais, na sua máquina de bordo ou na máquina virtual Azure (VM), da seguinte forma:
- Descarregue e instale a versão mais recente do [IR auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717).
- Se utilizar conectores de base de dados de ligação de objetos e incorporação (OLEDB) nas suas embalagens, descarregue e instale os controladores OLEDB relevantes na mesma máquina onde o seu IR auto-hospedado está instalado, se ainda não o fez.  

  Se utilizar a versão anterior do controlador OLEDB para o SQL Server (SQL Server Native Client [SQLNCLI]), [descarregue a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se utilizar a versão mais recente do controlador OLEDB para o SQL Server (MSOLEDBSQL), [descarregue a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se utilizar controladores OLEDB para outros sistemas de base de dados, tais como PostgreSQL, MySQL, Oracle, e assim por diante, pode descarregar as versões de 64 bits dos seus websites.
- Se ainda não o fez, [descarregue e instale a versão de 64 bits do tempo de funcionaamento Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na mesma máquina onde está instalado o seu IR auto-hospedado.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Prepare o serviço ligado ao armazenamento Azure Blob para a realização

Se ainda não o fez, crie um serviço ligado ao armazenamento Azure Blob na mesma fábrica de dados onde está configurado o seu Azure-SSIS IR. Para tal, consulte [Criar um serviço ligado à fábrica de dados Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Certifique-se de fazer o seguinte:
- Para **a Loja de Dados**, selecione **Azure Blob Storage**.  
- Para **ligar via tempo de integração**, selecione **AutoResolveIntegrationRuntime** (não o seu Azure-SSIS IR nem o seu IR auto-hospedado), porque utilizamos o Azure IR predefinido para obter credenciais de acesso para o seu Azure Blob Storage.
- Para **o método de autenticação**, selecione **Tecla de conta,** **SAS URI**ou **Principal de Serviço.**  

    >[!TIP]
    >Se selecionar o método Principal de **Serviço,** conceda ao seu titular de serviço pelo menos uma função *de Contribuinte de Dados blob de armazenamento.*   Para obter mais informações, consulte o [conector de armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

![Prepare o serviço ligado ao armazenamento Azure Blob para a realização](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configure um Azure-SSIS IR com o seu IR auto-hospedado como procuração

Depois de ter preparado o seu serviço de armazenamento ir e Azure Blob auto-hospedado para a encenação, pode agora configurar o seu novo ou existente Azure-SSIS IR com o IR auto-hospedado como representante no seu portal ou app de fábrica de dados. Antes de o fazer, porém, se o seu Azure-SSIS IR já estiver em funcionamento, pare-o e reinicie-o.

1. No painel de configuração de **tempo de integração,** passe pelas secções **De Definições Gerais** e **SQL** selecionando **a seguinte**. 

1. Na secção **Definições Avançadas,** faça o seguinte:

   1. Selecione o **Tempo de Execução de Integração Auto-hospedada como um proxy para a sua caixa de verificação de runtime de integração Azure-SSIS.** 

   1. Na lista de drop-down de **integração auto-hospedada,** selecione o seu IR auto-hospedado existente como um representante para o Azure-SSIS IR.

   1. Na lista de drop-down **do serviço ligado ao armazenamento de Staging,** selecione o seu serviço existente ligado ao armazenamento Azure Blob ou crie um novo para a encenação.

   1. Na caixa de **caminho de staging,** especifique um recipiente blob na sua conta de armazenamento Azure Blob selecionada ou deixe-a vazia para utilizar uma padrão para a encenação.

   1. Selecione **Continuar**.

   ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Também pode configurar o seu novo ou existente Azure-SSIS IR com o IR auto-hospedado como representante utilizando o PowerShell.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Permitir que os pacotes SSIS se conectem por procuração

Ao utilizar a mais recente extensão SSDT com SSIS Projects para Visual Studio ou um instalador autónomo, pode encontrar uma nova `ConnectByProxy` propriedade que foi adicionada nos gestores de conexão OLEDB ou Flat File.
* [Descarregue a extensão SSDT com projetos SSIS para Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Descarregue o instalador autónomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando conceber novos pacotes que contenham tarefas de fluxo de dados com fontes OLEDB ou Ficheiros Planos, que lhe permitem aceder a bases de dados ou ficheiros no local, pode capacitar esta propriedade definindo-a para *True* no painel **de propriedades** dos gestores de conexão relevantes.

![Ativar a propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Também pode ativar esta propriedade quando executar pacotes existentes, sem ter que alterá-los manualmente um por um.  Existem duas opções:
- **Opção A**: Abrir, reconstruir e recolocar o projeto que contém esses pacotes com o mais recente SSDT a funcionar no seu Azure-SSIS IR. Em seguida, pode ativar a propriedade definindo-a para *True* para os gestores de conexão relevantes. Quando estão a executar pacotes a partir de SSMS, estes gestores de ligação aparecem no **separador Connection Managers** da janela pop-up **do Pacote executar.**

  ![Ativar a propriedade ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Também pode ativar a propriedade definindo-a para *True* para os gestores de conexão relevantes que aparecem no **separador Gestores** de Ligação da [atividade do Pacote Executo SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando estão a executar pacotes em pipelines data factory.
  
  ![Ativar a propriedade ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opção B:** Reimplantar o projeto que contém esses pacotes para executar no seu SSIS IR. Em seguida, pode ativar a propriedade fornecendo o seu caminho de `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` propriedade, e defini-lo para *True* como uma sobreposição de propriedade no separador **Avançado** da janela pop-up **do Pacote Executar** quando estiver a executar pacotes a partir de SSMS.

  ![Ativar a propriedade ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Também pode ativar a propriedade fornecendo o seu caminho de `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` propriedade, e definindo-o para *True* como uma sobreposição de propriedade no separador **Propriedade Overrides** da atividade do [Pacote Executar SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando estiver executando pacotes em pipelines data Factory.
  
  ![Ativar a propriedade ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Depurar as primeira e segunda tarefas de encenação

No seu IR auto-hospedado, pode encontrar os registos de tempo de execução na pasta *C:\ProgramData\SSISTelemetry* e os registos de execução das primeiras tarefas de encenação na pasta *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Pode encontrar os registos de execução de segundas tarefas de preparação no seu SSISDB ou caminhos de registo especificados, dependendo se armazena as suas encomendas no SSISDB ou no sistema de ficheiros, nas partilhas de ficheiros ou nos Ficheiros Azure. Também pode encontrar os IDs únicos das primeiras tarefas de encenação nos registos de execução de segundas tarefas de encenação. 

![ID único da primeira tarefa de encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Utilize a autenticação do Windows em tarefas de preparação

Se as tarefas de preparação do seu IR auto-alojado exigirem a autenticação do Windows, [configuure os seus pacotes SSIS para utilizar a mesma autenticação do Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

As suas tarefas de encenação serão invocadas com a conta de serviço de IR auto-hospedada (*NT SERVICE\DIAHostService,* por padrão), e as suas lojas de dados serão acedidas com a conta de autenticação do Windows. Ambas as contas exigem que sejam atribuídas determinadas políticas de segurança. Na máquina de INFRAVERMELHOs auto-hospedada, vá à Atribuição de Direitos de Utilizador de Políticas Locais **de**  >  **Segurança**  >  **User Rights Assignment**Local, e, em seguida, faça o seguinte:

1. Atribua as *quotas de memória ajustar para um processo* e substituir as políticas de símbolo de *nível* de processo na conta de serviço de ir auto-hospedada. Isto deve ocorrer automaticamente quando instalar o seu IR auto-hospedado com a conta de serviço predefinida. Se não, atribua essas apólices manualmente. Se utilizar uma conta de serviço diferente, atribua-lhe as mesmas políticas.

1. Atribua o *Registo como uma* política de serviço à conta de Autenticação do Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faturação para a primeira e segunda tarefas de encenação

As primeiras tarefas de encenação que executam o seu IR auto-hospedado são faturadas separadamente, assim como quaisquer atividades de movimento de dados que funcionam em um IR auto-hospedado são faturadas. Isto é especificado no artigo de preços do pipeline de dados da [Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

As segundas tarefas de encenação que executam o seu Azure-SSIS IR não são faturadas separadamente, mas o seu Azure-SSIS IR em execução é faturado conforme especificado no artigo de preços do [Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Ativar o TLS 1.2

Se precisar de utilizar uma encriptação forte/protocolo de rede mais seguro (TLS 1.2) e desativar as versões SSL/TLS mais antigas no seu IR auto-hospedado, pode descarregar e executar o script *principal.cmd* que pode ser encontrado na pasta *CustomSetupScript/UserScenarios/TLS 1.2* do nosso recipiente de pré-visualização pública.  Utilizando [o Azure Storage Explorer,](https://storageexplorer.com/)pode ligar-se ao nosso recipiente de pré-visualização público, introduzindo o seguinte SAS URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitações atuais

- Apenas as tarefas de fluxo de dados com conectividade open database (ODBC)/OLEDB/Flat File sources ou o destino OLEDB são atualmente suportadas. 
- Apenas os serviços ligados ao armazenamento Azure Blob que estão configurados com *a chave conta,* *a Assinatura de Acesso Partilhado (SAS) URI*ou a autenticação *principal do serviço* são atualmente suportados.
- *O parâmetroMapping* em OLEDB Source ainda não é suportado. Como solução alternativa, utilize o *Comando SQL from Variable* como o *AccessMode* e use *a Expressão* para inserir as suas variáveis/parâmetros num comando SQL. Como ilustração, consulte o pacote *ParameterMappingSample.dtsx* que pode ser encontrado na pasta *SelfHostedIRProxy/Limitations* do nosso recipiente de pré-visualização pública. Utilizando o Azure Storage Explorer, pode ligar-se ao nosso recipiente de pré-visualização público introduzindo o SAS URI acima.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu IR auto-hospedado como um representante para o seu Azure-SSIS IR, pode implementar e executar os seus pacotes para aceder aos dados no local como executar atividades do Pacote SSIS em pipelines data Factory. Para saber como, consulte [os pacotes Run SSIS como executar atividades de pacote SSIS em pipelines data factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
