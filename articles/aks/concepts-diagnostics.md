---
title: Visão geral do Serviço Azure Kubernetes (AKS)
description: Conheça os clusters auto-diagnóstico no Serviço Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126600"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Visão geral do serviço Azure Kubernetes (pré-visualização)

Problemas de resolução de problemas O serviço Azure Kubernetes Service (AKS) é uma parte importante da manutenção do seu cluster, especialmente se o seu cluster estiver a executar cargas de trabalho críticas de missão. A AKS Diagnostics é uma experiência inteligente e auto-diagnóstico que o ajuda a identificar e resolver problemas no seu cluster. O AKS Diagnostics é nativo da nuvem, e você pode usá-lo sem configuração extra ou custo de faturação.

Esta funcionalidade está agora em pré-visualização pública.

## <a name="open-aks-diagnostics"></a>Diagnósticos AKS abertos

Para aceder aos Diagnósticos AKS:

- Navegue para o seu cluster Kubernetes no [portal Azure.](https://portal.azure.com)
- Clique em **Diagnosticar e resolver problemas** na navegação esquerda, o que abre o AKS Diagnostics.
- Escolha uma categoria que melhor descreva a questão do seu cluster utilizando as palavras-chave no azulejo da página inicial, ou escreva uma palavra-chave que melhor descreva o seu problema na barra de pesquisa, por _exemplo, problemas_de nó cluster .

![Página inicial](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico

Depois de clicar numa categoria, pode ver um relatório de diagnóstico específico do seu cluster. O relatório de diagnóstico chama inteligentemente se houver algum problema no seu cluster com ícones de estado. Pode aprofundar cada tópico clicando em **Mais Informações** para ver uma descrição detalhada do problema, ações recomendadas, links para documentos úteis, métricas relacionadas e dados de registo. Os relatórios de diagnóstico são gerados de forma inteligente com base no estado atual do seu cluster depois de executar uma variedade de verificações. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar o problema do seu cluster e encontrar os próximos passos para resolver o problema.

![Relatório de Diagnóstico](./media/concepts-diagnostics/diagnostic-report.png)

![Relatório de Diagnóstico Expandido](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights

Os seguintes controlos de diagnóstico estão disponíveis no **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problemas do Nó de Cluster

Cluster Node Issues verifica por questões relacionadas com o nó que podem fazer com que o seu cluster se compore inesperadamente.

- Problemas de prontidão do nó
- Falhas no nó
- Recursos insuficientes
- Configuração IP desaparecida do nó
- Falhas no Nó CNI
- Nó não encontrado
- Desligando a energia do nó
- Falha na autenticação do nó
- Nó kube-proxy velho

### <a name="create-read-update--delete-operations"></a>Criar, ler, atualizar e eliminar operações

A CRUD Operations verifica quaisquer operações crud que possam causar problemas no seu cluster.

- Sub-rede de utilização elimina erro de funcionamento
- Grupo de segurança da rede elimina erro de operação
- Tabela de rota de utilização elimina erro de funcionamento
- Erro de fornecimento de recursos referenciado
- Endereço IP público elimina erro de operação
- Falha de implantação devido a quota de implantação
- Erro de operação devido à política de organização
- Falta de registo de assinaturas
- Erro de provisionamento de extensão VM
- Capacidade de sub-rede
- Erro de quota ultrapassado

### <a name="identity-and-security-management"></a>Gestão de identidade e segurança

A Gestão de Identidade e Segurança deteta erros de autenticação e autorização que podem impedir a comunicação ao seu cluster.

- Falhas na autorização do nó
- 401 erros
- 403 erros

## <a name="next-steps"></a>Passos seguintes

Colete registos para ajudá-lo a resolver ainda mais problemas com os seus problemas de cluster utilizando o [Periscópio AKS](https://aka.ms/aksperiscope).

Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) adicionando "[Diag]" no título.
