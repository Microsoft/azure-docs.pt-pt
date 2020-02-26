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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588370"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conecte dados da Microsoft Cloud App Security 



Pode transmitir registos da [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Esta ligação permite-lhe transmitir os alertas da Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões de administrador global ou administrador de segurança
- Para transmitir registos da Cloud Discovery no Azure Sentinel, [ative o Azure Sentinel como o seu SIEM na Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de registos cloud Discovery está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Ligar à Segurança da Aplicação cloud

Se já tem Cloud App Security, certifique-se de que está [ativado na sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se a Cloud App Security for implementada e ingerir os seus dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados,** clique no azulejo **cloud App Security** e selecione página de **conector Open**.

1. Selecione quais os registos que pretende transmitir para O Sentinel a dispor, pode escolher **registos** de **Alertas** e Cloud Discovery (pré-visualização). 

1. Clique em **Ligar**.

1. Para utilizar o esquema relevante no Log Analytics para os alertas de Segurança de Aplicações cloud, procure **securityAlert**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
