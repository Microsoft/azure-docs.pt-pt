---
title: Recolher dados do Azure ATP sentinela na pré-visualização no Azure | Documentos da Microsoft
description: Saiba como recolher dados do Azure ATP no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5254e60b9b7c38e5f4534e90f8aabe938aef99b2
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574948"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Recolher dados do Azure Advanced proteção contra ameaças (ATP)

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



1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **ATP do Azure** mosaico.

2. Clique em **Ligar**.

6. Para utilizar o esquema relevante no Log Analytics para os alertas da ATP do Azure, procure **SecurityAlert**.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar a proteção de ameaças avançada do Azure ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

