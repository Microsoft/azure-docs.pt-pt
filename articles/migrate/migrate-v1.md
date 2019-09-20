---
title: Trabalhar com a versão anterior do migrações para Azure | Microsoft Docs
description: Fornece um resumo para trabalhar com a versão antiga do migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c63d63e57a23963f17b6773f244973b051b57eb
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162461"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Trabalhar com a versão anterior do migrações para Azure

Este artigo fornece informações sobre como trabalhar com a versão anterior do migrações para Azure.


Há duas versões do serviço migrações para Azure:

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: Se você estiver usando a versão anterior do migrações para Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Se você ainda precisar usar projetos de migrações para Azure criados na versão anterior, isso será o que você pode e não pode fazer:
    - Você não pode mais criar projetos de migração.
    - Recomendamos que você não execute novas descobertas.
    - Você ainda pode acessar projetos existentes.
    - Você ainda pode executar avaliações.
    

## <a name="upgrade-between-versions"></a>Atualização entre versões

Não é possível atualizar projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um novo projeto](how-to-add-tool-first-time.md)de migrações para Azure e adicionar ferramentas de avaliação e migração a ele.

## <a name="find-projects-from-previous-version"></a>Localizar projetos da versão anterior

Localize projetos da versão anterior da seguinte maneira:

1. Na portal do Azure > **todos os serviços**, procure e selecione migrações para **Azure**. 
2. No painel migrações para Azure, há uma notificação e um link para acessar projetos antigos de migrações para Azure.
3. Clique no link para abrir projetos v1.


## <a name="create-an-assessment"></a>Criar uma avaliação

Depois que as VMs forem descobertas no portal, você as agrupará e criará avaliações.

- Você pode criar imediatamente como avaliações locais imediatamente após as VMs serem descobertas no Portal.
- Para avaliações baseadas em desempenho, recomendamos que você aguarde pelo menos um dia antes de criar uma avaliação baseada em desempenho para obter recomendações de tamanho confiável.

Crie uma avaliação da seguinte maneira:

1. Na página **Descrição geral** do projeto, clique em **+ Criar avaliação**.
2. Clique em **Ver tudo** para rever as propriedades de avaliação.
3. Crie o grupo e especifique um nome.
4. Selecione as máquinas que quer adicionar ao grupo.
5. Clique em **Criar Avaliação**, para criar o grupo e a avaliação.
6. Após a criação da avaliação, visualize-a em **Descrição geral** > **Dashboard**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.

Se quiser atualizar uma avaliação existente com os dados de desempenho mais recentes, poderá utilizar o comando **Recalcular** na avaliação para a atualizar.

## <a name="review-an-assessment"></a>Examinar uma avaliação 

Uma avaliação tem três estágios:

- Uma avaliação começa com uma análise de adequação para descobrir se as máquinas são compatíveis no Azure.
- Estimativas de dimensionamento.
- Estimativa de custo mensal.

Um computador só se moverá para um estágio posterior se ele passar o anterior. Por exemplo, se um computador falhar na verificação de adequação, ele será marcado como inadequado para o Azure, e o dimensionamento e o custo não serão feitos.


### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

A vista de preparação para o Azure na avaliação mostra o estado de preparação de cada VM.

**Preparação** | **Status** | **Detalhes**
--- | --- | ---
Preparado para o Azure | Não há problemas de compatibilidade. O computador pode ser migrado no estado em que se encontra para o Azure e será inicializado no Azure com o suporte completo do Azure. | Para as VMs que estão prontas, o Azure Migrate recomenda um tamanho de VM no Azure.
Condicionalmente preparado para o Azure | O computador pode ser inicializado no Azure, mas pode não ter o suporte completo do Azure. Por exemplo, um computador com uma versão mais antiga do Windows Server que não tem suporte no Azure. | As migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.
Não está pronto para o Azure |  A VM não será inicializada no Azure. Por exemplo, se uma VM tiver um disco com mais de 4 TB, ela não poderá ser hospedada no Azure. | As migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.
Preparação desconhecida | As migrações para Azure não podem identificar a preparação do Azure, geralmente porque os dados não estão disponíveis.


#### <a name="azure-vm-properties"></a>Propriedades da VM do Azure
A preparação leva em consideração uma série de propriedades da VM, para identificar se a VM pode ser executada no Azure.


