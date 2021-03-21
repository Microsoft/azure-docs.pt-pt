---
title: Monitor de integração tempo de execução na Azure Data Factory
description: Saiba como monitorizar diferentes tipos de tempo de integração na Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740035"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorizar um runtime de integração no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**O tempo de integração** é a infraestrutura de computação utilizada pela Azure Data Factory (ADF) para fornecer várias capacidades de integração de dados em diferentes ambientes de rede. Existem três tipos de tempos de integração oferecidos pela Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Serviços de integração de servidores Azure-SQL (SSIS)

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
| Nome | Nome do tempo de integração do Azure. |  
| Estado | Estado do tempo de integração do Azure. | 
| Localização | Localização do tempo de funcionamento da integração do Azure. Para obter detalhes sobre a localização de um tempo de execução da integração Azure, consulte [Introdução ao tempo de execução da integração.](concepts-integration-runtime.md) |
| DataFactoryName | Nome da fábrica de dados a que pertence o tempo de integração do Azure. | 
| ResourceGroupName | Nome do grupo de recursos a que pertence a fábrica de dados.  |
| Description | Descrição do tempo de execução da integração.  |

### <a name="status"></a>Estado

O quadro que se segue fornece os possíveis estatutos de um tempo de execução da integração Azure:

| Estado | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O tempo de integração do Azure está online e pronto para ser usado. | 
| Offline | O tempo de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado

Esta secção fornece descrições para as propriedades devolvidas pelo Get-AzDataFactoryV2IntegrationRuntime cmdlet. 

> [!NOTE] 
> As propriedades devolvidas e o estado contêm informações sobre o tempo de integração auto-hospedado e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

O quadro seguinte apresenta descrições das propriedades de monitorização para **cada nó:**

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do tempo de integração auto-hospedado e nós associados a ele. O nó é uma máquina Windows no local que tem o tempo de execução de integração auto-hospedado instalado nele. |  
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

Pode sobrepor-se ao valor padrão calculado no portal Azure. Selecione > De autor > integração Prazos de execução > Editar > Nós > Modificar o valor de trabalho simultâneo por nó. Também pode utilizar o comando powerShell [update-Azdatafactoryv2integrationruntimenode.](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples)
  
### <a name="status-per-node"></a>Estado (por nó)

A tabela a seguir fornece os estatutos possíveis de um nó de tempo de execução de integração auto-hospedado:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | O nó está ligado ao serviço Data Factory. |
| Offline | O nó está desligado. |
| Atualizar | O nó está a ser atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido à emissão da porta HTTP 8060, problema de conectividade do autocarro de serviço ou um problema de sincronização de credenciais. |
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

O Azure-SSIS IR é um cluster totalmente gerido de máquinas virtuais Azure (VMs ou nós) dedicados a executar os seus pacotes SSIS. Pode invocar execuções de pacotes SSIS no Azure-SSIS IR utilizando vários métodos, por exemplo através de ferramentas de dados do servidor SQL ativadas pelo Azure (SSDT), utilitário de linha de comando AzureDTExec, T-SQL no SQL Server Management Studio (SSMS)/SQL Server Agent e Executar atividades de Pacote SSIS em oleodutos ADF. A Azure-SSIS IR não dirige outras atividades da ADF. Uma vez previsto, pode monitorizar as suas propriedades e estados gerais/específicos do nó através da Azure PowerShell, portal Azure e Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Monitorize o tempo de integração Azure-SSIS com a Azure PowerShell

Utilize o cmdlet Azure PowerShell para monitorizar as propriedades e estados gerais/específicos do nó da Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Propriedades

O quadro seguinte fornece descrições das propriedades devolvidas pelo cmdlet acima para um Azure-SSIS IR.

