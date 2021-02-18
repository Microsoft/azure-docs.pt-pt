---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure - Configuração com BMC
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com o BMC on Secure Export in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 5a78dc3923c8183db6dd2ddea1d2233149201c07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620832"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Ligue a Helix BMC ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto BMC Helix e a Secure Export em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada da BMC Helix Multi-Cloud Service Management (versão 19.08 ou posterior).

## <a name="configure-the-bmc-helix-connection"></a>Configure a ligação da BMC Helix

1. Utilize o seguinte procedimento no ambiente da Hélice BMC para obter o URI para a exportação segura:

   1. Faça login no Integration Studio.
   1. Procure o **fluxo de Alertas Create.**
   1. Copie o URL webhook .
   
   ![Screenshot do webhook U R L in Integration Studio.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Siga as instruções de acordo com a versão:
   * [Permitir a integração pré-construída com o Azure Monitor para a versão 20.02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Permitir a integração pré-construída com o Azure Monitor para a versão 19.11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Como parte da configuração da ligação na BMC Helix, entre na sua integração BMC instância e siga estas instruções:

   1. Selecione **catálogo**.
   2. Selecione **alertas Azure**.
   3. Selecione **conectores**.
   4. Selecione **a configuração**.
   5. Selecione a nova configuração **de ligação adicionar.**
   6. Preencha as informações para a secção de configuração:
      - **Nome:** Faça o seu próprio.
      - **Tipo de autorização**: **NENHUM**
      - **Descrição:** Invente o seu próprio.
      - **Local**: **Nuvem**
      - **Número de ocorrências**: **2**, o valor predefinido.
      - **Verifique:** Selecionado por predefinição para ativar a utilização.
      - O ID do inquilino Azure e o ID do pedido Azure são retirados da aplicação que definiu anteriormente.

![Screenshot que mostra a configuração do BMC.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
