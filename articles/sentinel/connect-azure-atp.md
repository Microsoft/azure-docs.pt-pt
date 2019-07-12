---
title: Ligar a dados da ATP do Azure para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados da ATP do Azure ao Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77f745f92133f4f43cd2a65f2b69ded1eff9e8ed
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620620"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Ligue os dados do Azure Advanced proteção contra ameaças (ATP)

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Pode transmitir em fluxo registos a partir [do Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) em Azure sentinela com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões de administrador de segurança ou de administrador global
- Tem de ser um cliente de pré-visualização privada da ATP do Azure

## <a name="connect-to-azure-atp"></a>Ligar ao Azure ATP

Certifique-se de que é a versão de pré-visualização privada do Azure ATP [ativada na sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o ATP do Azure é implementado e ingestão de seus dados, os alertas suspeitos podem facilmente ser transmitido em sentinela do Azure. Pode demorar até 24 horas para os alertas iniciar a transmissão em fluxo em sentinela do Azure.



1. No Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **ATP do Azure** mosaico.

2. Clique em **Ligar**.

6. Para utilizar o esquema relevante no Log Analytics para os alertas da ATP do Azure, procure **SecurityAlert**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a proteção de ameaças avançada do Azure ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

