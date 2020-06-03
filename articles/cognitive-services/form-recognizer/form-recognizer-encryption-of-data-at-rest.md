---
title: Encriptação do serviço do Reconhecimento de Formulários de Dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação do Reconhecimento de Formulários em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310721"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Encriptação do Reconhecimento de Formulários de Dados em repouso

O Azure Form Recogniser encripta automaticamente os seus dados quando os persiste na nuvem. A encriptação Do Reconhecimento de Formulários protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados depois de 11 de maio de 2020. Para utilizar CMK com O Reconhecimento de Formulários, terá de criar um novo recurso de Reconhecimento de Formulários. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* [Formulário Reconhecendo o formulário de pedido de chave gerido pelo cliente](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


