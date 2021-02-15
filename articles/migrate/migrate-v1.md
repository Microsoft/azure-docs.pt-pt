---
title: Trabalhar com a versão anterior de Azure Migrate
description: Descreve como trabalhar com a versão anterior de Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 719fcca74d2cd048bf170940eff7da6a25425469
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373288"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Trabalhar com a versão anterior de Azure Migrate

Este artigo fornece informações sobre o trabalho com a versão anterior do Azure Migrate. 


Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilize esta versão para criar projetos Azure Migrate, descubra máquinas no local e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: Se estiver a utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMS VMware no local), deverá agora utilizar a versão atual. Os projetos de versão anterior são referidos como projetos clássicos neste artigo. O clássico Azure Migrate vai reformar-se em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica do Azure Migrate deixará de ser suportada e os metadados de inventário em projetos clássicos serão eliminados. Se ainda precisa de usar projetos clássicos do Azure Migrate, isto é o que pode e não pode fazer:
    - Já não se pode criar projetos de migração.
    - Recomendamos que não realize novas descobertas.
    - Ainda pode aceder a projetos existentes.
    - Ainda pode fazer avaliações.
    

## <a name="upgrade-between-versions"></a>Upgrade entre versões

Não é possível atualizar projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um novo projeto Azure Migrate](create-manage-projects.md), e [adicionar-lhe ferramentas de avaliação e migração.](./create-manage-projects.md) Use os tutoriais para entender como usar as ferramentas de avaliação e migração disponíveis. Se tiver um espaço de trabalho log Analytics ligado a um projeto Clássico, pode anexá-lo a um projeto de versão atual depois de eliminar o projeto Classic.

## <a name="find-projects-from-previous-version"></a>Encontre projetos a partir de versão anterior

Encontre projetos da versão anterior da seguinte forma:

1. No portal Azure > **Todos os serviços,** procure e selecione **Azure Migrate**. 
2. No painel Azure Migrate, há uma notificação e um link para aceder a antigos projetos da Azure Migrate.
3. Clique no link para abrir projetos clássicos.

## <a name="delete-projects-from-previous-version"></a>Eliminar projetos de versão anterior

Localizar e apagar projetos da versão anterior da seguinte forma:

1. No portal Azure > **Todos os serviços,** procure e selecione **Azure Migrate**. 
2. No painel Azure Migrate, há uma notificação e um link para aceder a antigos projetos da Azure Migrate.
3. Clique no link para abrir projetos clássicos.
4. Selecione o projeto que pretende eliminar e eliminá-lo. 


## <a name="create-an-assessment"></a>Criar uma avaliação

Depois de os VM serem descobertos no portal, agrupa-os e cria-se avaliações.

- Pode criar imediatamente como avaliações no local imediatamente após a descoberta de VMs no portal.
- Para avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia antes de criar uma avaliação baseada no desempenho, para obter recomendações de tamanho fiável.

Criar uma avaliação da seguinte forma:

1. Na página **Descrição geral** do projeto, clique em **+ Criar avaliação**.
2. Clique em **Ver tudo** para rever as propriedades de avaliação.
3. Crie o grupo e especifique um nome.
4. Selecione as máquinas que quer adicionar ao grupo.
5. Clique em **Criar Avaliação**, para criar o grupo e a avaliação.
6. Após a criação da avaliação, visualize-a em **Descrição geral** > **Dashboard**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.

Se quiser atualizar uma avaliação existente com os dados de desempenho mais recentes, poderá utilizar o comando **Recalcular** na avaliação para a atualizar.

## <a name="review-an-assessment"></a>Rever uma avaliação 

Uma avaliação tem três fases:

- Uma avaliação começa com uma análise de adequação para descobrir se as máquinas são compatíveis em Azure.
- Estimativas de dimensionamento.
- Estimativa mensal dos custos.

Uma máquina só se move para uma fase posterior se passar a anterior. Por exemplo, se uma máquina falhar a verificação de adequação, é marcada como inadequada para Azure, e o tamanho e o custo não são feitos.


### <a name="review-azure-readiness"></a>Rever a preparação para o Azure

A vista de preparação para o Azure na avaliação mostra o estado de preparação de cada VM.

