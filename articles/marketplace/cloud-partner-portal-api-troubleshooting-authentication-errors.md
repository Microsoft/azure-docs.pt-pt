---
title: Resolução de erros comuns de autenticação Mercado Azure
description: Fornece assistência com erros de autenticação comuns ao utilizar as APIs do Portal do Parceiro cloud.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8c9bd6dbe54f797090c5f7405ad709db954f8fb9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115473"
---
# <a name="troubleshooting-common-authentication-errors"></a>Resolução de erros comuns de autenticação

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

Este artigo fornece assistência com erros de autenticação comuns ao utilizar as APIs do Portal do Parceiro cloud.

## <a name="unauthorized-error"></a>Erro não autorizado

Se tiver `401 unauthorized` erros consistentemente, verifique se tem um token de acesso válido.  Se ainda não o fez, crie uma aplicação básica do Azure Ative Directory (Azure AD) e um principal de serviço, conforme descrito no [portal Use para criar uma aplicação e um diretor de serviço azure ative que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md) Em seguida, utilize a aplicação ou um simples pedido HTTP POST para verificar o seu acesso.  Você incluirá o ID do inquilino, ID de aplicação, ID de objeto, e a chave secreta para obter o token de acesso como mostrado na imagem seguinte:

![Resolução de problemas do erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Erro de proibição

Se tiver um `403 forbidden` erro, certifique-se de que o titular do serviço correto foi adicionado à sua conta de editor no Portal do Parceiro Cloud.
Siga os passos na página [Pré-Requisitos](./cloud-partner-portal-api-prerequisites.md) para adicionar o seu principal de serviço ao portal.

Se o principal de serviço correto tiver sido adicionado, verifique todas as outras informações. Preste muita atenção ao ID do objeto introduzido no portal. Existem dois IDs de objeto na página de registo da aplicação Azure Ative Directory, e você deve usar o ID do objeto local. Pode encontrar o valor correto indo para a página **de registos** da App para a sua aplicação e clicando no nome da aplicação sob **aplicação Gerida no diretório local.** Isto leva-o às propriedades locais da aplicação, onde pode encontrar o ID do objeto correto na página **Propriedades,** como mostra a seguinte figura. Além disso, certifique-se de que utiliza o ID do editor correto quando adicionar o principal de serviço e fazer a chamada da API.

![Resolução de problemas do erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
