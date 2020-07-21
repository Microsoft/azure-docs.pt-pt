---
title: Ligue os dados de sign-on único de Okta ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados de Sign-On únicos da Okta ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532031"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Ligue o seu Okta Single Sign-On ao Azure Sentinel com a função Azure

O conector Okta Single Sign-On (SSO) permite-lhe ligar facilmente todos os seus registos de solução de segurança [Okta Single Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Okta Single Sign-On e Azure Sentinel faz uso de Funções Azure para retirar dados de registo usando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configure e ligue okta single sign-on

As Funções Azure podem integrar e puxar eventos e troncos diretamente de Okta Single Sign-On e encaminhá-los para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Okta Single Sign-On.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Okta Single Sign-On.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics sob a tabela **Okta_CL.**

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar Okta Single Sign-On ao Azure Sentinel usando Apps de Função Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

