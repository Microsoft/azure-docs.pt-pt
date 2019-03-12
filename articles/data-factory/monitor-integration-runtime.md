---
title: Monitorizar o runtime de integração no Azure Data Factory | Documentos da Microsoft
description: Saiba como monitorizar tipos diferentes de runtime de integração no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: b62cbe75730da8c5764839d41887deb7e6cd0e90
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576341"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorizar um runtime de integração no Azure Data Factory  
**Runtime de integração** é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer várias capacidades de integração de dados em diferentes ambientes de rede. Existem três tipos de runtimes de integração oferecidos pelo Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Runtime de integração de SSIS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o estado de uma instância do integration runtime (IR), execute o seguinte comando do PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet retorna informações diferentes para diferentes tipos de runtime de integração. Este artigo explica as propriedades e os Estados para cada tipo de runtime de integração.  

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
O recurso de computação para um runtime de integração do Azure totalmente gerido elástica no Azure. A tabela seguinte fornece descrições das propriedades retornadas pela **Get-AzDataFactoryV2IntegrationRuntime** comando:

### <a name="properties"></a>Propriedades
A tabela seguinte fornece descrições das propriedades devolvidas pelo cmdlet para um runtime de integração do Azure:

| Propriedade | Descrição |
-------- | ------------- | 
| Name | Nome do runtime de integração do Azure. |  
| Estado | Estado do runtime de integração do Azure. | 
| Localização | Localização do runtime de integração do Azure. Para obter detalhes sobre a localização de um runtime de integração do Azure, consulte [introdução ao runtime de integração](concepts-integration-runtime.md). |
| DataFactoryName | Nome da fábrica de dados que pertence o runtime de integração do Azure. | 
| ResourceGroupName | Nome do grupo de recursos que a fábrica de dados pertence.  |
| Descrição | Descrição do integration runtime.  |

### <a name="status"></a>Estado
A tabela seguinte fornece os Estados possíveis de um runtime de integração do Azure:

| Estado | Comentários/cenários | 
| ------ | ------------------ |
| Online | O runtime de integração do Azure está online e pronta para ser utilizada. | 
| Offline | O runtime de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Esta secção fornece descrições para propriedades devolvidas pelo cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> O estado e propriedades devolvidas contêm informações sobre o runtime de integração autoalojado geral e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

A tabela seguinte fornece descrições das propriedades de monitorização **cada nó**:

| Propriedade | Descrição | 
| -------- | ----------- | 
| Name | Nome do integration runtime autoalojado e nós associados a ele. Nó é uma máquina com Windows no local que tenha o runtime de integração autoalojado instalado no mesmo. |  
| Estado | O estado de cada nó e o runtime de integração autoalojado geral. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte a secção seguinte. | 
| Versão | A versão do integration runtime autoalojado e cada nó. A versão do runtime de integração autoalojado é determinada com base na versão da maioria de nós no grupo. Se existirem nós com versões diferentes na configuração do runtime de integração autoalojado, apenas os nós com o mesmo número de versão e a lógica autoalojado função do runtime de integração corretamente. Outras pessoas estão no modo de limitada e têm de ser atualizados manualmente (apenas no caso de falha de atualização automática). | 
| Memória disponível | Memória disponível num nó do runtime de integração autoalojado. Este valor é um instantâneo de quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó do runtime de integração autoalojado. Este valor é um instantâneo de quase em tempo real. |
| Funcionamento em rede (entrada/saída) | Utilização da rede de um nó do runtime de integração autoalojado. Este valor é um instantâneo de quase em tempo real. | 
| Tarefas simultâneas (em execução / limite) | **Executar**. Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo de quase em tempo real. <br/><br/>**Limite**. Limite significa o máximo de tarefas simultâneas para cada nó. Este valor é definido com base no tamanho de máquina. Pode aumentar o limite para a ampliação de execução da tarefa em simultâneo em cenários avançados, quando as atividades são exceder o tempo limite, mesmo quando da CPU, memória ou a rede está subutilizados. Esta capacidade também está disponível com um runtime de integração autoalojado de nó único. |
| Função | Existem dois tipos de funções num runtime de integração autoalojado de vários nós – dispatcher e de trabalho. Todos os nós são funções de trabalho, o que significa que eles podem todos ser usados para executar tarefas. Há apenas um nó de dispatcher, que é utilizado para extrair tarefas/tarefas dos serviços cloud e expedi-los para nós de trabalho diferentes. O nó de dispatcher é também um nó de trabalho. |

