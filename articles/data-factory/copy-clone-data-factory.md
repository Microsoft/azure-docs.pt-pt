---
title: Copiar ou clonar uma fábrica de dados na Azure Data Factory
description: Saiba copiar ou clonar uma fábrica de dados na Azure Data Factory
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
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414098"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar uma fábrica de dados na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como copiar ou clonar uma fábrica de dados na Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Utilize casos para clonagem de uma fábrica de dados

Estas são algumas das circunstâncias em que pode achar útil copiar ou clonar uma fábrica de dados:

-   **Renomear recursos.** O Azure não apoia a renomeação de recursos. Se quiser mudar o nome de uma fábrica de dados, pode clonar a fábrica de dados com um nome diferente e, em seguida, apagar a existente.

-   **A depuração muda** quando as características de depuração não são suficientes. Por vezes, para testar as suas alterações, é melhor testar as suas alterações numa fábrica diferente antes de as aplicar na sua principal. Na maioria dos cenários, podes usar o Debug. No entanto, as alterações nos gatilhos, tais como a forma como as suas alterações se comportam quando um gatilho é invocado automaticamente, ou ao longo de uma janela de tempo, podem não ser facilmente testável sem fazer o check-in. Nestes casos, clonar a fábrica e aplicar as suas mudanças faz muito sentido. Uma vez que a Azure Data Factory cobra principalmente pelo número de execuções, a segunda fábrica não leva a quaisquer encargos adicionais.

## <a name="how-to-clone-a-data-factory"></a>Como clonar uma fábrica de dados

1. A Data Factory UI no portal Azure permite-lhe exportar toda a carga útil da sua fábrica de dados para um modelo de Gestor de Recursos, juntamente com um ficheiro de parâmetroque permite alterar quaisquer valores que queira alterar quando clona a sua fábrica.

1. Como pré-requisito, é necessário criar a sua fábrica de dados-alvo a partir do portal Azure.

1. Se tiver um Tempo de Integração Auto-Anfitriãna na sua fábrica de origem, tem de o pré-criar com o mesmo nome na fábrica-alvo. Se quiser partilhar os IRs auto-anfitriões entre diferentes fábricas, pode utilizar o padrão publicado [aqui](source-control.md#best-practices-for-git-integration).

1. Se estiver no modo GIT, sempre que publicar a partir do portal, o modelo de Gestor de Recursos da fábrica é guardado no GIT no ramo adf_publish do repositório.

1. Para outros cenários, o modelo de Gestor de Recursos pode ser descarregado clicando no botão de modelo do Gestor de **Recursos de Exportação** no portal.

1. Depois de descarregar o modelo de Gestor de Recursos, pode implementá-lo através de métodos padrão de implementação do modelo de Gestor de Recursos.

1. Por razões de segurança, o modelo gerado do Gestor de Recursos não contém qualquer informação secreta, como palavras-passe para serviços ligados. Como resultado, tem de fornecer estas palavras-passe como parâmetros de implantação. Se o fornecimento de parâmetros não for desejável, tem de obter as cordas de ligação e as palavras-passe dos serviços ligados a partir do Cofre chave Azure.

## <a name="next-steps"></a>Passos seguintes

Reveja as orientações para a criação de uma fábrica de dados no portal Azure em Criar uma fábrica de [dados utilizando a Azure Data Factory UI](quickstart-create-data-factory-portal.md).