**Propriedade** | **Detalhes** | **Preparação**
--- | --- | ---
**Tipo de inicialização** | BIOS com suporte. UEFI sem suporte. | Condicionalmente pronto se o tipo de inicialização for UEFI.
**Núcleos** | O núcleo de máquinas < = o número máximo de núcleos (128) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão os núcleos utilizados.<br/>Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as migrações para Azure usarão os núcleos alocados, sem aplicar o fator de conforto. | Pronto se for menor ou igual a limites.
**Memória** | O tamanho da memória do computador < = a memória máxima (3892 GB no Azure série&nbsp;M Standard_M128m<sup>2</sup>) para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão a memória utilizada.<br/><br/>Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico, a memória alocada será usada, sem aplicar o fator de conforto.<br/><br/> | Pronto se estiver dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos anexados ao computador deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Pronto se estiver dentro dos limites.
**Redes** | Um computador deve ter 32 ou menos NICs conectados a ele. | Pronto se estiver dentro dos limites.

#### <a name="guest-operating-system"></a>Sistema operacional convidado

Juntamente com as propriedades da VM, as migrações para Azure também examinam o sistema operacional convidado da VM local para identificar se a VM pode ser executada no Azure.

- As migrações para Azure consideram o sistema operacional especificado em vCenter Server.
- Como a descoberta feita pela migração do Azure é baseada em dispositivo, ela não tem uma maneira de verificar se o sistema operacional em execução dentro da VM é o mesmo especificado na vCenter Server.

A lógica a seguir é usada.

