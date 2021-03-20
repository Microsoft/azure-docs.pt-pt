---
title: 'Ligação AZure AD: Opções do dispositivo | Microsoft Docs'
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
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279232"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opções de dispositivo

A documentação que se segue fornece informações sobre as várias opções do dispositivo disponíveis no Azure AD Connect. Pode utilizar o Azure AD Connect para configurar as seguintes duas operações: 
* **Hybrid Azure AD ad:** Se o seu ambiente tiver uma pegada AD no local e quiser os benefícios do Azure AD, pode implementar dispositivos híbridos Azure AD. Estes dispositivos estão ligados tanto ao seu Ative Directory no local como ao seu Diretório Ativo Azure.
* **Writeback do dispositivo**: A writeback do dispositivo é utilizada para permitir o acesso condicional com base em dispositivos a dispositivos protegidos AD FS (2012 R2 ou superior)

## <a name="configure-device-options-in-azure-ad-connect"></a>Configure as opções do dispositivo no Azure AD Connect

1.  Executar Azure Ad Connect. Na página **de tarefas adicionais,** selecione **opções do dispositivo configurar**.  Clique em **Seguinte**.
    ![Opções de configuração do dispositivo](./media/how-to-connect-device-options/deviceoptions.png) 

    A página **'Vista Geral'** apresenta os detalhes.
    ![Descrição Geral](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções do dispositivo Configurar apenas estão disponíveis na versão 1.1.819.0 e mais recentes.

2.  Depois de fornecer as credenciais para Azure AD, pode escolher a operação a ser executada na página de opções do Dispositivo.
    ![Operações de dispositivos](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passos seguintes

* [Configure Hybrid Azure AD juntar-se](../devices/hybrid-azuread-join-plan.md)
* [Configurar / Desativar a writeback do dispositivo](how-to-connect-device-writeback.md)