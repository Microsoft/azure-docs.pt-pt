---
title: Eliminar serviços de domínio de diretório ativo azure [ Microsoft Docs
description: Saiba como desativar ou eliminar, um domínio gerido pelo Azure Ative Directory Domain Services utilizando o portal Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655471"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Eliminar um domínio gerido por serviços de domínio de diretório ativo Azure utilizando o portal Azure

Se já não necessitar de um domínio gerido, pode eliminar uma instância de Serviços de Domínio de Diretório Ativo Azure (Azure AD DS). Não há opção de desligar ou desativar temporariamente um domínio gerido pelo Azure AD DS. A eliminação do domínio gerido pela AD DS Azure não elimina ou de outra forma afeta negativamente o inquilino da AD Azure. Este artigo mostra-lhe como usar o portal Azure para eliminar um domínio gerido pelo Azure AD DS.

> [!WARNING]
> **A eliminação é permanente e não pode ser invertida.**
> Quando elimina um domínio gerido pelo Azure AD DS, ocorrem os seguintes passos:
>   * Os controladores de domínio para o domínio gerido são desprovisionados e removidos da rede virtual.
>   * Os dados sobre o domínio gerido são eliminados permanentemente. Estes dados incluem OUs personalizados, GPOs, registos dNS personalizados, diretores de serviço, GMSAs, etc. que criou.
>   * As máquinas unidas ao domínio gerido perdem a sua relação de confiança com o domínio e precisam de ser desacompanhadas do domínio.
>       * Não podes inscrever-te nestas máquinas usando credenciais de anúncios corporativos. Em vez disso, deve utilizar as credenciais do administrador local para a máquina.

## <a name="delete-the-managed-domain"></a>Eliminar o domínio gerido

Para eliminar um domínio gerido pelo Azure AD DS, complete os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD**.
1. Selecione o nome do seu domínio gerido azure AD DS, como *aaddscontoso.com*.
1. Na página **Descrição geral**, selecione **Eliminar**. Para confirmar a eliminação, digite novamente o nome de domínio do domínio gerido e, em seguida, selecione **Delete**.

Pode levar 15-20 minutos ou mais para eliminar o domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Considere [partilhar feedback][feedback] para as funcionalidades que gostaria de ver no Azure AD DS.

Se quiser começar com o Azure AD DS novamente, consulte Create e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
