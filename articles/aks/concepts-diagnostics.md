---
title: Visão geral do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre agrupamentos de auto-diagnóstico no Serviço Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183367"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Visão geral do Serviço Azure Kubernetes (pré-visualização)

Problemas de resolução de problemas Problemas O cluster do Serviço Azure Kubernetes (AKS) é uma parte importante da manutenção do seu cluster, especialmente se o seu cluster estiver a executar cargas de trabalho críticas da missão. A AKS Diagnostics é uma experiência inteligente e de autodiagnósc diagnóstico que o ajuda a identificar e resolver problemas no seu cluster. O AKS Diagnostics é nativo da nuvem e pode usá-lo sem configuração extra ou custo de faturação.

Esta funcionalidade está agora em pré-visualização pública.

## <a name="open-aks-diagnostics"></a>Abrir diagnósticos AKS

Para aceder aos diagnósticos AKS:

- Navegue para o seu cluster Kubernetes no [portal Azure](https://portal.azure.com).
- Clique no **Diagnóstico e resolva problemas** na navegação à esquerda, que abre o Diagnóstico AKS.
- Escolha uma categoria que melhor descreva a questão do seu cluster utilizando as palavras-chave no azulejo inicial, ou escreva uma palavra-chave que melhor descreva o seu problema na barra de pesquisa, por _exemplo, Problemas de Nó de Cluster_.

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico

Depois de clicar numa categoria, pode ver um relatório de diagnóstico específico do seu cluster. Relatório de diagnóstico chama inteligentemente se houver algum problema no seu cluster com ícones de estado. Pode aprofundar cada tópico clicando em **Mais Informações** para ver a descrição detalhada do problema, ações recomendadas, links para documentos úteis, métricas relacionadas e dados de registo. Os relatórios de diagnóstico são gerados de forma inteligente com base no estado atual do seu cluster depois de executar uma variedade de verificações. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar o problema do seu cluster e encontrar os próximos passos para resolver o problema.

![Relatório de Diagnóstico](./media/concepts-diagnostics/diagnostic-report.png)

![Relatório de Diagnóstico Alargado](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights

As seguintes verificações de diagnóstico estão disponíveis no **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problemas no nó de cluster

O Nó de Cluster Emite verifica se existem problemas relacionados com nó que podem fazer com que o seu cluster se comporte de forma inesperada.

- Questões de prontidão do nó
- Falhas nos nódoas
- Recursos insuficientes
- Configuração IP em falta do nó
- Falhas no nó CNI
- Nó não encontrado
- Desligar a alimentação do nó
- Falha de autenticação do nó
- Nó kube-proxy velho

### <a name="create-read-update--delete-operations"></a>Criar, ler, atualizar & eliminar operações

As operações da CRUD verificam quaisquer operações CRUD que possam causar problemas no seu cluster.

- Sub-rede de utilização elimina erro de funcionamento
- Grupo de segurança de rede elimina erro de operação
- Tabela de rota em utilização elimina erro de operação
- Erro de fornecimento de recursos referenciado
- Endereço IP público apagar erro de funcionamento
- Falha de implantação devido a quota de implantação
- Erro de operação devido à política de organização
- Registo de assinatura em falta
- Erro de provisão de extensão VM
- Capacidade da sub-rede
- Erro excededo da quota

### <a name="identity-and-security-management"></a>Gestão de identidade e segurança

A Gestão de Identidade e Segurança deteta erros de autenticação e autorização que podem impedir a comunicação ao seu cluster.

- Falhas de autorização de nó
- 401 erros
- Erros 403

## <a name="next-steps"></a>Passos seguintes

Colete registos para ajudá-lo a resolver ainda mais os seus problemas de cluster utilizando [o Periscope AKS](https://aka.ms/aksperiscope).

Leia a secção de práticas de [triagem](/azure/architecture/operator-guides/aks/aks-triage-practices) do guia de operações do dia-2 da AKS.

Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) adicionando "[Diag]" no título.