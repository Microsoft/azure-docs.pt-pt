---
title: Monitorizar e gerir os testes no portal do Azure Stack VaaS | Documentos da Microsoft
description: Monitorizar e gerir os testes no portal do Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9b64631b274423edda35189a942e2f142ae591f1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761179"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorizar e gerir os testes no portal do VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Depois de agendar os testes em sua solução do Azure Stack, validação como um serviço (VaaS) começará a comunicar o estado de execução de teste. Essas informações estão disponíveis no portal do VaaS juntamente com as ações, como o valor do reagendamento e cancelamento de testes.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Navegue para a página de resumo de testes de fluxo de trabalho

1. No dashboard de soluções, selecione uma solução existente que tenha, pelo menos, um fluxo de trabalho.

    ![Mosaicos de fluxo de trabalho](media/tile_all-workflows.png)

1. Selecione **gerir** no mosaico do fluxo de trabalho. A página seguinte lista os fluxos de trabalho criados da solução selecionada.

1. Selecione o nome do fluxo de trabalho para abrir o seu resumo de teste.

## <a name="change-workflow-parameters"></a>Parâmetros de fluxo de trabalho de alteração

Pode editar a [testar parâmetros](azure-stack-vaas-parameters.md#test-parameters) especificado durante a criação de fluxo de trabalho para qualquer tipo de fluxo de trabalho.

1. Na página de resumo de testes, selecione o **editar** botão.

1. Fornecer novos valores de acordo com a [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

1. Selecione **submeter** para guardar os valores.

> [!NOTE]
> Na **aprovação de teste** fluxo de trabalho, será necessário concluir a seleção de teste e vá para a página de revisão para poder guardar os novos valores de parâmetro.

### <a name="add-tests-test-pass-only"></a>Adicionar testes (apenas para aprovação de teste)

Na **aprovação de teste** fluxos de trabalho, ambas as a **adicione testes** e **editar** botões permitem-lhe agendar novos testes no fluxo de trabalho.

> [!TIP]
> Selecione **adicionar testes** se só deseja programar novos testes e não é necessário editar parâmetros para um **aprovação de teste** fluxo de trabalho.

## <a name="managing-test-instances"></a>Gestão das instâncias de teste

Para não oficiais execuções (ou seja, o **aprovação de teste** fluxo de trabalho), a página de resumo de testes lista os testes agendados na solução do Azure Stack.

Para execuções de oficiais (ou seja, o **validação** fluxos de trabalho), a página de resumo de testes lista os testes necessários para concluir a validação da solução do Azure Stack. Testes de validação estão agendados a partir desta página.

Cada instância de teste agendada mostra as seguintes informações:

| colunas | Descrição |
| --- | --- |
| Nome do teste | O nome e versão do teste. |
| Categoria | O objetivo do teste. |
| Criado | A hora em que o teste foi agendado. |
| Iniciado | A hora em que o teste iniciou a execução. |
| Duração | O período de tempo que o teste foi executado. |
| Estado | O estado ou o resultado do teste. Estados de pré-execução ou em curso são: `Pending`, `Running`. Estados de terminal são: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nome do agente | O nome do agente que executou o teste. |
| Total de operações | O número total de operações tentada durante o teste. |
| Operações com êxito | O número de operações que foi concluída com êxito durante o teste. |
|  Operações com Falhas | O número de operações que falharam durante o teste. |

### <a name="actions"></a>Ações

Cada instância de teste apresenta uma lista de ações disponíveis que pode tomar quando seleciona o menu de contexto **[...]**  na tabela de instâncias de teste.

#### <a name="view-information-about-the-test-definition"></a>Ver informações sobre a definição de teste

Selecione **ver informações** no menu de contexto para ver informações gerais sobre a definição de teste. Isso é compartilhado por cada instância de teste com o mesmo nome e versão.

| Propriedade de teste | Descrição |
| -- | -- |
| Nome do teste | O nome do teste. |
| Versão de teste | A versão do teste. |
| Publicador | O publicador do teste. |
| Categoria |  O objetivo do teste. |
| Serviços de destino | O Azure Stack serviços que está sendo testado. |
| Descrição | A descrição do teste. |
| Duração estimada (minutos) | O tempo de execução esperado do teste. |
| Ligações | Quaisquer informações relevantes sobre a pontos de contacto ou de teste. |

#### <a name="view-test-instance-parameters"></a>Parâmetros de instância de teste do Vista

Selecione **ver parâmetros** no menu de contexto para ver os parâmetros fornecidos para a instância de teste no momento do agendamento. Cadeias de caracteres confidenciais como palavras-passe não são apresentadas. Esta ação só está disponível para testes que tiverem sido agendadas.

Esta janela inclui os seguintes metadados para todas as instâncias de teste:

| Propriedade de instância de teste | Descrição |
| -- | -- |
| Nome do teste | O nome do teste. |
| Versão de teste | A versão do teste. |
| ID de instância de teste | Um GUID a identificar a instância específica do teste. |

#### <a name="view-test-instance-operations"></a>Operações de instância de teste do Vista

Selecione **ver operações** do contexto do menu para ver um estado detalhado das operações executadas durante o teste. Esta ação só está disponível para testes que tiverem sido agendadas.

![ver as operações](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Transferir registos para uma instância de teste concluído

Selecione **transferir registos** no menu de contexto para transferir um `.zip` ficheiro de saída de registos durante a execução de teste. Esta ação só está disponível para testes que foram concluídos, ou seja, um teste com um status de `Cancelled`, `Failed`, `Aborted`, ou `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Reagendar uma instância de teste ou agendar um teste

Agendamento de testes a partir da página Gestão depende do tipo de fluxo de trabalho, que o teste é executado sob.

##### <a name="test-pass-workflow"></a>Fluxo de trabalho de aprovação de teste

O fluxo de trabalho de aprovação de teste, **reagendamento** uma instância de teste reutiliza o mesmo conjunto de parâmetros que a instância original do teste e *substitui* o resultado original, incluindo os respetivos registos. Terá de reintroduzir as cadeias de caracteres confidenciais como palavras-passe quando reagendar.

1. Selecione **reagendar** no menu de contexto para abrir uma linha de comandos para o valor do reagendamento a instância de teste.

1. Introduza quaisquer parâmetros aplicável.

1. Selecione **submeter** reagendar a instância de teste e substituir a instância existente.

##### <a name="validation-workflows"></a>Fluxos de trabalho de validação

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Cancelar uma instância de teste

Um teste agendado poderá ser cancelado se o respetivo estado é `Pending` ou `Running`.  

1. Selecione **Cancelar** no menu de contexto para abrir um prompt para cancelar a instância de teste.

1. Selecione **submeter** para cancelar a instância de teste.

## <a name="next-steps"></a>Passos Seguintes

- [Resolver problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)
