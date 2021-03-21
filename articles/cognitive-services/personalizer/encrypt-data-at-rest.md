---
title: Encriptação de serviço personalizado de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para Personalizer, e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524501"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Encriptação de serviço personalizado de dados em repouso

O serviço Personalizar encripta automaticamente os seus dados quando os persistia na nuvem. A encriptação do serviço Personalizer protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido de Pedido de Customer-Managed Desemperante](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização da CMK com o serviço Personalizar, terá de criar um novo recurso Personalizer e selecionar o E0 como O Nível de Preços. Uma vez criado o seu recurso Personalizer com o nível de preços E0, pode utilizar o Cofre de Chaves Azure para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* [Personalizar serviço Customer-Managed formulário de pedido chave](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)