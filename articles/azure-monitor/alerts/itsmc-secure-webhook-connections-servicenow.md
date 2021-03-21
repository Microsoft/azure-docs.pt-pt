---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure - Configuração com ServiçoNow
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com o ServiceNow on Secure Export in Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: cf19911bd8126bfb73f848c086aa817a7d7adb00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563696"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Ligue o ServiçoNow ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow e a Secure Export em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada do ServiceNow Event Management - ITOM (versão Nova Iorque ou posterior).
* [Aplicação](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) instalada na instância ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configure a ligação ServiceNow

1. Utilize o link https:// (nome de instância).now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Rio Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/concept/azure-integration.html)
