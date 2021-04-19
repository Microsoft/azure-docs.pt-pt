---
title: Problemas de conector e formato de resolução de problemas nos fluxos de dados de mapeamento
description: Saiba como resolver problemas de fluxo de dados relacionados com o conector e o formato na Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 7596bf3182f59758eeee66e248404e98c18da2e7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715545"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Problemas de conector e formato de resolução de problemas nos fluxos de dados de mapeamento na Azure Data Factory


Este artigo explora métodos de resolução de problemas relacionados com o conector e formato para mapear fluxos de dados na Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Suporte esquemas personalizados na fonte

#### <a name="symptoms"></a>Sintomas
Quando pretender utilizar o fluxo de dados ADF para mover ou transferir dados da Cosmos DB/JSON para outras lojas de dados, algumas colunas dos dados de origem podem ser perdidas. 

#### <a name="cause"></a>Causa 
Para os conectores livres de esquema (o número da coluna, o nome da coluna e o tipo de dados de coluna de cada linha podem ser diferentes quando comparados com outros), por padrão, a ADF utiliza linhas de amostra (por exemplo, dados de 100 ou 1000 linhas) para inferir o esquema, e o resultado inferido será usado como um esquema para ler dados. Portanto, se as suas lojas de dados tiverem colunas extras que não aparecem em linhas de amostra, os dados destas colunas extra não são lidos, transferidos ou transferidos para lojas de dados de sumidouros.

#### <a name="recommendation"></a>Recomendação
Para substituir o comportamento predefinido e trazer campos adicionais, a ADF fornece opções para personalizar o esquema de origem. Pode especificar colunas adicionais/em falta que possam faltar no resultado do código-inferir na projeção da fonte de fluxo de dados para ler os dados, podendo aplicar uma das seguintes opções para definir o esquema personalizado. Normalmente, **a Opção 1** é mais preferida.

- **Opção 1**: Em comparação com os dados originais de origem que podem ser um grande ficheiro, tabela ou recipiente que contém milhões de linhas com esquemas complexos, pode criar uma mesa/recipiente temporário com algumas linhas que contenham todas as colunas que pretende ler e, em seguida, passar para a seguinte operação: 

    1. Utilize a fonte de fluxo **de dados Definições de Debug** para ter **projeção de importação** com ficheiros/tabelas de amostras para obter o esquema completo. Pode seguir os passos na seguinte imagem:<br/>

        ![Screenshot que mostra a opção-1-1 da Recomendação](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Selecione **definições de Debug** na tela de fluxo de dados.
         1. No painel pop-up, selecione **a tabela Sample** sob o **separador cosmosSource.** 
         1. Insira o nome da sua mesa no bloco **de mesa.**
         1. **Selecione Guardar** para guardar as suas definições.
         1. **Selecione Projeção de importação**.<br/>  
    
    1. Altere as **Definições de Depurg** de volta para utilizar o conjunto de dados de origem para o movimento/transformação de dados restantes. Pode seguir em frente com os passos na seguinte imagem:<br/>

        ![Screenshot que mostra a opção-1-2 da Recomendação](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Selecione **definições de Debug** na tela de fluxo de dados.
         1. No painel pop-up, selecione Conjunto de **dados de origem** no **separador cosmosSource.**
         1. **Selecione Guardar** para guardar as suas definições.<br/>
    
    Posteriormente, o fluxo de dados ADF irá honrar e utilizar o esquema personalizado para ler dados da loja de dados original. <br/>

- **Opção 2**: Se estiver familiarizado com o esquema e o idioma DSL dos dados de origem, pode atualizar manualmente o script de fonte de fluxo de dados para adicionar colunas adicionais/perdidas para ler os dados. Um exemplo é mostrado na seguinte imagem: 

    ![Screenshot que mostra a Opção-2 da Recomendação](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais ajuda na resolução de problemas, consulte estes recursos:

*  [Fluxos de dados de mapeamento de resolução de problemas na Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)