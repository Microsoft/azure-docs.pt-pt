---
title: 'Azure AD Connect: Opções de dispositivos | Documentos da Microsoft'
description: Este documento detalha as opções de dispositivo disponíveis no Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e52f691c75d491897b06a4ebb492d87fda682e38
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917851"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opções de dispositivos

A seguinte documentação fornece informações sobre as várias opções de dispositivo disponíveis no Azure AD Connect. Pode utilizar o Azure AD Connect para configurar as seguintes duas operações: 
* **Associação ao Azure AD híbrido**: se o ambiente tiver no local requisitos de espaço do AD e também deseja o benefício dos recursos fornecidos pelo Azure Active Directory, pode implementar dispositivos de associados ao Azure AD híbrido. Estes são os dispositivos que estão ambos, associado ao seu local do Active Directory e o Azure Active Directory.
* **Repetição de escrita do dispositivo**: repetição de escrita do dispositivo é utilizada para ativar o acesso condicional com base em dispositivos para o AD FS (2012 R2 ou superior) protegidos de dispositivos

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurar opções do dispositivo no Azure AD Connect

1.  Execução do Azure AD Connect. Na **tarefas adicionais** página, selecione **configurar opções do dispositivo**.
    ![Configurar opções do dispositivo](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Ao clicar em seguinte, uma **descrição geral** é apresentada a página, que fornece detalhes sobre as operações que podem ser executadas.
    ![Descrição geral](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções de dispositivo de configurar está disponível apenas na versão 1.1.819.0 e mais recente.

2.  Depois de fornecer as credenciais para o Azure AD, pode selecionar a operação a ser executada na página de opções do dispositivo.
    ![Operações de dispositivo](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a associação ao Azure AD híbrido](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurar / desativar a repetição de escrita do dispositivo](./active-directory-aadconnect-feature-device-writeback.md)