**Prontidão** | **Estado** | **Detalhes**
--- | --- | ---
Preparado para o Azure | Sem problemas de compatibilidade. A máquina pode ser migrada como está para Azure, e irá arrancar em Azure com suporte Azure completo. | Para as VMs que estão prontas, o Azure Migrate recomenda um tamanho de VM no Azure.
Condicionalmente preparado para o Azure | A máquina pode arrancar em Azure, mas pode não ter suporte completo do Azure. Por exemplo, uma máquina com uma versão mais antiga do Windows Server que não é suportada no Azure. | Azure Migrate explica as questões de prontidão e fornece medidas de reparação.
Não está pronto para o Azure |  O VM não vai arrancar em Azure. Por exemplo, se um VM tem um disco superior a 4 TB, não pode ser hospedado no Azure. | Azure Migrate explica as questões de prontidão e fornece medidas de reparação.
Preparação desconhecida | Azure Migrate não consegue identificar a prontidão do Azure, normalmente porque os dados não estão disponíveis.


#### <a name="azure-vm-properties"></a>Propriedades Azure VM
A prontidão tem em conta uma série de propriedades VM, para identificar se o VM pode funcionar em Azure.


**Propriedade** | **Detalhes** | **Prontidão**
--- | --- | ---
**Tipo de bota** | BIOS suportado. UEFI não apoiada. | Condicionalmente pronto se o tipo de arranque for UEFI.
**Núcleos** | Máquinas core <= o número máximo de núcleos (128) suportados para um Azure VM.<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera os núcleos utilizados.<br/>Se um fator de conforto for especificado nas definições de avaliação, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, a Azure Migrate utiliza os núcleos atribuídos, sem aplicar o fator de conforto. | Pronto se for inferior ou igual a limites.
**Memória** | O tamanho da memória da máquina <= a memória máxima (3892 GB na série Azure M Standard_M128m &nbsp; <sup>2</sup>) para um Azure VM. [Saiba mais](../virtual-machines/sizes.md).<br/><br/> Se o histórico de desempenho estiver disponível, Azure Migrate considera a memória utilizada.<br/><br/>Se for especificado um fator de conforto, a memória utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver história, a memória atribuída é usada, sem aplicar o fator de conforto.<br/><br/> | Pronto se dentro dos limites.
**Disco de armazenamento** | O tamanho atribuído de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos ligados à máquina deve ser de 65 ou menos, incluindo o disco SO. | Pronto se dentro dos limites.
**Rede** | Uma máquina deve ter 32 OU menos NICs ligados a ela. | Pronto se dentro dos limites.

#### <a name="guest-operating-system"></a>Sistema operativo convidado

Juntamente com as propriedades VM, Azure Migrate também olha para o so convidado do VM no local para identificar se o VM pode funcionar em Azure.

- Azure Migrate considera o SISTEMA especificado no servidor vCenter.
- Uma vez que a descoberta feita pela Azure Migrate é baseada em aparelhos, não tem uma forma de verificar se o SISTEMA que corre dentro do VM é o mesmo que o especificado no servidor vCenter.

É utilizada a seguinte lógica.

