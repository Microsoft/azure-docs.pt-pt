---
title: Ligue os dados do Google Workspace (G Suite) ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do Google Workspace (G Suite) para ingerir eventos de atividade do Google Workspace em Azure Sentinel. Veja os dados do Google Workspace em livros de trabalho, crie alertas e melhore a investigação.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746288"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Ligue o seu Espaço de Trabalho do Google ao Azure Sentinel

> [!IMPORTANT]
> O conector google Workspace está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector de dados [do Google Workspace (anteriormente G Suite)](https://workspace.google.com/) fornece a capacidade de ingerir eventos de Atividade do Google Workspace em Azure Sentinel através da API REST. O conector dá a estes [eventos](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) visibilidade no seu SOC, o que o ajuda a examinar potenciais riscos de segurança, analisar o uso da colaboração da sua equipa, diagnosticar problemas de configuração, rastrear quem assina dentro e quando, analisar a atividade do administrador, entender como os utilizadores criam e partilham conteúdos, e rever mais eventos na sua organização.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

Para recolher os dados do Google Workspace, tem de ter as seguintes permissões e credenciais:

- Leia e escreva permissões no seu espaço de trabalho Azure Sentinel.

- Leia permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

- Leia e escreva permissões para as Funções Azure para criar uma App de Função. [Saiba mais sobre as Funções Azure](../azure-functions/index.yml).

- A credencial **GooglePickleString** é necessária para a API REST. [Saiba mais sobre a API do Google REST.](https://developers.google.com/admin-sdk/reports/v1/reference/activities) [Saiba como obter credenciais.](https://developers.google.com/admin-sdk/reports/v1/quickstart/python)

## <a name="configure-and-connect-google-workspace"></a>Configure e ligue o Google Workspace

O Google Workspace pode integrar e exportar registos diretamente para o Azure Sentinel usando uma App de Função Azure.

> [!NOTE]
> Este conector utiliza funções Azure para ligar à API do Google Reports para puxar eventos de atividade para o Azure Sentinel. Isto pode resultar em custos adicionais de ingestão de dados. Consulte a página [de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para obter mais detalhes.

1. No portal Azure Sentinel, clique nos **conectores de dados.** 

1. Selecione **o Google Workspace (G Suite) (Preview)** da galeria de conectores e selecione **a página do conector Open**.

1. Siga os passos descritos na secção **de Configuração** da página do conector.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e encontrar os seus dados

Pode levar até 20 minutos até que possa ver dados ingeridos em Azure Sentinel.

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos,** na secção **Registos Personalizados,** nas seguintes tabelas:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. [Siga estes passos](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) para criar o **pseudónimo GWorkspaceActivityReports** Kusto.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Google Workspace ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
