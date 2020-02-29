---
title: Questões comuns - descoberta, avaliação e análise de dependência em Azure Migrate
description: Obtenha respostas a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 6db521791e5ebb846585d4e0e823ba20d178d54a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918551"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Questões comuns sobre descoberta, avaliação e análise de dependência

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate. Se tiver outras perguntas, reveja estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-server-migration.md) sobre migração de servidores.
- Colocar perguntas sobre o [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantos VMs posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMware, até 5.000 VMs Hiper-V e até 250 servidores com um único aparelho. Se tiver mais máquinas no seu ambiente no local, leia sobre a escala [hyper-V,](scale-hyper-v-assessment.md) [VMware](scale-vmware-assessment.md) e avaliação [física.](scale-physical-assessment.md)



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>O tamanho da VM mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate recolhe continuamente informações sobre o ambiente no local. Mas uma avaliação é uma imagem pontual de VMs no local. Se alterar as definições de um VM que pretende avaliar, utilize a opção de recalcular para atualizar a avaliação com as alterações mais recentes.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como descubro VMs num ambiente multiarrendatário?

- Para a VMware, se o seu ambiente é partilhado entre inquilinos, e não quer descobrir os VMs de um inquilino na subscrição de outro inquilino, crie credenciais vCenter Server que só podem aceder aos VMs que pretende descobrir. Em seguida, use essas credenciais quando começar a ser descoberta no aparelho Migratório Azure.
- Para o Hyper-V, a descoberta usa credenciais de hospedeiro hiper-V. Se os VMs partilham o mesmo hospedeiro Hyper-V, não há forma de separar a descoberta.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Preciso do VCenter Server para a descoberta vMWare VM?

Sim, o Azure Migrate precisa do VCenter Server para realizar a descoberta num ambiente VMware. Não suporta a descoberta de anfitriões ESXi que não são geridos pelo VCenter Server.


## <a name="whats-the-difference-sizing-options"></a>Qual é a diferença de opções de tamanho?

Com o dimensionamento das instalações, a Azure Migrate não considera os dados de desempenho da VM para avaliação. Avalia os tamanhos de VM com base na configuração no local. Com o tamanho baseado no desempenho, o tamanho baseia-se em dados de utilização.

- Por exemplo, se um VM no local tiver 4 núcleos e 8 GB de memória a 50% de utilização de CPU e 50% de utilização da memória, ocorrerá o seguinte:
    - As-on-premises sku recomendarão um Azure VM SKU que tem 4 núcleos e 8 GB de memória.
    - O tamanho baseado no desempenho irá recomendar um VM SKU que tem 2 núcleos e 4 GB de memória, porque a percentagem de utilização é considerada.

- Da mesma forma, o dimensionamento do disco depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
    - Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for automático, o Azure Migrate leva em conta os valores de iOPS e de entrada do disco quando identifica o tipo de disco-alvo (standard ou premium).
    - Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for premium, a Azure Migrate recomenda um SKU de disco premium, com base no tamanho do disco no local. A mesma lógica aplica-se ao dimensionamento do disco, quando o tamanho é como no local, e o tipo de armazenamento é padrão ou premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>O histórico de desempenho/utilização impacta o tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

- A Azure Migrate recolhe o histórico de desempenho das máquinas no local e utiliza-a para recomendar o tamanho VM e o tipo de disco em Azure.
- O aparelho perfis continuamente do ambiente no local, para recolher dados de utilização em tempo real a cada 20 segundos.
- O aparelho enrola as amostras de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o aparelho seleciona o valor máximo de todas as amostras de 20 segundos.
- O aparelho envia estes dados para o Azure.

Quando se cria uma avaliação em Azure, com base na duração do desempenho e no valor percentil do histórico de desempenho, a Azure Migrate calcula o valor de utilização eficaz e utiliza-a para dimensionamento.

- Por exemplo, se fixar a duração do desempenho para um dia e o valor percentil para 95 percentil, o Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo colecionador no último dia em ordem ascendente, e escolhe o 95º valor percentil como o eficaz utilização.
- A utilização do valor do percentil 95 garante que os forasteiros são ignorados. Outliers podem ser incluídos se usar o percentil 99. Se quiser escolher o pico de utilização para o período, sem perder nenhum outliers, selecione o percentil 99.

## <a name="what-is-dependency-visualization"></a>O que é a visualização da dependência?

Utilize a visualização da dependência para avaliar grupos de VMs para migração com maior confiança. A visualização da dependência cruza as dependências das máquinas antes de fazer uma avaliação. Ajuda a garantir que nada é deixado para trás, e assim ajuda a evitar interrupções inesperadas quando migra para Azure. A Azure Migrate utiliza a solução Service Map no Monitor Azure para permitir a visualização da dependência. [aprender mais.](concepts-dependency-visualization.md)

> [!NOTE]
> A visualização da dependência não está disponível no Governo de Azure.

## <a name="do-i-pay-for-dependency-visualization"></a>Pago pela visualização da dependência?
Não. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.

## <a name="what-do-i-install-for-dependency-visualization"></a>O que posso instalar para visualização da dependência?

Para utilizar a visualização da dependência, é necessário descarregar e instalar agentes em cada máquina no local que pretende avaliar.

É necessário instalar os seguintes agentes em cada máquina:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se tiver máquinas sem conectividade de internet, precisa de descarregar e instalar o gateway Log Analytics.

Não precisas destes agentes a não ser que estejas a usar a visualização da dependência.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização da dependência?

Não, a visualização da dependência não pode ser exportada. No entanto, o Azure Migrate utiliza o Mapa de Serviço, e pode utilizar o Mapa de [Serviço REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências em formato JSON.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Posso automatizar a instalação do agente MMA/Dependency?

Utilize este [script para instalar o agente Dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Siga estas [instruções para instalar o MMA](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) utilizando a linha de comando ou automação. Para MMA, use [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, também pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e [a Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implementar os agentes.


## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operativos suporta mMmA?

- Ver a lista de [sistemas operativos Windows suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Ver a lista de [sistemas operativos Linux suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Posso visualizar dependências por mais de uma hora?
Não. Pode visualizar dependências até uma hora. Pode voltar a uma data específica na história, desde há um mês, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para ver dependências para ontem, mas você pode ver dependências apenas por uma janela de uma hora. No entanto, pode utilizar registos do Monitor Azure para [consultar dados](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) de dependência durante uma duração mais longa.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Pode visualizar dependências para grupos de mais de 10 VMs?
Pode [visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de grupos que contenham até 10 VMs. Se você tem um grupo com mais de 10 VMs, recomendamos que divida o grupo em grupos menores e, em seguida, visualize as dependências.




## <a name="next-steps"></a>Passos seguintes
Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
