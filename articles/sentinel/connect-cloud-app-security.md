---
title: Ligar a dados do Cloud App Security para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados do Cloud App Security ao Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 4e75c9003103e267e864a98c7ee5c1bef2176bae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612411"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Ligue os dados do Microsoft Cloud App Security 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos a partir [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) em Azure sentinela com um único clique. Esta ligação permite-lhe transmitir os alertas do Cloud App Security para o Azure sentinela. 

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões de administrador de segurança ou de administrador global

## <a name="connect-to-cloud-app-security"></a>Ligar ao Cloud App Security

Se já tiver o Cloud App Security, certificar-se de que é [ativada na sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se o Cloud App Security é implementado e ingestão de seus dados, os dados de alertas podem facilmente ser transmitido em sentinela do Azure.


1. No Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **Cloud App Security** mosaico.

2. Clique em **Ligar**.

3. Para utilizar o esquema relevante no Log Analytics para os alertas do Cloud App Security, procure **SecurityAlert**.


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft Cloud App Security ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
