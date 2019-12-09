---
title: Monitorar o tempo de execução de integração no Azure Data Factory
description: Saiba como monitorar diferentes tipos de tempo de execução de integração no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: a65bb119994e8bb56eecc730774535d7c0a4d8b6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928433"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorar um tempo de execução de integração no Azure Data Factory  
O **Integration Runtime** é a infraestrutura de computação usada pelo Azure data Factory para fornecer vários recursos de integração de dados em diferentes ambientes de rede. Há três tipos de tempos de execução de integração oferecidos pelo Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Runtime de integração de SSIS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o status de uma instância do IR (Integration Runtime), execute o seguinte comando do PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet retorna informações diferentes para tipos diferentes de tempo de execução de integração. Este artigo explica as propriedades e os status para cada tipo de tempo de execução de integração.  

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
O recurso de computação para um tempo de execução de integração do Azure é totalmente gerenciado de forma elástica no Azure. A tabela a seguir fornece descrições das propriedades retornadas pelo comando **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Propriedades
A tabela a seguir fornece descrições das propriedades retornadas pelo cmdlet para um tempo de execução de integração do Azure:

| Propriedade | Descrição |
-------- | ------------- | 
| Nome | Nome do tempo de execução de integração do Azure. |  
| Estado | Status do tempo de execução de integração do Azure. | 
| Localização | Local do tempo de execução de integração do Azure. Para obter detalhes sobre o local de um tempo de execução de integração do Azure, consulte [introdução ao Integration Runtime](concepts-integration-runtime.md). |
| DataFactoryName | Nome do data factory ao qual pertence o Azure Integration Runtime. | 
| ResourceGroupName | Nome do grupo de recursos ao qual o data factory pertence.  |
| Descrição | Descrição do Integration Runtime.  |

### <a name="status"></a>Estado
A tabela a seguir fornece os possíveis status de um tempo de execução de integração do Azure:

| Estado | Comentários/cenários | 
| ------ | ------------------ |
| Online | O tempo de execução de integração do Azure está online e pronto para ser usado. | 
| Offline | O tempo de execução de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Esta seção fornece descrições para propriedades retornadas pelo cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> As propriedades e o status retornados contêm informações sobre o tempo de execução de integração auto-hospedado global e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

A tabela a seguir fornece descrições das propriedades de monitoramento para **cada nó**:

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do tempo de execução de integração auto-hospedado e nós associados a ele. O nó é um computador local do Windows que tem o tempo de execução de integração auto-hospedado instalado nele. |  
| Estado | O status do tempo de execução de integração auto-hospedado global e de cada nó. Exemplo: online/offline/Limited/etc. Para obter informações sobre esses status, consulte a próxima seção. | 
| Versão | A versão do tempo de execução de integração auto-hospedado e cada nó. A versão do tempo de execução de integração auto-hospedado é determinada com base na versão da maioria dos nós no grupo. Se houver nós com versões diferentes na configuração de tempo de execução de integração auto-hospedado, somente os nós com o mesmo número de versão do tempo de execução de integração lógico auto-hospedado funcionarão corretamente. Outras estão no modo limitado e precisam ser atualizadas manualmente (somente em caso de falha de atualização automática). | 
| Memória disponível | Memória disponível em um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. |
| Rede (entrada/saída) | Utilização de rede de um nó de tempo de execução de integração auto-hospedado. Esse valor é um instantâneo quase em tempo real. | 
| Trabalhos simultâneos (em execução/limite) | **Em execução**. Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. <br/><br/>**Limite**. Limit significa o máximo de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho da máquina. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, quando as atividades esgotam o tempo limite mesmo quando a CPU, a memória ou a rede é subutilizada. Esse recurso também está disponível com um tempo de execução de integração autohospedado e de um único nó. |
| Função | Há dois tipos de funções em um tempo de execução de integração de vários nós hospedados automaticamente – Dispatcher e trabalho. Todos os nós são trabalhadores, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó Dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e expedir-os para diferentes nós de trabalho. O nó Dispatcher também é um nó de trabalho. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nós no tempo de execução de integração auto-hospedado (ou seja, em um cenário de expansão).

