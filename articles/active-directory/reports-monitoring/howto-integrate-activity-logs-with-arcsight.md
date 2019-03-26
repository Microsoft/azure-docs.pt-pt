---
title: Como integrar registos do Azure Active Directory com ArcSight através do Azure Monitor (pré-visualização) | Documentos da Microsoft
description: Saiba como integrar registos do Azure Active Directory com ArcSight através do Azure Monitor (pré-visualização)
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
ms.date: 12/03/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bd7101c641cf69112975cd0431a08ec74f99aac
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438109"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>Integrar registos do Azure Active Directory com ArcSight através do Azure Monitor (pré-visualização)

[Micro foco ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) é uma segurança informações e eventos (SIEM) solução de gestão que o ajuda a detetar e responder a ameaças de segurança na sua plataforma. Agora, pode encaminhar registos do Azure Active Directory (Azure AD) ArcSight através do Azure Monitor através do conector de ArcSight para o Azure AD. Esta funcionalidade permite-lhe monitorizar o seu inquilino para o compromisso de segurança usando ArcSight.  

Neste artigo, irá aprender a encaminhar registos do Azure AD para ArcSight através do Azure Monitor. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Registos de um hub de eventos do Azure que contém a atividade do Azure AD. Saiba como [transmitir os registos de atividade para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Uma instância configurada do cso Syslog NG Daemon SmartConnector (SmartConnector) ou o Balanceador de carga da ArcSight. Se os eventos são enviados para o Balanceador de carga da ArcSight, conseqüentemente são enviadas para o SmartConnector pelo balanceador de carga.

Transfira e abra o [guia de configuração do cso SmartConnector para o Hub de eventos do Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Este guia contém os passos que necessários para instalar e configurar o ArcSight SmartConnector para o Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrar registos do Azure AD com ArcSight

1. Em primeiro lugar, concluir os passos a **pré-requisitos** secção do guia de configuração. Esta secção inclui os seguintes passos:
    * Definir permissões de utilizador no Azure, para garantir que existe um utilizador com o **proprietário** função para implementar e configurar o conector.
    * Abrir portas no servidor com Syslog NG Daemon SmartConnector, pelo que é acessível a partir do Azure. 
    * A implementação executa um script de Windows PowerShell, pelo que deve ativar o PowerShell para executar scripts na máquina onde pretende implementar o conector.

2. Siga os passos a **implementar o conector** secção do guia de configuração para implementar o conector. Esta secção explica como transferir e extrair o conector, configurar as propriedades da aplicação e execute o script de implementação a partir da pasta de extraídos. 

3. Utilize os passos no **verificar a implementação no Azure** para se certificar-se de que o conector está configurado e que funciona corretamente. Verifique o seguinte:
    * As funções do Azure necessários são criadas na sua subscrição do Azure.
    * Os registos do Azure AD são transmitidos para o destino correto. 
    * As definições da aplicação da sua implementação são mantidas nas definições da aplicação nas aplicações de função do Azure. 
    * Um novo grupo de recursos para ArcSight é criado no Azure, com uma aplicação do Azure AD para o conector da ArcSight e contas de armazenamento que contém os arquivos mapeados no formato CEF.

4. Por fim, completa a pós-implementação etapas na **configurações de pós-implementação** o guia de configuração. Esta secção explica como executar configuração adicional, se estiver a utilizar um plano de serviço de aplicações para impedir que as aplicações function App vai inativo após um período de tempo limite, configurar a transmissão em fluxo de registos de diagnóstico do hub de eventos e atualizar a NG Daemon de SysLog Certificado de keystore SmartConnector para associá-lo com a conta de armazenamento recentemente criada.

5. O guia de configuração também explica como personalizar as propriedades do conector no Azure e como atualizar e desinstalar o conector. Também há uma seção sobre melhorias de desempenho, incluindo a atualização para uma [plano de consumo do Azure](https://azure.microsoft.com/pricing/details/functions) e configurar um balanceador de carga da ArcSight, se a carga do evento é maior do que o que pode um Daemon SmartConnector único do Syslog NG Identificador.

## <a name="next-steps"></a>Passos Seguintes

* [Guia de configuração do cso SmartConnector para o Hub de eventos do Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
