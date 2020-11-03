---
title: Atribuir uma política de acesso a aazure key vault (Portal)
description: Como usar o portal Azure para atribuir uma política de acesso ao Cofre chave a um principal de serviço ou identidade de aplicação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 9277d100eb6744adfc20fbba9f96b822654e64a3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287714"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Atribua uma política de acesso ao Cofre de Chaves utilizando o portal Azure

Uma política de acesso ao Cofre-Chave determina se um dado responsável de serviço, nomeadamente uma aplicação ou grupo de utilizadores, pode realizar diferentes operações em [segredos](../secrets/index.yml)do Cofre chave, [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Pode atribuir políticas de acesso utilizando o portal Azure (este artigo), o [Azure CLI](assign-access-policy-cli.md)ou [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre a criação de grupos no Azure Ative Directory através do portal Azure, consulte [Criar um grupo básico e adicionar membros](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Atribuir uma política de acesso

1.  No [portal Azure,](https://portal.azure.com)navegue para o recurso Key Vault. 

1.  Em **Definições** , selecione **políticas de acesso** e, em seguida, selecione Adicionar Política de **Acesso** :

    ![Selecione políticas de Acesso, selecionando a atribuição de funções de adicionar](../media/authentication/assign-policy-portal-01.png)

1.  Selecione as permissões desejada sob **permissões de Certificado,** **permissões chave** e **permissões secretas.** Também pode selecionar um modelo que contenha combinações de permissões comuns:

    ![Especificar permissões de política de acesso](../media/authentication/assign-policy-portal-02.png)

1. Em **Select principal** , escolha o link não **selecionado** para abrir o painel de seleção **principal.** Introduza o nome da aplicação ou do principal de serviço no campo de pesquisa, selecione o resultado apropriado e, em seguida, escolha **Selecione**.

    ![Selecionando o principal de serviço para a política de acesso](../media/authentication/assign-policy-portal-03.png)

    Se estiver a utilizar uma identidade gerida para a aplicação, procure e selecione o nome da própria app. (Para obter mais informações sobre os principais de identidade e serviço geridos, consulte [a autenticação key Vault - identidade de aplicações e princípios de serviço](authentication.md#app-identity-and-security-principals).)
 
1.  De volta ao painel de política de **acesso Adicionar,** selecione **Adicionar** para guardar a política de acesso.

    ![Adicionar a política de acesso com o principal serviço atribuído](../media/authentication/assign-policy-portal-04.png)

1. De volta à página de **políticas de Acesso,** verifique se a sua política de acesso está listada nas **Políticas de Acesso Corrente** e, em seguida, selecione **Guardar**. As políticas de acesso não são aplicadas até as salvares.

    ![Salvar as alterações da política de acesso](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Prenda o cofre da chave.](secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)
- [As melhores práticas do Azure Key Vault](best-practices.md)