#### <a name="concurrent-jobs-limit"></a>Limite de trabalhos simultâneos

O valor padrão do limite de trabalhos simultâneos é definido com base no tamanho da máquina. Os fatores usados para calcular esse valor dependem da quantidade de RAM e do número de núcleos de CPU da máquina. Portanto, quanto mais núcleos e mais memória, maior o limite padrão de trabalhos simultâneos.

Você escala horizontalmente aumentando o número de nós. Quando você aumenta o número de nós, o limite de trabalhos simultâneos é a soma dos valores de limite de trabalhos simultâneos de todos os nós disponíveis.  Por exemplo, se um nó permitir que você execute um máximo de doze trabalhos simultâneos, a adição de três nós mais semelhantes permitirá que você execute um máximo de 48 trabalhos simultâneos (ou seja, 4 x 12). É recomendável aumentar o limite de trabalhos simultâneos somente quando você vir o baixo uso de recursos com os valores padrão em cada nó.

Você pode substituir o valor padrão calculado no portal do Azure. Selecione criar > Conexões > tempos de execução de integração > Editar > nós > modificar o valor do trabalho simultâneo por nó. Você também pode usar o comando [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) do PowerShell.
  
### <a name="status-per-node"></a>Status (por nó)
A tabela a seguir fornece possíveis status de um nó de tempo de execução de integração auto-hospedado:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | O nó está conectado ao serviço de Data Factory. |
| Offline | O nó está offline. |
| Atualizar | O nó está sendo atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido ao problema de porta HTTP 8050, problema de conectividade do barramento de serviço ou um problema de sincronização de credencial. |
| Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria. |

Um nó pode ficar inativo quando não puder se conectar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (tempo de execução de integração auto-hospedado global)
A tabela a seguir fornece possíveis status de um tempo de execução de integração auto-hospedado. Esse status depende de status de todos os nós que pertencem ao tempo de execução. 

| Estado | Descrição |
| ------ | ----------- | 
| Precisa de registro | Nenhum nó está registrado nesse tempo de execução de integração auto-hospedado ainda. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós neste Integration Runtime de hospedagem interna estão em um estado íntegro. Esse status é um aviso de que alguns nós podem estar inativos. Esse status pode ser devido a um problema de sincronização de credencial no nó Dispatcher/de trabalho. |

Use o cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** para buscar o conteúdo JSON que contém as propriedades detalhadas do tempo de execução de integração auto-hospedado e seus valores de instantâneo durante o tempo de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Saída de exemplo (pressupõe que haja dois nós associados a este Integration Runtime de hospedagem interna):

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
O tempo de execução de integração do Azure-SSIS é um cluster totalmente gerenciado de máquinas virtuais do Azure (ou nós) dedicados para executar seus pacotes SSIS. Ele não executa nenhuma outra atividade do Azure Data Factory. Depois de provisionado, você pode consultar suas propriedades e monitorar seus status geral/específico do nó.

### <a name="properties"></a>Propriedades