| Propriedade/Estado              | Description                  |
| ---------------------------- | ---------------------------- |
| Criar Tempo                   | O tempo UTC quando o seu Azure-SSIS IR foi criado. |
| Nós                        | Os nós atribuídos/disponíveis do seu Azure-SSIS IR com estatutos específicos do nó (início/disponível/reciclagem/indisponível) e erros acccionáveis. |
| Outros Errantes                  | Os erros de ação não específicos do nó no seu Azure-SSIS IR. |
| Última Operação                | O resultado da última operação de arranque/paragem no seu Azure-SSIS IR com erros acccionáveis se este falhar. |
| Estado                        | O estado geral (inicial/início/início/paragem/paragem) do seu Azure-SSIS IR. |
| Localização                     | A localização do seu Azure-SSIS IR. |
| Tamanho do nó                     | O tamanho de cada nó no seu Azure-SSIS IR. |
| NodeCount                    | O número de nós no seu Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | O número máximo de execuções paralelas por nó no seu Azure-SSIS IR. |
| CatálogoServerEndpoint        | O ponto final do seu servidor de base de dados Azure SQL existente ou exemplo gerido para hospedar o catálogo SSIS (SSISDB). |
| CatálogoAdminUserName         | O nome de utilizador de administração para o seu servidor de base de dados Azure SQL existente ou instância gerida. A ADF utiliza esta informação para preparar e gerir o SSISDB em seu nome. |
| CatalogAdminPassword         | A palavra-passe de administração do seu servidor de base de dados Azure SQL existente ou instância gerida. |
| CatalogPricingTier           | O nível de preços do SSISDB hospedado pelo servidor Azure SQL Database.  Não aplicável à Azure SQL Managed Instance que hospeda O SSISDB. |
| VNetId                       | O ID de recursos de rede virtual para a sua Azure-SSIS IR aderir. |
| Sub-rede                       | O nome da sub-rede para o seu Azure-SSIS IR para aderir. |
| ID                           | A identificação de recursos do seu Azure-SSIS IR. |
| Tipo                         | O tipo de IR (Gerido/Auto-hospedado) do seu Azure-SSIS IR. |
| ResourceGroupName            | O nome do seu Grupo de Recursos Azure, no qual foram criados os seus ADF e Azure-SSIS IR. |
| DataFactoryName              | O nome da sua ADF. |
| Name                         | O nome do seu Azure-SSIS IR. |
| Description                  | A descrição do seu Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Estado (por nó IR Azure-SSIS)

O quadro a seguir fornece os estatutos possíveis de um nó IR Azure-SSIS:

| Estado específico do nó | Description |
| -------------------- | ----------- | 
| A iniciar             | Este nó está a ser preparado. |
| Disponível            | Este nó está pronto para implementar/executar pacotes SSIS. |
| Reciclagem            | Este nó está a ser reparado/reiniciado. |
| Indisponível          | Este nó não está pronto para implementar/executar pacotes SSIS e tem erros/problemas acccionáveis que pode resolver. |

#### <a name="status-overall-azure-ssis-ir"></a>Estado (geral Azure-SSIS IR)

O quadro seguinte fornece possíveis estatutos globais de um Azure-SSIS IR. O estatuto geral, por sua vez, depende dos estatutos combinados de todos os nós que pertencem ao Azure-SSIS IR. 

| Estado geral | Description | 
| -------------- | ----------- | 
| Initial (Inicial)        | Os nós do seu Azure-SSIS IR não foram atribuídos/preparados. | 
| A iniciar       | Os nós do seu Azure-SSIS IR estão a ser atribuídos/preparados e a faturação já começou. |
| Iniciado        | Os nós do seu Azure-SSIS IR foram atribuídos/preparados e estão prontos para que implemente/execute pacotes SSIS. |
| A parar       | Os nós do seu Azure-SSIS IR estão a ser libertados. |
| Parada        | Os nós do seu Azure-SSIS IR foram libertados e a faturação parou. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Monitorize o tempo de integração Azure-SSIS no portal Azure

Para monitorizar o seu Azure-SSIS IR no portal Azure, aceda à página de tempos de **integração** do **monitor** hub na ADF UI, onde poderá ver todos os seus tempos de integração.

