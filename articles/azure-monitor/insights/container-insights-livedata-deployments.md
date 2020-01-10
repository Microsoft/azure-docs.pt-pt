---
title: Exibir Azure Monitor para implantações de contêineres (visualização) | Microsoft Docs
description: Este artigo descreve a exibição em tempo real de implantações do kubernetes sem usar o kubectl no Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404777"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Como exibir implantações (visualização) em tempo real

Com Azure Monitor para contêineres, o recurso Exibir implantações (versão prévia) emula o acesso direto aos objetos de implantação kubernetes em tempo real, expondo os comandos `kubeclt get deployments` e `kubectl describe deployment {your deployment}`. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não têm suporte com esse recurso. Esse recurso depende diretamente do acesso à API kubernetes por meio de um servidor proxy do seu navegador. Habilitar a segurança de rede para bloquear a API do kubernetes desse proxy bloqueará esse tráfego. 

>[!NOTE]
>Esse recurso está disponível em todas as regiões do Azure, incluindo o Azure China. No momento, ele não está disponível no Azure no governo dos EUA.

Para saber mais, examine a documentação do kubernetes sobre [implantações](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Como funciona

O recurso de dados dinâmicos (versão prévia) acessa diretamente a API kubernetes e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

O recurso implantações (versão prévia) executa uma carga única (atualizável) no ponto de extremidade de implantações `/apis/apps/v1/deployments`. Ele permite que você selecione uma determinada implantação e carregue os detalhes da descrição para essa implantação específica no ponto de extremidade de implantação `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`. 

Selecionar **Atualizar** na parte superior esquerda da página atualiza a lista de implantação. Isso simula a execução do comando `kubectl` novamente. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante a sessão são excluídas quando você fecha o navegador ou navega para fora dela.  

>[!NOTE]
>Não é possível fixar dados dinâmicos (versão prévia) do console do em um painel do Azure.

## <a name="deployments-describe"></a>Descrever implantações

Para exibir os detalhes de uma implantação, que é equivalente a `kubectl describe deployment`, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**. 

3. Selecione a guia **implantações (visualização)** .

    ![Exibição de implantações no portal do Azure](./media/container-insights-livedata-deployments/deployment-view.png)

A exibição mostra uma lista de todas as implantações em execução junto com o namespace e outras informações detalhadas, emulando a execução do comando `kubectl get deployments –all-namespaces`. Você pode classificar os resultados selecionando qualquer uma das colunas. 

![Detalhes do painel de propriedades de implantações](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando você seleciona uma implantação na lista, um painel de propriedades é exibido automaticamente no lado direito da página. Ele mostra informações relacionadas à implantação selecionada que você exibirá se executou o comando `kubectl describe deployment {deploymentName}`. Talvez você tenha notado que estão faltando alguns detalhes nas informações de descrição. Muito notavelmente, o **modelo** está ausente. A seleção da guia **RAW** permite que você navegue até os detalhes de descrição não analisados.  

![Painel de propriedades de implantações detalhes brutos](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Ao examinar os detalhes da implantação, você pode ver os logs de contêiner e os eventos em tempo real. Selecione o painel **Mostrar console ao vivo** e os dados dinâmicos (versão prévia) serão exibidos abaixo da grade de dados de implantações, em que você pode exibir dados de log dinâmicos em um fluxo contínuo. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começam a transmitir para o console.

Você também pode filtrar por eventos de namespace ou de nível de cluster. Para saber mais sobre a exibição de dados em tempo real no console, consulte [exibir dados dinâmicos (versão prévia) com Azure monitor para contêineres](container-insights-livedata-overview.md). 

![As implantações exibem dados dinâmicos no console](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para criar alertas, visualizações ou executar análise adicional de seus clusters.