| Propriedade/status | Descrição |
| --------------- | ----------- |
| CreateTime | A hora UTC em que o tempo de execução de integração do Azure-SSIS foi criado. |
| Nós | Os nós alocados/disponíveis de seu tempo de execução de integração do Azure-SSIS com status específicos de nó (iniciando/disponível/reciclando/não disponível) e erros acionáveis. |
| OtherErrors | Os erros acionáveis não específicos do nó no tempo de execução de integração do Azure-SSIS. |
| LastOperation | O resultado da última operação de iniciar/parar no tempo de execução de integração do Azure-SSIS com erros acionáveis, caso tenha falhado. |
| Estado | O status geral (inicial/iniciando/iniciado/parando/parado) do tempo de execução de integração do Azure-SSIS. |
| Localização | O local do tempo de execução de integração do Azure-SSIS. |
| NodeSize | O tamanho de cada nó do tempo de execução de integração do Azure-SSIS. |
| NodeCount | O número de nós no tempo de execução de integração do Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó no tempo de execução de integração do Azure-SSIS. |
| CatalogServerEndpoint | O ponto de extremidade do seu servidor de banco de dados SQL/Instância Gerenciada do Azure existente para hospedar o SSISDB. |
| CatalogAdminUserName | O nome de usuário do administrador do seu servidor de banco de dados SQL/Instância Gerenciada do Azure existente. Data Factory serviço usa essas informações para preparar e gerenciar o SSISDB em seu nome. |
| CatalogAdminPassword | A senha de administrador do seu servidor de banco de dados SQL/Instância Gerenciada do Azure existente. |
| CatalogPricingTier | O tipo de preço para SSISDB hospedado pelo seu servidor de banco de dados SQL do Azure existente.  Não aplicável a Instância Gerenciada do Banco de Dados SQL do Azure a hospedagem do SSISDB. |
| VNetId | A ID de recurso de rede virtual para o tempo de execução de integração do Azure-SSIS ingressar. |
| Subrede | O nome da sub-rede para o tempo de execução de integração do Azure-SSIS ingressar. |
| ID | A ID de recurso do tempo de execução de integração do Azure-SSIS. |
| Tipo | O tipo (gerenciado/auto-hospedado) do tempo de execução de integração do Azure-SSIS. |
| ResourceGroupName | O nome do grupo de recursos do Azure, no qual o data factory e o tempo de execução de integração do Azure-SSIS foram criados. |
| DataFactoryName | O nome do seu data factory do Azure. |
| Nome | O nome do tempo de execução de integração do Azure-SSIS. |
| Descrição | A descrição do seu tempo de execução de integração do Azure-SSIS. |

  
### <a name="status-per-node"></a>Status (por nó)

| Estado | Descrição |
| ------ | ----------- | 
| A Iniciar | Este nó está sendo preparado. |
| Disponível | Este nó está pronto para implantar/executar pacotes do SSIS. |
| Reciclagem | Este nó está sendo reparado/reiniciando. |
| Indisponível | Este nó não está pronto para implantar/executar pacotes do SSIS e tem erros/problemas acionáveis que você pode resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (tempo de execução de integração do Azure-SSIS geral)

| Status geral | Descrição | 
| -------------- | ----------- | 
| Initial (Inicial) | Os nós do seu tempo de execução de integração do Azure-SSIS não foram alocados/preparados. | 
| A Iniciar | Os nós do seu tempo de execução de integração do Azure-SSIS estão sendo alocados/preparados e a cobrança foi iniciada. |
| Started | Os nós do seu tempo de execução de integração do Azure-SSIS foram alocados/preparados e estão prontos para você implantar/executar pacotes do SSIS. |
| A Parar  | Os nós do tempo de execução de integração do Azure-SSIS estão sendo liberados. |
| Parada | Os nós de seu tempo de execução de integração do Azure-SSIS foram liberados e a cobrança foi interrompida. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorar o tempo de execução de integração do Azure-SSIS no portal do Azure

As capturas de tela a seguir mostram como selecionar a Azure-SSIS IR para monitorar e fornecer um exemplo das informações exibidas.

![Selecione o tempo de execução de integração do Azure-SSIS para monitorar](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Exibir informações sobre o tempo de execução de integração do Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorar o tempo de execução de integração do Azure-SSIS com o PowerShell

Use um script como o exemplo a seguir para verificar o status do Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o tempo de execução de integração do Azure-SSIS

Consulte os artigos a seguir para saber mais sobre o tempo de execução de integração do Azure-SSIS:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre os tempos de execução de integração em geral, incluindo o Azure-SSIS IR. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande o tutorial e fornece instruções sobre como usar Instância Gerenciada do Banco de Dados SQL do Azure e ingressar o IR em uma rede virtual. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Ele também fornece etapas para usar portal do Azure para configurar a rede virtual para que o Azure-SSIS IR possa ingressar na rede virtual. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para monitorar pipelines de maneiras diferentes: 

- [Início rápido: criar um data Factory](quickstart-create-data-factory-dot-net.md).
- [Usar Azure Monitor para monitorar pipelines de Data Factory](monitor-using-azure-monitor.md)
