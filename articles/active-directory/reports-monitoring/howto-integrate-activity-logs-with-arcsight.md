---
title: Integrar registos com o ArcSight utilizando o monitor Azure | Microsoft Docs
description: Saiba como integrar os registos do Azure Ative Directory com o ArcSight utilizando o Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230522"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integre os registos do Azure Ative Directory com o ArcSight utilizando o Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) é uma solução de informação de segurança e gestão de eventos (SIEM) que o ajuda a detetar e responder a ameaças de segurança na sua plataforma. Agora pode encaminhar os registos do Azure Ative Directory (Azure AD) para o ArcSight utilizando o Azure Monitor utilizando o conector ArcSight para Azure AD. Esta funcionalidade permite-lhe monitorizar o seu inquilino para um compromisso de segurança utilizando o ArcSight.  

Neste artigo, aprende a encaminhar os registos Azure AD para o ArcSight utilizando o Azure Monitor. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um centro de eventos Azure que contém registos de atividade azure AD. Saiba como [transmitir os seus registos de atividade para um centro de eventos.](./tutorial-azure-monitor-stream-logs-to-event-hub.md) 
* Um exemplo configurado de ArcSight Syslog NG Daemon SmartConnector (SmartConnector) ou ArcSight Load Balancer. Se os eventos forem enviados para o Balanceador de Carga ArcSight, são, consequentemente, enviados para o SmartConnector pelo Balancer de Carga.

Faça o download e abra o [guia de configuração para ArcSight SmartConnector para O Centro de Eventos do Monitor Azure](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Este guia contém os passos necessários para instalar e configurar o ArcSight SmartConnector para o Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integre os registos AD do Azure com o ArcSight

1. Primeiro, complete os passos na secção **Pré-Requisitos** do guia de configuração. Esta secção inclui os seguintes passos:
    * Desconfie as permissões do utilizador no Azure, para garantir que existe um utilizador com a função **do proprietário** para implantar e configurar o conector.
    * Abra as portas no servidor com o Syslog NG Daemon SmartConnector, pelo que está acessível a partir do Azure. 
    * A implementação executa um script Windows PowerShell, pelo que deve permitir que o PowerShell execute scripts na máquina onde pretende implantar o conector.

2. Siga os passos na **secção de implantação do** guia de configuração do Conector para implantar o conector. Esta secção percorre a forma de descarregar e extrair as propriedades da aplicação do conector, configurar as propriedades da aplicação e executar o script de implementação a partir da pasta extraída. 

3. Utilize os passos na **Verificação da Implantação em Azure** para se certificar de que o conector está configurado e funciona corretamente. Verifique o seguinte:
    * As funções Azure necessárias são criadas na sua subscrição Azure.
    * Os registos AD AZure são transmitidos para o destino correto. 
    * As definições de aplicação da sua implementação são persistiu nas Definições de Aplicação em Aplicações de Função Azure. 
    * Um novo grupo de recursos para o ArcSight é criado em Azure, com uma aplicação AD Azure para o conector ArcSight e contas de armazenamento contendo os ficheiros mapeados no formato CEF.

4. Por fim, complete os passos de pós-implantação nas **Configurações pós-implantação** do guia de configuração. Esta secção explica como executar uma configuração adicional se estiver num Plano de Serviço de Aplicações para evitar que as aplicações de função se insuam após um período de tempo, configuriz o streaming de registos de recursos do centro do evento e atualize o certificado de teclas SysLog NG Daemon SmartConnector para associá-lo à conta de armazenamento recém-criada.

5. O guia de configuração também explica como personalizar as propriedades do conector em Azure, e como atualizar e desinstalar o conector. Há também uma secção sobre melhorias de desempenho, incluindo o upgrade para um [plano de consumo Azure](https://azure.microsoft.com/pricing/details/functions) e configurar um Balancer de Carga ArcSight se a carga do evento for maior do que um único Syslog NG Daemon SmartConnector pode lidar.

## <a name="next-steps"></a>Passos seguintes

[Guia de configuração para ArcSight SmartConnector para O Centro de Eventos do Monitor Azure](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)