---
title: Ligue os produtos Forcepoint ao Azure Sentinel| Microsoft Docs
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77588234"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Ligue os seus produtos Forcepoint ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados dos produtos Forcepoint em Azure Sentinel está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo explica como ligar os seus produtos Forcepoint ao Azure Sentinel. 

Os conectores de dados do Forcepoint permitem-lhe ligar os registos de Segurança de Acesso à Nuvem de Forcepoint e os registos de Firewall de Próxima Geração com o Azure Sentinel. Desta forma, pode exportar automaticamente registos definidos pelo utilizador para o Azure Sentinel em tempo real. O conector dá-lhe uma visibilidade enriquecida nas atividades do utilizador registadas pelos produtos Forcepoint. Também permite uma maior correlação com os dados das cargas de trabalho do Azure e outros feeds, e melhora a capacidade de monitorização com livros de trabalho dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Registos de produtos forward Forcepoint para o agente Syslog 

Configure o produto Forcepoint para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Configurar o produto Forcepoint para a integração do Azure Sentinel, conforme descrito nos seguintes guias de instalação:
 - [Guia de integração casb de ponto de força](https://frcpnt.com/casb-sentinel)
 - [Guia de Integração do Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Procure o CommonSecurityLog para utilizar o esquema relevante em Log Analytics com o nome DeviceVendor contém 'Forcepoint'. 

3. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).



## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os produtos Forcepoint ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)

- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.