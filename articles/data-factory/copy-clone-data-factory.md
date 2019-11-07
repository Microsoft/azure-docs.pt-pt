---
title: Copiar ou clonar uma data factory no Azure Data Factory
description: Saiba como copiar ou clonar um data factory no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: bafe70655f05b5dda32f51393591f82b4b5625f1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678118"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar uma data factory no Azure Data Factory

Este artigo descreve como copiar ou clonar um data factory no Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casos de uso para clonar um data factory

Aqui estão algumas das circunstâncias em que você pode achar útil copiar ou clonar um data factory:

-   **Renomeando recursos**. O Azure não dá suporte à renomeação de recursos. Se você quiser renomear um data factory, poderá clonar o data factory com um nome diferente e, em seguida, excluir o existente.

-   **Depuração de alterações** quando os recursos de depuração não são suficientes. Às vezes, para testar suas alterações, talvez você queira testar suas alterações em uma fábrica diferente antes de aplicá-las ao seu principal. Na maioria dos cenários, você pode usar debug. No entanto, as alterações nos gatilhos, como a forma como as alterações se comportam quando um gatilho é invocado automaticamente, ou ao longo de uma janela de tempo, podem não ser facilmente realizadas sem fazer check-in. Nesses casos, clonar a fábrica e aplicar suas alterações faz muito sentido. Como Azure Data Factory encargos principalmente pelo número de execuções, a segunda fábrica não leva a cobranças adicionais.

## <a name="how-to-clone-a-data-factory"></a>Como clonar um data factory

1. A interface do usuário do Data Factory no portal do Azure permite exportar todo o conteúdo do seu data factory para um modelo do Resource Manager, juntamente com um arquivo de parâmetro que permite alterar os valores que você deseja alterar ao clonar sua fábrica.

1. Como pré-requisito, você precisa criar o data factory de destino do portal do Azure.

1. Se você tiver um SelfHosted IntegrationRuntime na fábrica de origem, você precisará precriá-lo com o mesmo nome na fábrica de destino. Se você quiser compartilhar o IRs do SelfHosted entre fábricas diferentes, poderá usar o padrão publicado [aqui](source-control.md#best-practices-for-git-integration).

1. Se você estiver no modo GIT, sempre que publicar no portal, o modelo do Resource Manager da fábrica será salvo no GIT na ramificação adf_publish do repositório.

1. Para outros cenários, o modelo do Resource Manager pode ser baixado clicando no botão **Exportar modelo do Resource Manager** no Portal.

1. Depois de baixar o modelo do Resource Manager, você pode implantá-lo por meio de métodos padrão de implantação de modelo do Resource Manager.

1. Por motivos de segurança, o modelo do Resource Manager gerado não contém nenhuma informação secreta, como senhas para serviços vinculados. Como resultado, você precisa fornecer essas senhas como parâmetros de implantação. Se fornecer parâmetros não for desejável, você precisará obter as cadeias de conexão e as senhas dos serviços vinculados de Azure Key Vault.

## <a name="next-steps"></a>Passos seguintes

Examine as diretrizes para criar um data factory no portal do Azure em [criar um data Factory usando a interface do usuário do Azure data Factory](quickstart-create-data-factory-portal.md).
