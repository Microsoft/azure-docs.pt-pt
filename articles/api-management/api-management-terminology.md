---
title: Terminologia de gestão de API do Azure | Documentos da Microsoft
description: Este artigo apresenta as definições para os termos que são específicos da gestão de API.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074059"
---
# <a name="terminology"></a>Terminologia

Este artigo apresenta as definições para os termos que são específicos para gestão de API (APIM).

## <a name="term-definitions"></a>Definições do termo

* **API de back-end** -serviço de um HTTP que implementa a API e as respetivas operações. 
* **API de front-end**/**API de APIM** -uma API de APIM não aloje APIs, ele cria fachadas para as suas APIs para personalizar a fachada consoante as suas necessidades, sem tocar o back end de API. Para obter mais informações, consulte [importar e publicar uma API](import-and-publish.md).
* **Produto APIM** -um produto contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Pode incluir uma série de APIs e disponibilizá-las para os programadores através do portal do programador. Para obter mais informações, consulte [criar e publicar um produto](api-management-howto-add-products.md).
* **Operação de API de APIM** -API de APIM cada representa um conjunto de operações disponíveis para desenvolvedores. Cada API de APIM contém uma referência para o serviço de back-end que implementa a API e as respetivo operações efetuam o mapeamento para as operações implementadas pelo serviço de back-end. Para obter mais informações, consulte [respostas de API de simular](mock-api-responses.md).
* **Versão** – às vezes deseja publicar novos ou funcionalidades de API diferente para alguns usuários, enquanto outras pessoas querem ficar com a API que trabalha atualmente para eles. Para obter mais informações, consulte [publicar várias versões da sua API](api-management-get-started-publish-versions.md).
* **Revisão** - quando a sua API está tudo pronto e começa a ser utilizado pelos programadores, normalmente, precisa ter cuidado ao fazer alterações a essa API e ao mesmo tempo para não interromper os autores de chamadas da API. É também útil dar a conhecer aos programadores as alterações que efetuou. Para obter mais informações, consulte [utilizar revisões](api-management-get-started-revise-api.md).
* **Portal do programador** -os seus clientes (desenvolvedores) devem utilizar o portal do programador para aceder às suas APIs. O portal do programador pode ser personalizado. Para obter mais informações, consulte [personalizar o portal do programador](api-management-customize-styles.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma instância](get-started-create-service-instance.md)

