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
ms.date: 11/19/2020
ms.openlocfilehash: a79055a77ec73ce2b267bb4f16fa91f37e22ea75
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916785"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configure um IR auto-hospedado como representante de um Azure-SSIS IR na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar pacotes SQL Server Integration Services (SSIS) num Runtime de Integração Azure-SSIS (Azure-SSIS IR) na Azure Data Factory com um tempo de integração auto-hospedado (IR auto-hospedado) configurado como um proxy. 

Com esta funcionalidade, pode aceder aos dados no local sem ter de se juntar ao [seu Azure-SSIS IR a uma rede virtual.](./join-azure-ssis-integration-runtime-virtual-network.md) A funcionalidade é útil quando a sua rede corporativa tem uma configuração demasiado complexa ou uma política demasiado restritiva para que possa injetar o seu Azure-SSIS IR na sua.

Esta funcionalidade decompõe a sua tarefa de fluxo de dados SSIS em duas tarefas de preparação sempre que aplicável: 
* **Tarefa de preparação no local**: Esta tarefa executa o seu componente de fluxo de dados que se conecta a uma loja de dados no local no seu IR auto-hospedado. Move dados da loja de dados no local para uma área de preparação no seu armazenamento Azure Blob ou vice-versa.
* **Tarefa de preparação** em nuvem : Esta tarefa executa o seu componente de fluxo de dados que não se conecta a uma loja de dados no local no seu Azure-SSIS IR. Move dados da área de preparação do seu armazenamento Azure Blob para uma loja de dados em nuvem ou vice-versa.

Se a sua tarefa de fluxo de dados mover dados de instalações para nuvem, então as tarefas de primeira e segunda encenação serão tarefas de preparação no local e de paragem em nuvem, respectivamente. Se a sua tarefa de fluxo de dados mover dados da nuvem para as instalações, então as tarefas de primeira e segunda encenação serão tarefas de fase de cloud e no local, respectivamente. Se a sua tarefa de fluxo de dados mover dados de instalações para as instalações, então as tarefas de primeira e segunda encenação serão tarefas de preparação no local. Se a sua tarefa de fluxo de dados mover dados de nuvem para nuvem, então esta funcionalidade não é aplicável.

Outros benefícios e capacidades desta funcionalidade permitem-lhe, por exemplo, configurar o seu IR auto-hospedado em regiões que ainda não são suportadas por um IR Azure-SSIS, e permitir o endereço IP estático público do seu IR auto-hospedado na firewall das suas fontes de dados.

## <a name="prepare-the-self-hosted-ir"></a>Prepare o IR auto-hospedado

