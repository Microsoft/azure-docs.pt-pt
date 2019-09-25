---
title: Conectar dados de Cloud App Security ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar Cloud App Security dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240112"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar dados de Microsoft Cloud App Security 



Você pode transmitir logs de [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Essa conexão permite transmitir os alertas de Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança

## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver Cloud App Security, verifique se ele está [habilitado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security for implantado e ingerir seus dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no bloco **Cloud app Security** .

1. Selecione os logs que você deseja transmitir para o Azure Sentinel, você pode escolher **alertas**. 

1. Você pode selecionar se deseja que os alertas de Microsoft Cloud App Security gerem automaticamente incidentes no Azure Sentinel automaticamente. Em **criar incidentes** , selecione **habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Você pode editar essa regra em **análise** e em **regras ativas**.

1. Clique em **Ligar**.

1. Para usar o esquema relevante em Log Analytics para os alertas de Cloud App Security, procure **SecurityAlert**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
