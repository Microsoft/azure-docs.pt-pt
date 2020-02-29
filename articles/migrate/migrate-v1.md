---
title: Trabalhar com a versão anterior do Azure Migrate
description: Descreve como trabalhar com a versão anterior do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914386"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Trabalhar com a versão anterior do Azure Migrate

Este artigo fornece informações sobre o trabalho com a versão anterior do Azure Migrate.


Existem duas versões do serviço Azure Migrate:

- **Versão atual**: Utilize esta versão para criar projetos Azure Migrate, descubra máquinas no local e orquequequeas avaliações e migrações. [Saiba mais](whats-new.md) sobre o que há de novo nesta versão.
- **Versão anterior**: Se estiver a utilizar a versão anterior do Azure Migrate (apenas foi suportada a avaliação dos VMware VMware no local), deverá agora utilizar a versão atual. Se ainda precisa de utilizar projetos Azure Migrate criados na versão anterior, isto é o que pode e não pode fazer:
    - Já não se pode criar projetos de migração.
    - Recomendamos que não realize novas descobertas.
    - Ainda pode aceder a projetos existentes.
    - Ainda pode fazer avaliações.
    

## <a name="upgrade-between-versions"></a>Upgrade entre versões

Não é possível atualizar projetos ou componentes na versão anterior para a nova versão. É necessário [criar um novo projeto Azure Migrate](how-to-add-tool-first-time.md)e adicionar-lhe ferramentas de avaliação e migração.

## <a name="find-projects-from-previous-version"></a>Encontre projetos a partir de versão anterior

Encontre projetos da versão anterior da seguinte forma:

1. No portal Azure > **Todos os serviços,** procure e selecione **Azure Migrate**. 
2. No painel de migração Azure, há uma notificação e um link para aceder a antigos projetos azure migrate.
3. Clique no link para abrir projetos v1.


## <a name="create-an-assessment"></a>Criar uma avaliação

Depois de os VMs serem descobertos no portal, agrupa-os e cria-se avaliações.

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
- Estimativa mensal de custos.

Uma máquina só se move para uma fase posterior se passar a anterior. Por exemplo, se uma máquina falhar a verificação de adequação, está marcada como inadequada para o Azure, e o tamanho e o custo não são feitos.


### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

A vista de preparação para o Azure na avaliação mostra o estado de preparação de cada VM.

**Prontidão** | **Estado** | **Detalhes**
--- | --- | ---
Preparado para o Azure | Sem problemas de compatibilidade. A máquina pode ser migrada como está para Azure, e irá arrancar em Azure com todo o suporte Azure. | Para as VMs que estão prontas, o Azure Migrate recomenda um tamanho de VM no Azure.
Condicionalmente preparado para o Azure | A máquina pode arrancar em Azure, mas pode não ter suporte azure completo. Por exemplo, uma máquina com uma versão mais antiga do Windows Server que não é suportada no Azure. | A Azure Migrate explica as questões de prontidão e fornece medidas de reparação.
Não está pronto para o Azure |  O VM não arranca em Azure. Por exemplo, se um VM tem um disco que é superior a 4 TB, não pode ser hospedado no Azure. | A Azure Migrate explica as questões de prontidão e fornece medidas de reparação.
Preparação desconhecida | O Azure Migrate não consegue identificar a prontidão do Azure, normalmente porque os dados não estão disponíveis.


#### <a name="azure-vm-properties"></a>Propriedades Azure VM
A prontidão tem em conta uma série de propriedades VM, para identificar se o VM pode funcionar em Azure.


