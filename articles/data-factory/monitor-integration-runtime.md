---
title: Monitorizar o tempo de execução da integração na Fábrica de Dados azure
description: Saiba como monitorizar diferentes tipos de tempo de funcionação de integração na Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 6d2ea5c0b7354867086fc0cce43732f2d73c53ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398952"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorizar um runtime de integração no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
O tempo de execução da **integração** é a infraestrutura computacional utilizada pela Azure Data Factory para fornecer várias capacidades de integração de dados em diferentes ambientes de rede. Existem três tipos de tempos de integração oferecidos pela Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Runtime de integração de SSIS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o estatuto de um caso de tempo de integração (IR), executar o seguinte comando PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet devolve diferentes informações para diferentes tipos de tempo de integração. Este artigo explica as propriedades e os status para cada tipo de tempo de execução de integração.  

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
O recurso computacional para um tempo de funcionação de integração Azure é totalmente gerido elástico em Azure. A tabela seguinte fornece descrições para propriedades devolvidas pelo comando **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Propriedades
A tabela seguinte apresenta descrições de propriedades devolvidas pelo cmdlet para um tempo de execução de integração Azure:

| Propriedade | Descrição |
-------- | ------------- | 
| Nome | Nome do tempo de execução da integração Azure. |  
| Estado | Estado do tempo de execução da integração Azure. | 
| Localização | Localização do tempo de execução da integração Azure. Para mais detalhes sobre a localização de um tempo de execução de integração Azure, consulte Introdução ao tempo de execução da [integração.](concepts-integration-runtime.md) |
| DataFactoryName | Nome da fábrica de dados a que o tempo de execução de integração Azure pertence. | 
| ResourceGroupName | Nome do grupo de recursos a que a fábrica de dados pertence.  |
| Descrição | Descrição do tempo de execução da integração.  |

### <a name="status"></a>Estado
O quadro seguinte fornece possíveis estatutos de um tempo de execução de integração Azure:

| Estado | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O tempo de execução da integração Azure está online e pronto para ser usado. | 
| Offline | O tempo de funcionamento da integração Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Esta secção fornece descrições para propriedades devolvidas pelo Get-AzDataFactoryV2IntegrationTimetime cmdlet. 

> [!NOTE] 
> As propriedades e o estado devolvidos contêm informações sobre o tempo de execução geral da integração auto-hospedada e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

O quadro seguinte apresenta descrições de propriedades de monitorização para **cada nó:**

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do tempo de funcionação de integração auto-hospedado e nódosos associados a ele. O Nó é uma máquina Windows no local que tem o tempo de funcionação de integração auto-hospedado instalado nele. |  
| Estado | O estado do tempo de execução geral da integração auto-hospedada e de cada nó. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção seguinte. | 
| Versão | A versão do tempo de execução da integração auto-hospedada e cada nó. A versão do tempo de execução de integração auto-hospedado é determinada com base na versão da maioria dos nódosos do grupo. Se houver nós com versões diferentes na configuração de tempo de funcionamento de integração auto-hospedada, apenas os nós com o mesmo número de versão que o funcionamento de funcionamento de funcionamento de integração auto-hospedado lógico funcionam corretamente. Outros estão no modo limitado e precisam de ser atualizados manualmente (apenas no caso de a atualização automática falhar). | 
| Memória disponível | Memória disponível em um nó de tempo de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó de tempo de execução de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. |
| Networking (In/out) | Utilização da rede de um nó de tempo de execução de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. | 
| Empregos simultâneos (execução/ limite) | **A correr.** Número de postos de trabalho ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. <br/><br/>**Limite**. Limite significa o máximo de empregos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para aumentar a execução de emprego simultânea em cenários avançados, quando as atividades estão a cronometrar mesmo quando a CPU, a memória ou a rede são subutilizadas. Esta capacidade também está disponível com um tempo de execução de integração auto-anfitriãde de um nó único. |
| Função | Existem dois tipos de funções num tempo de execução de integração multi-nó – despachante e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Há apenas um nó de expedidor, que é usado para retirar tarefas/empregos dos serviços de nuvem e despachá-los para diferentes nós de trabalhadores. O nó de despacho também é um nó de trabalhador. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nós no tempo de execução de integração auto-hospedado (isto é, num cenário de escala fora).

