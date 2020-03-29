---
title: Visão geral da escala automática em Máquinas Virtuais, Serviços de Nuvem e Aplicações Web
description: Escala automática no Microsoft Azure. Aplica-se a Máquinas Virtuais, conjuntos de escala de máquinas virtuais, serviços de nuvem e aplicações web.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a60c03f1928b38c78a59edca4b5493307d7d19d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364378"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Descrição geral do dimensionamento automático em Máquinas Virtuais do Microsoft Azure, Serviços Cloud e Aplicações Web
Este artigo descreve o que é a escala automática do Microsoft Azure, os seus benefícios e como começar a usá-lo.  

A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

> [!NOTE]
> O Azure tem dois métodos de escala automática. Uma versão mais antiga da escala automática aplica-se às Máquinas Virtuais (conjuntos de disponibilidade). Esta funcionalidade tem suporte limitado e recomendamos a migração para conjuntos de escala de máquinas virtuais para um suporte de escala automática mais rápido e fiável. Neste artigo está incluído um link sobre como utilizar a tecnologia mais antiga.  
>
>

## <a name="what-is-autoscale"></a>O que é autoescala?
A escala automática permite-lhe dispor da quantidade certa de recursos em execução para lidar com a carga na sua aplicação. Permite-lhe adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Especifica um número mínimo e máximo de instâncias para executar e adicionar ou remover VMs automaticamente com base num conjunto de regras. Ter um mínimo garante que a sua aplicação está sempre a funcionar mesmo sem carga. Ter um limite máximo o seu custo total possível de hora. Escala-se automaticamente entre estes dois extremos usando regras que cria.

 ![Autoescala explicado. Adicionar e remover VMs](./media/autoscale-overview/AutoscaleConcept.png)

Quando as condições de regra são satisfeitas, uma ou mais ações de escala automática são desencadeadas. Pode adicionar e remover VMs, ou realizar outras ações. O diagrama conceptual seguinte mostra este processo.  

 ![Diagrama de fluxo de escala automática](./media/autoscale-overview/Autoscale_Overview_v4.png)

A seguinte explicação aplica-se às peças do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas de Recursos
Os recursos emitem métricas, estas métricas são posteriormente processadas por regras. As métricas vêm através de métodos diferentes.
Os conjuntos de escala de máquinas virtuais utilizam dados de telemetria de agentes de diagnóstico Azure, enquanto a telemetria para aplicações Web e serviços Cloud provém diretamente da Infraestrutura Azure. Algumas estatísticas comumente utilizadas incluem o uso do CPU, o uso da memória, as contagens de fios, o comprimento da fila e o uso do disco. Para obter uma lista dos dados de telemetria que pode utilizar, consulte [Métricas Comuns](../../azure-monitor/platform/autoscale-common-metrics.md)de Escala Automática .

## <a name="custom-metrics"></a>Métricas Personalizadas
Também pode alavancar as suas próprias métricas personalizadas que a sua aplicação(s) pode estar a emitir. Se configurar a sua aplicação(s) para enviar métricas para Application Insights, pode aproveitar essas métricas para tomar decisões sobre a escala ou não.

## <a name="time"></a>Hora
As regras baseadas em horários baseiam-se na UTC. Deve definir o seu fuso horário corretamente ao definir as suas regras.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de escala automática, mas pode sê-lo. Pode criar regras de sobreposição complexas, conforme necessário para a sua situação.  Os tipos de regras incluem  

* **Baseado em métricas** - Por exemplo, faça esta ação quando o uso de CPU é superior a 50%.
* **Baseado no tempo** - Por exemplo, desencadeie um webhook a cada 8h de sábado num determinado fuso horário.

As regras métricas medem a carga da aplicação e adicionam ou removem VMs com base nessa carga. As regras baseadas em horários permitem-lhe escalar quando vê padrões de tempo na sua carga e quer escalar antes que ocorra um possível aumento ou diminuição da carga.  

## <a name="actions-and-automation"></a>Ações e automação
As regras podem desencadear um ou mais tipos de ações.

