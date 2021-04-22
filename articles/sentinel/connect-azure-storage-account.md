---
title: Ligar registos de diagnóstico de conta de armazenamento Azure ao Azure Sentinel
description: Saiba como usar a Azure Policy para ligar os registos de diagnóstico de conta de armazenamento Azure ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879132"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Ligar registos de diagnóstico de conta de armazenamento Azure

A conta Azure Storage é uma solução em nuvem para cenários modernos de armazenamento de dados. Contém todos os seus objetos de dados: bolhas, ficheiros, filas, tabelas e discos.

Este conector permite-lhe transmitir os registos de diagnóstico das suas contas de Armazenamento Azure para o Azure Sentinel, permitindo-lhe monitorizar continuamente a atividade e detetar ameaças de segurança em todos os seus recursos de armazenamento Azure em toda a sua organização.

Saiba mais sobre [a monitorização da conta Azure Storage](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ingerir os registos de diagnósticos de conta de armazenamento Azure em Azure Sentinel:

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Para utilizar a Política Azure para aplicar uma política de streaming de registos aos recursos de armazenamento Azure, deve ter o papel de Proprietário para o âmbito de atribuição de políticas.

## <a name="connect-to-azure-storage-account"></a>Ligue-se à conta de armazenamento Azure

Este conector utiliza a Azure Policy para aplicar uma única configuração de streaming de registo a uma coleção de recursos de conta de armazenamento Azure, definida como um âmbito. Pode ver o registo de armazenamento Azure disponível e os tipos métricos no lado esquerdo da página do conector, nos **tipos de Dados**.

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **a conta de armazenamento Azure** na galeria de conectores de dados e, em seguida, selecione Abrir a Página do **Conector** no painel de pré-visualização.

1. Na secção **de Configuração** da página do conector, expanda **os registos de diagnóstico do Stream a partir da sua Conta de Armazenamento Azure à escala**.

1. Selecione o botão de assistente de **atribuição de política de lançamento azure.**

    O assistente de atribuição de políticas abre, pronto para criar uma nova política chamada **Configurar definições de diagnóstico para contas de armazenamento para log analytics workspace**.

    1. No separador **Básicos,** clique no botão com os três pontos ao lado **do Scope** para selecionar a sua subscrição (e, opcionalmente, um grupo de recursos). Também pode adicionar uma descrição.

    1. No **separador Parâmetros:**
        - Escolha o seu espaço de trabalho Azure Sentinel na lista de drop-down **do log Analytics.**
        - Selecione, desde os **serviços de Armazenamento até implementar a** lista de drop-down, os tipos de recursos de armazenamento (ficheiro, mesa, fila, etc.) aos quais pretende implementar definições de diagnóstico.
        - Deixe os campos **de definição** e **efeito** como está.
        - Os restantes campos de down-down representam os tipos de registo de diagnóstico e métricas disponíveis. Deixe marcado como "Verdadeiro" todos os tipos que deseja ingerir.

    1. Os passos acima aplicarão a política a todos os recursos de armazenamento futuros. Para aplicar a política nos recursos existentes, selecione o **separador Remediação** e marque a caixa de verificação de **tarefas de reparação.**

    1. No **separador 'Rever +' criar,** clique em **Criar.** A sua apólice está agora atribuída ao âmbito que escolheu.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nos últimos 14 dias. Uma vez passados 14 dias sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a conta de Armazenamento Azure ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
