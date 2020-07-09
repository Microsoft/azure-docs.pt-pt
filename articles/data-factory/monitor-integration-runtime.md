---
title: Monitor de integração tempo de execução na Azure Data Factory
description: Saiba como monitorizar diferentes tipos de tempo de integração na Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: cfb40375fe841dd363681aea3d2cf6355046cd51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84113694"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorizar um runtime de integração no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**O tempo de integração** é a infraestrutura de computação utilizada pela Azure Data Factory para fornecer várias capacidades de integração de dados em diferentes ambientes de rede. Existem três tipos de tempos de integração oferecidos pela Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Runtime de integração de SSIS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o estatuto de uma instância de execução de integração (IR), executar o seguinte comando PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet devolve informações diferentes para diferentes tipos de tempo de integração. Este artigo explica as propriedades e status para cada tipo de tempo de integração.  

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
O recurso compute para um tempo de integração Azure é totalmente gerido elasticamente em Azure. A tabela a seguir fornece descrições para as propriedades devolvidas pelo comando **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Propriedades
A tabela a seguir fornece descrições das propriedades devolvidas pelo cmdlet para um tempo de execução de integração Azure:

| Propriedade | Descrição |
-------- | ------------- | 
| Name | Nome do tempo de integração do Azure. |  
| Estado | Estado do tempo de integração do Azure. | 
| Localização | Localização do tempo de funcionamento da integração do Azure. Para obter detalhes sobre a localização de um tempo de execução da integração Azure, consulte [Introdução ao tempo de execução da integração.](concepts-integration-runtime.md) |
| DataFactoryName | Nome da fábrica de dados a que pertence o tempo de integração do Azure. | 
| ResourceGroupName | Nome do grupo de recursos a que pertence a fábrica de dados.  |
| Descrição | Descrição do tempo de execução da integração.  |

### <a name="status"></a>Estado
O quadro que se segue fornece os possíveis estatutos de um tempo de execução da integração Azure:

| Estado | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O tempo de integração do Azure está online e pronto para ser usado. | 
| Offline | O tempo de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Esta secção fornece descrições para propriedades devolvidas pelo cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> As propriedades devolvidas e o estado contêm informações sobre o tempo de integração auto-hospedado e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

O quadro seguinte apresenta descrições das propriedades de monitorização para **cada nó:**

| Propriedade | Descrição | 
| -------- | ----------- | 
| Name | Nome do tempo de integração auto-hospedado e nós associados a ele. O nó é uma máquina Windows no local que tem o tempo de execução de integração auto-hospedado instalado nele. |  
| Estado | O estado do tempo de integração auto-hospedado e cada nó. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção seguinte. | 
| Versão | A versão do tempo de integração auto-hospedado e cada nó. A versão do tempo de integração auto-hospedado é determinada com base na versão da maioria dos nós do grupo. Se existirem nós com diferentes versões na configuração do tempo de execução de integração auto-hospedada, apenas os nós com o mesmo número de versão que a função lógica de integração auto-hospedada funcionam corretamente. Outros encontram-se no modo limitado e precisam de ser atualizados manualmente (apenas no caso de falha de atualização automática). | 
| Memória disponível | Memória disponível num nó de tempo de execução de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização do CPU de um nó de tempo de execução de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. |
| Rede (In/out) | Utilização da rede de um nó de tempo de execução de integração auto-hospedado. Este valor é um instantâneo quase em tempo real. | 
| Empregos Simultâneos (Execução/ Limite) | **A correr.** Número de empregos ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. <br/><br/>**Limite.** O limite significa os empregos máximos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para escalar a execução de emprego simultânea em cenários avançados, quando as atividades estão a cronometrar mesmo quando a CPU, a memória ou a rede estão subutilizá-la. Esta capacidade também está disponível com um tempo de integração auto-hospedado de um único nó. |
| Função | Existem dois tipos de papéis num tempo de integração auto-hospedado em vários nós – despachante e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Há apenas um nó de despachante, que é usado para retirar tarefas/empregos dos serviços na nuvem e enviá-los para diferentes nós de trabalhadores. O nó do despachante é também um nó de trabalhadores. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nóns no tempo de integração auto-hospedado (isto é, num cenário de escala para fora).

