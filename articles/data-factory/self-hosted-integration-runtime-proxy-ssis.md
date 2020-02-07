---
title: Configurar o tempo de execução de integração auto-hospedado como um proxy para SSIS
description: Saiba como configurar o Integration Runtime auto-hospedado como um proxy para Azure-SSIS Integration Runtime.
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
ms.date: 02/06/2020
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048958"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Configurar o IR auto-hospedado como um proxy para Azure-SSIS IR no ADF

Este artigo descreve como executar pacotes do SQL Server Integration Services (SSIS) em Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF) com o IR auto-hospedado configurado como um proxy.  Esta funcionalidade permite-lhe aceder a dados nas instalações sem [aderir ao seu IR Azure-SSIS a uma rede virtual.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)  É útil quando sua rede corporativa tem uma política de configuração/restritiva muito complexa para você injetar sua Azure-SSIS IR.

Esse recurso dividirá o pacote que contém uma tarefa de fluxo de dados com a fonte de dados local em duas tarefas de preparo: a primeira em execução no seu IR hospedado primeiro moverá os dados da fonte de dados local para uma área de preparação no armazenamento de BLOBs do Azure, enquanto o segundo em execução na sua Azure-SSIS IR moverá os dados da área de preparo para o destino de dados pretendido.

Esse recurso também fornece outros benefícios/recursos, pois permite que você provisione seu IR de hospedagem interna em regiões que ainda não têm suporte pelo Azure-SSIS IR, permitem o endereço IP estático público do seu IR hospedado no firewall de suas fontes de dados, etc.

## <a name="prepare-self-hosted-ir"></a>Preparar IR auto-hospedado

