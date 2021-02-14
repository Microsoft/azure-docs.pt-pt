---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure - Configuração com ServiçoNow
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com o ServiceNow on Secure Export in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7801145ba1bcaa2ffd543becfe190f05d232e8c8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104965"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Ligue o ServiçoNow ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow e a Secure Export em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada do ServiceNow Event Management - ITOM (versão Nova Iorque ou posterior).

## <a name="configure-the-servicenow-connection"></a>Configure a ligação ServiceNow

1. Utilize o link https://(nome de instância).serviço-agora.com/api/sn_em_connector/em/inbound_event?source=azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Rio Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