#### <a name="concurrent-jobs-limit"></a>Limite de emprego simultâneo

O valor predefinido do limite de postos de trabalho simultâneo é definido com base no tamanho da máquina. Os fatores utilizados para calcular este valor dependem da quantidade de RAM e do número de núcleos de CPU da máquina. Assim, quanto mais núcleos e mais memória, maior o limite padrão de empregos simultâneos.

Aumenta-se o número de nós. Quando se aumenta o número de nós, o limite de emprego simultâneo é a soma dos valores-limite de emprego simultâneos de todos os nós disponíveis.  Por exemplo, se um nó permitir que você tenha um máximo de 12 empregos simultâneos, então adicionar mais três nóns semelhantes permite-lhe executar um máximo de 48 empregos simultâneos (ou seja, 4 x 12). Recomendamos que aumente o limite de postos de trabalho simultâneos apenas quando vê uma utilização de baixo recurso com os valores predefinidos em cada nó.

Pode sobrepor-se ao valor padrão calculado no portal Azure. Selecione > De autor > integração Prazos de execução > Editar > Nós > Modificar o valor de trabalho simultâneo por nó. Também pode utilizar o comando powerShell [update-Azdatafactoryv2integrationruntimenode.](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples)
  
### <a name="status-per-node"></a>Estado (por nó)
A tabela a seguir fornece os estatutos possíveis de um nó de tempo de execução de integração auto-hospedado:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | O nó está ligado ao serviço Data Factory. |
| Offline | O nó está desligado. |
| Modernização | O nó está a ser atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido à emissão da porta HTTP 8050, problema de conectividade do autocarro de serviço ou um problema de sincronização de credenciais. |
| Inativa | O nó está numa configuração diferente da configuração de outros nós maioritários. |

Um nó pode ser inativo quando não pode ligar-se a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Estado (tempo geral de integração auto-hospedado)
A tabela a seguir proporciona possíveis estatutos de um tempo de integração auto-hospedado. Este estatuto depende dos estatutos de todos os nós que pertencem ao tempo de funcionação. 

| Estado | Descrição |
| ------ | ----------- | 
| Registo de Necessidades | Nenhum nó está registado neste tempo de integração auto-hospedado ainda. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós neste tempo de integração auto-organizado estão num estado saudável. Este estado é um aviso de que alguns nós podem estar para baixo. Este estado pode dever-se a uma questão de sincronização de credencial no nó de despacho/trabalhador. |

Utilize o **cmdlet Get-AzDataFactoryV2IntegrationRuntimeMetric** para obter a carga útil JSON que contém as propriedades de execução de integração auto-hospedadas detalhadas e os seus valores instantâneos durante o período de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Saída da amostra (assume que existem dois nóns associados a este tempo de integração auto-hospedado):

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
O tempo de integração Azure-SSIS é um cluster totalmente gerido de máquinas virtuais Azure (ou nós) dedicados a executar os seus pacotes SSIS. Não gere quaisquer outras atividades da Azure Data Factory. Uma vez previsto, pode consultar as suas propriedades e monitorizar os seus estados gerais/específicos do nó.

### <a name="properties"></a>Propriedades

