---
title: Integrar registos com o ArcSight utilizando o Monitor Azure [ Monitor Azul] Microsoft Docs
description: Saiba como integrar registos de Diretórios Ativos Azure com o ArcSight usando o Monitor Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f03b146331069371106c1857f2acc68b566d3c5d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129236"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integre os registos de DirectórioActivo Azure com o ArcSight utilizando o Monitor Azure

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) é uma solução de informação de segurança e gestão de eventos (SIEM) que o ajuda a detetar e responder a ameaças de segurança na sua plataforma. Agora pode encaminhar os registos do Azure Ative Directory (Azure AD) para o ArcSight utilizando o Monitor Azure utilizando o conector ArcSight para AD Azure. Esta funcionalidade permite-lhe monitorizar o seu inquilino para um compromisso de segurança usando o ArcSight.  

Neste artigo, aprende-se a encaminhar os registos da AD Azure para o ArcSight utilizando o Monitor Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um centro de eventos Azure que contém registos de atividade da Azure AD. Aprenda a transmitir os seus registos de [atividade para um centro de eventos.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 
* Uma instância configurada de ArcSight Syslog NG Daemon SmartConnector (SmartConnector) ou ArcSight Load Balancer. Se os eventos forem enviados para o ArcSight Load Balancer, estes são, consequentemente, enviados para o SmartConnector pelo Balancer de Carga.

Descarregue e abra o guia de [configuração do ArcSight SmartConnector para o Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Este guia contém os passos necessários para instalar e configurar o ArcSight SmartConnector para o Monitor Azure. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integre os registos de Anúncios Azure com a ArcSight

1. Primeiro, preencha os passos na secção **pré-requisitos** do guia de configuração. Esta secção inclui os seguintes passos:
    * Detete as permissões do utilizador no Azure, para garantir que existe um utilizador com a função do **proprietário** para implementar e configurar o conector.
    * Abra portas no servidor com Syslog NG Daemon SmartConnector, por isso é acessível a partir do Azure. 
    * A implementação executa um script Windows PowerShell, por isso deve ativar o PowerShell para executar scripts na máquina onde pretende implementar o conector.

2. Siga os passos na implantação da secção de configuração do **Conector** para implantar o conector. Esta secção acompanha-o através de como descarregar e extrair o conector, configurar as propriedades da aplicação e executar o script de implementação a partir da pasta extraída. 

3. Utilize os passos na **verificação da implantação em Azure** para se certificar de que o conector está configurado e funciona corretamente. Verifique o seguinte:
    * As funções azure necessárias são criadas na sua subscrição Azure.
    * Os registos da AD Azure são transmitidos para o destino correto. 
    * As definições de aplicação a partir da sua implementação são persistidas nas Definições de Aplicação em Aplicações de Função Azure. 
    * Um novo grupo de recursos para o ArcSight é criado em Azure, com uma aplicação Azure AD para as contas de conector e armazenamento ArcSight contendo os ficheiros mapeados no formato CEF.

4. Por fim, complete os passos de pós-implantação nas **Configurações pós-implantação** do guia de configuração. Esta secção explica como executar uma configuração adicional se estiver num Plano de Serviço de Aplicações para evitar que as aplicações de função fiquem inativas após um período de tempo, configure o streaming de registos de recursos do centro do evento e atualize o certificado de keystore SysLog NG Daemon SmartConnector para associá-lo à conta de armazenamento recém-criada.

5. O guia de configuração também explica como personalizar as propriedades do conector em Azure, e como atualizar e desinstalar o conector. Há também uma secção sobre melhorias de desempenho, incluindo a atualização para um plano de [consumo azure](https://azure.microsoft.com/pricing/details/functions) e configurar um Equilíbrio de Carga ArcSight se a carga do evento for maior do que o que um único Syslog NG Daemon SmartConnector pode lidar.

## <a name="next-steps"></a>Passos seguintes

[Guia de configuração do ArcSight SmartConnector para o Hub de Eventos do Monitor Azure](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
