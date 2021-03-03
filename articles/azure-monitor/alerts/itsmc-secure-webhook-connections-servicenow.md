---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure - Configuração com ServiçoNow
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com o ServiceNow on Secure Export in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 02b167219a4f1604d340d72f9dc47e67919c2542
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714141"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Ligue o ServiçoNow ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow e a Secure Export em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada do ServiceNow Event Management - ITOM (versão Nova Iorque ou posterior).
* [Aplicação](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) instalada na instância ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configure a ligação ServiceNow

1. Utilize o link https://(nome de instância).serviço-agora.com/api/sn_em_connector/em/inbound_event?source=azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Rio Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
