---
title: Descrição geral do dimensionamento automático em máquinas virtuais, serviços Cloud e aplicações Web
description: Dimensionamento automático no Microsoft Azure. Aplica-se às máquinas virtuais, os conjuntos de dimensionamento de máquina de Virtual, serviços Cloud e aplicações Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 05f20aec536ebdb702caea37051a65af9bbc659f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085648"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Descrição geral do dimensionamento automático em máquinas virtuais do Microsoft Azure, serviços Cloud e aplicações Web
Este artigo descreve o dimensionamento automático do Microsoft Azure é, seus benefícios e como começar a usá-lo.  

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> O Azure tem dois métodos de dimensionamento automático. Uma versão mais antiga do dimensionamento automático aplica-se às máquinas virtuais (conjuntos de disponibilidade). Esta funcionalidade tem suporte limitado e recomendamos que migre para conjuntos de dimensionamento de máquinas virtuais para o suporte de dimensionamento automático mais rápida e confiável. Este artigo inclui uma ligação sobre como utilizar a tecnologia mais antiga.  
>
>

## <a name="what-is-autoscale"></a>O que é o dimensionamento automático?
Dimensionamento automático permite que tenha a quantidade certa de recursos em execução para processar a carga na sua aplicação. Ele permite que adicionar recursos para processar os aumentos de carga e também poupar dinheiro ao remover os recursos que estão parados inativo. Especifique um número mínimo e máximo de instâncias para executar e adicionar ou remover VMs automaticamente com base num conjunto de regras. Ter um mínimo de torna-se de que seu aplicativo está sempre em execução mesmo sob nenhuma carga. Ter um máximo limita o seu total possível de custo por hora. Dimensionar automaticamente entre esses dois extremos usando regras criadas.

 ![Dimensionamento automático explicado. Adicionar e remover VMs](./media/autoscale-overview/AutoscaleConcept.png)

Quando forem cumpridas condições de regra, uma ou mais ações de dimensionamento automático são acionadas. Pode adicionar e remover VMs ou executar outras ações. O diagrama conceptual seguinte mostra esse processo.  

 ![Diagrama de fluxo de dimensionamento automático](./media/autoscale-overview/Autoscale_Overview_v4.png)

