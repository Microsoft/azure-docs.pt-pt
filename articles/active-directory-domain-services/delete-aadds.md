---
title: Excluir Serviços de Domínio do Diretório Ativo Azure / Microsoft Docs
description: Saiba como desativar ou eliminar um domínio gerido por Serviços de Domínio do Diretório Ativo Azure utilizando o portal Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 993a73a42887b3869c097d14b6945a1093ac6889
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723066"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Eliminar um domínio gerido por Serviços de Domínio do Diretório Ativo Azure utilizando o portal Azure

Se já não necessitar de um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS), pode eliminá-lo. Não há opção para desligar ou desativar temporariamente um domínio gerido pela Azure AD DS. A eliminação do domínio gerido não apaga ou afeta negativamente o inquilino da AD Azure.

Este artigo mostra-lhe como usar o portal Azure para eliminar um domínio gerido.

> [!WARNING]
> **A eliminação é permanente e não pode ser revertida.**
> 
> Quando elimina um domínio gerido, ocorrem os seguintes passos:
>   * Os controladores de domínio para o domínio gerido são desavisionados e removidos da rede virtual.
>   * Os dados sobre o domínio gerido são eliminados permanentemente. Estes dados incluem OUs personalizados, GPOs, registos de DNS personalizados, diretores de serviço, GMSAs, etc. que criou.
>   * As máquinas unidas ao domínio gerido perdem a sua relação de confiança com o domínio e precisam de ser uninadas do domínio.
>       * Não podes entrar nestas máquinas usando credenciais de AD corporativas. Em vez disso, deve utilizar as credenciais de administrador local para a máquina.

## <a name="delete-the-managed-domain"></a>Eliminar o domínio gerido

Para eliminar um domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**.
1. Selecione o nome do seu domínio gerido, como *aaddscontoso.com*.
1. Na página **Descrição geral**, selecione **Eliminar**. Para confirmar a eliminação, escreva novamente o nome de domínio do domínio gerido e, em seguida, selecione **Delete**.

Pode levar 15-20 minutos ou mais para eliminar o domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Considere [partilhar feedback][feedback] para as funcionalidades que gostaria de ver no Azure AD DS.

Se quiser começar com a Azure AD DS novamente, consulte Criar e configurar um domínio gerido por [Serviços de Domínio do Diretório Ativo Azure][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md