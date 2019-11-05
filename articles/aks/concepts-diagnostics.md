---
title: Visão geral de diagnóstico do AKS (serviço kubernetes do Azure)
description: Saiba mais sobre clusters de diagnóstico automático no serviço kubernetes do Azure.
services: container-service
author: yunjchoi
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 69ae6766414dbe533500860b01852ee8d25c3a1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513916"
---
# <a name="azure-kubernetes-service-aks-diagnostics-overview"></a>Visão geral de diagnóstico do AKS (serviço kubernetes do Azure)

A solução de problemas de cluster do AKS (serviço de kubernetes do Azure) é uma parte importante da manutenção do cluster, especialmente se o cluster estiver executando cargas de trabalho de missão crítica. O diagnóstico de AKS é uma experiência de autodiagnóstico inteligente que ajuda você a identificar e resolver problemas em seu cluster. O diagnóstico de AKS é nativo de nuvem e você pode usá-lo sem nenhuma configuração extra ou custo de cobrança.

## <a name="open-aks-diagnostics"></a>Abrir o diagnóstico do AKS

Para acessar o diagnóstico do AKS:

- Navegue até o cluster kubernetes no [portal do Azure](https://portal.azure.com).
- Clique em **diagnosticar e resolver problemas** no painel de navegação esquerdo, que abre o diagnóstico de AKs.
- Escolha uma categoria que melhor descreva o problema do cluster usando as palavras-chave no bloco de Home Page ou digite uma palavra-chave que melhor descreva seu problema na barra de pesquisa, por exemplo, _problemas de nó de cluster_.

![Principal](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de clicar em uma categoria, você pode exibir um relatório de diagnóstico específico para seu cluster. O relatório de diagnóstico chama de forma inteligente se há algum problema em seu cluster com ícones de status. Você pode fazer uma busca detalhada de cada tópico clicando em **mais informações** para ver uma descrição detalhada do problema, as ações recomendadas, links para documentos úteis, métricas relacionadas e dados de log. Os relatórios de diagnóstico são gerados de forma inteligente com base no estado atual do cluster após a execução de várias verificações. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar o problema do cluster e encontrar as próximas etapas para resolver o problema.

![Relatório de diagnóstico](./media/concepts-diagnostics/diagnostic-report.png)

![Relatório de diagnóstico expandido](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Insights de cluster

As verificações de diagnóstico a seguir estão disponíveis no **insights do cluster**.

### <a name="cluster-node-issues"></a>Problemas de nó de cluster

Problemas de nó de cluster verifica se há problemas relacionados a nós que podem fazer com que o cluster se comporte inesperadamente.

- Problemas de preparação do nó
- Falhas de nó
- Recursos insuficientes
- Configuração de IP ausente no nó
- Falhas de CNI de nó
- Nó não encontrado
- Desligar nó
- Falha na autenticação do nó
- Nó Kube-proxy obsoleto

### <a name="create-read-update--delete-operations"></a>Criar, ler, atualizar & operações de exclusão

As operações CRUD verificam as operações CRUD que podem causar problemas no cluster.

- Erro na operação de exclusão da sub-rede em uso
- Erro na operação de exclusão do grupo de segurança de rede
- Erro na operação de exclusão da tabela de rotas em uso
- Erro de provisionamento de recurso referenciado
- Erro na operação de exclusão do endereço IP público
- Falha na implantação devido à cota de implantação
- Erro de operação devido à política da organização
- Registro de assinatura ausente
- Erro de provisionamento de extensão de VM
- Capacidade da sub-rede
- Erro de Cota excedida

### <a name="identity-and-security-management"></a>Gestão de identidade e segurança

O gerenciamento de identidades e segurança detecta erros de autenticação e autorização que podem impedir a comunicação com o cluster.

- Falhas de autorização de nó
- erros 401
- erros 403

## <a name="next-steps"></a>Passos seguintes

Colete logs para ajudá-lo a solucionar ainda mais os problemas de cluster usando o [AKs Periscope](https://aka.ms/aksperiscope).

Poste suas perguntas ou comentários no [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) adicionando "[diag]" no título.
