---
title: Encriptação personalizada da Visão de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação personalizada da Visão de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310296"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Encriptação personalizada da Visão de dados em repouso

O Azure Custom Vision encripta automaticamente os seus dados quando os persistia na nuvem. A encriptação personalizada da Vision protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados depois de 11 de maio de 2020. Para utilizar CMK com Visão Personalizada, terá de criar um novo recurso De Visão Personalizada. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

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
