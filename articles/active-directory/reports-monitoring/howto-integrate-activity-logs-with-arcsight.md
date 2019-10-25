---
title: Integrar logs com o ArcSight usando o Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o ArcSight usando Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037240e6006b5e0625bf8fb75492d8e458899a8e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820893"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrar logs de Azure Active Directory com o ArcSight usando Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) é uma solução de Siem (gerenciamento de informações e eventos de segurança) que ajuda você a detectar e responder a ameaças de segurança em sua plataforma. Agora você pode rotear logs do Azure Active Directory (AD do Azure) para ArcSight usando Azure Monitor usando o conector do ArcSight para o Azure AD. Esse recurso permite que você monitore seu locatário quanto ao comprometimento da segurança usando o ArcSight.  

Neste artigo, você aprenderá a rotear os logs do Azure AD para o ArcSight usando Azure Monitor. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um hub de eventos do Azure que contém logs de atividade do Azure AD. Saiba como [transmitir seus logs de atividade para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Uma instância configurada do ArcSight syslog NG daemon SmartConnector (SmartConnector) ou ArcSight Load Balancer. Se os eventos forem enviados para ArcSight Load Balancer, eles serão, consequentemente, enviados para o SmartConnector pelo Load Balancer.

Baixe e abra o [Guia de configuração do ArcSight SmartConnector para Azure monitor Hub de eventos](https://community.microfocus.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/8/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Este guia contém as etapas necessárias para instalar e configurar o ArcSight SmartConnector para Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrar logs do Azure AD ao ArcSight

1. Primeiro, conclua as etapas na seção **pré-requisitos** do guia de configuração. Esta seção inclui as seguintes etapas:
    * Defina permissões de usuário no Azure para garantir que haja um usuário com a função de **proprietário** para implantar e configurar o conector.
    * Abra portas no servidor com o syslog NG daemon SmartConnector, para que ele possa ser acessado do Azure. 
    * A implantação executa um script do Windows PowerShell, portanto, você deve habilitar o PowerShell para executar scripts no computador em que deseja implantar o conector.

2. Siga as etapas na seção **implantando o conector** do guia de configuração do para implantar o conector. Esta seção explica como baixar e extrair o conector, configurar as propriedades do aplicativo e executar o script de implantação da pasta extraída. 

3. Use as etapas em **verificando a implantação no Azure** para verificar se o conector está configurado e funcionando corretamente. Verifique o seguinte:
    * O requisito do Azure Functions é criado em sua assinatura do Azure.
    * Os logs do Azure AD são transmitidos para o destino correto. 
    * As configurações do aplicativo de sua implantação são persistidas nas configurações do aplicativo em aplicativos de funções do Azure. 
    * Um novo grupo de recursos para ArcSight é criado no Azure, com um aplicativo do Azure AD para o conector ArcSight e as contas de armazenamento que contêm os arquivos mapeados no formato CEF.

4. Por fim, conclua as etapas pós-implantação nas **configurações pós-implantação** do guia de configuração. Esta seção explica como executar uma configuração adicional se você estiver em um plano do serviço de aplicativo para impedir que os aplicativos de funções fiquem ociosos após um período de tempo limite, configurar o streaming de logs de diagnóstico do hub de eventos e atualizar o daemon do SysLog NG SmartConnector certificado de repositório de chaves para associá-lo à conta de armazenamento recém-criada.

5. O guia de configuração também explica como personalizar as propriedades do conector no Azure e como atualizar e desinstalar o conector. Também há uma seção sobre melhorias de desempenho, incluindo a atualização para um [plano de consumo do Azure](https://azure.microsoft.com/pricing/details/functions) e a configuração de um Load Balancer de ArcSight se a carga do evento for maior do que o que um único syslog do daemon ng SmartConnector pode manipular.

## <a name="next-steps"></a>Passos seguintes

[Guia de configuração para ArcSight SmartConnector para Azure Monitor Hub de eventos](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
