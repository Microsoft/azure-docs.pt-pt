---
title: Configurar uma análise de dependência sem agente na avaliação do servidor Azure Migrate
description: Configure a análise de dependência sem agente na Avaliação do Servidor Azure Migrate.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 8893b0f41bb7a06c02a6c2aa28002980144b582a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337698"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analisar dependências de máquinas (sem agente)

Este artigo descreve como configurar uma análise de dependência sem agente no Azure Migrate:Server Assessment. [A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar e a compreender dependências entre máquinas para avaliação e migração para Azure.


> [!IMPORTANT]
> A visualização de dependência de agentes está atualmente em pré-visualização para VMware VMs descobertos com a ferramenta Azure Migrate:Server Assessment.
> As características podem ser limitadas ou incompletas.
> Esta pré-visualização é coberta pelo suporte ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitações atuais

- Na visão de análise de dependência, não é possível adicionar ou remover um servidor de um grupo.
- Um mapa de dependência de um grupo de servidores não está disponível atualmente.
- Num projeto da Azure Migrate, a recolha de dados de dependência pode ser configurada simultaneamente para 1000 servidores. Pode analisar um maior número de servidores sequenciando em lotes de 1000.

## <a name="before-you-start"></a>Antes de começar

- [Reveja](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operativos suportados e as permissões necessárias.
- Certifique-se de:
    - Tenha um projeto Azure Migrate. Se não o fizeres, [cria](how-to-add-tool-first-time.md) um agora.
    - Verifique se [adicionou](how-to-assess.md) a ferramenta Azure Migrate:Server Assessment ao projeto.
    - Crie um [aparelho Azure Migrate](migrate-appliance.md) para descobrir máquinas no local. [Configurar um aparelho](how-to-set-up-appliance-vmware.md) para VMware VMs. O aparelho descobre máquinas no local e envia metadados e dados de desempenho para a Azure Migrate:Avaliação do servidor.
- Verifique se as Ferramentas VMware (mais tarde de 10.2) estão instaladas em cada VM que pretende analisar.


## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de utilizador para a descoberta

Crie uma conta de utilizador para que a Avaliação do Servidor possa aceder ao VM para descobrir dependências. [Saiba mais](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sobre os requisitos de conta para Windows e Linux VMs.


## <a name="add-the-user-account-to-the-appliance"></a>Adicione a conta de utilizador ao aparelho

Adicione a conta de utilizador ao aparelho.

1. Abra a aplicação de gestão do aparelho. 
2. Navegue para o painel **de detalhes do Provide vCenter.**
3. Na **aplicação Discover e dependências em VMs,** clique em **Adicionar credenciais**
3. Escolha o **sistema Operativo,** forneça um nome amigável para a conta e a **User name** / **palavra-passe** do nome de utilizador
6. Clique em **Guardar**.
7. Clique **em Guardar e iniciar a descoberta.**

    ![Adicionar conta de utilizador VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar a descoberta da dependência

Escolha as máquinas em que deseja permitir a descoberta da dependência. 

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone **de análise de Dependência.**
3. Clique **em Adicionar servidores**.
4. Na página **'Adicionar servidores',** escolha o aparelho que está a descobrir as máquinas relevantes.
5. Na lista de máquinas, selecione as máquinas.
6. Clique **em Adicionar servidores**.

    ![Iniciar a descoberta da dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Pode visualizar dependências cerca de seis horas após iniciar a descoberta da dependência. Se pretender ativar várias máquinas, poderá utilizar [o PowerShell](#start-or-stop-dependency-discovery-using-powershell) para o fazer.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Procure a máquina que deseja ver.
3. Na coluna **Dependências,** clique **em Ver Dependências**
4. Altere o período de tempo para o qual pretende visualizar o mapa utilizando o desauso da duração do **tempo.**
5. Expandir o grupo **Cliente** para listar as máquinas com uma dependência da máquina selecionada.
6. Expanda o grupo **Port** para listar as máquinas que têm uma dependência da máquina selecionada.
7. Para navegar para a vista do mapa de qualquer uma das máquinas dependentes, clique no nome da máquina > **mapa do servidor carregar**

    ![Expandir o grupo de portas do servidor e carregar o mapa do servidor](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expandir a máquina selecionada para visualizar detalhes de nível de processo para cada dependência.

    ![Expandir servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> A informação do processo para uma dependência nem sempre está disponível. Se não estiver disponível, a dependência é representada com o processo marcado como "Processo desconhecido".

## <a name="export-dependency-data"></a>Dados de dependência das exportações

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone **de análise de Dependência.**
3. Clique em **Dependências de Aplicações de Exportação.**
4. Na página **'Dependências de Aplicações de Exportação',** escolha o aparelho que está a descobrir as máquinas relevantes.
5. Selecione a hora de início e a hora de fim. Note que só pode descarregar os dados nos últimos 30 dias.
6. Clique **na dependência da exportação.**

Os dados de dependência são exportados e descarregados num formato CSV. O ficheiro descarregado contém os dados de dependência em todas as máquinas ativadas para análise de dependência. 

![Dependências das exportações](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informação sobre dependência

Cada linha no CSV exportado corresponde a uma dependência observada na faixa horária especificada. 

O quadro seguinte resume os campos do CSV exportado. Note que os campos de nome, aplicação e processo do servidor são preenchidos apenas para servidores que tenham uma análise de dependência sem agentes ativada.

**Nome do campo** | **Detalhes**
--- | --- 
Horário | O horário durante o qual a dependência foi observada. <br/> Os dados de dependência são capturados ao longo de 6 horas de slots atualmente.
Nome do servidor de origem | Nome da máquina de origem 
Aplicação de origem | Nome da aplicação na máquina de origem 
Processo de origem | Nome do processo na máquina de origem 
Nome do servidor de destino | Nome da máquina de destino
IP de destino | Endereço IP da máquina de destino
Aplicação de destino | Nome da aplicação na máquina de destino
Processo de destino | Nome do processo na máquina de destino 
Porta de destino | Número de porta na máquina de destino


## <a name="stop-dependency-discovery"></a>Parar a descoberta da dependência

Escolha as máquinas em que pretende parar a descoberta da dependência. 

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Clique no ícone **de análise de Dependência.**
3. Clique **em Remover servidores**.
3. Na página **'Remover servidores',** escolha o **aparelho** que está a descobrir os VMs em que procura impedir a descoberta da dependência.
4. Na lista de máquinas, selecione as máquinas.
5. Clique **em Remover servidores**.

Se quiser parar a dependência de várias máquinas, poderá utilizar [o PowerShell](#start-or-stop-dependency-discovery-using-powershell) para o fazer.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Iniciar ou parar a descoberta da dependência usando o PowerShell

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

2. Selecione a subscrição na qual criou o projeto Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importe o módulo powershell AzMig_Dependencies descarregado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Ativar ou desativar a recolha de dados de dependência

1. Obtenha a lista de VMware VMs descobertos no seu projeto Azure Migrate utilizando os seguintes comandos. No exemplo abaixo, o nome do projeto é FabrikamDemoProject, e o grupo de recursos a que pertence é FabrikamDemoRG. A lista de máquinas será guardada em FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    No ficheiro, pode ver o nome de exibição VM, o estado atual da recolha de dependência e o ID ARM de todos os VMs descobertos. 

2. Para ativar ou desativar as dependências, crie um ficheiro CSV de entrada. O ficheiro é necessário para ter uma coluna com cabeçalho "ARM ID". Quaisquer cabeçalhos adicionais no ficheiro CSV serão ignorados. Pode criar o CSV utilizando o ficheiro gerado no passo anterior. Crie uma cópia do ficheiro que retenha os VMs que pretende ativar ou desativar dependências. 

    No exemplo seguinte, a análise da dependência está a ser ativada na lista de VMs no ficheiro de entrada FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    No exemplo seguinte, a análise da dependência está a ser desativada na lista de VMs no ficheiro de entrada FabrikamDemo_VMs_Disable.csv.

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

- Selecione a subscrição na qual criou o projeto Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importe o módulo powershell AzMig_Dependencies descarregado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Execute o seguinte comando. Este comando descarrega os dados de dependências num CSV e processa-os para gerar uma lista de dependências únicas que podem ser usadas para visualização no Power BI. No exemplo abaixo o nome do projeto é FabrikamDemoProject, e o grupo de recursos a que pertence é FabrikamDemoRG. As dependências serão descarregadas para máquinas descobertas pela FabrikamAppliance. As dependências únicas serão salvas em FabrikamDemo_Dependencies.csv

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

[Máquinas de grupo](how-to-create-a-group.md) para avaliação.
