---
title: Desabilitar Azure Active Directory Domain Services | Microsoft Docs
description: Desabilitar Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171960"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Desabilitar Azure Active Directory Domain Services usando o portal do Azure
Este artigo mostra como usar o portal do Azure para desabilitar os serviços de domínio do Azure Active Directory (AD) para seu diretório do Azure AD.

> [!WARNING]
> **A exclusão é permanente e não pode ser revertida.**
> Continue com cuidado! Quando você exclui o domínio gerenciado:
>   * Os controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
>   * Os dados no domínio gerenciado são excluídos permanentemente. Isso inclui UOs personalizadas, GPOs, registros DNS personalizados, entidades de serviço, GMSAs, etc. que você criou no domínio gerenciado.
>   * Os computadores ingressados no domínio gerenciado perdem sua relação de confiança com o domínio e precisam ser retirados do domínio.
>   * Você não pode entrar nesses computadores usando credenciais corporativas do AD. Em vez disso, use as credenciais de administrador local para o computador.
> Excluir o domínio gerenciado não exclui seu diretório do Azure AD ou, de outra forma, afeta negativamente o diretório.

Execute as seguintes etapas para excluir seu Azure AD Domain Services domínio gerenciado:
1. Navegue até a [extensão de Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no nome do seu domínio gerenciado.

    ![Selecionar domínio para excluir](./media/getting-started/domain-services-delete-select-domain.png)

3. Na página **visão geral** , clique no botão **excluir** .

    ![Eliminar domínio](./media/getting-started/domain-services-delete-domain.png)

4. Para confirmar a exclusão, digite o nome de domínio DNS do domínio gerenciado. Clique no botão **excluir** quando terminar.

    ![Excluir confirmação de domínio](./media/getting-started/domain-services-delete-domain-confirm.png)

Pode levar de 15-20 minutos ou mais para excluir o domínio gerenciado.

Considere [compartilhar comentários](contact-us.md) para nos ajudar a entender quais recursos o ajudarão a escolher Azure AD Domain Services no futuro. Esses comentários nos ajudam a desenvolver o serviço para se adequar melhor às suas necessidades de implantação e casos de uso.