#### <a name="concurrent-jobs-limit"></a>Limite de empregos simultâneos

O valor padrão do limite de empregos simultâneos é definido com base no tamanho da máquina. Os fatores utilizados para calcular este valor dependem da quantidade de RAM e do número de núcleos de CPU da máquina. Assim, quanto mais núcleos e mais memória, maior o limite padrão de empregos simultâneos.

Aumenta-se o número de nós. Quando se aumenta o número de nós, o limite de postos de trabalho simultâneos é a soma dos valores-limite de emprego simultâneos de todos os nós disponíveis.  Por exemplo, se um nó lhe permite executar um máximo de doze empregos simultâneos, então adicionar mais três nós semelhantes permite-lhe executar um máximo de 48 empregos simultâneos (isto é, 4 x 12). Recomendamos que aumente o limite de empregos simultâneos apenas quando vê o baixo uso de recursos com os valores predefinidos em cada nó.

Pode anular o valor de predefinição calculado no portal Azure. Selecione Author > Connections > Integration Runtimes > Editar > Nóesos > Modificar o valor de trabalho simultâneo por nó. Também pode utilizar o comando powerShell [update-Azdatafactoryv2integrationtimenode.](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples)
  
### <a name="status-per-node"></a>Estado (por nó)
O quadro seguinte fornece possíveis estatutos de um nó de tempo de integração auto-hospedado:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | O nó está ligado ao serviço data Factory. |
| Offline | O nó está offline. |
| Upgrade | O nó está a ser atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido a http porta 8050, problema de conectividade de ônibus de serviço, ou um problema de sincronização credencial. |
| Inativa | O nó está numa configuração diferente da configuração de outros nódosos maioritários. |

Um nó pode estar inativo quando não consegue ligar-se a outros nódosos.

### <a name="status-overall-self-hosted-integration-runtime"></a>Estado (tempo de execução geral de integração auto-hospedada)
A tabela que se segue fornece possíveis estatutos de um tempo de execução de integração auto-hospedado. Este estatuto depende dos estatutos de todos os nós que pertencem ao tempo de corrido. 

| Estado | Descrição |
| ------ | ----------- | 
| Necessidade de Inscrição | Nenhum nó está registado neste tempo de integração auto-hospedado ainda. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós neste tempo de integração auto-hospedado estão num estado saudável. Este estado é um aviso de que alguns nós podem estar em baixo. Este estatuto pode dever-se a uma questão de sincronização credencial no nó de expedidor/trabalhador. |

Utilize o **Cmdlet Get-AzDataFactoryV2IntegrationTimeTimeMetric** cmdlet para obter a carga útil JSON contendo as propriedades de tempo de execução de integração auto-hospedada detalhada, e os seus valores instantâneos durante o tempo de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Saída da amostra (pressupõe que existem dois nós associados a este tempo de execução de integração auto-hospedado):

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
O tempo de funcionação de integração Azure-SSIS é um conjunto totalmente gerido de máquinas virtuais Azure (ou nós) dedicadas a executar os seus pacotes SSIS. Não executa quaisquer outras atividades da Azure Data Factory. Uma vez provisionado, pode consultar as suas propriedades e monitorizar os seus estatutos globais/específicos do nó.

### <a name="properties"></a>Propriedades

