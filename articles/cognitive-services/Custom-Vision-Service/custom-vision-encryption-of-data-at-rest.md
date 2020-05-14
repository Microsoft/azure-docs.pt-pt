---
title: Encriptação personalizada de dados da Visão Em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação personalizada de dados da Visão Em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202273"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Encriptação personalizada de dados da Visão Em repouso

A Azure Custom Vision encripta automaticamente os seus dados quando persiste na nuvem. A encriptação Custom Vision protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para utilizar a CMK com visão personalizada, terá de criar um novo recurso Custom Vision. Assim que o recurso for criado, pode utilizar o Cofre chave Azure para configurar a sua identidade gerida.

### <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão atualmente disponíveis nestas regiões:

* E.U.A. Centro-Sul
* E.U.A.Oeste 2
* E.U.A. Leste
* US Gov - Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Próximos passos

* [Formulário de pedido de chave gerido pelo cliente de visão personalizada](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