A explicação seguinte aplica-se às partes do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas de Recursos
Recursos emitem métricas, estas métricas são processadas mais tarde. As métricas são fornecidos através de métodos diferentes.
Conjuntos de dimensionamento de máquina virtual utilizam dados de telemetria dos agentes de diagnóstico do Azure, ao passo que vem de telemetria para aplicações Web e serviços Cloud diretamente a partir de infraestrutura do Azure. Algumas estatísticas usadas incluem a utilização da CPU, utilização da memória, contagens de thread, comprimento da fila e utilização do disco. Para obter uma lista dos dados de telemetria que pode utilizar, consulte [métricas comuns do dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Métricas Personalizadas
Também pode aproveitar suas próprias métricas personalizadas que podem ser emitindo a suas aplicações. Se tiver configurado as aplicações para enviar métricas para o Application Insights, pode aproveitar essas métricas para tomar decisões sobre o dimensionamento ou não.

## <a name="time"></a>Hora
Regras com base na agenda são baseadas em UTC. Tem de definir seu fuso horário corretamente quando configurar as regras.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de dimensionamento automático, mas pode ter muitos deles. Pode criar regras complexas de sobreposição, conforme necessário para a sua situação.  Incluem tipos de regra  

* **Com base na métrica** -por exemplo, execute esta ação quando a utilização de CPU é superior a 50%.
* **Com base no tempo** – por exemplo, acionar um webhook am cada 8 no Sábado num determinado fuso de horário.

Regras com base na métrica medem a carga da aplicação e adicionar ou remover VMs com base nessa carga. Regras com base na agenda permitem-lhe dimensionar quando ver padrões de tempo na sua carga e dimensionamento antes de um aumento de carga possível ou diminuição ocorre.  

## <a name="actions-and-automation"></a>Ações e automatização
Regras podem acionar um ou mais tipos de ações.

* **Dimensionamento** -dimensionamento de VMs dentro ou para fora
* **E-mail** -enviar e-mail para os administradores de subscrições, coadministradores e/ou endereço de e-mail adicionais que especificar
* **Automatizar através de webhooks** -chamar webhooks, que pode acionar várias ações complexas dentro ou fora do Azure. Dentro do Azure, pode iniciar um runbook da automatização do Azure, uma função do Azure ou uma aplicação lógica do Azure. Exemplo de terceiros URL fora do Azure incluem serviços como o Slack e Twilio.

## <a name="autoscale-settings"></a>Definições do Dimensionamento Automático
Dimensionamento automático utilize a seguinte terminologia e estrutura.

- Uma **definição de dimensionamento automático** são lidos pelo mecanismo de dimensionamento automático para determinar se deve aumentar ou reduzir verticalmente. Ele contém um ou mais perfis, informações sobre o recurso de destino e as definições de notificação.

  - Uma **perfil de dimensionamento automático** é uma combinação de r:

    - **definição de capacidade**, que indica que o mínimo, máximo e valores predefinidos para o número de instâncias.
    - **conjunto de regras**, cada um deles inclui um acionador (tempo ou métrica) e uma ação de dimensionamento (para cima ou para baixo).
    - **periodicidade**, que indica quando o dimensionamento automático deve colocar este perfil em vigor.

      Pode ter vários perfis permitem-lhe lidar com diferentes requisitos sobrepostos. Pode ter perfis de dimensionamento automático diferentes para diferentes horas do dia ou dias da semana, por exemplo.

  - R **definição de notificação** define que notificações devem ocorrer quando um evento de dimensionamento automático ocorre com base no que satisfaça os critérios de um dos perfis de definição de dimensionamento automático. Dimensionamento automático pode notificar um ou mais endereços de e-mail ou fazer chamadas para um ou mais webhooks.


![Definição de dimensionamento automático do Azure, perfil e estrutura de regra](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos configuráveis e descrições está disponível na [API do REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931928.aspx).

Para obter exemplos de código, consulte

* [Configuração avançada do dimensionamento automático com modelos do Resource Manager para conjuntos de dimensionamento de VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Dimensionamento vertical do vs horizontais
Dimensionamento automático só pode ser dimensionada horizontalmente, que é um aumento ("de saída") ou diminuição ("") no número de instâncias de VM.  Horizontal é mais flexível numa situação de nuvem, pois permite a execução de potencialmente milhares de VMs para processar carga.

Por outro lado, dimensionamento vertical é diferente. Ele mantém o mesmo número de VMs, mas torna as VMs mais ("up") ou menos ("down") poderoso. Power é medido em memória, velocidade da CPU, espaço em disco, etc.  Dimensionamento vertical tem limitações mais. É dependente da disponibilidade de hardware de maior, que rapidamente atinge um limite superior e pode variar consoante a região. Normalmente, também dimensionamento vertical requer uma VM parar e reiniciar.

Para obter mais informações, consulte [aumentar verticalmente a máquina virtual do Azure com a automatização do Azure](../../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Métodos de acesso
Pode configurar o dimensionamento automático através de

* [Portal do Azure](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Interface de Linha de Comandos (CLI) de várias plataformas](../../azure-monitor/platform/cli-samples.md#autoscale)
* [API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Serviços suportados do dimensionamento automático
| Serviço | Esquema e documentos |
| --- | --- |
| Aplicações Web |[Dimensionar aplicações Web](../../azure-monitor/platform/autoscale-get-started.md) |
| Serviços Cloud |[Dimensionamento automático um serviço em nuvem](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas virtuais: Clássica |[Dimensionar conjuntos de disponibilidade de Máquina Virtual clássica](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: Conjuntos de dimensionamento do Windows |[Conjuntos de dimensionamento de dimensionamento de máquinas virtuais no Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Máquinas virtuais: Conjuntos de dimensionamento do Linux |[Conjuntos de dimensionamento de dimensionamento de máquinas virtuais no Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Máquinas virtuais: Exemplo do Windows |[Configuração avançada do dimensionamento automático com modelos do Resource Manager para conjuntos de dimensionamento de VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Serviço da Gestão de API|[Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre dimensionamento automático, utilize as instruções do dimensionamento automático listadas anteriormente, ou consulte os seguintes recursos:

* [Métricas comuns do Azure Monitor dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Melhores práticas do dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Utilizar ações de dimensionamento automático para enviar o e-mail e webhook notificações de alerta](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)
* [Resolução de problemas Autoscale de conjuntos de dimensionamento de Máquina Virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