Para utilizar esta funcionalidade, cria primeiro uma fábrica de dados e cria-se um IR Azure-SSIS na sua área. Se ainda não o fez, siga as instruções em [Configurar um Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

Em seguida, instalou o seu IR auto-hospedado na mesma fábrica de dados onde está configurado o seu Azure-SSIS IR. Para tal, consulte [Criar um IR auto-hospedado.](./create-self-hosted-integration-runtime.md)

Por fim, descarrega e instala a versão mais recente do IR auto-hospedado, bem como os controladores e tempo de execução adicionais, na sua máquina de bordo ou na máquina virtual Azure (VM), da seguinte forma:
- Descarregue e instale a versão mais recente do [IR auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717).
- Se utilizar a Base de Dados de Ligação de Objetos e Incorporação (OLEDB), a Conectividade da Base de Dados Aberta (ODBC) ou ADO.NET conectores nas suas embalagens, descarregue e instale os controladores relevantes na mesma máquina onde o seu IR auto-hospedado está instalado, se ainda não o fez.  

  Se utilizar a versão anterior do controlador OLEDB para o SQL Server (SQL Server Native Client [SQLNCLI]), [descarregue a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se utilizar a versão mais recente do controlador OLEDB para o SQL Server (MSOLEDBSQL), [descarregue a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se utilizar controladores OLEDB/ODBC/ADO.NET para outros sistemas de base de dados, tais como PostgreSQL, MySQL, Oracle, e assim por diante, pode descarregar as versões de 64 bits dos seus websites.
- Se ainda não o fez, [descarregue e instale a versão de 64 bits do tempo de funcionaamento Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na mesma máquina onde está instalado o seu IR auto-hospedado.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Ativar a autenticação do Windows para tarefas de preparação no local

Se as tarefas de preparação no local do seu IR auto-hospedado exigirem a autenticação do Windows, [configuure os seus pacotes SSIS para utilizar a mesma autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

As suas tarefas de preparação no local serão invocadas com a conta de serviço de IR auto-hospedada *(NT SERVICE\DIAHostService,* por padrão), e as suas lojas de dados serão acedidas com a conta de autenticação do Windows. Ambas as contas exigem que sejam atribuídas determinadas políticas de segurança. Na máquina de INFRAVERMELHOs auto-hospedada, vá à Atribuição de Direitos de Utilizador de Políticas Locais **de**  >  **Segurança**  >  **User Rights Assignment** Local, e, em seguida, faça o seguinte:

1. Atribua as *quotas de memória ajustar para um processo* e substituir as políticas de símbolo de *nível* de processo na conta de serviço de ir auto-hospedada. Isto deve ocorrer automaticamente quando instalar o seu IR auto-hospedado com a conta de serviço predefinida. Se não, atribua essas apólices manualmente. Se utilizar uma conta de serviço diferente, atribua-lhe as mesmas políticas.

1. Atribua o *Registo como uma* política de serviço à conta de Autenticação do Windows.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Prepare o serviço ligado ao armazenamento Azure Blob para a realização

Se ainda não o fez, crie um serviço de armazenamento Azure Blob na mesma fábrica de dados onde está configurado o seu Azure-SSIS IR. Para tal, consulte [Criar um serviço ligado à fábrica de dados Azure](./quickstart-create-data-factory-portal.md#create-a-linked-service). Certifique-se de fazer o seguinte:
- Para **a Loja de Dados**, selecione **Azure Blob Storage**.  
- Para **ligar via tempo de integração**, selecione **AutoResolveIntegrationRuntime** (não o seu Azure-SSIS IR nem o seu IR auto-hospedado), porque utilizamos o Azure IR predefinido para obter credenciais de acesso para o seu Azure Blob Storage.
- Para **método de autenticação**, selecione **Tecla de conta,** **SAS URI,** **Principal de Serviço** ou Identidade **Gerida.**  

>[!TIP]
>Se selecionar o método Principal de **Serviço,** conceda ao seu titular de serviço pelo menos uma função *de Contribuinte de Dados blob de armazenamento.* Para mais informações, consulte [o conector de armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties). Se selecionar o método **identidade gerida,** conceda à sua identidade gerida pela ADF funções adequadas para aceder ao Azure Blob Storage. Para obter mais informações, consulte [o Access Azure Blob Storage utilizando a autenticação do Azure Ative Directory com identidade gerida pela ADF.](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication)

![Prepare o serviço ligado ao armazenamento Azure Blob para a realização](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configure um Azure-SSIS IR com o seu IR auto-hospedado como procuração

Depois de ter preparado o seu serviço de armazenamento ir e Azure Blob auto-hospedado para a encenação, pode agora configurar o seu novo ou existente Azure-SSIS IR com o IR auto-hospedado como representante no seu portal ou app de fábrica de dados. Antes de o fazer, porém, se o seu Azure-SSIS IR já estiver em funcionamento, pare-o e reinicie-o.

1. No painel de configuração de **tempo de integração,** passe pelas secções **De Definições Gerais** e **SQL** selecionando **a seguinte**. 

1. Na secção **Definições Avançadas,** faça o seguinte:

   1. Selecione o **Prazo de execução de integração Self-Hosted configuração como um proxy para a sua caixa de verificação de tempo de execução de integração Azure-SSIS.** 

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

Ao utilizar o SSDT mais recente como extensão de Projetos SSIS para Estúdio Visual ou um instalador autónomo, pode encontrar uma nova `ConnectByProxy` propriedade que foi adicionada nos gestores de ligação para componentes de fluxo de dados suportados.
* [Descarregue a extensão dos Projetos SSIS para o Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Descarregue o instalador autónomo](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando concebe novos pacotes que contenham tarefas de fluxo de dados com componentes que acedam aos dados nas instalações, pode ativar esta propriedade definindo-a para *True* no painel **de Propriedades** dos gestores de conexão relevantes.

![Ativar a propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Também pode ativar esta propriedade quando executar pacotes existentes, sem ter que alterá-los manualmente um por um.  Existem duas opções:
- **Opção A**: Abrir, reconstruir e recolocar o projeto que contém esses pacotes com o mais recente SSDT a funcionar no seu Azure-SSIS IR. Em seguida, pode ativar a propriedade definindo-a para *True* para os gestores de conexão relevantes. Quando está a executar pacotes a partir de SSMS, estes gestores de ligação aparecem no **separador Gestores** de Ligação da janela pop-up **do Pacote Executar.**

  ![Ativar a propriedade ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Também pode ativar a propriedade definindo-a para *True* para os gestores de conexão relevantes que aparecem no **separador Gestores** de Ligação da [atividade do Pacote Executo SSIS](./how-to-invoke-ssis-package-ssis-activity.md) quando estiver a executar pacotes em pipelines data factory.
  
  ![Ativar a propriedade ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opção B:** Reimplantar o projeto que contém esses pacotes para executar no seu SSIS IR. Em seguida, pode ativar a propriedade fornecendo o seu caminho de `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` propriedade, e defini-lo para *True* como uma sobreposição de propriedade no separador **Avançado** da janela pop-up **do Pacote Executar** quando estiver a executar pacotes a partir de SSMS.

  ![Ativar a propriedade ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Também pode ativar a propriedade fornecendo o seu caminho de `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` propriedade, e definindo-o para *True* como uma sobreposição de propriedade no separador **Propriedade Overrides** da atividade do [Pacote Executar SSIS](./how-to-invoke-ssis-package-ssis-activity.md) quando estiver executando pacotes em pipelines data Factory.
  
  ![Ativar a propriedade ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Depurar as tarefas de preparação de nuvens e em nuvem

No seu IR auto-hospedado, pode encontrar os registos de tempo de execução na pasta *C:\ProgramData\SSISTelemetry* e os registos de execução de tarefas de encenação no local na pasta *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Pode encontrar os registos de execução de tarefas de encenação em nuvem no seu SSISDB, caminhos de ficheiros de registo especificados ou Monitor Azure, dependendo se armazena os seus pacotes no SSISDB, possibilite [a integração do Azure Monitor,](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#monitor-ssis-operations-with-azure-monitor)etc. Também pode encontrar os IDs exclusivos de tarefas de encenação no local nos registos de execução de tarefas de encenação em nuvem. 

![ID único da primeira tarefa de encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Se tiver levantado bilhetes de apoio ao cliente, pode selecionar o botão **Enviar registos** no separador De **diagnóstico** do Gestor de Configuração de Tempo de Execução de **Integração** da Microsoft que está instalado no seu IR auto-hospedado para enviar registos de operação/execução recentes para que possamos investigar.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Faturação para as tarefas de preparação no local e cloud staging

As tarefas de preparação no local que funcionam no seu IR auto-hospedado são faturadas separadamente, assim como quaisquer atividades de movimento de dados que funcionam em um IR auto-hospedado são faturadas. Isto é especificado no artigo de preços do pipeline de dados da [Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

As tarefas de preparação em nuvem que funcionam no seu Azure-SSIS IR não são faturadas separadamente, mas o seu Azure-SSIS IR em execução é faturado conforme especificado no artigo de preços do [Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enable-custom3rd-party-components"></a>Ativar componentes personalizados/3º partidos 

Para permitir que os seus componentes personalizados/3º partidos acedam a dados nas instalações utilizando o IR auto-hospedado como representante da Azure-SSIS IR, siga estas instruções:

1. Instale os seus componentes personalizados/3º partidos direcionados para o SQL Server 2017 no Azure-SSIS IR através [de configurações personalizadas standard/express](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

1. Crie as seguintes teclas de registo DTSPath em IR auto-hospedado se já não existirem:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` definido para `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` definido para `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Instale os seus componentes personalizados/3º partidos direcionados para o SQL Server 2017 no IR auto-hospedado sob o DTSPath acima e certifique-se de que o seu processo de instalação:

   1. Cria, `<DTSPath>` `<DTSPath>/Connections` e `<DTSPath>/PipelineComponents` `<DTSPath>/UpgradeMappings` pastas se já não existirem.
   
   1. Cria o seu próprio ficheiro XML para mapeamentos de extensão na `<DTSPath>/UpgradeMappings` pasta.
   
   1. Instala todos os conjuntos referenciados pelos conjuntos de componentes personalizados/3º partidos na cache de montagem global (GAC).

Aqui estão exemplos dos nossos parceiros, [Theobald Software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) e [Aecorsoft,](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir)que adaptaram os seus componentes para usar a nossa configuração personalizada expressa e o IR auto-hospedado como proxy para a Azure-SSIS IR.

## <a name="enforce-tls-12"></a>Impor TLS 1.2

Se precisar de utilizar uma encriptação forte/protocolo de rede mais seguro (TLS 1.2) e desativar as versões SSL/TLS mais antigas no seu IR auto-hospedado, pode descarregar e executar o script *principal.cmd* que pode ser encontrado na pasta *CustomSetupScript/UserScenarios/TLS 1.2* do nosso recipiente de pré-visualização pública.  Utilizando [o Azure Storage Explorer,](https://storageexplorer.com/)pode ligar-se ao nosso recipiente de pré-visualização público, introduzindo o seguinte SAS URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitações atuais

- Apenas os componentes de fluxo de dados que são incorporados/pré-instalados na Azure-SSIS IR Standard Edition, com exceção dos componentes Hadoop/HDFS/DQS, são atualmente suportados, ver [todos os componentes incorporados/pré-instalados no Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).
- Apenas componentes de fluxo de dados personalizados/3º partidos que estão escritos em código gerido (.NET Framework) são atualmente suportados - Os que estão escritos em código nativo (C++) não são atualmente suportados.
- A alteração dos valores variáveis tanto nas tarefas de preparação no local como na nuvem não é atualmente suportada.
- A alteração dos valores variáveis do objeto tipo em tarefas de encenação no local não será refletida noutras tarefas.
- *O modo de aplicação de parâmetros* na Fonte OLEDB não é atualmente suportado. Como solução alternativa, utilize o *Comando SQL from Variable* como o *AccessMode* e use *a Expressão* para inserir as suas variáveis/parâmetros num comando SQL. Como ilustração, consulte o pacote *ParameterMappingSample.dtsx* que pode ser encontrado na pasta *SelfHostedIRProxy/Limitations* do nosso recipiente de pré-visualização pública. Utilizando o Azure Storage Explorer, pode ligar-se ao nosso recipiente de pré-visualização público introduzindo o SAS URI acima.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu IR auto-hospedado como um representante para o seu Azure-SSIS IR, pode implementar e executar os seus pacotes para aceder aos dados no local como executar atividades do Pacote SSIS em pipelines data Factory. Para saber como, consulte [os pacotes Run SSIS como executar atividades de pacote SSIS em pipelines data factory](./how-to-invoke-ssis-package-ssis-activity.md).