Algumas definições das propriedades fazem mais sentido quando existirem dois ou mais nós no runtime de integração autoalojado (ou seja, num cenário de dimensionamento).

#### <a name="concurrent-jobs-limit"></a>Limite de tarefas simultâneas

O valor predefinido de tarefas simultâneas, limite está definido com base no tamanho de máquina. Os fatores utilizados para calcular esse valor dependem da quantidade de RAM e o número de núcleos de CPU da máquina. Por isso, o maior número de núcleos e mais memória, mais alto a predefinição limite de tarefas simultâneas.

Aumentar horizontalmente, aumentando o número de nós. Quando aumenta o número de nós, o limite de tarefas simultâneas é a soma dos valores de limite da tarefa em simultâneo de todos os nós disponíveis.  Por exemplo, se um nó permite-lhe executar um máximo de tarefas simultâneas doze, em seguida, adicionar três nós mais semelhantes permite-lhe executar um máximo de tarefas simultâneas 48 (ou seja, 4 x 12). Recomendamos que aumente o limite de tarefas simultâneas apenas quando o vir baixa utilização de recursos com os valores predefinidos em cada nó.

É possível substituir o valor padrão calculado no portal do Azure. Selecione criar > ligações > Runtimes de integração > Editar > nós > modificar o valor de tarefa em simultâneo por nó. Também pode utilizar o PowerShell [Azdatafactoryv2integrationruntimenode atualização](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) comando.
  
### <a name="status-per-node"></a>Estado (por nó)
A tabela seguinte fornece os Estados possíveis de um nó do runtime de integração autoalojado:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | Nó for ligado ao serviço Data Factory. |
| Offline | O nó está offline. |
| A atualizar | O nó está a ser atualizados automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido a problema de 8050 porta HTTP, problema de conectividade de barramento de serviço ou um problema de sincronização de credenciais. |
| Inativa | O nó está numa configuração diferente da configuração dos outros nós da maioria. |

Um nó pode ficar inativo, quando ele não é possível ligar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Estado (runtime de integração autoalojado geral)
A tabela seguinte fornece os Estados possíveis de um runtime de integração autoalojado. Este estado depende de Estados de todos os nós que pertencem ao tempo de execução. 

| Estado | Descrição |
| ------ | ----------- | 
| Tem de registo | Nenhum nó ainda está registada para este integration runtime autoalojado. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós neste Runtime de integração autoalojado estão em bom estado de funcionamento. Este estado é um aviso de que alguns nós podem estar inativa. Este estado pode ser devido a um problema de sincronização de credenciais no nó de distribuidor/trabalhador. |

Utilize o **Get-AzDataFactoryV2IntegrationRuntimeMetric** propriedades de runtime de integração de autoalojado de cmdlet para obter o payload JSON que contém o detalhadas e seus instantâneo valores durante o tempo de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Saída de exemplo (parte do princípio de que existem dois nós associados a este runtime de integração autoalojado):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Runtime de integração de SSIS do Azure
Runtime de integração Azure-SSIS é um cluster totalmente gerido do Azure máquinas virtuais (ou nós) dedicadas à execução dos pacotes do SSIS. Não é executado qualquer outras atividades do Azure Data Factory. Depois de aprovisionada, pode consultar as respetivas propriedades e monitorizar seus Estados geral/específicas de nós.

### <a name="properties"></a>Propriedades

