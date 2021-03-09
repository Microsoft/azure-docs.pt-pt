---
title: Ligue os registos de diagnósticos do Azure Key Vault ao Azure Sentinel
description: Saiba como usar a Política Azure para ligar os registos de diagnósticos do Azure Key Vault ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 56587ae91de086cccb7cc85c125f935be2a56f73
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491313"
---
# <a name="connect-azure-key-vault-diagnostics-and-auditing-logs"></a>Ligue os diagnósticos do Azure Key Vault e os registos de auditoria

Azure Key Vault é um serviço de nuvem para armazenar e aceder a segredos de forma segura. Um segredo é qualquer coisa a que queira controlar firmemente o acesso, como chaves API, palavras-passe, certificados ou chaves criptográficas.

Este conector permite-lhe transmitir os seus registos de diagnóstico azure Key Vault para o Azure Sentinel, permitindo-lhe monitorizar continuamente a atividade em todos os seus casos.

Saiba mais sobre [a monitorização do Cofre da Chave Azure](../azure-monitor/insights/key-vault-insights-overview.md) e sobre [a telemetria de diagnóstico do Azure Key Vault](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ingerir os registos do Azure Key Vault em Azure Sentinel:

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Para utilizar a Política Azure para aplicar uma política de streaming de registos aos recursos do Azure Key Vault, tem de lhe ser atribuída a função de Proprietário para o âmbito de atribuição de políticas.

## <a name="connect-to-azure-key-vault"></a>Ligue-se ao cofre da chave Azure

Este conector utiliza a Azure Policy para aplicar uma única configuração de streaming de log-streaming do Azure Key Vault a uma coleção de instâncias, definida como um âmbito. Pode ver os tipos de registo ingeridos a partir do Cofre da Chave Azure no lado esquerdo da página do conector, nos **tipos de Dados**.

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **Azure Key Vault** a partir da galeria de conectores de dados e, em seguida, selecione Abrir a Página do **Conector** no painel de pré-visualização.

1. Na secção **de configuração** da página do conector, expanda os **registos de ativação no Cofre da Chave Azure**.

1. Selecione o botão de assistente de **atribuição de política de lançamento azure.**

    O assistente de atribuição de políticas abre, pronto para criar uma nova política chamada **Implementação - Configurações de diagnóstico de configuração para Azure Key Vault para Log Analytics workspace**.

    1. No separador **Básicos,** clique no botão com os três pontos no **Âmbito** para selecionar a sua subscrição (e, opcionalmente, um grupo de recursos). Também pode adicionar uma descrição.

    1. No **separador Parâmetros,** escolha o seu espaço de trabalho Azure Sentinel na lista de drop-down do espaço de **trabalho Log Analytics.** Os restantes campos de down-down representam os tipos de registo de diagnóstico disponíveis. Deixe marcado como "Verdadeiro" todos os tipos de registo que pretende ingerir.

    1. Para aplicar a política nos recursos existentes, selecione o **separador Remediação** e marque a caixa de verificação de **tarefas de reparação.**

    1. No **separador 'Rever +' criar,** clique em **Criar.** A sua apólice está agora atribuída ao âmbito que escolheu.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Cofre da Chave Azure ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