**Sistema Operacional** | **Detalhes** | **Preparação**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 e todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2008 R2 e todos os SPs | O Azure fornece suporte completo.| Preparado para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Sem suporte e precisa de um contrato de [suporte personalizado (CSA)](https://aka.ms/WSosstatement) para dar suporte no Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Sem suporte. O computador pode ser inicializado no Azure, mas nenhum suporte do sistema operacional é fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável atualizar o sistema operacional antes de migrar para o Azure.
Windows Client 7, 8 e 10 | O Azure fornece suporte [apenas com a assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente preparado para o Azure
Área de trabalho do Windows 10 pro | O Azure fornece suporte com [direitos de hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Sem suporte. O computador pode ser inicializado no Azure, mas nenhum suporte do sistema operacional é fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável atualizar o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais Linux podem ser inicializados no Azure, mas é recomendável atualizar o sistema operacional para uma versão endossada, antes de migrar para o Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, Apple Mac OS, etc., FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. O computador pode ser inicializado no Azure, mas nenhum suporte do sistema operacional é fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável instalar um sistema operacional com suporte antes de migrar para o Azure.  
Sistema operacional especificado como **outro** no vCenter Server | As migrações para Azure não podem identificar o sistema operacional nesse caso. | Preparação desconhecida. Verifique se o sistema operacional em execução dentro da VM tem suporte no Azure.
sistemas operacionais de 32 bits | O computador pode ser inicializado no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional do computador do sistema operacional de 32 bits para o sistema operacional de 64 bits antes de migrar para o Azure.


### <a name="review-sizing"></a>Revisar dimensionamento

 A recomendação de tamanho de migrações para Azure depende do critério de dimensionamento especificado nas propriedades de avaliação.

- Se o dimensionamento for baseado em desempenho, a recomendação de tamanho considerará o histórico de desempenho das VMs (CPU e memória) e discos (IOPS e taxa de transferência).
- Se o critério de dimensionamento for ' como local ', a recomendação de tamanho no Azure será baseada no tamanho da VM local. O dimensionamento de disco é baseado no tipo de armazenamento especificado nas propriedades de avaliação (o padrão é discos Premium). As migrações para Azure não consideram os dados de desempenho para a VM e os discos.

### <a name="review-cost-estimates"></a>Examinar estimativas de custo

As estimativas de custo mostram o custo total de computação e armazenamento da execução das VMs no Azure, juntamente com os detalhes de cada computador.

- As estimativas de custo são calculadas usando a recomendação de tamanho para um computador de VM, seus discos e as propriedades de avaliação.
- Os custos mensais estimados para computação e armazenamento são agregados para todas as VMs no grupo.
- A estimativa de custo é para executar a VM local como VMs de IaaS (infraestrutura como serviço) do Azure. As migrações para Azure não consideram a PaaS (plataforma como serviço) ou os custos de SaaS (software como serviço).

### <a name="review-confidence-rating-performance-based-assessment"></a>Examinar a classificação de confiança (avaliação baseada em desempenho)

Cada avaliação baseada em desempenho é associada a uma classificação de confiança.

- Uma classificação de confiança varia de uma estrela a cinco estrelas (uma estrela sendo a mais baixa e cinco estrelas a mais alta).
- A classificação de confiança é atribuída a uma avaliação, com base na disponibilidade dos pontos de dados necessários para computar a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- A classificação de confiança não está disponível para as avaliações locais "no estado em que se encontram".

Para o dimensionamento baseado em desempenho, as migrações para Azure precisam do seguinte:
- Dados de utilização da CPU.
- Dados de memória da VM.
- Para cada disco anexado à VM, ele precisa dos dados de taxa de transferência e IOPS de disco.
- Para cada adaptador de rede anexado a uma VM, as migrações para Azure precisam da entrada/saída de rede.
- Se qualquer um dos itens acima não estiver disponível, as recomendações de tamanho (e, portanto, classificações de confiança) podem não ser confiáveis.


Dependendo da porcentagem de pontos de dados disponíveis, as classificações de confiança possíveis são resumidas na tabela.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Problemas de avaliação que afetam as classificações de confiança

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a vários motivos:

- Você não fez o profile do seu ambiente durante a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta ou todos os pontos de dados a serem coletados.
- Algumas VMs foram desligadas durante o período para o qual a avaliação foi calculada. Se alguma VM fosse desligada por parte da duração, as migrações para Azure não podem coletar dados de desempenho para esse período.
- Algumas VMs foram criadas entre durante o período de cálculo da avaliação. Por exemplo, se você criar uma avaliação usando o histórico de desempenho do mês passado, mas criar várias VMs no ambiente por semana atrás, o histórico de desempenho das novas VMs não será por toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de cinco estrelas, aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcule a avaliação. Se você não puder, o dimensionamento baseado em desempenho poderá não ser confiável. Se você não quiser recalcular, recomendamos mudar para como o dimensionamento local, alterando as propriedades de avaliação.



## <a name="create-groups-using-dependency-visualization"></a>Criar grupos usando a visualização de dependência

Além de criar grupos manualmente, você pode criar grupos usando a visualização de dependência.
- Normalmente, você usa esse método quando deseja avaliar grupos com níveis mais altos de confiança por meio da verificação de dependências de máquina, antes de executar uma avaliação.
- A visualização de dependência pode ajudá-lo a planejar efetivamente sua migração para o Azure. Ele ajuda a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure.
- Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos e identificar se um sistema em execução ainda está atendendo aos usuários ou é um candidato para encerramento em vez de migração.
- As migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência.

> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

Para configurar a visualização de dependência, você associa um espaço de trabalho Log Analytics a um projeto de migrações para Azure, instala agentes em computadores para os quais deseja visualizar dependências e, em seguida, cria grupos usando informações de dependência. 



### <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics

Para usar a visualização de dependência, você associa um espaço de trabalho Log Analytics a um projeto de migração. Você só pode criar ou anexar um espaço de trabalho na mesma assinatura em que o projeto de migração é criado.

1. Para anexar um espaço de trabalho Log Analytics a um projeto, em **visão geral**, > **Essentials**, clique em **requer configuração**.
2. Você pode criar um novo espaço de trabalho ou anexar um existente:
  - Para criar um novo espaço de trabalho, especifique um nome. O espaço de trabalho é criado em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Ao anexar um espaço de trabalho existente, você pode escolher entre todos os espaços de trabalho disponíveis na mesma assinatura que o projeto de migração. Somente os espaços de trabalho são listados, que foram criados em uma [região mapa do serviço com suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Para anexar um espaço de trabalho, verifique se você tem acesso de ' leitor ' ao espaço de trabalho.

> [!NOTE]
> Não é possível alterar o espaço de trabalho associado a um projeto de migração.

### <a name="download-and-install-vm-agents"></a>Baixar e instalar agentes de VM

Depois de configurar um espaço de trabalho, baixe e instale agentes em cada computador local que você deseja avaliar. Além disso, se você tiver computadores sem conectividade com a Internet, será necessário baixar e instalar [log Analytics gateway](../azure-monitor/platform/gateway.md) neles.

1. Em **visão geral**, clique em **gerenciar** > **computadores**e selecione o computador necessário.
2. Na coluna **dependências** , clique em **instalar agentes**.
3. Na página **dependências** , baixe e instale o Microsoft Monitoring Agent (MMA) e o agente de dependência em cada VM que você deseja avaliar.
4. Copie o ID e a chave da área de trabalho. Você precisará delas quando instalar o MMA no computador local.

> [!NOTE]
> Para automatizar a instalação de agentes, você pode usar uma ferramenta de implantação, como System Center Configuration Manager ou uma ferramenta de parceiro como, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), que fornece uma solução de implantação de agente para migrações para Azure.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalar o agente MMA em um computador Windows

Para instalar o agente em um computador Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Em **pasta de destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de instalação do agente**, selecione **Azure log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho log Analytics. Cole a ID do espaço de trabalho e a chave que você copiou do Portal. Clique em **Seguinte**.

Você pode instalar o agente da linha de comando ou usando um método automatizado, como System Center Configuration Manager. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre como usar esses métodos para instalar o agente do MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalar o agente MMA em um computador Linux

Para instalar o agente em um computador Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando o SCP/SFTP.
2. Instale o pacote usando o argumento--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sobre a lista de suporte de sistemas operacionais Linux pelo MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instalar o agente do MMA em um computador monitorado pelo Operations Manager

Para máquinas monitorizadas pelo System Center Operations Manager 2012 R2 ou posterior, não é preciso instalar o agente MMA. Mapa do Serviço integra-se com o Operations Manager MMA para coletar os dados de dependência necessários. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). O agente de dependência precisa ser instalado.

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência

1. Para instalar o Dependency Agent em um computador Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Dependency Agent em um computador Linux, instale como raiz usando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- Saiba mais sobre o [suporte do agente de dependência](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) para os sistemas operacionais Windows e Linux.
- [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sobre como você pode usar scripts para instalar o Dependency Agent.

>[!NOTE]
> O artigo Azure Monitor para VMs referenciado para fornecer uma visão geral dos pré-requisitos e métodos do sistema para implantar o agente de dependência também se aplica à solução de Mapa do Serviço.

### <a name="create-a-group-with-dependency-mapping"></a>Criar um grupo com mapeamento de dependência

1. Depois de instalar os agentes, acesse o portal e clique em **gerenciar** > **computadores**.
2. Pesquise o computador onde você instalou os agentes.
3. A coluna dependências para o computador agora deve ser mostrada como **Exibir dependências**. Clique na coluna para exibir as dependências do computador.
4. O mapa de dependências da máquina mostra os seguintes detalhes:
    - Conexões TCP de entrada (clientes) e saída (servidores) para/do computador
        - Os computadores dependentes que não têm o MMA e o agente de dependência instalados são agrupados por números de porta.
        - Os computadores dependentes que têm o MMA e o agente de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro do computador, você pode expandir cada caixa da máquina para exibir os processos
    - As propriedades da máquina, incluindo o FQDN, o sistema operacional, o endereço MAC, são mostradas. Você pode clicar em cada caixa da máquina para exibir detalhes.

4. Você pode exibir dependências para durações de tempo diferentes clicando no tempo duração no rótulo intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo de tempo ou especificar datas de início e de término e duração.

   > [!NOTE]
   >    Há suporte para um intervalo de tempo de até uma hora. Use os logs de Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

5. Depois de identificar os computadores dependentes que você deseja agrupar, use Ctrl + clique para selecionar vários computadores no mapa e clique em **Agrupar computadores**.
6. Especifique um nome de grupo. Verifique se os computadores dependentes são descobertos pelas migrações para Azure.

    > [!NOTE]
    > Se um computador dependente não for descoberto pelas migrações para Azure, você não poderá adicioná-lo ao grupo. Para adicionar esses computadores ao grupo, você precisa executar o processo de descoberta novamente com o escopo correto em vCenter Server e garantir que o computador seja descoberto pelas migrações para Azure.  

7. Se você quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Depois que o grupo é criado, é recomendável instalar agentes em todos os computadores do grupo e refinar o grupo visualizando a dependência do grupo inteiro.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Consultar dados de dependência de logs de Azure Monitor

Os dados de dependência capturados pelo Mapa do Serviço estão disponíveis para consulta no espaço de trabalho Log Analytics associado ao projeto de migrações para Azure. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados de mapa do serviço para consultar em logs de Azure monitor. 

Para executar as consultas do Kusto:

1. Depois de instalar os agentes, acesse o portal e clique em **visão geral**.
2. Em **visão geral**, vá para a seção **Essentials** do projeto e clique no nome do espaço de trabalho fornecido ao lado de **espaço de trabalho do OMS**.
3. Na página log Analytics espaço de trabalho, clique em**logs** **gerais** > .
4. Escreva sua consulta para coletar dados de dependência usando logs de Azure Monitor. Encontre consultas de exemplo na próxima seção.
5. Execute sua consulta clicando em executar. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como escrever consultas Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Consultas de logs de Azure Monitor de exemplo

Veja a seguir as consultas de exemplo que você pode usar para extrair dados de dependência. Você pode modificar as consultas para extrair seus pontos de dados preferenciais. Uma lista completa dos campos em registros de dados de dependência está disponível [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Encontre mais consultas de exemplo [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir conexões de entrada em um conjunto de computadores

Os registros na tabela para métricas de conexão, VMConnection, não representam conexões de rede física individuais. Várias conexões de rede física são agrupadas em uma conexão lógica. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de conexão de rede física são agregados em um único registro lógico em VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos em conexões de entrada entre um conjunto de computadores

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre](migrate-services-overview.md) a versão mais recente das migrações para Azure.
