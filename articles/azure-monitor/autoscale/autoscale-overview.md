---
title: Autoescala no Microsoft Azure
description: Autoescala no Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726007"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Visão geral da autoescala no Microsoft Azure
Este artigo descreve o que é a escala automática da Microsoft Azure, os seus benefícios e como começar a usá-lo.  

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviços de aplicações - Aplicações Web,](https://azure.microsoft.com/services/app-service/web/) [serviços de gestão API](../../api-management/api-management-key-concepts.md)e [Clusters Azure Data Explorer](/azure/data-explorer/).

> [!NOTE]
> Azure tem dois métodos de autoescala. Uma versão mais antiga de autoescala aplica-se a Máquinas Virtuais (conjuntos de disponibilidade). Esta funcionalidade tem suporte limitado e recomendamos migrar para conjuntos de escala de máquinas virtuais para suportes de autoescala mais rápidos e fiáveis. Neste artigo está incluído um link sobre como utilizar a tecnologia mais antiga.  
>

## <a name="what-is-autoscale"></a>O que é autoescala?
A autoescala permite-lhe ter a quantidade certa de recursos a correr para lidar com a carga na sua aplicação. Permite-lhe adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Especifica um número mínimo e máximo de instâncias para executar e adicionar ou remover VMs automaticamente com base num conjunto de regras. Ter um mínimo garante que a sua aplicação está sempre a funcionar mesmo sem carga. Tendo um limite máximo o seu custo horário total possível. Escala-se automaticamente entre estes dois extremos usando regras que cria.

 ![Autoscale explicado. Adicionar e remover VMs](./media/autoscale-overview/AutoscaleConcept.png)

Quando as condições de regra são satisfeitas, uma ou mais ações de autoescala são desencadeadas. Pode adicionar e remover VMs ou executar outras ações. O seguinte diagrama conceptual mostra este processo.  

 ![Diagrama de fluxo de autoescala](./media/autoscale-overview/Autoscale_Overview_v4.png)

A seguinte explicação aplica-se às peças do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas de Recursos
Os recursos emitem métricas, estas métricas são processadas mais tarde por regras. As métricas vêm através de diferentes métodos.
Conjuntos de escala de máquinas virtuais usam dados de telemetria de agentes de diagnóstico Azure, enquanto a telemetria para aplicações Web e serviços cloud vem diretamente da Infraestrutura Azure. Algumas estatísticas geralmente utilizadas incluem uso de CPU, uso da memória, contagem de fios, comprimento da fila e uso do disco. Para obter uma lista dos dados de telemetria que pode utilizar, consulte [métricas comuns de autoescala.](autoscale-common-metrics.md)

## <a name="custom-metrics"></a>Métricas Personalizadas
Também pode aproveitar as suas próprias métricas personalizadas que as suas aplicações podem estar a emitir. Se configurar a sua(s) aplicação(s) para enviar métricas para Application Insights, pode aproveitar essas métricas para tomar decisões sobre a escala ou não.

## <a name="time"></a>Hora
As regras baseadas em horários baseiam-se na UTC. Tem de definir corretamente o seu fuso horário ao definir as suas regras.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de autoescalação, mas pode ter muitas delas. Pode criar regras complexas de sobreposição, conforme necessário para a sua situação.  Os tipos de regras incluem  

* **Baseado em métrica** - Por exemplo, faça esta ação quando o uso do CPU é superior a 50%.
* **Baseado no tempo** - Por exemplo, desencadeie um webhook a cada 8h de sábado em um determinado fuso horário.

As regras métricas medem a carga da aplicação e adicionam ou removem VMs com base nessa carga. As regras baseadas no calendário permitem-lhe escalar quando vê os padrões de tempo na sua carga e pretende escalar antes que ocorra um possível aumento ou diminuição de carga.  

## <a name="actions-and-automation"></a>Ações e automação
As regras podem desencadear um ou mais tipos de ações.

* **Escala** - Escala VMs dentro ou fora
* **Email** - Enviar e-mail para administradores de subscrição, coadministradores e/ou endereço de e-mail adicional que especifique
* **Automatizar via webhooks** - Call webhooks, que podem desencadear múltiplas ações complexas dentro ou fora de Azure. Dentro do Azure, pode iniciar um runbook Azure Automation, Azure Function ou Azure Logic App. Exemplo de URL de terceiros fora de Azure incluem serviços como Slack e Twilio.

## <a name="autoscale-settings"></a>Definições do Dimensionamento Automático
Utilize automaticamente a seguinte terminologia e estrutura.

- Uma **definição de autoescala** é lida pelo motor de autoescala para determinar se deve escalar para cima ou para baixo. Contém um ou mais perfis, informações sobre o recurso-alvo e definições de notificação.

  - Um **perfil de autoescala** é uma combinação de a:

    - **definição de capacidade**, que indica os valores mínimos, máximos e predefinidos para o número de casos.
    - **conjunto de regras**, cada uma das quais inclui um gatilho (tempo ou métrica) e uma ação de escala (para cima ou para baixo).
    - **recorrência**, que indica quando a autoescala deve colocar este perfil em vigor.

      Pode ter vários perfis, que lhe permitem cuidar de diferentes requisitos de sobreposição. Pode ter diferentes perfis de autoescala para diferentes horas do dia ou dias da semana, por exemplo.

  - Uma **definição de notificação** define quais as notificações que devem ocorrer quando ocorre um evento de autoescala com base na satisfação dos critérios de um dos perfis da definição de autoescala. A autoescala pode notificar um ou mais endereços de e-mail ou fazer chamadas para um ou mais webhooks.


![Definição de autoescala azul, perfil e estrutura de regras](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos e descrições configuráveis está disponível na [API autoescala REST](/rest/api/monitor/autoscalesettings).

Para exemplos de código, ver

* [Configuração avançada de autoescalação utilizando modelos de gestor de recursos para conjuntos de escala VM](autoscale-virtual-machine-scale-sets.md)  
* [API de REPOUSO de Autoescala](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling&quot;></a>Escala horizontal vs vertical
A escala automática apenas horizontalmente, o que é um aumento (&quot;out") ou diminuição ("in") no número de instâncias VM.  Horizontal é mais flexível numa situação de nuvem, pois permite-lhe executar potencialmente milhares de VMs para manusear a carga.

Em contraste, a escala vertical é diferente. Mantém o mesmo número de VMs, mas torna os VMs mais ("up") ou menos ("down") poderosos. A energia é medida na memória, na velocidade do CPU, no espaço do disco, etc.  A escala vertical tem mais limitações. Depende da disponibilidade de hardware maior, que rapidamente atinge um limite superior e pode variar por região. A escala vertical também requer geralmente um VM para parar e reiniciar.

## <a name="methods-of-access"></a>Métodos de acesso
Pode configurar a autoescala através

* [Portal do Azure](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [Interface de Linha de Comandos (CLI) de várias plataformas](../cli-samples.md#autoscale)
* [API REST do Azure Monitor](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Serviços apoiados para autoescala
| Serviço | Schema & Docs |
| --- | --- |
| Aplicações Web |[Aplicativos Web de escala](autoscale-get-started.md) |
| Serviços Cloud |[Autoescala um Serviço de Nuvem](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas Virtuais: Clássico |[Conjuntos clássicos de disponibilidade de máquinas virtuais](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Máquinas virtuais: conjuntos de escala de windows |[Escala de conjuntos de escala de máquina virtual de escala no Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Máquinas virtuais: conjuntos de escala de linux |[Conjuntos de escala de máquina virtual de escala em Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Máquinas virtuais: Exemplo do Windows |[Configuração avançada de autoescalação utilizando modelos de gestor de recursos para conjuntos de escala VM](autoscale-virtual-machine-scale-sets.md) |
| Serviço de Aplicações do Azure |[Dimensione uma aplicação no serviço Azure App](../../app-service/manage-scale-up.md)|
| Serviço de Gestão da API|[Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure](../../api-management/api-management-howto-autoscale.md)
| Aglomerados de exploradores de dados Azure|[Gerir clusters Azure Data Explorer escalando para acomodar a procura em mudança](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[Adicionar capacidade de ambiente de serviço de integração (ISE)](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Configurar o dimensionamento automático para aplicações de microsserviços](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Atualizar automaticamente as unidades de mensagens de um espaço de nomes do Azure Service Bus](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a autoescala, utilize as Imagens auto-escala listadas anteriormente ou consulte os seguintes recursos:

* [Azure Monitor autoescala métricas comuns](autoscale-common-metrics.md)
* [Melhores práticas para o dimensionamento automático do Azure Monitor](autoscale-best-practices.md)
* [Utilize ações de escala automática para enviar notificações de alerta de email e webhook](autoscale-webhook-email.md)
* [API de REPOUSO de Autoescala](/rest/api/monitor/autoscalesettings)
* [Resolução de problemas escala de máquina virtual define autoescala](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)