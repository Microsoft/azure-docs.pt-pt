---
title: Atribuir uma política de acesso a aazure key vault (Portal)
description: Como usar o portal Azure para atribuir uma política de acesso ao Cofre chave a um principal de segurança ou identidade de aplicação.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751495"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Atribua uma política de acesso ao Cofre de Chaves utilizando o portal Azure

Uma política de acesso ao Cofre-Chave determina se um dado principal de segurança, nomeadamente um utilizador, aplicação ou grupo de utilizadores, pode realizar diferentes operações em [segredos](../secrets/index.yml)do Cofre chave, [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Pode atribuir políticas de acesso utilizando o portal Azure (este artigo), o [Azure CLI](assign-access-policy-cli.md)ou [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre a criação de grupos no Azure Ative Directory através do portal Azure, consulte [Criar um grupo básico e adicionar membros](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Atribuir uma política de acesso

1.  No [portal Azure,](https://portal.azure.com)navegue para o recurso Key Vault. 

1.  Em **Definições**, selecione **políticas de acesso** e, em seguida, selecione Adicionar Política de **Acesso**:

    ![Selecione políticas de Acesso, selecionando a atribuição de funções de adicionar](../media/authentication/assign-policy-portal-01.png)

1.  Selecione as permissões desejada sob **permissões de Certificado,** **permissões chave** e **permissões secretas.** Também pode selecionar um modelo que contenha combinações de permissões comuns:

    ![Especificar permissões de política de acesso](../media/authentication/assign-policy-portal-02.png)

1. Em **Select principal**, escolha o link não **selecionado** para abrir o painel de seleção **principal.** Introduza o nome do utilizador, app ou principal de serviço no campo de pesquisa, selecione o resultado apropriado e, em seguida, escolha **Select**.

    ![Selecionando o principal de segurança para a política de acesso](../media/authentication/assign-policy-portal-03.png)

    Se estiver a utilizar uma identidade gerida para a aplicação, procure e selecione o nome da própria app. (Para obter mais informações sobre os principais de identidade e serviço geridos, consulte [a autenticação key Vault - identidade de aplicações e princípios de serviço](authentication.md#app-identity-and-security-principals).)
 
1.  De volta ao painel de política de **acesso Adicionar,** selecione **Adicionar** para guardar a política de acesso.

    ![Adicionar a política de acesso com o principal de segurança atribuído](../media/authentication/assign-policy-portal-04.png)

1. De volta à página de **políticas de Acesso,** verifique se a sua política de acesso está listada nas **Políticas de Acesso Corrente** e, em seguida, selecione **Guardar**. As políticas de acesso não são aplicadas até as salvares.

    ![Salvar as alterações da política de acesso](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](security-overview.md#identity-management)
- [Prenda o cofre da chave.](security-overview.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)