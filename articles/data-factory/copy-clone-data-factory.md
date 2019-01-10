---
title: Copiar ou clonar uma fábrica de dados no Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar ou clonar uma fábrica de dados no Azure Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: c62581447cd395bd48a787fa7dc89659d5172486
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192250"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar uma fábrica de dados no Azure Data Factory

Este artigo descreve como copiar ou clonar uma fábrica de dados no Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casos de utilização para a clonagem de uma fábrica de dados

Aqui estão algumas das circunstâncias em que talvez ache útil para copiar ou clonar uma fábrica de dados:

-   **Mudar o nome de recursos**. O Azure não suporta a mudança de nome de recursos. Se quiser mudar o nome de uma fábrica de dados, pode clonar a fábrica de dados com um nome diferente e, em seguida, elimine a já existente.

-   **Depuração alterações** quando os recursos de depuração não são suficientes. Por vezes, para testar as suas alterações, pode querer testar as alterações numa fábrica de diferente antes de aplicá-las à sua principal. Na maioria dos cenários, pode utilizar a depuração. As alterações em acionadores, no entanto, como o comportam das suas alterações quando um acionador é invocado automaticamente ou numa janela de tempo, pode não ser que podem ser testadas facilmente sem dar entrada. Nestes casos, a fábrica de clonagem e aplicar as alterações aqui faz muito sentido. Uma vez que os encargos do Azure Data Factory principalmente pelo número de execuções, a fábrica de segundo não resultar em despesas adicionais.

## <a name="how-to-clone-a-data-factory"></a>Como clonar uma fábrica de dados

1. IU do Data Factory no portal do Azure permite-lhe exportar a carga inteira fábrica de dados num modelo do Resource Manager, juntamente com um ficheiro de parâmetros que lhe permite alterar quaisquer valores que pretende alterar quando clona sua fábrica.

1. Como pré-requisito, tem de criar a fábrica de dados de destino do portal do Azure.

1. Se estiver no modo GIT, sempre que publicar a partir do portal, o modelo do Resource Manager de fábrica é guardado no GIT no ramo adf_publish do repositório.

1. Para outros cenários, o modelo do Resource Manager pode ser transferido, ao clicar no **modelo do Resource Manager/exportar** botão no portal.

1. Depois de transferir o modelo do Resource Manager, pode implementá-la por meio do método de implementação de modelo do Resource Manager padrão.

1. Por motivos de segurança, o modelo do Resource Manager gerado não contém quaisquer informações secretas, tais como palavras-passe para os serviços ligados. Como resultado, terá de fornecer estas palavras-passe como parâmetros de implementação. Se não for desejável fornecer parâmetros, terá de obter as cadeias de ligação e palavras-passe dos serviços ligados do Azure Key Vault.

## <a name="next-steps"></a>Passos Seguintes

Reveja as orientações para criar uma fábrica de dados no portal do Azure no [criar uma fábrica de dados utilizando a IU do Azure Data Factory](quickstart-create-data-factory-portal.md).