**Propriedade** | **Detalhes** | **Prontidão**
--- | --- | ---
**Tipo de arranque** | BIOS suportado. UeFI não apoiado. | Pronto condicionalmente se o tipo de arranque for UEFI.
**Núcleos** | Núcleo de máquinas <= o número máximo de núcleos (128) suportados para um VM Azure.<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera os núcleos utilizados.<br/>Se um fator de conforto for especificado nas definições de avaliação, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, a Azure Migrate usa os núcleos atribuídos, sem aplicar o fator de conforto. | Pronto se for inferior ou igual a limites.
**Memória** | O tamanho da memória da máquina <= a memória máxima (3892 GB na série Azure M Standard_M128m&nbsp;<sup>2</sup>) para um VM Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera a memória utilizada.<br/><br/>Se for especificado um fator de conforto, a memória utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver história, a memória atribuída é usada, sem aplicar o fator de conforto.<br/><br/> | Pronto se dentro dos limites.
**Disco de armazenamento** | O tamanho atribuído de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos ligados à máquina deve ser de 65 ou menos, incluindo o disco OS. | Pronto se dentro dos limites.
**Redes** | Uma máquina deve ter 32 NICs ou menos ligados a ela. | Pronto se dentro dos limites.

#### <a name="guest-operating-system"></a>Sistema operativo convidado

Juntamente com as propriedades VM, a Azure Migrate também olha para o oss o de hóspedes do VM no local para identificar se o VM pode funcionar em Azure.

- A Azure Migrate considera o SISTEMA especificado no VCenter Server.
- Uma vez que a descoberta feita pela Azure Migrate é baseada em aparelhos, não tem como verificar se o SISTEMA que funciona dentro do VM é o mesmo especificado no VCenter Server.

A seguinte lógica é usada.

