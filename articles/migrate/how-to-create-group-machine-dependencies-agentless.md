---
title: Configurar análise de dependência sem agente na Azure Migrate
description: Configure a análise de dependência sem agente em Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7966750d7c3e0f12bb9404a4d78bbc27e4075c52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786588"
---
# <a name="analyze-server-dependencies-agentless"></a>Analisar as dependências do servidor (sem agente)

Este artigo descreve como configurar uma análise de dependência sem agente usando a ferramenta Azure Migrate:Discovery e assessment. [A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar e a compreender dependências entre servidores para avaliação e migração para Azure.

> [!IMPORTANT]
> A análise da dependência de agentes está atualmente em pré-visualização para servidores em execução no seu ambiente VMware, descobertos com a ferramenta Azure Migrate:Discovery e assessment.
> Esta pré-visualização é coberta pelo suporte ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitações atuais

- Na visão de análise de dependência, não é possível adicionar ou remover um servidor de um grupo.
- Um mapa de dependência de um grupo de servidores não está disponível atualmente.
- Num projeto da Azure Migrate, a recolha de dados de dependência pode ser ativada simultaneamente para 1000 servidores. Pode analisar um maior número de servidores sequenciando em lotes de 1000 servidores.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de que [criou um projeto](./create-manage-projects.md) com a ferramenta Azure Migrate:Discovery e a ferramenta de avaliação adicionada ao mesmo.
- Reveja [os requisitos da VMware](migrate-support-matrix-vmware.md#vmware-requirements) para realizar análises de dependência.
- [Reveja os requisitos do aparelho](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurar o aparelho.
- [Rever os requisitos de análise de dependência](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) antes de permitir a análise da dependência nos servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implementar e configurar o aparelho Azure Migrate

1. [Reveja](migrate-appliance.md#appliance---vmware) os requisitos para a colocação do aparelho Azure Migrate.
2. Reveja os URLs Azure que o aparelho terá de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Reveja](migrate-appliance.md#collected-data---vmware) os dados que o aparelho recolhe durante a descoberta e avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso à porta para o aparelho.
5. [Desloque o aparelho Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implementar o aparelho, descarregue e importe um modelo OVA em VMware para criar um servidor em execução no seu servidor vCenter. Depois de colocar o aparelho, é necessário registá-lo com o projeto e configurá-lo para iniciar a descoberta.
6. Ao configurar o aparelho, tem de especificar o seguinte no gestor de configuração do aparelho:
    - Os detalhes do servidor vCenter ao qual pretende ligar.
    - vCenter Server credenciais de mira para descobrir os servidores no seu ambiente VMware.
    - Credenciais de servidor que podem ser domínio/ Windows (não-domínio)/ Credenciais Linux (não-domínio). [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.

## <a name="verify-permissions"></a>Verificar permissões

- É necessário [criar uma conta de leitura do vCenter Server apenas](./tutorial-discover-vmware.md#prepare-vmware) para a descoberta e avaliação. A conta apenas de leitura necessita de privilégios habilitados para operações **de hóspedes de máquinas virtuais,**  >  de forma a interagir com os servidores para recolher dados de dependência.
- Precisa de uma conta de utilizador para que o Azure Migrate possa aceder ao servidor para recolher dados de dependência. [Saiba mais](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sobre os requisitos de conta para servidores Windows e Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Adicione credenciais e inicie a descoberta

1. Abra o gestor de configuração do aparelho, complete as verificações e o registo do aparelho.
2. Navegue para o painel **de credenciais de Gestão e fontes de descoberta.**
1.  No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para fornecer credenciais para a conta vCenter Server que o aparelho utilizará para descobrir servidores em execução no servidor vCenter.
1. No **passo 2: Fornecer detalhes do servidor vCenter**, clique na **fonte de descoberta adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** do painel de identificação do servidor vCenter :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 no gestor de configuração do aparelho para obter detalhes do servidor vCenter":::
1. No **Passo 3: Forneça credenciais de servidor para realizar inventário de software, análise de dependência de agente e descoberta de instâncias e bases de dados do SQL Server**, clique em Adicionar **credenciais** para fornecer várias credenciais de servidor para iniciar o inventário de software.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor vCenter.

 Após a conclusão do vCenter Server, o aparelho inicia a descoberta de aplicações, funções e funcionalidades instaladas (inventário de software). Durante o inventário do software, as credenciais de servidores adicionados serão iteradas contra servidores e validadas para análise de dependência de agente. Pode ativar a análise de dependência de agentes para servidores a partir do portal. Apenas os servidores onde a validação é bem sucedida podem ser selecionados para permitir a análise da dependência de agentes.

## <a name="start-dependency-discovery"></a>Iniciar a descoberta da dependência

Selecione os servidores em que deseja permitir a descoberta da dependência.

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
2. Escolha o **nome do Aparelho** cuja descoberta pretende rever.
1. Pode ver o estado de validação dos servidores sob a coluna **Dependencies (sem agente).**
1. Clique na **análise da Dependência.**
1. Clique **em Adicionar servidores**.
1. Na página **'Adicionar servidores',** selecione os servidores onde pretende ativar a análise da dependência. Pode ativar o mapeamento de dependência apenas nos servidores onde a validação foi bem sucedida. O próximo ciclo de validação funcionará 24 horas após a última hora de validação.
1. Depois de selecionar os servidores, clique em **Adicionar servidores**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Iniciar análise de dependência":::

Pode visualizar dependências cerca de seis horas após permitir a análise da dependência nos servidores. Se pretender simultaneamente ativar vários servidores para análise de dependência, pode utilizar [o PowerShell](#start-or-stop-dependency-analysis-using-powershell) para o fazer.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
1. Escolha o **nome do Aparelho** cuja descoberta pretende rever.
1. Procure o servidor cujas dependências, deseja rever.
1. Sob a coluna **Dependências (sem agente),** clique **em Ver Dependências**
1. Altere o período de tempo para o qual pretende visualizar o mapa utilizando o desauso da duração do **tempo.**
1. Expandir o grupo **Cliente** para listar os servidores com uma dependência do servidor selecionado.
1. Expanda o grupo **Port** para listar os servidores que têm uma dependência do servidor selecionado.
1. Para navegar para a vista do mapa de qualquer um dos servidores dependentes, clique no nome do servidor > **Mapa do servidor de carga** Expandir o grupo de portas do servidor e carregar o mapa do 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="servidor":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Expandir grupo de clientes":::

8. Expanda o servidor selecionado para visualizar detalhes de nível de processo para cada dependência.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Expandir servidor para mostrar processos":::

> [!NOTE]
> A informação do processo para uma dependência nem sempre está disponível. Se não estiver disponível, a dependência é representada com o processo marcado como "Processo desconhecido".

## <a name="export-dependency-data"></a>Dados de dependência das exportações

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
2. Clique na **análise da Dependência.**
3. Clique **nas dependências de aplicações de exportação.**
4. Na página **de dependências de aplicações Export,** escolha o nome do aparelho que está a descobrir os servidores pretendidos.
5. Selecione a hora de início e a hora de fim. Note que só pode descarregar os dados nos últimos 30 dias.
6. Clique **na dependência da exportação.**

Os dados de dependência são exportados e descarregados num formato CSV. O ficheiro descarregado contém os dados de dependência em todos os servidores ativados para análise de dependência. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Dependências das exportações":::

### <a name="dependency-information"></a>Informação sobre dependência

Cada linha no CSV exportado corresponde a uma dependência observada na faixa horária especificada.

O quadro seguinte resume os campos do CSV exportado. Note que os campos de nome, aplicação e processo do servidor são preenchidos apenas para servidores que tenham uma análise de dependência sem agentes ativada.

**Nome do campo** | **Detalhes**
--- | --- 
Horário | O horário durante o qual a dependência foi observada. <br/> Os dados de dependência são capturados ao longo de 6 horas de slots atualmente.
Nome do servidor de origem | Nome do servidor de origem 
Aplicação de origem | Nome da aplicação no servidor de origem  
Processo de origem | Nome do processo no servidor de origem  
Nome do servidor de destino | Nome do servidor de destino
IP de destino | Endereço IP do servidor de destino
Aplicação de destino | Nome da aplicação no servidor de destino
Processo de destino | Nome do processo no servidor de destino  
Porta de destino | Número de porta no servidor de destino

## <a name="stop-dependency-discovery"></a>Parar a descoberta da dependência

Selecione os servidores nos quais pretende parar a descoberta da dependência.

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
1. Escolha o **nome do Aparelho** cuja descoberta pretende rever.
1. Clique na **análise da Dependência.**
1. Clique **em Remover servidores**.
1. Na página **'Remover servidores',** selecione o servidor que pretende parar para análise de dependência.
1. Depois de selecionar os servidores, clique em **Remover servidores**.

Se quiser parar a dependência simultaneamente em vários servidores, pode utilizar [o PowerShell](#start-or-stop-dependency-analysis-using-powershell) para o fazer.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Iniciar ou parar a análise da dependência usando o PowerShell

Descarregue o módulo PowerShell a partir de [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) repo no GitHub.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

1. Inicie sessão na sua subscrição Azure utilizando o Connect-AzAccount cmdlet.

    ```PowerShell
    Connect-AzAccount
    ```
    Se utilizar o Governo Azure, utilize o seguinte comando.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Selecione a subscrição na qual criou o projeto 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importe o módulo powershell AzMig_Dependencies descarregado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Ativar ou desativar a recolha de dados de dependência

1. Obtenha a lista de servidores descobertos no seu projeto usando os seguintes comandos. No exemplo abaixo, o nome do projeto é FabrikamDemoProject, e o grupo de recursos a que pertence é FabrikamDemoRG. A lista de servidores será guardada em FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    No ficheiro, pode ver o nome de exibição do servidor, o estado atual da recolha de dependência e o ID ARM de todos os servidores descobertos. 

2. Para ativar ou desativar as dependências, crie um ficheiro CSV de entrada. O ficheiro é necessário para ter uma coluna com cabeçalho "ARM ID". Quaisquer cabeçalhos adicionais no ficheiro CSV serão ignorados. Pode criar o CSV utilizando o ficheiro gerado no passo anterior. Crie uma cópia do ficheiro que retenha os servidores que pretende ativar ou desativar dependências. 

    No exemplo seguinte, a análise de dependência está a ser ativada na lista de servidores no ficheiro de entrada FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    No exemplo seguinte, a análise de dependência está a ser desativada na lista de servidores do ficheiro de entrada FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualizar ligações de rede no Power BI

O Azure Migrate oferece um modelo power BI que pode utilizar para visualizar as ligações de rede de muitos servidores ao mesmo tempo, e filtrar por processo e servidor. Para visualizar, carregue o Power BI com dados de dependência de acordo com as instruções abaixo.

1. Descarregue o módulo PowerShell e o modelo Power BI a partir de [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) repo no GitHub.

2. Faça login em Azure usando as instruções abaixo: 
    - Inicie sessão na sua subscrição Azure utilizando o Connect-AzAccount cmdlet.

        ```PowerShell
        Connect-AzAccount
        ```

    - Se utilizar o Governo Azure, utilize o seguinte comando.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Selecione a subscrição na qual criou o projeto

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importe o módulo powershell AzMig_Dependencies descarregado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Execute o seguinte comando. Este comando descarrega os dados de dependências num CSV e processa-os para gerar uma lista de dependências únicas que podem ser usadas para visualização no Power BI. No exemplo abaixo o nome do projeto é FabrikamDemoProject, e o grupo de recursos a que pertence é FabrikamDemoRG. As dependências serão descarregadas para servidores descobertos pela FabrikamAppliance. As dependências únicas serão salvas em FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Abra o modelo de Power BI descarregado

6. Carregue os dados de dependência descarregados no Power BI.
    - Abra o modelo no Power BI.
    - Clique em **Obter Dados** na barra de ferramentas. 
    - Escolha **Texto/CSV** a partir de fontes de dados comuns.
    - Escolha o ficheiro de dependências descarregado.
    - Clique **em Carregar**.
    - Verá que uma tabela é importada com o nome do ficheiro CSV. Dá para ver a mesa na barra de campo à direita. Mude o nome para AzMig_Dependencies
    - Clique em atualizar a partir da barra de ferramentas.

    O gráfico de Ligações de Rede e o nome do servidor Source, nome do servidor de destino, nome do processo de origem, cortadores de nomes de processo de destino devem iluminar-se com os dados importados.

7. Visualizar o mapa das ligações de rede filtrando por servidores e processos. Guarde o ficheiro.

## <a name="next-steps"></a>Passos seguintes

[Servidores de grupo](how-to-create-a-group.md) para avaliação.