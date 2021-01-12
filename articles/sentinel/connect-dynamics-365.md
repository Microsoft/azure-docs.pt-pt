---
title: Ligue os registos Dynamics 365 ao Azure Sentinel ! Microsoft Docs
description: Aprenda a utilizar o conector de atividades do Dynamics 365 Common Data Service (CDS) para trazer informações sobre as atividades de administração, utilizador e suporte em curso.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104197"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Ligue os registos de atividades da Dynamics 365 ao Azure Sentinel

O conector [de atividades do Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) fornece informações sobre as atividades de administração, utilizador e suporte, bem como eventos de registo de envolvimento social da Microsoft. Ao ligar os registos de CRM dynamics 365 ao Azure Sentinel, pode ver estes dados em livros, usá-lo para criar alertas personalizados e alavancar os registos para melhorar o seu processo de investigação. Este novo conector Azure Sentinel recolhe os dados do CDS dinâmicos da API de Gestão de Escritórios. Para saber mais sobre as atividades do Dynamics CDS auditadas na Plataforma power, visite [Enable and Use Activity Logging.](/power-platform/admin/enable-use-comprehensive-auditing)

> [!IMPORTANT]
>
> O conector de atividades dynamics 365 Common Data Service (CDS) está atualmente em **PRÉ-VISUALIZAÇÃO**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter uma licença de [produção Microsoft Dynamics 365.](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Este conector não está disponível para ambientes de caixa de areia.
    - Uma subscrição Microsoft 365 Enterprise [E3 ou E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) é necessária para fazer registo de atividade.

- Para obter dados da API de Gestão do Escritório:
    - Deve ser um administrador global do seu inquilino.

    - [O registo de auditoria do Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) deve ser ativado no Centro de Segurança e Conformidade do [Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Ativar o conector de dados de atividades Dynamics 365

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Dynamics 365 (Preview)** e, em seguida, selecione **a página do conector Aberto** no painel de pré-visualização.

1. No separador **Instruções,** em **Configuração,** clique em **Ligar**. 

    Uma vez ativado o conector, levará cerca de 30 minutos até que possa ver os dados que chegam ao gráfico. 

    De acordo com o [registo de auditoria do Office no centro de conformidade,](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)pode demorar até 30 minutos ou até 24 horas após a ocorrência de um evento para que o registo de registo de auditoria correspondente seja devolvido nos resultados.

1. Os registos de auditoria da Microsoft Dynamics podem ser encontrados na `Dynamics365Activity` tabela. Consulte a referência de [esquema](/azure/azure-monitor/reference/tables/dynamics365activity)da mesa.

## <a name="querying-the-data"></a>Consulta dos dados

1. A partir do menu de navegação Azure Sentinel, selecione **Logs**.

1. Faça a seguinte consulta para verificar se os registos chegam:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar dados de atividades da Dynamics 365 ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
