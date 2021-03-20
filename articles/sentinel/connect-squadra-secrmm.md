---
title: Ligue os dados da Squadra Technologies ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados da Squadra Technologies ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632906"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Ligue os seus dados de secrMM da Squadra Technologies ao Azure Sentinel 

O conector secRMM da Squadra Technologies permite-lhe ligar facilmente os registos de soluções de segurança da Squadra Technologies com o Azure Sentinel. Permite-lhe ver dashboards, criar alertas personalizados e melhorar a investigação. Este conector dá-lhe informações sobre eventos de armazenamento amovíveis USB. A integração entre a Squadra Technologies secRMM e a Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configure e ligue a Squadra Technologies secRMM 

A Squadra Technologies secRMM pode integrar e exportar registos diretamente para o Azure Sentinel.
1. No portal Azure Sentinel, clique nos conectores de dados e selecione Squadra Technologies secRMM e, em seguida, abra a página do conector.

2. Siga os passos delineados no [guia de embarque da Squadra Technologies para que o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) obtenha dados da Squadra secrMM em Azure Sentinel.   

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs**, na secção **De Registos Personalizados,** na `secRMM_CL` tabela.

Para consultar os registos secRMM da Squadra Technologies, insira o nome da tabela no topo da janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Squadra Technologies secRMM ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
