---
title: Desativar os serviços de domínio do Active Directory do Azure | Documentos da Microsoft
description: Desativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: 122caddd73219060689ac8f6e3ab6408ea99a422
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246228"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Desativar o Azure Active Directory Domain Services no portal do Azure
Este artigo mostra-lhe como utilizar o portal do Azure para desativar os serviços de domínio do Azure Active Directory (AD) para o diretório do Azure AD.

> [!WARNING]
> **Eliminação é permanente e não pode ser revertida.**
> Avance com cuidado! Ao eliminar o domínio gerido:
>   * Controladores de domínio para o domínio gerido são desconfiguradas e removidos da rede virtual.
>   * Dados no domínio gerido são eliminados permanentemente. Isto inclui UOs personalizadas, GPOs, registos DNS personalizados, principais de serviço, etc. de GMSAs que tenha criado no domínio gerido.
>   * As máquinas associadas ao domínio gerido perdem a relação de confiança com o domínio e tem de ser retirado do domínio.
>   * Não consigo iniciar sessão nestas máquinas utilizando as credenciais empresariais do AD. Utilize as credenciais de administrador local para a máquina, em vez disso.
> A eliminar o domínio gerido não eliminar o diretório do Azure AD ou caso contrário, ter um impacto adverso no diretório.

Execute os passos seguintes para eliminar o seu domínio gerido do Azure AD Domain Services:
1. Navegue para o [extensão do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no nome do seu domínio gerido.

    ![Selecione o domínio para eliminar](./media/getting-started/domain-services-delete-select-domain.png)

3. Sobre o **descrição geral** página, clique no **eliminar** botão.

    ![Eliminar domínio](./media/getting-started/domain-services-delete-domain.png)

4. Para confirmar a eliminação, escreva o nome de domínio DNS do domínio gerido. Clique nas **eliminar** botão quando tiver terminado.

    ![Eliminar confirmação de domínio](./media/getting-started/domain-services-delete-domain-confirm.png)

O domínio gerido é eliminado em cerca de 15 a 20 minutos.

Considere [partilhar comentários](contact-us.md) para ajudar-na perceber o que ajudaria funcionalidades escolheu o Azure AD Domain Services no futuro. Seus comentários ajudam-na evoluir o serviço para se adequar melhor às suas necessidades de implementação e casos de utilização.
