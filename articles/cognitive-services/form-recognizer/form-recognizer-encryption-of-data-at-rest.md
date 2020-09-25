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
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326801"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Encriptação do Reconhecimento de Formulários de Dados em repouso

O Azure Form Recogniser encripta automaticamente os seus dados ao persistência na nuvem. A encriptação Do Reconhecimento de Formulários protege os seus dados para o ajudar a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados depois de 11 de maio de 2020. Para utilizar CMK com O Reconhecimento de Formulários, terá de criar um novo recurso de Reconhecimento de Formulários. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* [Formulário Reconhecendo o formulário de pedido de chave gerido pelo cliente](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)