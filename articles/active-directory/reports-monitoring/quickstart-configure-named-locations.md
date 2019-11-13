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
ms.openlocfilehash: df45ab0a7b1729ae6c1602c9769cd5b6da26f6ac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014346"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Início Rápido: Configurar localizações com nome no Azure Active Directory

Com as localizações com nome, pode etiquetar os intervalos de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:
- Detectar falsos positivos em [detecções de risco](concept-risk-events.md). Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador.   
- Configure o [acesso condicional baseado no local](../conditional-access/location-condition.md).

Neste início rápido, vai aprender a configurar localizações com nome no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

* Um inquilino do Azure AD. Inscreva-se numa [avaliação gratuita](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Um utilizador, que é um administrador global do inquilino.
* Um intervalo de IP que está bem estabelecido e é credível na sua organização. O intervalo de IP tem de estar no formato **encaminhamento CIDR (Classless InterDomain Routing)** .

## <a name="configure-named-locations"></a>Configurar localizações com nome

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. No painel esquerdo, selecione **Azure Active Directory**e, em seguida, selecione **acesso condicional** na seção **segurança** .

    ![Guia acesso condicional](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na página **Acesso Condicional**, selecione **Localizações com nome** e **Nova localização**.

    ![Localização com nome](./media/quickstart-configure-named-locations/namedlocation.png)

6. Preencha o formulário na nova página. 

   * Na caixa **Nome**, introduza um nome para a sua localização com nome.
   * Na caixa **Intervalos de IP**, escreva o intervalo de IP no formato CIDR.  
   * Clique em **Criar**.
    
     ![Novo painel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

- [Acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md).
- [Condições de localização no acesso condicional do Azure AD](../conditional-access/location-condition.md)
- [Relatório de entradas arriscadas](concept-risky-sign-ins.md).  
