---
title: 'Azure AD Connect: Opções do dispositivo / Microsoft Docs'
description: Este documento detalha as opções do dispositivo disponíveis no Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67109533"
---
# <a name="azure-ad-connect-device-options"></a>Ligação Azure AD: Opções do dispositivo

A seguinte documentação fornece informações sobre as várias opções do dispositivo disponíveis no Azure AD Connect. Pode utilizar o Azure AD Connect para configurar as seguintes duas operações: 
* **Ad Hybrid Azure junta-se:** Se o seu ambiente tiver uma pegada ad no local e quiser os benefícios do Azure AD, pode implementar dispositivos híbridos Azure AD. Estes dispositivos são unidos tanto ao seu Diretório Ativo no local como ao seu Diretório Ativo Azure.
* **Redação do dispositivo**: A reescrita do dispositivo é utilizada para permitir o acesso condicional com base em dispositivos a Dispositivos AD FS (2012 R2 ou superior) protegidos

## <a name="configure-device-options-in-azure-ad-connect"></a>Configure opções de dispositivos no Azure AD Connect

1.  Executar Azure AD Connect. Na página **de tarefas Adicionais,** selecione **Configurar as opções**do dispositivo .  Clique em **Seguinte**.
    ![Configure as opções do dispositivo](./media/how-to-connect-device-options/deviceoptions.png) 

    A página **'Overview'** apresenta os detalhes.
    ![Descrição Geral](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções do dispositivo Configure só estão disponíveis na versão 1.1.819.0 e mais recentes.

2.  Depois de fornecer as credenciais para o Azure AD, pode escolher a operação a ser realizada na página de opções do Dispositivo.
    ![Operações de dispositivos](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passos seguintes

* [Configure Hybrid Azure AD aderir](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurar / Desativar a reescrita do dispositivo](how-to-connect-device-writeback.md)

