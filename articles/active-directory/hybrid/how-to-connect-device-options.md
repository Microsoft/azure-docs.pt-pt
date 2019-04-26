---
title: 'Azure AD Connect: Opções de dispositivo | Documentos da Microsoft'
description: Este documento detalha as opções de dispositivo disponíveis no Azure AD Connect
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
ms.openlocfilehash: b8f397401581cc1bc947f1b19ce248c17fba26f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245629"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opções do dispositivo

A seguinte documentação fornece informações sobre as várias opções de dispositivo disponíveis no Azure AD Connect. Pode utilizar o Azure AD Connect para configurar as seguintes duas operações: 
* **Associação ao Azure AD híbrido**: Se o ambiente tiver no local requisitos de espaço do AD e deseja os benefícios do Azure AD, pode implementar a dispositivos de associados ao Azure AD híbrido. Estes dispositivos estão associados ao Active Directory no local e o Azure Active Directory.
* **Repetição de escrita do dispositivo**: Repetição de escrita do dispositivo é utilizada para ativar o acesso condicional com base em dispositivos para o AD FS (2012 R2 ou superior) protegidos de dispositivos

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurar opções do dispositivo no Azure AD Connect

1.  Execução do Azure AD Connect. Na **tarefas adicionais** página, selecione **configurar opções do dispositivo**.  Clique em **Seguinte**.
    ![Configurar opções do dispositivo](./media/how-to-connect-device-options/deviceoptions.png) 

    O **descrição geral** página apresenta os detalhes.
    ![Descrição geral](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções de dispositivo de configurar está disponível apenas na versão 1.1.819.0 e mais recente.

2.  Depois de fornecer as credenciais para o Azure AD, pode selecionar a operação a ser executada na página de opções do dispositivo.
    ![Operações de dispositivo](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a associação ao Azure AD híbrido](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurar / desativar a repetição de escrita do dispositivo](how-to-connect-device-writeback.md)

