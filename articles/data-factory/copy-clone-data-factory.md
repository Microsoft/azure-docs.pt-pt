---
title: Copiar ou clonar uma fábrica de dados na Azure Data Factory
description: Saiba como copiar ou clonar uma fábrica de dados na Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85552965"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar uma fábrica de dados na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como copiar ou clonar uma fábrica de dados na Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Utilizar casos para clonagem de uma fábrica de dados

Estas são algumas das circunstâncias em que poderá achar útil copiar ou clonar uma fábrica de dados:

- **Mover a Fábrica de Dados** para uma nova região. Se quiser mover a sua Fábrica de Dados para uma região diferente, a melhor maneira é criar uma cópia na região visada e eliminar a existente.

- **Renomear a Fábrica de Dados.** O Azure não apoia a renomeação de recursos. Se quiser mudar o nome de uma fábrica de dados, pode clonar a fábrica de dados com um nome diferente e eliminar a existente.

- **Depurar muda** quando as características do depurg não são suficientes. Na maioria dos cenários, pode utilizar [o Debug.](iterative-development-debugging.md) Noutros, testar as mudanças num ambiente clonado de caixas de areia faz mais sentido. Por exemplo, como os seus oleodutos ETL parametrizados se comportariam quando um gatilho dispara após a chegada do ficheiro versus sobre a janela do tempo de Tumbling, pode não ser facilmente testado apenas através de Debug. Nestes casos, pode querer clonar um ambiente de caixa de areia para experimentar. Uma vez que a Azure Data Factory cobra principalmente pelo número de corridas, uma segunda fábrica não leva a quaisquer encargos adicionais.

## <a name="how-to-clone-a-data-factory"></a>Como clonar uma fábrica de dados

1. Como pré-requisito, primeiro precisa de criar a sua fábrica de dados-alvo a partir do portal Azure.

1. Se estiver no modo GIT:
    1. Sempre que publica a partir do portal, o modelo de Gestor de Recursos da fábrica é guardado em GIT na sucursal de publicação da ADF \_
    1. Ligue a nova fábrica ao _mesmo_ repositório e construa a partir da filial de publicação da \_ ADF. Recursos, tais como oleodutos, conjuntos de dados e gatilhos, serão transportados

1. Se estiver em modo Live:
    1. A UI da Data Factory permite-lhe exportar toda a carga útil da sua fábrica de dados para um ficheiro de modelo do Gestor de Recursos e um ficheiro de parâmetros. Podem ser acedidos a partir do modelo ARM \ Botão de **modelo do Gestor de Recursos de Exportação** no portal.
    1. Pode escamar as alterações adequadas no ficheiro de parâmetros e trocar em novos valores para a nova fábrica
    1. Em seguida, pode implantá-lo através de métodos padrão de implementação do modelo do Gestor de Recursos.

1. Se tiver um SelfHosted IntegrationRuntime na sua fábrica de origem, tem de precriá-lo com o mesmo nome na fábrica alvo. Se quiser partilhar o Tempo de Execução de Integração SelfHosted entre diferentes fábricas, pode usar o padrão publicado [aqui](create-shared-self-hosted-integration-runtime-powershell.md) na partilha do SelfHosted IR.

1. Por razões de segurança, o modelo gerado do Gestor de Recursos não contém nenhuma informação secreta, por exemplo, palavras-passe para serviços ligados. Por isso, é necessário fornecer as credenciais como parâmetros de implantação. Se a credencial de inserção manual não for desejável para as suas definições, considere recuperar as cordas de ligação e as palavras-passe do Azure Key Vault. [Ver mais](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Passos seguintes

Reveja as orientações para a criação de uma fábrica de dados no portal Azure na [Criação de uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md).