**Sistema operativo** | **Detalhes** | **Prontidão**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2008 R2 e todos os SPs | O Azure oferece todo o apoio.| Preparado para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Fora de suporte e precisa de um Acordo de [Apoio Personalizado (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) para apoio em Azure. | Condicionalmente pronto para o Azure, considere atualizar o SO antes de migrar para Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Sem apoio. A máquina pode arrancar em Azure, mas nenhum suporte de SO é fornecido pela Azure. | Sem condições para o Azure, recomenda-se atualizar o SO antes de migrar para Azure.
Windows Cliente 7, 8 e 10 | O Azure fornece suporte apenas com [subscrição do Visual Studio.](../virtual-machines/windows/client-images.md) | Condicionalmente preparado para o Azure
Windows 10 Pro Desktop | A Azure fornece suporte com [direitos de hospedagem multitenant.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Sem apoio. A máquina pode arrancar em Azure, mas nenhum suporte de SO é fornecido pela Azure. | Sem condições para o Azure, recomenda-se atualizar o SO antes de migrar para Azure.
Linux | O Azure apoia estes [sistemas operativos Linux.](../virtual-machines/linux/endorsed-distros.md) Outros sistemas operativos Linux podem arrancar em Azure, mas recomendamos o upgrade do SO para uma versão endossada, antes de migrar para Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operativos<br/><br/> Por exemplo, Oracle Solaris, Apple macOS etc., FreeBSD, etc. | O Azure não apoia estes sistemas operativos. A máquina pode arrancar em Azure, mas nenhum suporte de SO é fornecido pela Azure. | Sem condições para o Azure, recomenda-se instalar um SISTEMA suportado antes de migrar para Azure.  
OS especificado como **Outros** no servidor vCenter | Azure Migrate não pode identificar o SO neste caso. | Prontidão desconhecida. Certifique-se de que o SISTEMA que corre no interior do VM é suportado em Azure.
Sistemas operativos de 32 bits | A máquina pode arrancar em Azure, mas a Azure pode não fornecer suporte total. | Condicionalmente pronto para o Azure, considere atualizar o SO da máquina de 32 bits de SO a 64-bit de SO antes de migrar para Azure.


### <a name="review-sizing"></a>Análise de tamanho

 A recomendação de tamanho Azure Migrate depende do critério de dimensionamento especificado nas propriedades de avaliação.

- Se o dimensionamento for baseado no desempenho, a recomendação de tamanho considera o histórico de desempenho dos VMs (CPU e memória) e discos (IOPS e produção).
- Se o critério de dimensionamento for "como no local", a recomendação de tamanho em Azure baseia-se no tamanho do VM no local. O tamanho do disco baseia-se no tipo de Armazenamento especificado nas propriedades de avaliação (o padrão são discos premium). A Azure Migrate não considera os dados de desempenho dos VM e dos discos.

### <a name="review-cost-estimates"></a>Rever as estimativas de custos

As estimativas de custos mostram o custo total de cálculo e armazenamento da execução dos VMs em Azure, juntamente com os detalhes de cada máquina.

- As estimativas de custos são calculadas usando a recomendação de tamanho para uma máquina VM, e seus discos, e as propriedades de avaliação.
- Os custos mensais estimados para computação e armazenamento são agregados para todas as VMs no grupo.
- A estimativa de custos é para executar os VM no local como Azure Infrastructure como um VM de serviço (IaaS). A Azure Migrate não considera a Plataforma como um serviço (PaaS), ou Software como um serviço (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>Rever a classificação de confiança (avaliação baseada no desempenho)

Cada avaliação baseada no desempenho está associada a uma classificação de confiança.

- Uma classificação de confiança varia de uma estrela a cinco estrelas (uma estrela sendo a mais baixa e cinco estrelas a mais alta).
- A classificação de confiança é atribuída a uma avaliação, com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- A classificação de confiança não está disponível para avaliações "as-is" no local.

Para o dimensionamento baseado no desempenho, a Azure Migrate precisa do seguinte:
- Dados de utilização do CPU.
- Dados de memória VM.
- Para cada disco ligado ao VM, necessita do disco IOPS e dos dados de produção.
- Para cada adaptador de rede ligado a um VM, a Azure Migrate necessita da entrada/saída da rede.
- Se alguma das recomendações de tamanho (e, portanto, classificações de confiança) não forem fiáveis.


Dependendo da percentagem de pontos de dados disponíveis, as possíveis classificações de confiança são resumidas na tabela.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Questões de avaliação que afetam as notações de confiança

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a uma série de razões:

- Não perfilou o seu ambiente durante a avaliação. Por exemplo, se criar a avaliação com a duração de desempenho definida para um dia, deve esperar pelo menos um dia depois de iniciar a descoberta, ou todos os pontos de dados a serem recolhidos.
- Alguns VM foram encerrados durante o período durante o qual a avaliação foi calculada. Se algum VMs foi desligado durante parte da duração, a Azure Migrate não pode recolher dados de desempenho durante esse período.
- Alguns VM foram criados entre durante o período de cálculo da avaliação. Por exemplo, se criar uma avaliação usando o histórico de desempenho do mês passado, mas criar uma série de VMs no ambiente há uma semana, o histórico de desempenho dos novos VMs não será para toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, aguarde pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Se não fizeres o tamanho baseado no desempenho pode não ser fiável. Se não quiser recalcular, recomendamos que se mude para o tamanho do local, alterando as propriedades de avaliação.



## <a name="create-groups-using-dependency-visualization"></a>Criar grupos que utilizem visualização de dependência

Além de criar grupos manualmente, pode criar grupos usando a visualização da dependência.
- Normalmente, utiliza-se este método quando se pretende avaliar grupos com níveis de confiança mais elevados, verificando as dependências das máquinas, antes de fazer uma avaliação.
- A visualização da dependência pode ajudá-lo a planear eficazmente a sua migração para Azure. Ajuda-o a garantir que nada é deixado para trás, e que não ocorrem interrupções surpresa quando está a migrar para Azure.
- Pode descobrir todos os sistemas interdependentes que precisam de migrar em conjunto e identificar se um sistema de funcionamento ainda está a servir os utilizadores ou se é candidato ao desmantelamento em vez de migração.
- A Azure Migrate utiliza a solução de Mapa de Serviço no Monitor Azure para permitir a visualização da dependência.

> [!NOTE]
> A visualização da dependência não está disponível no Governo de Azure.

Para configurar a visualização da dependência, associa um espaço de trabalho Log Analytics a um projeto Azure Migrate, instala agentes em máquinas para as quais pretende visualizar dependências e, em seguida, cria grupos usando informações de dependência. 



### <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho Log Analytics

Para utilizar a visualização da dependência, associa um espaço de trabalho log Analytics a um projeto de migração. Só é possível criar ou anexar um espaço de trabalho na mesma subscrição onde o projeto de migração é criado.

1. Para anexar um espaço de trabalho Log Analytics a um projeto, em **visão geral,**> **Essentials,** clique em **Requer a configuração**.
2. Pode criar um novo espaço de trabalho ou anexar um existente:
  - Para criar um novo espaço de trabalho, especifique um nome. O espaço de trabalho é criado numa região da mesma [geografia azul que](https://azure.microsoft.com/global-infrastructure/geographies/) o projeto de migração.
  - Quando anexar um espaço de trabalho existente, pode escolher entre todos os espaços de trabalho disponíveis na mesma subscrição que o projeto de migração. Apenas esses espaços de trabalho estão listados que foram criados numa [região de Mapa de Serviço suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) Para anexar um espaço de trabalho, certifique-se de que tem acesso 'Reader' ao espaço de trabalho.

> [!NOTE]
> Não se pode mudar o espaço de trabalho associado a um projeto de migração.

### <a name="download-and-install-vm-agents"></a>Descarregue e instale agentes VM

Depois de configurar um espaço de trabalho, descarregue e instale agentes em cada máquina no local que pretende avaliar. Além disso, se tiver máquinas sem conectividade com a Internet, tem de descarregar e instalar o [Gateway Log Analytics](../azure-monitor/platform/gateway.md) nelas.

1. Em **Visão Geral,** clique em **Gerir**  >  **Máquinas** e selecione a máquina necessária.
2. Na coluna **Dependências,** clique **em Instalar agentes.**
3. Na página **Dependências,** descarregue e instale o Microsoft Monitoring Agent (MMA) e o agente De dependência em cada VM que pretende avaliar.
4. Copie o ID e a chave da área de trabalho. Precisa destes quando instalar o MMA na máquina no local.

> [!NOTE]
> Para automatizar a instalação de agentes pode utilizar uma ferramenta de implantação como o Gestor de Configuração ou uma ferramenta parceira como a [Intigua,](https://www.intigua.com/intigua-for-azure-migration)que fornece uma solução de implementação de agente para a Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instale o agente MMA numa máquina Windows

Para instalar o agente numa máquina Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **Pasta destino**, guarde ou modifique a pasta de instalação predefinido > **Seguinte**.
4. Nas **opções de configuração do agente**, selecione **Azure Log Analytics**  >  **Next**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho Log Analytics. Cole no iD do espaço de trabalho e na chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou utilizando um método automatizado, como o Gestor de Configurações. [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#installation-options) sobre a utilização destes métodos para instalar o agente MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instale o agente MMA numa máquina Linux

Para instalar o agente numa máquina Linux:

1. Transfira o pacote apropriado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
2. Instale o pacote utilizando o argumento de instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](../azure-monitor/platform/agents-overview.md#supported-operating-systems) sobre a lista de sistemas operativos Linux suportados pelo MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instale o agente MMA numa máquina monitorizada pelo Gestor de Operações

Para máquinas monitorizadas pelo System Center Operations Manager 2012 R2 ou posterior, não é preciso instalar o agente MMA. O Mapa de Serviços integra-se com o Gestor de Operações MMA para recolher os dados de dependência necessários. [Saiba mais](../azure-monitor/insights/service-map-scom.md#prerequisites). O agente de dependência precisa de ser instalado.

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência

1. Para instalar o agente Dependency numa máquina Windows, clique duas vezes no ficheiro de configuração e siga o assistente.
2. Para instalar o agente Desadependiado numa máquina Linux, instale como raiz utilizando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- Saiba mais sobre o suporte do [agente Dependency](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) para os sistemas operativos Windows e Linux.
- [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) sobre como pode usar scripts para instalar o agente Dependency.

>[!NOTE]
> O artigo do Monitor Azure para VMs referenciado para fornecer uma visão geral dos pré-requisitos e métodos do sistema para a implantação do agente de dependência são também aplicáveis à solução do Mapa de Serviços.

### <a name="create-a-group-with-dependency-mapping"></a>Criar um grupo com mapeamento de dependência

1. Depois de instalar os agentes, vá ao portal e clique em **Gerir**  >  **Máquinas**.
2. Procure a máquina onde instalou os agentes.
3. A coluna **Dependências** para a máquina deve agora mostrar como **Dependências de Visualização**. Clique na coluna para ver as dependências da máquina.
4. O mapa de dependência da máquina mostra os seguintes detalhes:
    - Ligações TCP de entrada (Clientes) e saída (Servidores) de/para a máquina
        - As máquinas dependentes que não têm o MMA e o agente de dependência instalados são agrupadas por números de porta.
        - As máquinas dependentes que têm o MMA e o agente de dependência instalados são mostradas como caixas separadas.
    - Processos em execução dentro da máquina, pode expandir cada caixa de máquinas para ver os processos
    - As propriedades da máquina, incluindo o FQDN, sistema operativo, endereço MAC são mostrados. Pode clicar em cada caixa de máquinas para ver detalhes.

4. Pode ver dependências para diferentes durações de tempo clicando na duração do tempo na etiqueta do intervalo de tempo. Por defeito, o alcance é de uma hora. Pode modificar o intervalo de tempo, ou especificar datas de início e fim e duração.

   > [!NOTE]
   >    Um intervalo de tempo de até uma hora é suportado. Utilize registos do Azure Monitor para [consultar dados de dependência](./how-to-create-group-machine-dependencies.md) durante uma duração mais longa.

5. Depois de identificar máquinas dependentes que pretende agrupar, utilize o Ctrl+Click para selecionar várias máquinas no mapa e clique em **máquinas de grupo**.
6. Especifique um nome de grupo. Verifique se as máquinas dependentes são descobertas pela Azure Migrate.

    > [!NOTE]
    > Se uma máquina dependente não for descoberta pela Azure Migrate, não pode adicioná-la ao grupo. Para adicionar estas máquinas ao grupo, é necessário executar novamente o processo de descoberta com o âmbito certo no vCenter Server e garantir que a máquina é descoberta pela Azure Migrate.  

7. Se pretender criar uma avaliação para este grupo, selecione a caixa de verificação para criar uma nova avaliação para o grupo.
8. Clique **em OK** para salvar o grupo.

Uma vez criado o grupo, recomenda-se instalar agentes em todas as máquinas do grupo e refinar o grupo visualizando a dependência de todo o grupo.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dados de dependência de consultas a partir de registos do Azure Monitor

Os dados de dependência capturados pelo Mapa de Serviços estão disponíveis para consulta no espaço de trabalho Log Analytics associado ao seu projeto Azure Migrate. [Saiba mais](../azure-monitor/insights/service-map.md#log-analytics-records) sobre as tabelas de dados do Mapa de Serviço para consultar nos registos do Azure Monitor. 

Para executar as consultas de Kusto:

1. Depois de instalar os agentes, vá ao portal e clique em **Overview**.
2. Em **Visão Geral**, vá à secção **Essentials** do projeto e clique no nome do espaço de trabalho fornecido junto ao **Espaço de Trabalho OMS.**
3. Na página do espaço de trabalho Log Analytics, clique em  >  **Registos Gerais**.
4. Escreva a sua consulta para recolher dados de dependência utilizando registos do Azure Monitor. Encontre consultas de amostra na secção seguinte.
5. Executar a sua consulta clicando em Run. 

[Saiba mais](../azure-monitor/log-query/log-analytics-tutorial.md) sobre como escrever consultas kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Experimente consultas de registos do Monitor Azure

Seguem-se as consultas de amostra que pode utilizar para extrair dados de dependência. Pode modificar as consultas para extrair os seus pontos de dados preferidos. Uma lista exaustiva dos campos de registos de dados de dependência está disponível [aqui.](../azure-monitor/insights/service-map.md#log-analytics-records) Encontre mais consultas de amostra [aqui.](../azure-monitor/insights/service-map.md#sample-log-searches)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir as ligações de entrada num conjunto de máquinas

Os registos na tabela para métricas de ligação, VMConnection, não representam ligações físicas individuais. Múltiplas ligações físicas de rede são agrupadas numa ligação lógica. [Saiba mais](../azure-monitor/insights/service-map.md#connections) sobre como os dados de ligação à rede física são agregados num único registo lógico em VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos nas ligações de entrada entre um conjunto de máquinas

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
[Conheça](migrate-services-overview.md) a versão mais recente do Azure Migrate.