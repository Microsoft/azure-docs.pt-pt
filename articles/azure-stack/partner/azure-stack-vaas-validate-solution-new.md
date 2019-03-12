---
title: Validar uma nova solução do Azure Stack | Documentos da Microsoft
description: Saiba como validar uma nova solução do Azure Stack com validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fb7b98a5307606f7335e76ada42d583b975730f5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759668"
---
# <a name="validate-a-new-azure-stack-solution"></a>Validar uma nova solução do Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como utilizar o **validação de solução** fluxo de trabalho para certificar novas soluções do Azure Stack.

Uma solução do Azure Stack é uma lista de hardware de materiais (LM) que tem sido em conjunto, faz um acordo entre a Microsoft e o parceiro depois de cumprir os requisitos de certificação de logótipo do Windows Server. Uma solução tem de ser recertified quando tiver ocorrido uma alteração ao hardware BoM. Para fazer outras perguntas sobre quando voltar a certificar o soluções, contacte a equipa em [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Para certificar a sua solução, executada duas vezes o fluxo de trabalho de validação de solução. Executá-lo uma vez para o *minimamente* suportado de configuração. Executá-lo uma segunda vez para o *maximally* suportado de configuração. Microsoft certifica-se que a solução se ambas as configurações de passar em todos os testes.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Criar um fluxo de trabalho de validação de solução

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selecione **começar** sobre o **validações de solução** mosaico.

    ![Mosaico de fluxo de trabalho de validações de solução](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Selecione o **configuração da solução**.
    - **Mínimo**: a solução está configurada com o número mínimo suportado de nós.
    - **Máximo**: a solução está configurada com o número máximo suportado de nós.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informações de validação de solução](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros do ambiente não podem ser modificados depois de criar um fluxo de trabalho.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Será redirecionado para a página de resumo de testes.

## <a name="run-solution-validation-tests"></a>Executar testes de validação de solução

Na **resumo de testes de validação de solução** página, verá uma lista de testes necessários para concluir a validação.

Em fluxos de trabalho de validação, **agendamento** um teste utiliza os parâmetros comuns de nível de fluxo de trabalho que especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se torne inválido, deve resupply-los com as instruções na [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Um teste de validação de agendamento sobre uma instância existente, irá criar uma nova instância em vez da instância antiga no portal. Registos para a instância antiga serão mantidos, mas não estão acessíveis a partir do portal.  
Quando um teste tiver sido concluída com êxito, o **agenda** ação torna-se desativada.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Selecione os seguintes testes:
    - Mecanismo de simulação de cloud
    - Computação Suite operacional do SDK
    - Teste de identificação de disco
    - Conjunto de operacional de SDK de extensão do Cofre de chaves
    - Suite operacionais do SDK do Cofre de chaves
    - Conjunto de operacional de SDK de rede
    - Conjunto operacional do SDK de conta de armazenamento

3. Selecione **agenda** no menu de contexto para abrir um prompt para agendar a instância de teste.

4. Reveja os parâmetros de teste e, em seguida, selecione **submeter** para agendar o teste para execução.

![Teste de validação de solução de agendamento](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)