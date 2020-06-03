---
title: Encriptação do serviço facial de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação do serviço facial de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309038"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Encriptação do serviço facial de dados em repouso

O serviço Face encripta automaticamente os seus dados quando os persiste na nuvem. A encriptação do serviço Face protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido de Chave Gerido pelo Cliente](https://aka.ms/cogsvc-cmk)do Serviço Facial . Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização de CMK com o serviço Face, terá de criar um novo recurso Face e selecionar o E0 como O Nível de Preços. Uma vez criado o seu recurso Face com o nível de preços E0, pode utilizar o Cofre da Chave Azure para configurar a sua identidade gerida.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão atualmente disponíveis nestas regiões:

* E.U.A. Centro-Sul
* E.U.A.Oeste 2
* E.U.A. Leste
* US Gov - Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa de serviços que suportam a CMK, consulte [chaves geridas pelo cliente para serviços cognitivos](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulário de pedido de chave gerido pelo cliente de serviços cognitivos](https://aka.ms/cogsvc-cmk)
