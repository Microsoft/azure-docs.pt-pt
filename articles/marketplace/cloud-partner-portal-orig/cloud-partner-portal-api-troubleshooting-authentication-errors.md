---
title: Resolução de erros de autenticação comum de resolução de problemas Mercado Azure
description: Fornece assistência com erros de autenticação comuns ao utilizar as APIs do Portal do Parceiro cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255996"
---
# <a name="troubleshooting-common-authentication-errors"></a>Resolução de erros de autenticação comuns

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Este artigo fornece assistência com erros comuns de autenticação ao utilizar as APIs do Portal do Parceiro cloud.

## <a name="unauthorized-error"></a>Erro não autorizado

Se tiver `401 unauthorized` erros consistentemente, verifique se tem um sinal de acesso válido.  Se ainda não o fez, crie uma aplicação básica do Azure Ative Directory (Azure AD) e um diretor de serviço, tal como descrito no [portal Use, para criar uma aplicação e diretor de serviço sinuoso azure ative que possa aceder a recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Em seguida, utilize a aplicação ou um simples pedido HTTP POST para verificar o seu acesso.  Incluirá o ID do Inquilino, id de aplicação, id do objeto, e a chave secreta para obter o token de acesso como mostrado na seguinte imagem:

![Resolução de problemas do erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Erro de proibição

Se tiver `403 forbidden` um erro, certifique-se de que o diretor de serviço correto foi adicionado à sua conta de editor no Portal do Parceiro cloud.
Siga os passos na página [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para adicionar o seu principal de serviço ao portal.

Se o diretor de serviço correto tiver sido adicionado, verifique todas as outras informações. Preste muita atenção ao ID do Objeto introduzido no portal. Existem duas IDs de Objeto na página de registo de aplicações Azure Ative Directory, e você deve usar o ID de objeto local. Pode encontrar o valor correto indo para a página de **registos** da App para a sua aplicação e clicando no nome da aplicação sob **aplicação gerida no diretório local**. Isto leva-o às propriedades locais para a aplicação, onde pode encontrar o ID de objeto correto na página **Propriedades,** como mostra a figura seguinte. Além disso, certifique-se de que utiliza o ID da editora correta quando adicionar o diretor de serviço e fazer a chamada a API.

![Resolução de problemas do erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