**Sistema Operativo** | **Detalhes** | **Prontidão**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2008 R2 e todos os SPs | O Azure oferece todo o apoio.| Preparado para o Azure
Windows Server 2008 (32-bits e 64-bits) | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Fora de apoio e precisa de um Acordo de [Apoio Personalizado (CSA)](https://aka.ms/WSosstatement) para apoio no Azure. | Preparado condicionalmente para o Azure, considere melhorar o Sistema operativo antes de migrar para Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Fora de apoio. A máquina pode arrancar em Azure, mas nenhum suporte para os Sistemas operativos é fornecido pelo Azure. | Preparado condicionalmente para o Azure, recomenda-se a atualização do Sistema operativo antes de migrar para O Azure.
Windows Cliente 7, 8 e 10 | O Azure fornece suporte apenas com [subscrição do Estúdio Visual.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente preparado para o Azure
Windows 10 Pro Desktop | A Azure presta apoio com os Direitos de [Hospedagem Multitenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Fora de apoio. A máquina pode arrancar em Azure, mas nenhum suporte para os Sistemas operativos é fornecido pelo Azure. | Preparado condicionalmente para o Azure, recomenda-se a atualização do Sistema operativo antes de migrar para O Azure.
Linux | O Azure apoia estes [sistemas operativos Linux.](../virtual-machines/linux/endorsed-distros.md) Outros sistemas operativos Linux podem arrancar em Azure, mas recomendamos a modernização do SO para uma versão endossada, antes de migrar para o Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Prepare-se condicionalmente se a versão não for endossada.
Outros sistemas operativos<br/><br/> Por exemplo, Oracle Solaris, Apple Mac OS etc., FreeBSD, etc. | O Azure não apoia estes sistemas operativos. A máquina pode arrancar em Azure, mas nenhum suporte para os Sistemas operativos é fornecido pelo Azure. | Preparado condicionalmente para o Azure, recomenda-se instalar um Sistema operativo suportado antes de migrar para O Azure.  
OS especificado como **Outros** no servidor vCenter | A Azure Migrate não consegue identificar o SO neste caso. | Prontidão desconhecida. Certifique-se de que o Sistema operativo que corre dentro do VM é suportado em Azure.
Sistemas operativos de 32 bits | A máquina pode arrancar em Azure, mas o Azure pode não fornecer suporte total. | Preparado condicionalmente para o Azure, considere atualizar o Os da máquina de 32 bits os os para 64 bits so antes de migrar para Azure.


### <a name="review-sizing"></a>Avaliação do tamanho

 A recomendação do tamanho do migrador Azure depende do critério de dimensionamento especificado nas propriedades de avaliação.

- Se o tamanho for baseado no desempenho, a recomendação de tamanho considera o histórico de desempenho dos VMs (CPU e memória) e discos (IOPS e entrada).
- Se o critério de dimensionamento for "como no local", a recomendação de dimensão em Azure baseia-se na dimensão do VM no local. O tamanho do disco baseia-se no tipo de Armazenamento especificado nas propriedades de avaliação (padrão são discos premium). A Azure Migrate não considera os dados de desempenho do VM e dos discos.

### <a name="review-cost-estimates"></a>Estimativas de custos de revisão

As estimativas de custos mostram o custo total de cálculo e armazenamento de funcionamento dos VMs em Azure, juntamente com os detalhes para cada máquina.

- As estimativas de custos são calculadas usando a recomendação de tamanho para uma máquina VM, e seus discos, e as propriedades de avaliação.
- Os custos mensais estimados para computação e armazenamento são agregados para todas as VMs no grupo.
- A estimativa de custos destina-se a executar os VM no local como Infraestruturas Azure como um serviço (IaaS) VMs. A Azure Migrate não considera a Plataforma como um serviço (PaaS), ou software como um serviço (SaaS) custos.

### <a name="review-confidence-rating-performance-based-assessment"></a>Avaliar a classificação da confiança (avaliação baseada no desempenho)

Cada avaliação baseada no desempenho está associada a uma classificação de confiança.

- Uma classificação de confiança varia de uma estrela a cinco estrelas (uma estrela é a mais baixa e cinco estrelas mais alta).
- A classificação de confiança é atribuída a uma avaliação, com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- A classificação de confiança não está disponível para avaliações "as-is" no local.

Para dimensionamento baseado no desempenho, o Azure Migrate precisa do seguinte:
- Dados de utilização para CPU.
- Dados de memória VM.
- Para cada disco ligado ao VM, precisa do IOPS do disco e dos dados de entrada.
- Para cada adaptador de rede ligado a um VM, o Azure Migrate precisa da entrada/saída da rede.
- Se alguma das anteriores não estiver disponível, as recomendações de tamanho (e, portanto, classificações de confiança) podem não ser fiáveis.


Dependendo da percentagem de pontos de dados disponíveis, as possíveis classificações de confiança são resumidas na tabela.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Questões de avaliação que afetam os ratings da confiança

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a uma série de razões:

- Não perfilou o seu ambiente durante a avaliação. Por exemplo, se criar a avaliação com a duração do desempenho definida para um dia, deve esperar pelo menos um dia após o início da descoberta, ou todos os pontos de dados a serem recolhidos.
- Alguns VMs foram encerrados durante o período para o qual a avaliação foi calculada. Se algum VMs foi desligado durante parte da duração, a Azure Migrate não pode recolher dados de desempenho durante esse período.
- Alguns VMs foram criados entre os dois durante o período de cálculo da avaliação. Por exemplo, se criar uma avaliação usando o histórico de desempenho do mês passado, mas criar uma série de VMs no ambiente há uma semana, o histórico de desempenho dos novos VMs não será para toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, aguarde pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Se não fizer esquelés baseado no desempenho, pode não ser fiável. Se não quiser recalcular, recomendamos que mude para o tamanho no local, alterando as propriedades de avaliação.



## <a name="create-groups-using-dependency-visualization"></a>Criar grupos usando a visualização da dependência

Além de criar grupos manualmente, pode criar grupos usando a visualização da dependência.
- Normalmente, utiliza este método quando pretende avaliar grupos com níveis de confiança mais elevados através da verificação cruzada das dependências das máquinas, antes de fazer uma avaliação.
- A visualização da dependência pode ajudá-lo a planear eficazmente a sua migração para Azure. Ajuda-o a garantir que nada é deixado para trás, e que não ocorrem interrupções surpresa quando se está a migrar para Azure.
- Pode descobrir todos os sistemas interdependentes que precisam de migrar em conjunto e identificar se um sistema de funcionamento ainda serve os utilizadores ou se é candidato ao desmantelamento em vez de migração.
- A Azure Migrate utiliza a solução Service Map no Monitor Azure para permitir a visualização da dependência.

> [!NOTE]
> A visualização da dependência não está disponível no Governo azure.

Para configurar a visualização da dependência, associa um espaço de trabalho log Analytics a um projeto Azure Migrate, instala agentes em máquinas para as quais pretende visualizar dependências e, em seguida, criar grupos usando informações de dependência. 



### <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho log analytics

Para utilizar a visualização da dependência, associa um espaço de trabalho log Analytics a um projeto de migração. Só é possível criar ou anexar um espaço de trabalho na mesma subscrição onde o projeto de migração é criado.

1. Para anexar um espaço de trabalho de Log Analytics a um projeto, em **Visão Geral,** > **Essencial,** clique **requer configuração .**
2. Pode criar um novo espaço de trabalho, ou anexar um existente:
  - Para criar um novo espaço de trabalho, especifique um nome. O espaço de trabalho é criado numa região da mesma [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Quando anexa um espaço de trabalho existente, pode escolher entre todos os espaços de trabalho disponíveis na mesma subscrição que o projeto de migração. Apenas esses espaços de trabalho estão listados que foram criados numa região de Mapa de [Serviços suportado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites) Para fixar um espaço de trabalho, certifique-se de que tem acesso ao 'Leitor' ao espaço de trabalho.

> [!NOTE]
> Não se pode mudar o espaço de trabalho associado a um projeto de migração.

### <a name="download-and-install-vm-agents"></a>Descarregue e instale agentes VM

Depois de configurar um espaço de trabalho, descarrega e instala agentes em cada máquina no local que pretende avaliar. Além disso, se tiver máquinas sem conectividade de internet, precisa de descarregar e instalar o [gateway Log Analytics.](../azure-monitor/platform/gateway.md)

1. Em **resumo,** clique em **Gerir** > **Máquinas**, e selecione a máquina necessária.
2. Na coluna **Dependências,** clique em **instalar agentes**.
3. Na página **Dependencies,** faça o download e instale o Microsoft Monitoring Agent (MMA) e o agente Dependency em cada VM que pretende avaliar.
4. Copie o ID e a chave da área de trabalho. Precisa destes quando instalar o MMA na máquina no local.

> [!NOTE]
> Para automatizar a instalação de agentes pode utilizar uma ferramenta de implantação como o Gestor de Configuração ou uma ferramenta parceira, como a [Intigua,](https://www.intigua.com/getting-started-intigua-for-azure-migration)que fornece uma solução de implementação de agentes para a Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instale o agente MMA numa máquina windows

Para instalar o agente numa máquina do Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **pasta destino,** guarde ou modifique a pasta de instalação predefinida > **Seguinte**.
4. Nas opções de configuração do **agente,** selecione **Azure Log Analytics** > **Seguinte**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho log Analytics. Colhe no ID do espaço de trabalho e na chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou utilizar um método automatizado, como o 'Gestor de Configuração'. [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) sobre a utilização destes métodos para instalar o agente MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instale o agente MMA numa máquina Linux

Para instalar o agente numa máquina Linux:

1. Transfira o pacote apropriado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
2. Instale o pacote utilizando o argumento --instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sobre a lista de suporte de sistemas operativos Linux por MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instale o agente MMA numa máquina monitorizada pelo Gestor de Operações

Para máquinas monitorizadas pelo System Center Operations Manager 2012 R2 ou posterior, não é preciso instalar o agente MMA. O Mapa de Serviçointegra-se ao Gestor de Operações MMA para recolher os dados de dependência necessários. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). O agente da Dependência precisa de ser instalado.

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência

1. Para instalar o agente Dependency numa máquina Windows, clique duas vezes no ficheiro de configuração e siga o assistente.
2. Para instalar o agente Dependency numa máquina Linux, instale como raiz utilizando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- Saiba mais sobre o suporte do [agente Dependency](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) para os sistemas operativos Windows e Linux.
- [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sobre como pode usar scripts para instalar o agente Dependency.

>[!NOTE]
> O artigo do Monitor Azure para VMs referenciado para fornecer uma visão geral dos pré-requisitos e métodos do sistema para implementar o agente dependency também são aplicáveis à solução do Mapa de Serviços.

### <a name="create-a-group-with-dependency-mapping"></a>Criar um grupo com mapeamento de dependência

1. Depois de instalar os agentes, dirija-se ao portal e clique em **Gerir** > **Máquinas**.
2. Procure a máquina onde instalou os agentes.
3. A coluna **Dependencies** para a máquina deve agora mostrar como Dependências de **Visualização**. Clique na coluna para ver as dependências da máquina.
4. O mapa de dependência da máquina mostra os seguintes detalhes:
    - Ligações TCP de entrada (Clientes) e outbound (Servidores) de/para a máquina
        - As máquinas dependentes que não possuem o MMA e o agente de dependência instalados são agruparadas por números de porta.
        - As máquinas dependentes que têm o MMA e o agente de dependência instalados são mostradas como caixas separadas.
    - Processos que correm dentro da máquina, pode expandir cada caixa de máquinas para ver os processos
    - As propriedades da máquina, incluindo o FQDN, sistema operativo, endereço MAC são mostradas. Pode clicar em cada caixa de máquinas para visualizar detalhes.

4. Pode visualizar dependências durante diferentes períodos de tempo clicando na duração do tempo na etiqueta do intervalo de tempo. Por defeito, o intervalo é de uma hora. Pode modificar o intervalo de tempo, ou especificar datas de início e fim e duração.

   > [!NOTE]
   >    Um intervalo de tempo de até uma hora é suportado. Utilize registos do Monitor Azure para [consultar dados](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) de dependência durante uma duração mais longa.

5. Depois de identificar máquinas dependentes que pretende agrupar, use Ctrl+Click para selecionar várias máquinas no mapa e clique em **máquinas de grupo**.
6. Especifique um nome de grupo. Verifique se as máquinas dependentes são descobertas pela Azure Migrate.

    > [!NOTE]
    > Se uma máquina dependente não for descoberta pela Azure Migrate, não pode adicioná-la ao grupo. Para adicionar tais máquinas ao grupo, é necessário executar novamente o processo de descoberta com o âmbito certo no VCenter Server e garantir que a máquina é descoberta pela Azure Migrate.  

7. Se quiser criar uma avaliação para este grupo, selecione a caixa de verificação para criar uma nova avaliação para o grupo.
8. Clique **em OK** para salvar o grupo.

Uma vez criado o grupo, recomenda-se instalar agentes em todas as máquinas do grupo e aperfeiçoar o grupo visualizando a dependência de todo o grupo.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dados de dependência de consulta dos registos do Monitor Do Azure

Os dados de dependência capturados pelo Service Map estão disponíveis para consulta no espaço de trabalho log Analytics associado ao seu projeto Azure Migrate. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados do Mapa de Serviço saquea dos registos do Monitor De Serviço. 

Para executar as consultas de Kusto:

1. Depois de instalar os agentes, vá ao portal e clique em **Visão Geral**.
2. Em **visão geral,** vá à secção **Essencial do** projeto e clique no nome do espaço de trabalho fornecido ao lado do **Workspace OMS**.
3. Na página do espaço de trabalho Log Analytics, clique em **Registos** **gerais** de > .
4. Escreva a sua consulta para recolher dados de dependência utilizando registos do Monitor Azure. Encontre consultas de amostra na secção seguinte.
5. Execute a sua consulta clicando em Run. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como escrever consultas kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Consultas de registos do Monitor Azure da amostra

Seguem-se as consultas de amostra que pode utilizar para extrair dados de dependência. Pode modificar as consultas para extrair os seus pontos de dados preferidos. Uma lista exaustiva dos campos nos registos de dados de dependência está disponível [aqui.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) Encontre mais consultas de amostra [aqui.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir as ligações de entrada num conjunto de máquinas

Os registos na tabela para métricas de ligação, VMConnection, não representam ligações físicas individuais da rede. Várias ligações físicas de rede são agruparadas numa ligação lógica. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de ligação física da rede são agregados num único registo lógico na VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos em ligações de entrada entre um conjunto de máquinas

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
