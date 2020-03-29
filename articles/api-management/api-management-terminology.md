---
title: Terminologia azure API Management [ Microsoft Docs
description: Este artigo dá definições para os termos específicos da Gestão API.
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
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074059"
---
# <a name="terminology"></a>Terminologia

Este artigo apresenta definições para os termos específicos da Gestão da API (APIM).

## <a name="term-definitions"></a>Definições de prazo

* **Backend API** - Um serviço HTTP que implementa a sua API e as suas operações. 
* **Frontend API APIM**/**API** - Uma API API api não acolhe APIs, cria fachadas para as suas APIs de forma a personalizar a fachada de acordo com as suas necessidades sem tocar na API traseira. Para mais informações, consulte [Importar e publicar uma API](import-and-publish.md).
* **Produto APIM** - um produto contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Pode incluir uma série de APIs e oferecê-las aos desenvolvedores através do portal Developer. Para mais informações, consulte [Criar e publicar um produto](api-management-howto-add-products.md).
* **Operação APIM API** - Cada API API representa um conjunto de operações disponíveis para os desenvolvedores. Cada APIM API contém uma referência ao serviço de backend atrás que implementa a API, e o seu mapa de operações para as operações implementadas pelo serviço de backenda. Para mais informações, consulte [as respostas da API de Mock](mock-api-responses.md).
* **Versão** - Por vezes, pretende publicar novas ou diferentes funcionalidades de API a alguns utilizadores, enquanto outros querem manter-se na API que atualmente funciona para eles. Para mais informações, consulte [Publicar várias versões da sua API](api-management-get-started-publish-versions.md).
* **Revisão** - Quando a sua API está pronta para ir e começa a ser usada pelos desenvolvedores, normalmente precisa de ter cuidado para fazer alterações nessa API e, ao mesmo tempo, não perturbar os chamadores da sua API. É também útil dar a conhecer aos programadores as alterações que efetuou. Para mais informações, consulte [Revisões de Utilização](api-management-get-started-revise-api.md).
* **Portal** de desenvolvimento - Os seus clientes (desenvolvedores) devem utilizar o portal Developer para aceder às suas APIs. O portal Developer pode ser personalizado. Para mais informações, consulte [Personalizar o portal Developer](api-management-customize-styles.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma instância](get-started-create-service-instance.md)

