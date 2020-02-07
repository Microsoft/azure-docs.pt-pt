---
title: Perguntas comuns – descoberta, avaliação e análise de dependência em migrações para Azure
description: Obtenha respostas para perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 201d67c38e9583de16cb6075cc1c4bf826c204fd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059492"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Perguntas comuns sobre descoberta, avaliação e análise de dependência

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure. Se tiver mais dúvidas depois de ler este artigo, publique-as no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-server-migration.md) sobre migração de servidores.



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantas VMs posso descobrir com um dispositivo?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 250 servidores com um único dispositivo. Se tiver mais máquinas no seu ambiente no local, leia sobre a escala [hyper-V,](scale-hyper-v-assessment.md) [VMware](scale-vmware-assessment.md) e avaliação [física.](scale-physical-assessment.md)



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Tamanho da VM alterado. Posso executar uma avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local. Porém, uma avaliação é um instantâneo pontual de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como fazer descobrir VMs em um ambiente multilocatário?

- Para o VMware, se seu ambiente for compartilhado entre locatários e você não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie vCenter Server credenciais que possam acessar somente as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.
- Para o Hyper-V, a descoberta usa credenciais de host do Hyper-V. Se as VMs compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Preciso de vCenter Server para a descoberta de VM VMWare?

Sim, as migrações para Azure precisam vCenter Server para executar a descoberta em um ambiente VMware. Ele não dá suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.


## <a name="whats-the-difference-sizing-options"></a>Quais são as opções de dimensionamento de diferença?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM para avaliação. Ele avalia os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

- Por exemplo, se um VM no local tiver 4 núcleos e 8 GB de memória a 50% de utilização de CPU e 50% de utilização da memória, ocorrerá o seguinte:
    - O dimensionamento local recomendará um SKU de VM do Azure que tenha 4 núcleos e 8 GB de memória.
    - O dimensionamento baseado em desempenho recomendará uma SKU de VM com 2 núcleos e 4 GB de memória, pois a porcentagem de utilização é considerada.

- Da mesma forma, o dimensionamento de disco depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
    - Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, as migrações para Azure levarão os valores de IOPS e taxa de transferência do disco em conta quando identificarem o tipo de disco de destino (Standard ou Premium).
    - Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, as migrações para Azure recomendarão uma SKU de disco Premium, com base no tamanho do disco local. A mesma lógica é aplicada ao dimensionamento de disco, quando o dimensionamento é o local, e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>O histórico de desempenho/utilização afeta o dimensionamento?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

- As migrações para Azure coletam o histórico de desempenho de máquinas locais e as usam para recomendar o tamanho da VM e o tipo de disco no Azure.
- O dispositivo cria o perfil continuamente no ambiente local, para coletar dados de utilização em tempo real a cada 20 segundos.
- O dispositivo acumula os exemplos de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o dispositivo seleciona o valor de pico de todos os exemplos de 20 segundos.
- O dispositivo envia esse ponto de dados para o Azure.

Quando você cria uma avaliação no Azure, com base no valor de duração de desempenho e do percentual de histórico de desempenho, as migrações para Azure calculam o valor de utilização efetivo e as usam para o dimensionamento.

- Por exemplo, se você definir a duração do desempenho para um dia e o valor de percentil como 95 percentil, as migrações para Azure classificarão os pontos de exemplo de 15 minutos enviados pelo coletor para o dia anterior em ordem crescente e escolherá o valor de 95 º percentil como o efetivo utilização.
- O uso do valor 95 º percentil garante que as exceções sejam ignoradas. As exceções podem ser incluídas se você usar o 99 º percentil. Se você quiser escolher o pico de uso do período, sem exceções, selecione o 99 º percentil.

## <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

Use a visualização de dependência para avaliar grupos de VMs para migração com maior confiança. A visualização de dependência faz verificações entre as dependências do computador antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás e, portanto, ajuda a evitar interrupções inesperadas ao migrar para o Azure. As migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [aprender mais.](concepts-dependency-visualization.md)

> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

## <a name="do-i-pay-for-dependency-visualization"></a>Eu pago pela visualização de dependência?
Não. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.

## <a name="what-do-i-install-for-dependency-visualization"></a>O que instalo para a visualização de dependência?

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja avaliar.

Você precisa instalar os seguintes agentes em cada computador:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se você tiver computadores sem conectividade com a Internet, será necessário baixar e instalar Log Analytics gateway neles.

Você não precisa desses agentes, a menos que esteja usando a visualização de dependência.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#how-does-it-work).

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, a visualização de dependência não pode ser exportada. No entanto, o Azure Migrate utiliza o Mapa de Serviço, e pode utilizar o Mapa de [Serviço REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências em formato JSON.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Posso automatizar a instalação do MMA/agente de dependência?

Utilize este [script para instalar o agente Dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Siga estas [instruções para instalar o MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) utilizando a linha de comando ou automação. Para MMA, use [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, também pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e [a Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implementar os agentes.


## <a name="what-operating-systems-does-mma-support"></a>A quais sistemas operacionais o MMA dá suporte?

- Ver a lista de [sistemas operativos Windows suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Ver a lista de [sistemas operativos Linux suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Posso Visualizar dependências por mais de uma hora?
Não. Você pode visualizar dependências por até uma hora. Você pode voltar para uma data específica no histórico, até um mês, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibir dependências somente para uma janela de uma hora. No entanto, pode utilizar registos do Monitor Azure para [consultar dados](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) de dependência durante uma duração mais longa.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Pode visualizar dependências de grupos com mais de 10 VMs?
Pode [visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de grupos que contenham até 10 VMs. Se você tiver um grupo com mais de 10 VMs, é recomendável dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.




## <a name="next-steps"></a>Passos seguintes
Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
