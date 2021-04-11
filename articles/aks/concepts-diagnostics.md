---
title: Visão geral do Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre agrupamentos de auto-diagnóstico no Serviço Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011563"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Visão geral do Serviço Azure Kubernetes (pré-visualização)

Problemas de resolução de problemas Problemas O cluster do Serviço Azure Kubernetes (AKS) desempenha um papel importante na manutenção do seu cluster, especialmente se o seu cluster estiver a executar cargas de trabalho críticas da missão. AKS Diagnostics é uma experiência inteligente e auto-diagnóstico que:
* Ajuda-o a identificar e a resolver problemas no seu agrupamento. 
* É nativo da nuvem.
* Não requer configuração extra ou custo de faturação.

Esta funcionalidade está agora em pré-visualização pública. 

## <a name="open-aks-diagnostics"></a>Abrir diagnósticos AKS

Para aceder aos diagnósticos AKS:

1. Navegue para o seu cluster Kubernetes no [portal Azure](https://portal.azure.com).
1. Clique no **Diagnóstico e resolva problemas** na navegação à esquerda, que abre o Diagnóstico AKS.
1. Escolha uma categoria que melhor descreva a questão do seu cluster, como _o Cluster Node Issues,_ por:
    * Utilizando as palavras-chave no azulejo da página inicial.
    * Digitando uma palavra-chave que melhor descreve o seu problema na barra de pesquisa.

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Ver um relatório de diagnóstico

Depois de clicar numa categoria, pode ver um relatório de diagnóstico específico do seu cluster. Os relatórios de diagnóstico chamam inteligentemente quaisquer problemas no seu cluster com ícones de estado. Pode aprofundar cada tópico clicando em **Mais Informações** para ver uma descrição detalhada de:
* Questões
* Ações recomendadas
* Links para docs úteis
* Métricas relacionadas
* A registar dados 

Os relatórios de diagnóstico geram com base no estado atual do seu cluster depois de executar várias verificações. Podem ser úteis para identificar o problema do seu cluster e compreender os próximos passos para resolver o problema.

![Relatório de Diagnóstico](./media/concepts-diagnostics/diagnostic-report.png)

![Relatório de Diagnóstico Alargado](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights

As seguintes verificações de diagnóstico estão disponíveis no **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problemas no nó de cluster

O Cluster Node Issues verifica se existem problemas relacionados com nó que fazem com que o seu cluster se comporte de forma inesperada.

- Questões de prontidão do nó
- Falhas nos nódoas
- Recursos insuficientes
- Configuração IP em falta do nó
- Falhas no nó CNI
- Nó não encontrado
- Desligar a alimentação do nó
- Falha de autenticação do nó
- Nó kube-proxy velho

### <a name="create-read-update--delete-crud-operations"></a>Criar, ler, atualizar & eliminar operações (CRUD)

As operações da CRUD verificam quaisquer operações CRUD que causem problemas no seu cluster.

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

A Gestão de Identidade e Segurança deteta erros de autenticação e autorização que impedem a comunicação ao seu cluster.

- Falhas de autorização de nó
- 401 erros
- Erros 403

## <a name="next-steps"></a>Passos seguintes

* Colete registos para ajudá-lo a resolver ainda mais os seus problemas de cluster utilizando [o Periscope AKS](https://aka.ms/aksperiscope).

* Leia a secção de práticas de [triagem](/azure/architecture/operator-guides/aks/aks-triage-practices) do guia de operações do dia-2 da AKS.

* Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) adicionando "[Diag]" no título.