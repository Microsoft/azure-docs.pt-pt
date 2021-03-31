---
title: Ligue os dados da Cloud do Serviço Salesforce ao Azure Sentinel | Microsoft Docs
description: Saiba como utilizar o conector de dados salesforce Service Cloud para puxar os registos salesforce para o Azure Sentinel. Ver dados da Salesforce em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570535"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Ligue a sua Nuvem de Serviço Salesforce ao Azure Sentinel

> [!IMPORTANT]
> O conector Salesforce Service Cloud encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar a sua solução Salesforce Service Cloud ao Azure Sentinel. O conector de dados Salesforce Service Cloud permite-lhe conectar facilmente os seus dados salesforce com o Azure Sentinel, para que possa vê-lo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre a Salesforce Service Cloud e a Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

- Deve ter lido e escrito permissões para as Funções Azure para criar uma App de Função. [Saiba mais sobre as Funções Azure](../azure-functions/index.yml).

- Tem de ter as seguintes credenciais de API salesforce REST: **Nome de utilizador da Salesforce API,** Password **API Salesforce,** **Salesforce Security Token,** **Salesforce Consumer Key**, **Salesforce Consumer Secret**. [Saiba mais sobre a Salesforce REST API.](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)

## <a name="configure-and-connect-salesforce-service-cloud"></a>Configure e ligue a Cloud do Serviço Salesforce

Salesforce Service Cloud pode integrar e exportar registos diretamente para Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **a Cloud do Serviço Salesforce (Pré-visualização)** e, em seguida, **Abra a página do conector**.

1. Siga os passos descritos na secção **de Configuração** da página do conector.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs**, na secção **De Registos Personalizados,** na `SalesforceServiceCloud_CL` tabela.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Salesforce Service Cloud ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.