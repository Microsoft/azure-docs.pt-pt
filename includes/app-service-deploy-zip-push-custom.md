---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184062"
---
## <a name="deployment-customization"></a>Personalização de implementação

O processo de implementação pressupõe que o ficheiro .zip que empurra contém uma aplicação pronta a ser executada. Por padrão, não são executadas personalizações. Para permitir os mesmos processos de construção que obtém com integração contínua, adicione o seguinte às definições da sua aplicação:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Quando utilizar a implementação do impulso .zip, esta definição é **falsa** por defeito. O padrão é **verdadeiro** para implementações contínuas de integração. Quando **definido como verdadeiro,** as definições relacionadas com a implementação são utilizadas durante a implementação. Pode configurar estas definições quer como definições de aplicação quer num ficheiro de configuração de .implementação que esteja localizado na raiz do seu ficheiro .zip. Para mais informações, consulte [as definições de Repositório e de implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) na referência de implantação.