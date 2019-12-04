---
title: Transformar XML entre formatos
description: Criar transformações ou mapas que convertem XML entre formatos em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792392"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Crie mapas que transformam XML entre formatos em aplicativos lógicos do Azure com Enterprise Integration Pack

O conector de transformação de integração corporativa converte dados de um formato para outro formato. Por exemplo, você pode ter uma mensagem de entrada que contém a data atual no formato AnoMêsDia. Você pode usar uma transformação para reformatar a data para que ela fique no formato Mêsdiaano.

## <a name="what-does-a-transform-do"></a>O que uma transformação faz?
Uma transformação, que também é conhecida como um mapa, consiste em um esquema XML de origem (a entrada) e um esquema XML de destino (a saída). Você pode usar funções internas diferentes para ajudar a manipular ou controlar os dados, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores de data e hora e até mesmo construções em loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Você pode criar uma transformação/mapa usando o [SDK de Enterprise Integration](https://aka.ms/vsmapsandschemas)do Visual Studio. Quando terminar de criar e testar a transformação, você carregará a transformação em sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de carregar a transformação/mapa em sua conta de integração, você pode usá-la para criar um aplicativo lógico. O aplicativo lógico executa suas transformações sempre que o aplicativo lógico é disparado (e há um conteúdo de entrada que precisa ser transformado).

**Estas são as etapas para usar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de integração e adicionar um mapa a ela  

Agora que você já se encarrega dos pré-requisitos, é hora de criar seu aplicativo lógico:  

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um aplicativo lógico") que contém o mapa.
2. Adicionar um gatilho de **solicitação** ao seu aplicativo lógico  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicione a ação **transformar XML** selecionando primeiro **adicionar uma ação**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Insira a palavra *transformação* na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação **transformar XML**   
6. Adicione o **conteúdo** XML que você transformar. Você pode usar quaisquer dados XML recebidos na solicitação HTTP como o **conteúdo**. Neste exemplo, selecione o corpo da solicitação HTTP que disparou o aplicativo lógico.

   > [!NOTE]
   > Verifique se o conteúdo do XML de **transformação** é XML. Se o conteúdo não estiver em XML ou for codificado na base64, você deverá especificar uma expressão que processe o conteúdo. Por exemplo, você pode usar [funções](logic-apps-workflow-definition-language.md#functions), como ```@base64ToBinary``` para decodificar conteúdo ou ```@xml``` para processar o conteúdo como XML.
 

7. Selecione o nome do **mapa** que você deseja usar para executar a transformação. O mapa já deve estar em sua conta de integração. Em uma etapa anterior, você já deu ao seu aplicativo lógico acesso à sua conta de integração que contém o mapa.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Salve seu trabalho  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste ponto, você concluiu a configuração do mapa. Em um aplicativo do mundo real, talvez você queira armazenar os dados transformados em um aplicativo LOB, como o SalesForce. Você pode facilmente como uma ação para enviar a saída da transformação para o Salesforce. 

Agora você pode testar sua transformação fazendo uma solicitação para o ponto de extremidade HTTP.  


## <a name="features-and-use-cases"></a>Recursos e casos de uso
* A transformação criada em um mapa pode ser simples, como copiar um nome e um endereço de um documento para outro. Ou, você pode criar transformações mais complexas usando as operações de mapeamento prontas para uso.  
* Várias operações ou funções de mapeamento estão prontamente disponíveis, incluindo cadeias de caracteres, funções de data e hora e assim por diante.  
* Você pode fazer uma cópia direta de dados entre os esquemas. No mapeador incluído no SDK, isso é tão simples quanto desenhar uma linha que conecta os elementos no esquema de origem com suas contrapartes no esquema de destino.  
* Ao criar um mapa, você exibe uma representação gráfica do mapa, que mostra todas as relações e os links que você cria.
* Use o recurso testar mapa para adicionar uma mensagem XML de exemplo. Com um simples clique, você pode testar o mapa criado e ver a saída gerada.  
* Carregar mapas existentes  
* Inclui suporte para o formato XML.

## <a name="advanced-features"></a>Funcionalidades avançadas

### <a name="reference-assembly-or-custom-code-from-maps"></a>Assembly de referência ou código personalizado de mapas 
A ação de transformação também dá suporte a mapas ou transformações com referência ao assembly externo. Essa funcionalidade habilita chamadas para código .NET personalizado diretamente de mapas XSLT. Aqui estão os pré-requisitos para usar o assembly no Maps.

* O mapa e o assembly referenciado do mapa precisam ser [carregados na conta de integração](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > O mapa e o assembly devem ser carregados em uma ordem específica. Você deve carregar o assembly antes de carregar o mapa que faz referência ao assembly.

* O mapa também deve ter esses atributos e uma seção CDATA que contenha a chamada para o código do assembly:

    * **nome** é o nome do assembly personalizado.
    * **namespace** é o namespace em seu assembly que inclui o código personalizado.

  Este exemplo mostra um mapa que faz referência a um assembly chamado "XslUtilitiesLib" e chama o método `circumreference` do assembly.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Marca de ordem de byte
Por padrão, a resposta da transformação começa com a marca de ordem de byte (BOM). Você pode acessar essa funcionalidade somente enquanto trabalha no editor de exibição de código. Para desabilitar essa funcionalidade, especifique `disableByteOrderMark` para a propriedade `transformOptions`:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Saber mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas de integração corporativa")  