![Monitorizar todos os tempos de integração](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Em seguida, selecione o nome do seu Azure-SSIS IR para abrir a sua página de monitorização, onde pode ver as suas propriedades e status específicos do nó geral/ nó. Nesta página, dependendo da configuração das definições gerais, de implantação e avançadas do seu IR Azure-SSIS, encontrará vários azulejos informativos/funcionais.

Os azulejos informativos **TYPE** e **REGION** mostram o tipo e a região do seu Azure-SSIS IR, respectivamente.

O azulejo informativo **NODE SIZE** mostra o SKU (SSIS edition_VM tier_VM série), o número de núcleos de CPU e o tamanho de RAM por nó para o seu Azure-SSIS IR. 

O azulejo informativo **RUNNING /REQUESTED NODE(S)** compara o número de nós atualmente em execução com o número total de nós previamente solicitados para o seu Azure-SSIS IR.

O azulejo informativo **DUAL STANDBY PAIR / ROLE** mostra o nome do seu duplo standby Azure-SSIS IR que funciona em sincronização com a Azure SQL Database/Managed Instance failover group for business continuity and disaster recovery (BCDR) e o atual papel primário/secundário do seu Azure-SSIS IR. Quando ocorrer o failover do SSISDB, os seus IRs Azure-SSIS primários e secundários trocarão de funções (ver [Configurar o seu Azure-SSIS IR para BCDR).](./configure-bcdr-azure-ssis-integration-runtime.md)

Os azulejos funcionais são descritos em mais detalhes abaixo.

![Monitorize o seu Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Azulejo status

No azulejo **status** da sua página de monitorização Azure-SSIS IR, pode ver o seu estado geral, por exemplo **Running** ou **Stop**. Selecionar o estado **de Funcionamento** aparece numa janela com o botão **stop** ao vivo para parar o seu Azure-SSIS IR. A seleção do estado **Stop** aparece numa janela com o botão **Iniciar** ao vivo para iniciar o seu Azure-SSIS IR. A janela pop-up também tem um botão **de pacote Execute SSIS** para gerar automaticamente um pipeline ADF com a atividade do Pacote SSIS executado que funciona no seu Azure-SSIS IR (ver [pacotes Running SSIS como execute as atividades do Pacote SSIS em oleodutos ADF)](./how-to-invoke-ssis-package-ssis-activity.md)e uma caixa de texto **de ID** de recursos, a partir da qual pode copiar o seu ID de recursos Azure-SISIS `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` (). O sufixo do seu ID de recursos IR Azure-SSIS que contém os seus nomes ADF e Azure-SSIS IR forma um ID de cluster que pode ser usado para adquirir componentes SSIS premium/licenciados adicionais a fornecedores de software independentes (ISVs) e ligá-los ao seu Azure-SSIS IR (ver [Instalar componentes premium/licenciados no seu Azure-SSIS IR).](./how-to-develop-azure-ssis-ir-licensed-components.md)

![Monitorize o seu azulejo Azure-SSIS IR - STATUS](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Azulejo endpoint do servidor SSISDB

Se utilizar o Modelo de Implementação do Projeto onde as embalagens são armazenadas no SSISDB hospedado pelo seu servidor de base de dados Azure SQL ou por exemplo gerido, verá o azulejo **ENDPOINT do SSISDB** SERVER na sua página de monitorização Azure-SSIS IR (ver [Configurar as definições de implementação do Azure-SSIS IR).](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page) Neste azulejo, pode selecionar um link que designe o seu servidor de base de dados Azure SQL ou uma instância gerida para abrir uma janela, onde pode copiar o ponto final do servidor a partir de uma caixa de texto e usá-lo ao ligar a partir de SSMS para implementar, configurar, executar e gerir as suas encomendas. Na janela pop-up, também pode selecionar a Base de **Dados Azure SQL ou** a ligação de configurações de instância gerida para reconfigurar/redimensionar o seu SSISDB no portal Azure.

![Monitorize o seu azulejo Azure-SSIS IR - SSISDB](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>PROXY / PARAGEM DE AZULEJOS

Se descarregar, instalar e configurar Self-Hosted IR (SHIR) como procuração para o seu Azure-SSIS IR aceder aos dados nas instalações, verá o azulejo **PROXY/STAGING** na sua página de monitorização do IR Azure-SSIS (ver [SHIR configurante como procuração para o seu Azure-SSIS IR).](./self-hosted-integration-runtime-proxy-ssis.md) Neste azulejo, pode selecionar um link que designa o seu SHIR para abrir a sua página de monitorização. Também pode selecionar outro link que designe o seu Azure Blob Storage para a realização para reconfigurar o seu serviço ligado.

#### <a name="validate-vnet--subnet-tile"></a>VALIDAR azulejo VNET / SUBNET

Se se juntar ao seu Azure-SSIS IR a um VNet, verá o azulejo **VNET / SUBNET validado** na sua página de monitorização Azure-SSIS IR (ver [Juntar o seu Azure-SSIS IR a uma VNet).](./join-azure-ssis-integration-runtime-virtual-network.md) Neste azulejo, pode selecionar um link que designa o seu VNet e a sub-rede para abrir uma janela, onde pode copiar o seu ID de recurso VNet ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) e o nome da sub-rede a partir de caixas de texto, bem como validar as configurações do VNet e da sub-rede para garantir que os tráfegos de rede de entrada/saída necessários e a gestão do seu Azure-SSIS IR não sejam obstruídos.

![Monitorize o seu Azure-SSIS IR - VALIDATE Azulejo](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>DIAGNÓSTICO CONECTIVIDADE azulejo

No azulejo de **CONECTIVIDADE DIAGNOSTICO** da sua página de monitorização Azure-SSIS IR, pode selecionar a **ligação de ligação de Teste** para aparecer uma janela, onde pode verificar as ligações entre o seu Azure-SSIS IR e as lojas de pacote/configuração/dados relevantes, bem como serviços de gestão, através do seu nome de domínio totalmente qualificado (FQDN)/endereço IP e porta designada (ver [ligações de teste a partir do seu Azure-SSIS IR).](./ssis-integration-runtime-diagnose-connectivity-faq.md)

![Monitorize o seu Azure-SSIS IR - Diagnosticar azulejos](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>ENDEREÇOS IP PÚBLICOS ESTÁTICOS

Se trouxer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR, verá o azulejo **ESTÁTICO ENDEREÇOS IP NA** sua página de monitorização do Azure-SSIS IR (ver [Trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR).](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP) Neste azulejo, pode selecionar links que designam os seus endereços IP públicos estáticos de primeira/segunda para Azure-SSIS IR para abrir uma janela, onde pode copiar o seu ID de recursos `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` () a partir de uma caixa de texto. Na janela pop-up, também pode selecionar o link **de configurações de endereço IP público estático de primeira/segunda** para gerir o seu primeiro/segundo endereço IP público estático no portal Azure.

![Monitorize o seu Azure-SSIS IR - Azulejo estático](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Azulejo de lojas de pacotes

Se utilizar o Modelo de Implementação de Pacotes onde as embalagens são armazenadas no sistema de ficheiros/Azure Files/SQL Server database (MSDB) hospedado pela sua Azure SQL Managed Instance e gerido através de lojas de pacotes Azure-SSIS IR, verá o azulejo **pacote LOJAS** NA sua página de monitorização Azure-SSIS IR (ver [Configurar as definições de implementação do IR Azure-SISIS).](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page) Neste azulejo, pode selecionar um link que designe o número de lojas de pacotes anexadas ao seu Azure-SSIS IR para abrir uma janela, onde pode reconfigurar os serviços ligados relevantes para as suas lojas de pacotes Azure-SSIS IR em cima do sistema de ficheiros/Ficheiros Azure/MSDB hospedados pela sua Azure SQL Managed Instance.

![Monitorize o seu azulejo Azure-SSIS IR - PACKAGE](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>AZULEJO ERROR(S)

Se houver problemas com o início/paragem/manutenção/upgrade do seu Azure-SSIS IR, verá um azulejo de **ERRO (S)** adicional na sua página de monitorização Azure-SSIS IR. Neste azulejo, pode selecionar um link que designe o número de erros gerados pelo seu Azure-SSIS IR para abrir uma janela, onde pode ver esses erros em mais detalhes e copiá-los para encontrar as soluções recomendadas no nosso guia de resolução de problemas (ver [Resolução de Problemas do seu Azure-SSIS IR).](./ssis-integration-runtime-management-troubleshoot.md)

![Monitorize o seu Azure-SSIS IR - ERROR tile](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Monitorize o tempo de integração Azure-SSIS com o Azure Monitor

Para monitorizar o seu Azure-SSIS IR com o Azure Monitor, consulte [as operações de Monitorização do SSIS com o Azure Monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o tempo de integração do Azure-SSIS

Consulte os seguintes artigos para saber mais sobre o tempo de integração do Azure-SSIS:

- [Tempo de execução da integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceptuais sobre os tempos de integração em geral, incluindo o Azure-SSIS IR. 
- [Tutorial: implementar pacotes do SSIS no Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar o seu Azure-SSIS IR e utilizar a Base de Dados Azure SQL para hospedar o catálogo SSIS (SSISDB). 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se sobre o tutorial e fornece instruções sobre a utilização de Azure SQL Managed Instance para hospedar o SSISDB. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como iniciar, parar ou eliminar o seu Azure-SSIS IR. Também mostra como escaloná-lo adicionando mais nós. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece instruções sobre a adesão do seu Azure-SSIS IR a uma rede virtual.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos de monitorização dos gasodutos de diferentes formas: 

- [Quickstart: criar uma fábrica de dados.](quickstart-create-data-factory-dot-net.md)
- [Utilize o Monitor Azure para monitorizar os oleodutos da Fábrica de Dados](monitor-using-azure-monitor.md)
