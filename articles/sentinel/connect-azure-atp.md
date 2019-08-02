---
title: Conectar dados do Azure ATP à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Azure ATP ao Azure Sentinel.
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
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599152"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Conectar dados da ATP (proteção avançada contra ameaças) do Azure

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Você pode transmitir logs da [proteção avançada contra ameaças do Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) para o Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança
- Você deve ser um cliente de visualização do Azure ATP

## <a name="connect-to-azure-atp"></a>Conectar-se ao Azure ATP

Verifique se a versão de visualização do Azure ATP está [habilitada em sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP for implantado e ingerir seus dados, os alertas suspeitos poderão ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para que os alertas comecem a transmitir para o Azure Sentinel.


1. Para conectar o Azure ATP ao Azure Sentinel, você deve primeiro habilitar a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter informações sobre como fazer isso, consulte [integração da proteção avançada contra ameaças do Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **do Azure ATP** .

2. Clique em **Ligar**.

6. Para usar o esquema relevante no Log Analytics para os alertas do Azure ATP, procure **SecurityAlert**.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a conectar a proteção avançada contra ameaças do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

