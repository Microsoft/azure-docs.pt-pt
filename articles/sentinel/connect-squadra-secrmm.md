---
title: Ligue os dados da Squadra Technologies ao Azure Sentinel. Microsoft Docs
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588115"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Ligue os seus dados de secrMM da Squadra Technologies ao Azure Sentinel 

> [!IMPORTANT]
> O conector de dados da Squadra Technologies Security Removable Media Manager (secRMM) em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


O conector secRMM da Squadra Technologies permite-lhe ligar facilmente os registos de soluções de segurança da Squadra Technologies com o Azure Sentinel. Permite-lhe ver dashboards, criar alertas personalizados e melhorar a investigação. Este conector dá-lhe informações sobre eventos de armazenamento amovíveis USB. A integração entre a Squadra Technologies secRMM e a Azure Sentinel faz uso da REST API.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configure e ligue a Squadra Technologies secRMM 

A Squadra Technologies secRMM pode integrar e exportar registos diretamente para o Azure Sentinel.
1. No portal Azure Sentinel, clique nos conectores de dados e selecione Squadra Technologies secRMM e, em seguida, abra a página do conector.

2. Siga os passos delineados no [guia de embarque da Squadra Technologies para que o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) obtenha dados da Squadra secrMM em Azure Sentinel.   


## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics em SecRMM_CL.
Para utilizar o esquema relevante no Log Analytics para o secRMM da Squadra Technologies, procure secRMM_CL.

## <a name="validate-connectivity"></a>Validar conectividade
Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Squadra Technologies secRMM ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

