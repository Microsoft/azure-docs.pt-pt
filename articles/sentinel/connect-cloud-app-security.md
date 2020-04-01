---
title: Conecte dados de segurança de aplicações em nuvem ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados de Segurança da Cloud App ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422151"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conecte dados da Microsoft Cloud App Security 



O conector [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) permite transmitir alertas e [registos de Cloud Discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) do MCAS para o Azure Sentinel. Isto permitir-lhe-á ganhar visibilidade nas suas aplicações na nuvem, obter análises sofisticadas para identificar e combater ameaças cibernéticas e controlar como os seus dados viajam.

## <a name="prerequisites"></a>Pré-requisitos

- O seu utilizador deve ter lido e escrito permissões no espaço de trabalho.
- O seu utilizador deve ter permissões de Administrador Global ou Administrador de Segurança no inquilino do espaço de trabalho.
- Para transmitir registos da Cloud Discovery no Azure Sentinel, [ative o Azure Sentinel como o seu SIEM na Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de registos cloud Discovery está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
 
## <a name="connect-to-cloud-app-security"></a>Ligar à Segurança da Aplicação cloud

Se já tem Cloud App Security, certifique-se de que está [ativado na sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se a Cloud App Security for implementada e ingerir os seus dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. A partir do menu de navegação Azure Sentinel, selecione **conectores de dados**. A partir da lista de conectores, clique no azulejo **microsoft Cloud App Security** e, em seguida, no botão de página do **conector Open** na parte inferior direita.

1. Selecione quais os registos que pretende transmitir para O Sentinela Azul; pode escolher **Alertas** e **Registos de Descoberta de Nuvem** (pré-visualização). 

1. Clique em **Aplicar Alterações**.

1. Para utilizar o esquema relevante no Log Analytics para `SecurityAlert` alertas de Segurança de Aplicações cloud, digite na janela de consulta. Para o esquema de logs `McasShadowItReporting`Cloud Discovery, escreva .

> [!NOTE]
> O Cloud Discovery ajuda a detetar e identificar tendências, agregando as ligações dos utilizadores subjacentes às aplicações em nuvem.
>
> Uma vez que os dados da Cloud Discovery são agregados por dia, esteja ciente de que até 24 horas dos dados mais recentes não serão refletidos no Azure Sentinel. No caso de uma investigação de baixo nível necessitar de dados mais imediatos, deve ser feito diretamente no aparelho de origem ou no serviço onde residem os dados brutos.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar a detetar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