| Propriedade/Estado | Descrição |
| --------------- | ----------- |
| Criar Tempo | O tempo UTC quando o seu tempo de integração Azure-SSIS foi criado. |
| Nós | Os nós atribuídos/disponíveis do seu tempo de integração Azure-SSIS com estatutos específicos do nó (início/disponível/reciclagem/indisponível) e erros acccionáveis. |
| Outros Errantes | Os erros de ação não específicos do nó no seu tempo de integração Azure-SSIS. |
| Última Operação | O resultado da última operação de início/paragem no seu tempo de integração Azure-SSIS com erros acccionáveis se falhar. |
| Estado | O estado geral (inicial/início/início/paragem/paragem) do seu tempo de integração Azure-SSIS. |
| Localização | A localização do seu tempo de integração Azure-SSIS. |
| Tamanho do nó | O tamanho de cada nó do seu tempo de integração Azure-SSIS. |
| NodeCount | O número de nós no seu tempo de integração Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó no seu tempo de integração Azure-SSIS. |
| CatálogoServerEndpoint | O ponto final da sua base de dados SQL/SQL De gestão de ocorrências para hospedar o SSISDB. |
| CatálogoAdminUserName | O nome de utilizador de administrador da sua base de dados SQL/SQL Desaconseção Gerida. O serviço Data Factory utiliza esta informação para preparar e gerir o SSISDB em seu nome. |
| CatalogAdminPassword | A palavra-passe de administrador da sua base de dados SQL/SQL Desaquia. |
| CatalogPricingTier | O nível de preços do SSISDB hospedado pela SQL Database.  Não aplicável à SQL Managed Instance que hospeda O SSISDB. |
| VNetId | O ID de recursos de rede virtual para o seu tempo de integração Azure-SSIS para aderir. |
| Subrede | O nome da sub-rede para o seu tempo de integração Azure-SSIS para aderir. |
| ID | O ID de recursos do seu tempo de integração Azure-SSIS. |
| Tipo | O tipo (Gerido/Auto-hospedado) do seu tempo de integração Azure-SSIS. |
| ResourceGroupName | O nome do seu Grupo de Recursos Azure, no qual a sua fábrica de dados e o tempo de integração Azure-SSIS foram criados. |
| DataFactoryName | O nome da sua fábrica de dados Azure. |
| Name | O nome do seu tempo de integração Azure-SSIS. |
| Descrição | A descrição do seu tempo de integração Azure-SSIS. |

  
### <a name="status-per-node"></a>Estado (por nó)

| Estado | Descrição |
| ------ | ----------- | 
| A iniciar | Este nó está a ser preparado. |
| Disponível | Este nó está pronto para implementar/executar pacotes SSIS. |
| Reciclagem | Este nó está a ser reparado/reiniciado. |
| Indisponível | Este nó não está pronto para implementar/executar pacotes SSIS e tem erros/problemas acccionáveis que pode resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Estado (tempo geral de integração Azure-SSIS)

| Estado geral | Descrição | 
| -------------- | ----------- | 
| Initial (Inicial) | Os nós do seu tempo de integração Azure-SSIS não foram atribuídos/preparados. | 
| A iniciar | Os nós do seu tempo de integração Azure-SSIS estão a ser atribuídos/preparados e a faturação já começou. |
| Iniciado | Os nós do seu tempo de integração Azure-SSIS foram atribuídos/preparados e estão prontos para implementar/executar pacotes SSIS. |
| A parar  | Os nós do seu tempo de integração Azure-SSIS estão a ser lançados. |
| Parada | Os nós do seu tempo de integração Azure-SSIS foram lançados e a faturação parou. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorize o tempo de integração Azure-SSIS no portal Azure

As imagens que se seguem mostram como selecionar o Azure-SSIS IR para monitorizar e fornecer um exemplo das informações apresentadas.

![Selecione o tempo de integração Azure-SSIS para monitorizar](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Ver informações sobre o tempo de integração do Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorize o tempo de integração Azure-SSIS com o PowerShell

Utilize um script como o seguinte exemplo para verificar o estado do Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o tempo de integração do Azure-SSIS

Consulte os seguintes artigos para saber mais sobre o tempo de integração do Azure-SSIS:

- [Tempo de execução da integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceptuais sobre os tempos de integração em geral, incluindo o Azure-SSIS IR. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e utiliza a Base de Dados SQL para hospedar o catálogo SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da SQL Managed Instance e a adesão do IR a uma rede virtual. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também fornece passos para usar o portal Azure para configurar a rede virtual para que o Azure-SSIS IR possa aderir à rede virtual. 

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes artigos de monitorização dos gasodutos de diferentes formas: 

- [Quickstart: criar uma fábrica de dados.](quickstart-create-data-factory-dot-net.md)
- [Utilize o Monitor Azure para monitorizar os oleodutos da Fábrica de Dados](monitor-using-azure-monitor.md)