| Propriedade/Estado | Descrição |
| --------------- | ----------- |
| CreateTime | A hora UTC quando foi criado o runtime de integração Azure-SSIS. |
| Nós | Os nós alocados/disponíveis do Azure-SSIS integration runtime com Estados de nó específico (Iniciar/disponíveis/Reciclagem/indisponível) e erros acionáveis. |
| OtherErrors | Os erros acionáveis não nó específico no seu Azure-SSIS integration runtime. |
| LastOperation | O resultado da última operação de iniciar/parar no seu Azure-SSIS integration runtime com erros acionáveis caso de falha. |
| Estado | O estado geral (inicial/iniciar/iniciada/parar/interrompida) do Azure-SSIS integration runtime. |
| Localização | A localização do Azure-SSIS integration runtime. |
| NodeSize | O tamanho de cada nó do integration runtime Azure-SSIS. |
| NodeCount | O número de nós no seu runtime de integração Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó no seu runtime de integração Azure-SSIS. |
| CatalogServerEndpoint | O ponto final do seu servidor de instância de base de dados/gerida de SQL do Azure existente para alojar o SSISDB. |
| CatalogAdminUserName | O nome de utilizador de administrador do seu servidor existente do Azure SQL da base de dados/instância gerida. Serviço do Data Factory utiliza estas informações para preparar e gerir o SSISDB em seu nome. |
| CatalogAdminPassword | A palavra-passe do administrador do seu servidor existente do Azure SQL da base de dados/instância gerida. |
| CatalogPricingTier | O escalão de preço de SSISDB alojado pelo seu servidor de base de dados do Azure SQL existente.  Não é aplicável ao Azure SQL Database Managed Instance alojar SSISDB. |
| VNetId | O rede virtual ID de recurso para o runtime de integração Azure-SSIS para associar. |
| Subrede | O nome da sub-rede para o runtime de integração Azure-SSIS associar. |
| ID | O ID de recurso do Azure-SSIS integration runtime. |
| Type | O tipo (gerida/Self-Hosted) do Azure-SSIS integration runtime. |
| ResourceGroupName | O nome do seu grupo de recursos do Azure, em que foram criados sua fábrica de dados e o runtime de integração Azure-SSIS. |
| DataFactoryName | O nome da sua fábrica de dados do Azure. |
| Name | O nome do Azure-SSIS integration runtime. |
| Descrição | A descrição do Azure-SSIS integration runtime. |

  
### <a name="status-per-node"></a>Estado (por nó)

| Estado | Descrição |
| ------ | ----------- | 
| A iniciar | Este nó está a ser preparado. |
| Disponível | Este nó está pronto para implementar/executar pacotes SSIS. |
| Reciclagem | Este nó está a ser reparado/reiniciar. |
| Indisponível | Este nó não está pronto para implementar/executar pacotes SSIS e tem acionáveis erros/problemas que pode resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Estado (runtime de integração Azure-SSIS geral)

| Estado geral | Descrição | 
| -------------- | ----------- | 
| Inicial | Os nós do Azure-SSIS integration runtime não foram alocados/preparado. | 
| A iniciar | Os nós do Azure-SSIS integration runtime estão a ser atribuída/preparado e faturação foi iniciado. |
| Iniciado | Os nós do Azure-SSIS integration runtime foram alocados/preparado e eles estão prontos para que possa implementar/executar pacotes do SSIS. |
| A parar  | Os nós do Azure-SSIS integration runtime estão a ser lançados. |
| Parada | Os nós do Azure-SSIS integration runtime foram lançados e faturação parou. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorizar o runtime de integração de SSIS do Azure no portal do Azure

As capturas de ecrã seguintes mostram como selecionar o IR Azure-SSIS para monitorizar e fornecer um exemplo das informações que são apresentados.

![Selecione o runtime de integração de SSIS do Azure para monitorizar](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Ver informações sobre o runtime de integração Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorizar o runtime de integração Azure-SSIS com o PowerShell

Utilizar um script semelhante ao seguinte exemplo para verificar o estado do ir Azure-SSIS.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Obter mais informações sobre o runtime de integração Azure-SSIS

Consulte os seguintes artigos para saber mais sobre o runtime de integração Azure-SSIS:

- [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o ir Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo é uma continuação do tutorial e fornece instruções sobre como utilizar a instância gerida da base de dados SQL do Azure e associar o IR a uma rede virtual. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar a rede virtual para que o IR Azure-SSIS pode aderir a rede virtual. 

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para monitorizar pipelines de formas diferentes: 

- [Início rápido: criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md).
- [Utilizar o Azure Monitor para monitorizar os pipelines do Data Factory](monitor-using-azure-monitor.md)
