---
title: Validação de pilha do Azure como um conceito de principais de serviço | Documentos da Microsoft
description: Descreve os principais conceitos no Azure Stack validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a128be720675898a2512f43bf714b9ad6aca0450
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338540"
---
# <a name="validation-as-a-service-key-concepts"></a>Validação como um conceito de principais de serviço

Este artigo descreve os conceitos-chave na validação como um serviço (VaaS).

## <a name="solutions"></a>Soluções

Uma solução de VaaS representa uma solução do Azure Stack com uma lista de determinados hardwares de materiais (LM). A solução de VaaS atua como um contentor para os fluxos de trabalho que são executados em relação a solução do Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Criar uma solução no portal do VaaS

1. Inicie sessão para o [VaaS portal](https://azurestackvalidation.com).
2. No dashboard de soluções, selecione no **nova solução**.
3. Introduza um nome para a solução. Para sugestões de nomenclatura, veja [Convenção de nomenclatura para soluções de VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Selecione **guardar** para criar a solução.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho VaaS opera no contexto de uma solução de VaaS. Ele representa um conjunto de pacotes de testes que exercitem a funcionalidade de uma implementação do Azure Stack. Um fluxo de trabalho deve ser criado para todas as atualizações de software ou de implementação de uma solução do Azure Stack.

Fluxos de trabalho são categorizados por tipo de cenário de teste. No serviço de teste não oficial, o **aprovação de teste** fluxo de trabalho permite-lhe para selecionar os testes de todos os materiais de VaaS disponíveis. No teste oficial, o **validação** fluxos de trabalho destinam a cenários de testes específicos selecionados pela Microsoft.

![Mosaicos de fluxo de trabalho de VaaS](media/tile_all-workflows.png)

> [!NOTE]
> O **validação do pacote** atualmente, o fluxo de trabalho suporta dois cenários: [Validar pacotes de OEM](azure-stack-vaas-validate-oem-package.md) e [validar atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Para obter mais informações sobre tipos de fluxo de trabalho, consulte [o que é a validação como um serviço para o Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Guia de introdução VaaS fluxos de trabalho

1. No dashboard de soluções, criar uma nova solução ou selecione um existente. Isso é atualizada e permite que os mosaicos de fluxo de trabalho.
2. Para criar um novo fluxo de trabalho, selecione no **iniciar** em qualquer mosaico. Para obter informações específicas para cada fluxo de trabalho, consulte os artigos seguintes:
    - Aprovação de teste: [Quickstart: Utilize a validação como um portal de serviço para agendar o seu primeiro teste](azure-stack-vaas-schedule-test-pass.md)
    - Validação de solução: [Validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Validação do pacote (atualização mensal): [Validar as atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Validação do pacote (assinatura de pacote): [Validar pacotes do OEM](azure-stack-vaas-validate-oem-package.md)

3. Para gerir ou monitorizar um fluxo de trabalho existente, selecione no **gerir** no mosaico do fluxo de trabalho. Selecione o nome do fluxo de trabalho e utilize o **editar** botão para ver as propriedades ou altere os parâmetros comuns de teste.

Para obter mais informações sobre as propriedades de fluxo de trabalho e os parâmetros, consulte [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testes

Um teste no VaaS consiste num conjunto de ações que executar numa solução do Azure Stack. Testes têm diferentes fins pretendidos identificados por uma categoria, como funcional ou confiabilidade e um ou mais serviços do Azure Stack de destino. Cada teste define seu próprio conjunto de parâmetros, alguns dos quais são especificadas pela parâmetros comuns do fluxo de trabalho que o contém.

Para obter mais informações sobre como gerir e testes de monitorização, consulte [Monitor e gerenciem testes no portal do VaaS](azure-stack-vaas-monitor-test.md).

Para obter mais informações sobre parâmetros de teste, consulte [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agentes

Um agente de VaaS unidades de execução do teste. Dois tipos de agentes a executar testes de VaaS:

- O **agente de cloud**. Este é o agente de predefinidas disponível no VaaS. Nenhuma configuração é necessária, mas isso requer conectividade no ligado ao seu ambiente e pontos finais de pilha do Azure tem de ser resolvidos a partir da internet. Alguns testes não são compatíveis com o agente de cloud.
- R **agente local**. Isso permite que executa a validação em cenários onde não é viável em ligados a conectividade ao seu ambiente. Alguns testes exigem execução através do agente local.

Os agentes locais não estão associados a qualquer solução do Azure Stack ou VaaS específica. Como melhor prática, deve executar fora de um ambiente do Azure Stack.

Para obter instruções sobre como adicionar um agente local, consulte [implementar o agente local](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Passos Seguintes

- [Melhores práticas para a validação como um serviço](azure-stack-vaas-best-practice.md)