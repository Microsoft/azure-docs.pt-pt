---
title: Terminologia de Gestão AZURE API | Microsoft Docs
description: Este artigo dá definições para os termos específicos da Gestão da API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491829"
---
# <a name="azure-api-management-terminology"></a>Terminologia de Gestão AZURE API

Este artigo dá definições para os termos específicos da Gestão da API (APIM).

## <a name="term-definitions"></a>Definições de prazo

* **Backend API** - Um serviço HTTP que implementa a sua API e as suas operações. Para mais informações, consulte [Backends](backends.md).
* **Frontend API** / **APIM API** - Uma API APIM não acolhe APIs, cria fachadas para as suas APIs. Personaliza a fachada de acordo com as suas necessidades sem tocar na API de backend. Para mais informações, consulte [Import e publique uma API.](import-and-publish.md)
* **Produto APIM** - um produto contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Pode incluir uma série de APIs e oferecê-las aos desenvolvedores através do portal Developer. Para mais informações, consulte [Criar e publicar um produto.](api-management-howto-add-products.md)
* **Operação APIM API** - Cada APIM API representa um conjunto de operações disponíveis para os desenvolvedores. Cada APIM API contém uma referência ao serviço de backend que implementa a API, e o seu mapa de operações às operações implementadas pelo serviço backend. Para obter mais informações, consulte [as respostas da Mock API](mock-api-responses.md).
* **Versão** - Por vezes pretende publicar novas ou diferentes funcionalidades de API para alguns utilizadores, enquanto outros querem ficar com a API que atualmente funciona para eles. Para mais informações, consulte [publicar várias versões da sua API.](api-management-get-started-publish-versions.md)
* **Revisão** - Quando a sua API está pronta para ir e começa a ser usada pelos desenvolvedores, normalmente tem de ter cuidado ao fazer alterações a essa API e, ao mesmo tempo, não perturbar os chamadores da sua API. É também útil dar a conhecer aos programadores as alterações que efetuou. Para obter mais informações, consulte [as revisões da utilização.](api-management-get-started-revise-api.md)
* **Portal do desenvolvedor** - Os seus clientes (desenvolvedores) devem utilizar o portal Developer para aceder às suas APIs. O portal Developer pode ser personalizado. Para mais informações, consulte [Personalizar o portal Developer.](api-management-customize-styles.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma instância](get-started-create-service-instance.md)

