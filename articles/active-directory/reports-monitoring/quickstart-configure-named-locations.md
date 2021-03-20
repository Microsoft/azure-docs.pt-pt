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
ms.openlocfilehash: 94f4d17596936dd9d0ebbdae3c351cac9ed2a570
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89299869"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Início Rápido: Configurar localizações com nome no Azure Active Directory

Com as localizações com nome, pode etiquetar os intervalos de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:
- Detetar falsos positivos nas [deteções de risco.](../identity-protection/overview-identity-protection.md) Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador.   
- Configure [o acesso condicional baseado na localização.](../conditional-access/location-condition.md)

Neste início rápido, vai aprender a configurar localizações com nome no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

* Um inquilino do Azure AD. Inscreva-se para um [teste gratuito.](https://azure.microsoft.com/trial/get-started-active-directory/) 
* Um utilizador, que é um administrador global do inquilino.
* Um intervalo de IP que está bem estabelecido e é credível na sua organização. O intervalo de IP tem de estar no formato **encaminhamento CIDR (Classless InterDomain Routing)**.

## <a name="configure-named-locations"></a>Configurar localizações com nome

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel esquerdo, selecione **Azure Ative Directory** e, em seguida, selecione **Acesso Condicional** a partir da secção **Segurança.**

    ![Separador de acesso condicional](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na página **Acesso Condicional**, selecione **Localizações com nome** e **Nova localização**.

    ![Localização com nome](./media/quickstart-configure-named-locations/namedlocation.png)

6. Preencha o formulário na nova página. 

   * Na caixa **Nome**, introduza um nome para a sua localização com nome.
   * Na caixa **Intervalos de IP**, escreva o intervalo de IP no formato CIDR.  
   * Clique em **Criar**.
    
     ![Novo painel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

- [Localização como condição no Acesso Condicional.](../conditional-access/concept-conditional-access-conditions.md#locations)
- [Relatório de inscrições arriscados.](../identity-protection/overview-identity-protection.md)