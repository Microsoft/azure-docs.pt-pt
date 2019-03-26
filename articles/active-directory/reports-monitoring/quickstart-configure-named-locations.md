---
title: Configurar localizações com nome no Azure Active Directory | Microsoft Docs
description: Saiba como configurar localizações com nome.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b21c4727e7679c25dc2574a6506f8d3932aec2
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434794"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Início rápido: Configurar localizações com nome no Azure Active Directory

Com as localizações com nome, pode etiquetar os intervalos de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:
- Detetar falsos positivos em [eventos de risco](concept-risk-events.md). Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador.   
- Configurar o [acesso condicional com base na localização](../conditional-access/location-condition.md).

Neste início rápido, vai aprender a configurar localizações com nome no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

* Um inquilino do Azure AD. Inscreva-se numa [avaliação gratuita](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Um utilizador, que é um administrador global do inquilino.
* Um intervalo de IP que está bem estabelecido e é credível na sua organização. O intervalo de IP tem de estar no formato **encaminhamento CIDR (Classless InterDomain Routing)**.

## <a name="configure-named-locations"></a>Configurar localizações com nome

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel esquerdo, selecione **Azure Active Directory** e, em seguida, selecione **Acesso condicional** na secção **Segurança**.

    ![Separador Acesso condicional](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na página **Acesso Condicional**, selecione **Localizações com nome** e **Nova localização**.

    ![Localização com nome](./media/quickstart-configure-named-locations/namedlocation.png)

6. Preencha o formulário na nova página. 

   * Na caixa **Nome**, introduza um nome para a sua localização com nome.
   * Na caixa **Intervalos de IP**, escreva o intervalo de IP no formato CIDR.  
   * Clique em **Criar**.
    
     ![Novo painel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

- [Acesso do Azure AD condicional](../active-directory-conditional-access-azure-portal.md).
- [Condições de localização no acesso condicional do Microsoft Azure AD](../conditional-access/location-condition.md)
- [Relatório de inícios de sessão arriscados](concept-risky-sign-ins.md).  
