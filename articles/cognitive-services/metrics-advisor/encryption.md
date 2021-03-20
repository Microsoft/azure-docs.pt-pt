---
title: Encriptação do serviço Métrica Advisor
titleSuffix: Azure Cognitive Services
description: Métricas Advisor encriptação de dados em repouso.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909748"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Métricas Advisor encriptação de dados em repouso

O serviço Metrics Advisor encripta automaticamente os seus dados quando os persistia na nuvem. A encriptação do serviço Metrics Advisor protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido chave do Serviço de Aconselhamento métrica Customer-Managed .](https://aka.ms/cogsvc-cmk) Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização da CMK com o serviço Metrics Advisor, terá de criar um novo recurso Metrics Advisor e selecionar o E0 como O Nível de Preços. Uma vez criado o seu recurso Metrics Advisor com o nível de preços E0, pode utilizar o Cofre de Chaves Azure para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Passos seguintes

* [Serviço de Aconselhamento de Métricas Customer-Managed Formulário de Pedido chave](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)