Para utilizar esta funcionalidade, terá primeiro de criar um ADF e fornecer o seu IR Azure-SSIS ao seu abrigo, caso ainda não o tenha feito, seguindo o How to provisionado um artigo do [IR Azure-SSIS.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

Em seguida, terá de fornecer o seu IR auto-hospedado sob a mesma ADF onde o seu IR Azure-SSIS é provisionado seguindo o Como criar um artigo de [IR auto-hospedado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Por fim, você precisará baixar e instalar a versão mais recente do IR auto-hospedado, bem como os drivers adicionais e o tempo de execução, em seu computador local/VM (máquina virtual) do Azure da seguinte maneira:
- Descarregue e instale a versão mais recente do IR auto-hospedado a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=39717)
- Se você usar conectores OLEDB em seus pacotes, baixe e instale os drivers OLEDB relevantes no mesmo computador em que o IR auto-hospedado é instalado, caso ainda não tenha feito isso.  Se utilizar a versão anterior do controlador OLEDB para o SQL Server (SQLNCLI), pode descarregar a versão de 64 bits [daqui](https://www.microsoft.com/download/details.aspx?id=50402).  Se utilizar a versão mais recente do controlador OLEDB para o SQL Server (MSOLEDBSQL), pode descarregar a versão de 64 bits a partir [daqui](https://www.microsoft.com/download/details.aspx?id=56730).  Se você usar drivers OLEDB para outros sistemas de banco de dados, como PostgreSQL, MySQL, Oracle, etc., você poderá baixar a versão de 64 bits de seus respectivos sites.
- Baixe e instale o C++ tempo de execução do Visual (VC) no mesmo computador em que o ir auto-hospedado está instalado, se você ainda não tiver feito isso.  Pode baixar a versão de 64 bits a partir [daqui](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo

Crie um serviço ligado ao Armazenamento Azure Blob sob a mesma ADF onde o seu IR Azure-SSIS é provisionado, se ainda não o fez, seguindo o Como criar um artigo de [serviço ligado à ADF.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)  Verifique o seguinte:
- **Armazenamento Azure Blob** é selecionado para **Data Store**
- **AutoResolveIntegrationRuntime** é selecionado para Connect através do tempo de **execução de integração**
- Chave **de conta**/**SAS URI**/Diretor de **Serviço** é selecionado para método **de autenticação**

>[!TIP]
>Quando for selecionado o **diretor de serviço** , conceda pelo menos a função de Colaborador de Dados **blob de armazenamento**. Para mais informações, consulte o [conector de armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

![Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Configurar Azure-SSIS IR com o IR auto-hospedado como um proxy

Tendo preparado seu serviço vinculado de IR e armazenamento de BLOBs do Azure autohospedado para preparo, agora você pode configurar seu Azure-SSIS IR novo/existente com o IR hospedado internamente como um proxy no portal/aplicativo do ADF.  Se o Azure-SSIS IR existente estiver em execução, interrompa-o antes de fazer isso e, em seguida, reinicie-o depois.

1. No painel de configuração do tempo de integração, avance através das **definições gerais** e das **definições SQL** selecionando o botão **Seguinte.** 

1. Na secção **Definições Avançadas:**

   1. Selecione o tempo de execução de integração auto-hospedado como procuração para a sua caixa de verificação de tempo de **integração Azure-SSIS.** 

   1. Para o tempo de execução de **integração auto-hospedado,** selecione o seu IR auto-hospedado existente como procuração para o Ir Azure-SSIS.

   1. Para o Serviço Ligado ao Armazenamento de **Montagem,** selecione o serviço ligado ao armazenamento Azure Blob existente ou crie um novo para encenação.

   1. Para **o Caminho de Encenação,** especifique um recipiente de bolha na sua conta de armazenamento Azure Blob selecionada ou deixe-o vazio para utilizar um predefinido para a encenação.

   1. Selecione **Continuar**.

   ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Também pode configurar o seu novo/existente IR Azure-SSIS com IR auto-hospedado como um proxy usando powerShell.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Habilitar pacotes SSIS para se conectar por proxy

Utilizando a mais recente extensão sSDT com Projetos SSIS para Estúdio Visual que pode ser descarregado [daqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como um instalador autónomo que pode ser descarregado a partir [daqui,](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)você pode encontrar uma nova propriedade **ConnectByProxy** que foi adicionada em OLEDB/Flat File Connection Managers.  

Ao conceber novos pacotes que contenham Tarefas de Fluxo de Dados com Fontes de FicheiroOLEDB/Flat para aceder a bases de dados/ficheiros nas instalações, pode ativar esta propriedade definindo-a como **True** no painel Propriedades dos gestores de ligação relevantes.

![Habilitar Propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Você também pode habilitar essa propriedade ao executar pacotes existentes sem precisar alterá-los manualmente um por um.  Existem duas opções:
- Abertura, reconstrução e reimplantação do projeto que contém esses pacotes com o mais recente SSDT a executar no seu IR Azure-SSIS: A propriedade pode então ser ativada definindo-a como **True** para os gestores de conexão relevantes que aparecem no separador Gestores de **Ligação** do Pacote executar ao executar pacotes a partir de SSMS.

  ![Habilitar ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A propriedade também pode ser ativada definindo-a para **True** para os gestores de conexão relevantes que aparecem no separador **Gestores** de Ligação da [atividade do Pacote Executar SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) ao executar pacotes em pipelines ADF.
  
  ![Habilitar ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Reimplantando o projeto contendo esses pacotes para executar no seu SSIS IR: A propriedade pode então ser ativada fornecendo o seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, e definindo-o **como** uma propriedade sobreposição no separador **Avançado** de Executar Pacote pop-up janela ao executar pacotes de SSMS.

  ![Habilitar ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A propriedade também pode ser ativada fornecendo o seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, e definindo-o **como** uma propriedade sobreposição no separador **Desobreações de Propriedade** da atividade do Pacote Execute [SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) ao executar pacotes em pipelines ADF.
  
  ![Habilitar ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Depurar a primeira e a segunda tarefas de preparo

No seu IV auto-hospedado, pode encontrar os registos de tempo de execução na pasta `C:\ProgramData\SSISTelemetry` e os registos de execução das primeiras tarefas de encenação na pasta `C:\ProgramData\SSISTelemetry\ExecutionLog`.  Os logs de execução de segundo tarefas de preparo podem ser encontrados nos caminhos do SSISDB ou do log especificado, dependendo de você armazenar seus pacotes no SSISDB ou no sistema de arquivos/compartilhamentos de arquivos/arquivos do Azure, respectivamente.  As IDs exclusivas das primeiras tarefas de preparo também podem ser encontradas nos logs de execução de segundo tarefas de preparo, por exemplo, 

![ID exclusiva da primeira tarefa de preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Usando a autenticação do Windows em tarefas de preparo

Se as suas tarefas de encenação no IR auto-hospedado necessitarem de autenticação do Windows, é necessário [configurar os seus pacotes SSIS para utilizar a mesma autenticação do Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). As suas tarefas de encenação serão invocadas com a conta de serviço IR Auto-Hospedada (`NT SERVICE\DIAHostService` por defeito) e as suas lojas de dados serão acedidas com a conta de autenticação do Windows. Ambas as contas exigem que determinadas políticas de segurança sejam atribuídas a elas. Consequentemente, na máquina de infravermelhos auto-hospedada, abra `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` e complete os seguintes passos.
- Atribuir as quotas de **memória Ajustar para um processo** e substituir uma política de ficha de **nível de processo** na conta de serviço ir auto-hospedada. Isso deve ser feito automaticamente ao instalar o IR auto-hospedado com a conta de serviço padrão. Se você usar uma conta de serviço diferente, atribua as mesmas políticas a ela.
- Atribuir o **Registo como uma** política de serviço à conta de autenticação do Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Cobrança da primeira e segunda tarefa de preparo

As primeiras tarefas de encenação em execução do seu IR auto-hospedado serão faturadas separadamente da mesma forma que quaisquer atividades de movimento de dados em execução em IR auto-hospedado são faturadas como especificado no artigo de preços do pipeline de [dados DaDF.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

As segundas tarefas de encenação em execução do seu IR Azure-SSIS não serão faturadas separadamente, mas o seu ir Azure-SSIS será faturado conforme especificado no artigo de [preços do Ir Azure-SSIS.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="current-limitations"></a>Limitações atuais

- Apenas são suportadas as tarefas de fluxo de dados com os gestores de ligação de ficheiros ODBC/OLEDB/Flat e ODBC/Flat File ou o Destino OLEDB. 
- Apenas os serviços ligados ao armazenamento Azure Blob configurados com **a chave de conta**/a autenticação principal do **serviço** **SAS URI**/são atualmente suportados.

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o seu IR auto-hospedado como procuração para o seu IR Azure-SSIS, pode implementar e executar os seus pacotes para aceder a dados nas instalações como executar atividades do Pacote SSIS em pipelines ADF, consulte [os pacotes Run SSIS como executar as atividades do Pacote SSIS em pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
