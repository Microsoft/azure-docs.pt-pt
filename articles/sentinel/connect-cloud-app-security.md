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
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: 95e38cee8f6995e09dbbb2194cd5a9d0ebc301c1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498659"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar dados de Microsoft Cloud App Security 



Você pode transmitir logs de [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Essa conexão permite transmitir os alertas de Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança
- Para transmitir Cloud Discovery logs para o Azure Sentinel, [habilite o Azure Sentinel como seu Siem no Microsoft Cloud app Security](aka.ms. https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de logs de Cloud Discovery está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver Cloud App Security, verifique se ele está [habilitado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security for implantado e ingerir seus dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados**, clique no bloco **Cloud app Security** e selecione a **página abrir conector**.

1. Selecione os logs que você deseja transmitir para o Azure Sentinel, você pode escolher **alertas** e **Cloud Discovery logs** (versão prévia). 

1. Clique em **Ligar**.

1. Para usar o esquema relevante em Log Analytics para os alertas de Cloud App Security, procure **SecurityAlert**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
