---
title: Encriptação do serviço do Reconhecimento de Formulários de Dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para o Form Recogniser e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 7a8b331c1295ed19afa64e95318bfa14414e6d9f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913063"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Encriptação do Reconhecimento de Formulários de Dados em repouso

O Azure Form Recogniser encripta automaticamente os seus dados ao persistência na nuvem. A encriptação Do Reconhecimento de Formulários protege os seus dados para o ajudar a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados depois de 11 de maio de 2020. Para utilizar CMK com O Reconhecimento de Formulários, terá de criar um novo recurso de Reconhecimento de Formulários. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* [Formulário Reconhecedor de Customer-Managed Formulário de Pedido chave](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)