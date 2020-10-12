---
title: Ligue os dados de Segurança da Cloud App ao Azure Sentinel. Microsoft Docs
description: Aprenda a utilizar o conector Microsoft Cloud App Security (MCAS) para transmitir alertas e registos cloud discovery de MCAS para Azure Sentinel. 
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
ms.openlocfilehash: 5c67f517ffd3704644fbe8041f3ac1e7b0d1f09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904552"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conecte dados da Microsoft Cloud App Security 

O conector [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) permite transmitir alertas e [registos cloud discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) de MCAS para Azure Sentinel. Isto irá permitir-lhe ganhar visibilidade nas suas aplicações em nuvem, obter análises sofisticadas para identificar e combater ameaças cibernéticas e controlar como os seus dados viajam.

## <a name="prerequisites"></a>Pré-requisitos

- O seu utilizador deve ter lido e escrito permissões no espaço de trabalho.
- O seu utilizador deve ter permissões de Administrador Global ou Administrador de Segurança no inquilino do espaço de trabalho.
- Para transmitir os registos do Cloud Discovery no Azure Sentinel, [ative o Azure Sentinel como o seu SIEM na Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Ingestão de registos cloud discovery está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conecte-se à Cloud App Security

Se já tem Cloud App Security, certifique-se de que está [ativado na sua rede.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
Se a Cloud App Security for implementada e ingerir os seus dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**. A partir da lista de conectores, clique no azulejo de Segurança da **Aplicação Microsoft Cloud** e, em seguida, no botão **de página do conector Open** no direito inferior.

1. Selecione quais os registos que pretende transmitir para Azure Sentinel; pode escolher **Alertas** e **Registos de Descoberta de Nuvem** (pré-visualização). 

1. Clique **em Aplicar Alterações**.

1. Pode selecionar se deseja que os alertas do Azure Defender do Azure Security Center gerem automaticamente incidentes em Azure Sentinel. Em **In Create incidents**, selecione **Ativado** para ativar a regra de análise predefinitiva que cria automaticamente incidentes a partir de alertas. Em seguida, pode editar esta regra no **âmbito de Analytics,** no separador **Regras Ative.**

1. Para utilizar o esquema relevante no Log Analytics para alertas de Segurança da Aplicação cloud, digite `SecurityAlert` na janela de consulta. Para o esquema de registos cloud Discovery, escreva `McasShadowItReporting` .

> [!NOTE]
> O Cloud Discovery ajuda a detetar e identificar tendências agregando os dados subjacentes às ligações dos utilizadores às aplicações na nuvem.
>
> Uma vez que os dados da Cloud Discovery são agregados por dia, esteja ciente de que até 24 horas de dados mais recentes não serão refletidos no Azure Sentinel. No caso de uma investigação de baixo nível exigir dados mais imediatos, deve ser feito diretamente no aparelho de origem ou no serviço onde residem os dados brutos.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