| Propriedade/Estatuto | Descrição |
| --------------- | ----------- |
| Tempo de Criação | O tempo utc quando o seu tempo de execução de integração Azure-SSIS foi criado. |
| Nós | Os nós atribuídos/disponíveis do seu tempo de execução de integração Azure-SSIS com estatutos específicos do nó (arranque/disponível/reciclagem/indisponíveis) e erros atores. |
| Outros Erros | Os erros não específicos de ação no seu tempo de execução de integração Azure-SSIS. |
| Última Operação | O resultado da última operação de arranque/paragem no seu tempo de funcionamento de integração Azure-SSIS com erros atores se falhar. |
| Estado | O estado global (inicial/arranque/arranque/paragem/paragem) do seu tempo de execução de integração Azure-SSIS. |
| Localização | A localização do seu tempo de execução de integração Azure-SSIS. |
| Tamanho do nó | O tamanho de cada nó do seu tempo de execução de integração Azure-SSIS. |
| NodeCount | O número de nós no seu tempo de execução de integração Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó no seu tempo de execução de integração Azure-SSIS. |
| Ponto final do CatalogServer | O ponto final do seu servidor de base de dados Azure SQL/Instância Gerida existente para hospedar o SSISDB. |
| Nome de utilizador de catálogoAdminUser | O nome de utilizador administrativo do seu atual servidor de base de dados Azure SQL/Instância Gerida. O serviço Data Factory utiliza estas informações para preparar e gerir o SSISDB em seu nome. |
| CatalogAdminPassword | A senha de administração do seu atual servidor de base de dados Azure SQL/Instância Gerida. |
| CatálogoPricingTier | O nível de preços do SSISDB hospedado pelo seu servidor de base de dados Azure SQL existente.  Não aplicável à Base de Dados Azure SQL Managed Instance hospedando SSISDB. |
| Vnetid | O ID de recursos de rede virtual para o seu tempo de execução de integração Azure-SSIS para aderir. |
| Subrede | O nome da sub-rede para o seu tempo de execução de integração Azure-SSIS para aderir. |
| ID | O id de recurso do seu tempo de execução de integração Azure-SSIS. |
| Tipo | O tipo (Gerido/Auto-Hospedado) do seu tempo de execução de integração Azure-SSIS. |
| ResourceGroupName | O nome do seu Grupo de Recursos Azure, no qual a sua fábrica de dados e o tempo de execução de integração Azure-SSIS foram criados. |
| DataFactoryName | O nome da sua fábrica de dados Azure. |
| Nome | O nome do seu tempo de execução de integração Azure-SSIS. |
| Descrição | A descrição do seu tempo de execução de integração Azure-SSIS. |

  
### <a name="status-per-node"></a>Estado (por nó)

| Estado | Descrição |
| ------ | ----------- | 
| A iniciar | Este nó está a ser preparado. |
| Disponível | Este nó está pronto para que implemente/execute pacotes SSIS. |
| Reciclagem | Este nó está a ser reparado/reiniciado. |
| Indisponível | Este nó não está pronto para que implemente/execute pacotes SSIS e tem erros/problemas atores que poderá resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Estado (tempo de execução geral da integração Azure-SSIS)

| Estatuto geral | Descrição | 
| -------------- | ----------- | 
| Initial (Inicial) | Os nós do seu tempo de execução de integração Azure-SSIS não foram atribuídos/preparados. | 
| A iniciar | Os nós do seu tempo de execução de integração Azure-SSIS estão a ser atribuídos/preparados e a faturação já começou. |
| Iniciado | Os nós do seu tempo de execução de integração Azure-SSIS foram atribuídos/preparados e estão prontos para que implemente/execute pacotes SSIS. |
| A parar  | Os nódosos do seu tempo de execução de integração Azure-SSIS estão a ser lançados. |
| Parada | Os nódosos do seu tempo de execução de integração Azure-SSIS foram lançados e a faturação parou. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorize o tempo de execução da integração Azure-SSIS no portal Azure

As imagens que se seguem mostram como selecionar o IR Azure-SSIS para monitorizar e fornecer um exemplo da informação que é exibida.

![Selecione o tempo de execução de integração Azure-SSIS para monitorizar](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Ver informações sobre o tempo de execução da integração Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorize o tempo de execução da integração Azure-SSIS com a PowerShell

Utilize um guião como o seguinte exemplo para verificar o estado do IR Azure-SSIS.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o tempo de execução da integração Azure-SSIS

Consulte os seguintes artigos para saber mais sobre o tempo de execução da integração Azure-SSIS:

- Tempo de [execução de integração Azure-SSIS.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Este artigo fornece informações conceptuais sobre os tempos de integração em geral, incluindo o IR Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também fornece passos para usar o portal Azure para configurar a rede virtual para que o Ir Azure-SSIS possa aderir à rede virtual. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para monitorização de gasodutos de diferentes formas: 

- [Arranque rápido: criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md).
- [Use o Monitor Azure para monitorizar os oleodutos da Fábrica de Dados](monitor-using-azure-monitor.md)
