---
title: Autenticação
description: Explica como funciona a autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681639"
---
# <a name="configure-authentication"></a>Autenticação configurada

A renderização remota Azure utiliza o mesmo mecanismo de autenticação que [as Âncoras Espaciais Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Os clientes precisam de definir *o AccountKey,* *AutenticaçãoToken*ou *AccessToken* para ligar com sucesso às APIs rest. *O AccountKey* pode ser obtido no separador "Teclas" para a conta de renderização remota no portal Azure. *AutenticaçãoToken* é um símbolo Azure AD, que pode ser obtido através da [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* é um símbolo de MR, que pode ser obtido a partir do Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Autenticação para aplicações implantadas

 Recomenda-se a utilização das chaves de conta para embarque rápido, mas apenas durante o desenvolvimento/prototipagem. Recomenda-se vivamente não enviar a sua aplicação para produção utilizando uma chave de conta incorporada na seleção e utilizar as abordagens de autenticação Azure AD baseadas no utilizador ou no serviço.

 A autenticação Azure AD é descrita na secção de autenticação do [utilizador Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) do serviço [Azure Spatial Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para ajudar a controlar o nível de acesso concedido a aplicações, serviços ou utilizadores de AD Azure do seu serviço, foram criadas as seguintes funções para que possa atribuir as necessárias às suas contas de Renderização Remota Azure:

* **Administrador de renderização remota**: Fornece ao utilizador capacidades de conversão, gestão de sessão, renderização e diagnóstico para a renderização remota azure.
* Cliente de **renderização remota**: Fornece ao utilizador capacidades de gestão, renderização e diagnóstico para a renderização remota Azure.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta](create-an-account.md)
* [Utilização das APIs Frontend Azure para autenticação](frontend-apis.md)
* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
