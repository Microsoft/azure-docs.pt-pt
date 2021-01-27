---
title: Conecte os dados de segurança do e-mail à Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector de dados de segurança de e-mail proofpoint on demand para puxar os registos de segurança do POD Email para o Azure Sentinel. Ver dados de segurança do POD Email nos livros, criar alertas e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: bdc9cbf942b88af93befa34f73eb3a90469cfcef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873506"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Ligue o seu ponto de verificação à segurança de e-mail (POD) à Azure Sentinel

> [!IMPORTANT]
> O conector de segurança de e-mail proofpoint on demand está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu ponto de prova a pedido Enviar por email o artigo Segurança. O conector de dados POD permite-lhe conectar facilmente os seus registos POD com o Azure Sentinel, para que possa ver os dados nos livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação.  A integração entre a Proofpoint On Demand Email Security e a Azure Sentinel faz uso da Websocket API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

- Deve ter lido e escrito permissões para as Funções Azure para criar uma App de Função. [Saiba mais sobre as Funções Azure](../azure-functions/index.yml).

- Você deve ter as seguintes credenciais websocket API: ProofpointClusterID, ProofpointToken. [Saiba mais sobre websocket API.](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Configure e conecte o ponto de alerta na segurança de e-mail de pedido

Proofpoint On Demand Email Security pode integrar e exportar registos diretamente para Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Proofpoint On Demand Email Security (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga os passos descritos na secção **de Configuração** da página do conector.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos**, em *Registos Personalizados,* nas seguintes tabelas:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Proofpoint On Demand Email Security ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.