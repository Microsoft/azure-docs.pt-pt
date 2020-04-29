---
title: Ligue produtos forcepoint ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os produtos Forcepoint ao Azure Sentinel.
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588234"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Ligue os seus produtos Forcepoint ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados de produtos Forcepoint em Azure Sentinel encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


Este artigo explica como ligar os seus produtos Forcepoint ao Azure Sentinel. 

Os conectores de dados do Forcepoint permitem ligar o Forcepoint Cloud Access Security Broker e os registos de Firewall da Próxima Geração com o Azure Sentinel. Desta forma, pode exportar automaticamente registos definidos pelo utilizador para o Azure Sentinel em tempo real. O conector dá-lhe visibilidade enriquecida nas atividades do utilizador registadas pelos produtos Forcepoint. Também permite uma maior correlação com os dados das cargas de trabalho do Azure e outros feeds, e melhora a capacidade de monitorização com os Livros de Trabalho dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Registos de produtos De Forcepoint avançados para o agente Syslog 

Configure o produto Forcepoint para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Configurar o produto Forcepoint para a integração do Sentinel a Azure, conforme descrito nos seguintes guias de instalação:
 - [Guia de Integração CASB de Forcepoint](https://frcpnt.com/casb-sentinel)
 - [Guia de Integração de NGFW de Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Procure o CommonSecurityLog para utilizar o esquema relevante no Log Analytics com o nome Do Fornecedor de Dispositivos contém 'Forcepoint'. 

3. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)



## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar produtos Da Forcepoint ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)

- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.