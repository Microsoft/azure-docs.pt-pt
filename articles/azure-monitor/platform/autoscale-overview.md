---
title: Visão geral do dimensionamento automático em máquinas virtuais, serviços de nuvem e aplicativos Web
description: Dimensionamento automático em Microsoft Azure. Aplica-se a máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais, serviços de nuvem e aplicativos Web.
ms.service: azure-monitor
ms.subservice: autoscale
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 2aeb0bc174eb5a94d485f4eafe000ecb3f693e5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552283"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Visão geral do dimensionamento automático em Máquinas Virtuais do Microsoft Azure, serviços de nuvem e aplicativos Web
Este artigo descreve o que Microsoft Azure o dimensionamento automático é, seus benefícios e como começar a usá-lo.  

Azure Monitor dimensionamento automático se aplica somente aos [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> O Azure tem dois métodos de dimensionamento automático. Uma versão mais antiga do dimensionamento automático se aplica a máquinas virtuais (conjuntos de disponibilidade). Esse recurso tem suporte limitado e é recomendável migrar para conjuntos de dimensionamento de máquinas virtuais para oferecer suporte a dimensionamento automático mais rápido e confiável. Um link sobre como usar a tecnologia mais antiga está incluído neste artigo.  
>
>

## <a name="what-is-autoscale"></a>O que é dimensionamento automático?
O dimensionamento automático permite que você tenha a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite que você adicione recursos para lidar com aumentos de carga e também Economize dinheiro removendo os recursos que estão ociosos. Você especifica um número mínimo e máximo de instâncias a serem executadas e adiciona ou remove VMs automaticamente com base em um conjunto de regras. Ter um mínimo garante que seu aplicativo está sempre em execução mesmo sem carga. Ter um máximo limita o total possível de custo por hora. Você dimensiona automaticamente entre esses dois extremos usando regras criadas por você.

 ![Dimensionamento automático explicado. Adicionar e remover VMs](./media/autoscale-overview/AutoscaleConcept.png)

Quando condições de regra são atendidas, uma ou mais ações de dimensionamento automático são disparadas. Você pode adicionar e remover VMs ou executar outras ações. O diagrama conceitual a seguir mostra esse processo.  

 ![Diagrama de fluxo de dimensionamento automático](./media/autoscale-overview/Autoscale_Overview_v4.png)

A explicação a seguir se aplica às partes do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas de Recursos
Os recursos emitem métricas, essas métricas são processadas posteriormente por regras. As métricas são fornecidas por meio de métodos diferentes.
Os conjuntos de dimensionamento de máquinas virtuais usam dados de telemetria de agentes de diagnóstico do Azure, enquanto a telemetria para aplicativos Web e serviços de nuvem vem diretamente da infraestrutura do Azure. Algumas estatísticas comumente usadas incluem uso de CPU, uso de memória, contagens de threads, comprimento da fila e uso do disco. Para obter uma lista de quais dados de telemetria você pode usar, consulte [métricas comuns de dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Métricas Personalizadas
Você também pode aproveitar suas próprias métricas personalizadas que seus aplicativos podem emitir. Se você configurou seus aplicativos para enviar métricas para Application Insights você pode aproveitar essas métricas para tomar decisões sobre se dimensionar ou não.

## <a name="time"></a>Tempo
As regras baseadas em agendamento são baseadas em UTC. Você deve definir seu fuso horário corretamente ao configurar suas regras.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de dimensionamento automático, mas você pode ter muitos deles. Você pode criar regras complexas de sobreposição conforme necessário para sua situação.  Os tipos de regra incluem  

* **Baseado em métrica** – por exemplo, execute esta ação quando o uso da CPU estiver acima de 50%.
* **Com base no tempo** – por exemplo, dispare um webhook a cada 8:00 no sábado em um determinado fuso horário.

As regras com base em métrica medem a carga do aplicativo e adicionam ou removem VMs com base nessa carga. As regras baseadas em agendamento permitem que você dimensione quando você vê os padrões de tempo em sua carga e deseja dimensionar antes que ocorra um possível aumento ou diminuição da carga.  

## <a name="actions-and-automation"></a>Ações e automação
As regras podem disparar um ou mais tipos de ações.

* **Expandir ou** reduzir as VMs em escala
* **Email** -enviar email para administradores de assinatura, coadministradores e/ou endereço de email adicional que você especificar
* **Automatizar por meio de WebHooks** – chame WebHooks, que podem disparar várias ações complexas dentro ou fora do Azure. Dentro do Azure, você pode iniciar um runbook de automação do Azure, Azure Function ou aplicativo lógico do Azure. O exemplo de URL de terceiros fora do Azure inclui serviços como margem de atraso e twilio.

## <a name="autoscale-settings"></a>Definições do Dimensionamento Automático
O dimensionamento automático usa a seguinte terminologia e estrutura.

- Uma **configuração de dimensionamento automático** é lida pelo mecanismo de dimensionamento automático para determinar se é possível escalar ou reduzir verticalmente. Ele contém um ou mais perfis, informações sobre o recurso de destino e as configurações de notificação.

  - Um **perfil de dimensionamento automático** é uma combinação de um:

    - **configuração de capacidade**, que indica os valores mínimo, máximo e padrão para o número de instâncias.
    - **conjunto de regras**, cada uma delas inclui um gatilho (tempo ou métrica) e uma ação de escala (para cima ou para baixo).
    - **recorrência**, que indica quando o dimensionamento automático deve colocar esse perfil em vigor.

      Você pode ter vários perfis, o que permite cuidar de diferentes requisitos de sobreposição. Você pode ter perfis de dimensionamento automático diferentes para diferentes horas do dia ou dias da semana, por exemplo.

  - Uma **configuração de notificação** define quais notificações devem ocorrer quando um evento de dimensionamento automático ocorre com base na satisfação dos critérios de um dos perfis da configuração de dimensionamento automático. O dimensionamento automático pode notificar um ou mais endereços de email ou fazer chamadas para um ou mais WebHooks.


![Configuração de autoescala do Azure, perfil e estrutura de regra](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos e descrições configuráveis está disponível na [API REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931928.aspx).

Para obter exemplos de código, consulte

* [Configuração avançada de dimensionamento automático usando modelos do Resource Manager para conjuntos de dimensionamento de VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [API REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Escala horizontal vs vertical
O dimensionamento automático só dimensiona horizontalmente, que é um aumento ("out") ou diminui ("in") no número de instâncias de VM.  A horizontal é mais flexível em uma situação de nuvem, pois permite que você execute milhares de VMs para lidar com a carga.

Por outro lado, o dimensionamento vertical é diferente. Ele mantém o mesmo número de VMs, mas torna as VMs mais ("acima") ou menos ("inoperante") poderosas. A potência é medida na memória, na velocidade da CPU, no espaço em disco, etc.  O dimensionamento vertical tem mais limitações. Depende da disponibilidade de um hardware maior, que atinge rapidamente um limite superior e pode variar por região. O dimensionamento vertical normalmente também requer uma VM para parar e reiniciar.


## <a name="methods-of-access"></a>Métodos de acesso
Você pode configurar o dimensionamento automático via

* [Portal do Azure](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Interface de Linha de Comandos (CLI) de várias plataformas](../../azure-monitor/platform/cli-samples.md#autoscale)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Serviços com suporte para dimensionamento automático
| Serviço | Documentos de & de esquema |
| --- | --- |
| Aplicações Web |[Dimensionando aplicativos Web](../../azure-monitor/platform/autoscale-get-started.md) |
| Serviços em Nuvem |[Dimensionamento automático de um serviço de nuvem](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas virtuais: clássico |[Dimensionando conjuntos de disponibilidade de máquina virtual clássica](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: conjuntos de dimensionamento do Windows |[Dimensionamento de conjuntos de dimensionamento de máquinas virtuais no Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Máquinas virtuais: conjuntos de dimensionamento do Linux |[Dimensionamento de conjuntos de dimensionamento de máquinas virtuais no Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Máquinas virtuais: exemplo do Windows |[Configuração avançada de dimensionamento automático usando modelos do Resource Manager para conjuntos de dimensionamento de VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Serviço de gerenciamento de API|[Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o dimensionamento automático, use os passo a passos de dimensionamento automático listados anteriormente ou consulte os seguintes recursos:

* [Azure Monitor métricas comuns de dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Práticas recomendadas para Azure Monitor dimensionamento automático](../../azure-monitor/platform/autoscale-best-practices.md)
* [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)
* [Solucionando problemas de dimensionamento automático de conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

