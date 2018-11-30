---
title: Utilize a validação como um serviço para o portal do Azure Stack para agendar o seu primeiro teste | Documentos da Microsoft
description: Utilize a validação como um serviço para o portal do Azure Stack para agendar o seu primeiro teste.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 3021fafe7d74f71450eec9b3720c974b04ada224
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426932"
---
# <a name="schedule-your-first-test"></a>Agendar o seu primeiro teste

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Agende um teste de validação como um portal de serviço (VaaS) para a sua solução do Azure Stack. Uma solução de VaaS representa uma solução do Azure Stack com uma lista de determinados hardwares de materiais (LM). Pode agendar um teste para verificar que o hardware pode executar o Azure Stack.

Para verificar a sua solução, crie o fluxo de trabalho para um teste. Um fluxo de trabalho VaaS opera no contexto de uma solução de VaaS. Ele representa um conjunto de pacotes de testes que exercitem a funcionalidade de uma implementação do Azure Stack no seu hardware. Adicionar parâmetros de ambiental da sua solução e selecione um ou mais testes para executar na sua solução.

Enquanto o fluxo de trabalho de aprovação de teste pode ser utilizado para executar qualquer teste fornecida pelo VaaS, incluindo testes de fluxos de trabalho de validação, os resultados do fluxo de trabalho de aprovação de teste não são considerados *oficial*. Para obter informações sobre fluxos de trabalho de validação oficial, consulte [fluxos de trabalho](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este guia de introdução, deverá concluir os seguintes itens:

- [Configurar a sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md)
- [Implementar o agente local](azure-stack-vaas-local-agent.md) (recomendado)
- [Validação como um conceito de principais de serviço](azure-stack-vaas-key-concepts.md) (recomendado)

## <a name="start-a-workflow"></a>Iniciar um fluxo de trabalho

![Inicie sessão no portal do VaaS](media/vaas_portalsignin.png)

Inicie sessão no portal, selecione ou criação de uma solução e, em seguida, selecione a solução.

1. Inicie sessão para o [VaaS portal](https://azurestackvalidation.com).
2. Escreva o nome de uma solução existente ou selecione **nova solução** para criar uma nova solução. Para obter instruções, consulte [criar uma solução no portal do VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Selecione **iniciar** sobre o **Passes de teste** mosaico.

## <a name="specify-parameters"></a>Especifique parâmetros

![Texto alternativo](media/vaas_test_pass_parameters.png)

Defina o fluxo de trabalho para a sua solução. O fluxo de trabalho tem as etapas de processo utilizado para testar a sua solução.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Selecione **seguinte** para selecionar os testes para agendar.

## <a name="select-tests-to-run"></a>Selecione a execução

Escolha os testes que pretende executar no seu fluxo de trabalho.

1. Selecione a que pretende executar no seu fluxo de trabalho de teste (s).

    Se quiser substituir os parâmetros comuns (ou seja, os parâmetros fornecidos na secção anterior) de qualquer teste, selecione o **editar** ligação seguinte para especificar os novos valores.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. Selecione **seguinte** para rever o fluxo de trabalho.

## <a name="review-and-submit"></a>Rever e submeter

Rever, criar e, em seguida, agendar, o fluxo de trabalho.

1. Reveja as informações apresentadas.

    Os serviços cria seu fluxo de trabalho com as informações fornecidas e serão agendados com os testes selecionados.

    Se nada for exibida incorreto, utilize o **Previous** botões para ir para uma seção anterior.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)
