---
title: Conectar dados de BIG-IP do F5 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de BIG-IP F5 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: ff5a8f940348c36ca2a19fb31a173b2dfc4f4d6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514137"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Conectar seu dispositivo de BIG-IP F5 

> [!IMPORTANT]
> O conector de dados BIG-IP F5 no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de BIG-IP F5 permite que você conecte facilmente todos os seus logs de BIG-IP do F5 com o Azure Sentinel, para exibir pastas de trabalho, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança. A integração entre F5 BIG-IP e o Azure Sentinel usa a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configurar e conectar F5 BIG-IP 

F5 BIG-IP pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **F5 Big-IP** e **abra a página conector**. 
1. Para conectar seu F5 BIG-IP, você precisa postar uma declaração JSON no ponto de extremidade de API do sistema. Para obter instruções sobre como fazer isso, consulte [integrando o Big-IP do F5 com o Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na página do conector F5 BIG-IP, copie a ID do espaço de trabalho e a chave primária e cole-as conforme instruído em [dados de streaming para o Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Depois de concluir as instruções F5 BIG-IP, na página conector do Azure Sentinel, você verá os tipos de dados conectados.
1. Para usar o esquema relevante em Log Analytics para os eventos BIG-IP F5, pesquise **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Validar conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar F5 BIG-IP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