* **Escala** - VMs de escala dentro ou fora
* Email - Envie **e-mail** para administradores de subscrição, coadministradores e/ou endereço de e-mail adicional que especifique
* **Automatizar através de webhooks** - Call webhooks, que podem desencadear múltiplas ações complexas dentro ou fora do Azure. Dentro do Azure, pode iniciar um livro de execução Azure Automation, Função Azure ou App Lógica Azure. Exemplo, o URL de terceiros fora do Azure inclui serviços como Slack e Twilio.

## <a name="autoscale-settings"></a>Definições do Dimensionamento Automático
A escala automática utilize a seguinte terminologia e estrutura.

- Uma **definição de escala automática** é lida pelo motor de escala automática para determinar se deve escalar para cima ou para baixo. Contém um ou mais perfis, informações sobre o recurso-alvo e definições de notificação.

  - Um **perfil de escala automática** é uma combinação de:

    - **regulação da capacidade**, que indica os valores mínimos, máximos e predefinidos para o número de instâncias.
    - **conjunto de regras,** cada uma das quais inclui um gatilho (tempo ou métrica) e uma ação de escala (para cima ou para baixo).
    - **recorrência**, que indica quando a escala automática deve pôr este perfil em vigor.

      Pode ter vários perfis, que lhe permitem cuidar de diferentes requisitos de sobreposição. Pode ter diferentes perfis de escala automática para diferentes horas do dia ou dias da semana, por exemplo.

  - Uma definição de **notificação** define quais as notificações que devem ocorrer quando ocorre um evento de escala automática com base na satisfação dos critérios de um dos perfis da definição de escala automática. A escala automática pode notificar um ou mais endereços de e-mail ou fazer chamadas para um ou mais webhooks.


![Definição, perfil e estrutura de regras de escala automática Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos e descrições configuráveis está disponível na [API REST](https://msdn.microsoft.com/library/dn931928.aspx)De Escala Automática .

Para exemplos de código, ver

* [Configuração avançada de escala automática usando modelos de Gestor de Recursos para conjuntos de escala VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [API REST DE Escala Automática](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Escala horizontal vs vertical
A escala automática apenas escala horizontalmente, o que é um aumento ("out") ou diminuição ("in") no número de casos vm.  A horizontal é mais flexível numa situação de nuvem, pois permite-lhe executar potencialmente milhares de VMs para lidar com a carga.

Em contraste, a escala vertical é diferente. Mantém o mesmo número de VMs, mas torna os VMs mais ("para cima") ou menos ("down") poderosos. A potência é medida na memória, velocidade cpu, espaço do disco, etc.  A escala vertical tem mais limitações. Depende da disponibilidade de hardware maior, que rapidamente atinge um limite superior e pode variar por região. A escala vertical também requer geralmente um VM para parar e reiniciar.


## <a name="methods-of-access"></a>Métodos de acesso
Pode configurar a escala automática via

* [Portal Azure](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Interface de Linha de Comandos (CLI) de várias plataformas](../../azure-monitor/platform/cli-samples.md#autoscale)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Serviços suportados para escala automática
| Serviço | Schema & Docs |
| --- | --- |
| Aplicações Web |[Aplicações web de escala](../../azure-monitor/platform/autoscale-get-started.md) |
| Serviços Cloud |[Escala automática de um serviço de nuvem](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas Virtuais: Clássico |[Conjuntos clássicos de disponibilidade de máquinas virtuais](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: conjuntos de escala do Windows |[Escalar conjuntos de escala de máquinas virtuais no Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Máquinas Virtuais: Conjuntos de escala de linux |[Conjuntos de escala virtual de escala de máquinas em Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Máquinas Virtuais: Exemplo do Windows |[Configuração avançada de escala automática usando modelos de Gestor de Recursos para conjuntos de escala VM](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Serviço de Gestão API|[Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a escala automática, utilize os Walkthroughs de Escala Automática listados anteriormente ou consulte os seguintes recursos:

* [Métricas comuns de escala automática Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Melhores práticas para o dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Utilize ações de escala automática para enviar notificações de alerta de e-mail e webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API REST DE Escala Automática](https://msdn.microsoft.com/library/dn931953.aspx)
* [Resolução de problemas de escala de máquina virtual define automaticamente](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

