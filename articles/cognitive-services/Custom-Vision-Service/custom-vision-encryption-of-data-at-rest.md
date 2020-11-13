---
title: Encriptação personalizada da Visão de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para a Visão Personalizada e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616217"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Encriptação personalizada da Visão de dados em repouso

O Azure Custom Vision encripta automaticamente os seus dados quando os persistia na nuvem. A encriptação personalizada da Vision protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados depois de 11 de maio de 2020. Para utilizar CMK com Visão Personalizada, terá de criar um novo recurso De Visão Personalizada. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão atualmente disponíveis nestas regiões:

* E.U.A. Centro-Sul
* E.U.A. Oeste 2
* E.U.A. Leste
* US Gov - Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa de serviços que suportam a CMK, consulte [chaves geridas pelo cliente para serviços cognitivos](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault?](../../key-vault/general/overview.md)
* [Formulário de pedido chave de Customer-Managed serviços cognitivos](https://aka.ms/cogsvc-cmk)