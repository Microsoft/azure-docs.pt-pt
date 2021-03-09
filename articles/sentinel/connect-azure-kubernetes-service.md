---
title: Ligar registos de diagnóstico do Serviço Azure Kubernetes (AKS) a Azure Sentinel
description: Saiba como usar a Azure Policy para ligar os registos de diagnóstico do Serviço Azure Kubernetes ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: b85c280f99dc1bb4837169c2167b8b2ffb902b7d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491321"
---
# <a name="connect-azure-kubernetes-service-diagnostics-and-auditing-logs"></a>Ligar diagnósticos e registos de auditoria do Serviço Azure Kubernetes

O Azure Kubernetes Service (AKS) é um serviço de orquestração de contentores de código aberto e totalmente gerido que lhe permite implantar, escalar e gerir contentores Docker e aplicações baseadas em contentores num ambiente de cluster.

Este conector permite-lhe transmitir os seus registos de diagnóstico do Serviço Azure Kubernetes (AKS) para o Azure Sentinel, permitindo-lhe monitorizar continuamente a atividade em todos os seus casos. 

Saiba mais sobre [a monitorização do Serviço Azure Kubernetes](../azure-monitor/containers/container-insights-overview.md) e sobre [a telemetria de diagnóstico AKS](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ingerir registos AKS em Azure Sentinel:

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Para utilizar a Política Azure para aplicar uma política de streaming de registos aos recursos AKS, deve ser-lhe atribuída a função de Proprietário para o âmbito de atribuição de políticas.

## <a name="connect-to-azure-kubernetes-service"></a>Ligue-se ao Serviço Azure Kubernetes

Este conector utiliza a Azure Policy para aplicar uma única configuração de streaming de serviço Azure Kubernetes a uma coleção de instâncias, definida como um âmbito. Pode ver os tipos de registo ingeridos do Serviço Azure Kubernetes no lado esquerdo da página do conector, nos **tipos de Dados**.

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **O Serviço Azure Kubernetes (AKS)** da galeria de conectores de dados e, em seguida, selecione **A Página do Conector Aberto** no painel de pré-visualização.

1. Na secção **de Configuração** da página do conector, expanda os **registos de Ativação de Diagnóstico no Serviço Azure Kubernetes (AKS)**.

1. Selecione o botão de assistente de **atribuição de política de lançamento azure.**

    O assistente de atribuição de políticas abre, pronto para criar uma nova política chamada **Implementação - Configurações de diagnóstico de configuração para O Serviço Azure Kubernetes para Log Analytics workspace**.

    1. No separador **Básicos,** clique no botão com os três pontos no **Âmbito** para selecionar a sua subscrição (e, opcionalmente, um grupo de recursos). Também pode adicionar uma descrição.

    1. No **separador Parâmetros,** escolha o seu espaço de trabalho Azure Sentinel na lista de drop-down do espaço de **trabalho Log Analytics.** Os restantes campos de down-down representam os tipos de registo de diagnóstico disponíveis. Deixe marcado como "Verdadeiro" todos os tipos de registo que pretende ingerir.

    1. Para aplicar a política nos recursos existentes, selecione o **separador Remediação** e marque a caixa de verificação de **tarefas de reparação.**

    1. No **separador 'Rever +' criar,** clique em **Criar.** A sua apólice está agora atribuída ao âmbito que escolheu.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Serviço Azure Kubernetes ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
