---
title: Transformar XML entre formatos - Azure Logic Apps | Documentos da Microsoft
description: Criar transformações ou mapas XML de converter entre formatos no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427291"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Criar mapas que transformam XML entre formatos no Azure Logic Apps com o Enterprise Integration Pack

O conector de transformação de integração do Enterprise converte dados de um formato para outro formato. Por exemplo, pode ter uma mensagem de entrada que contém a data atual no formato YearMonthDay. Pode usar uma transformação para reformatar a data de estar no formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
Uma transformação, que é também conhecido como um mapa, consiste num esquema de XML de origem (entrada) e um esquema de XML de destino (a saída). Pode utilizar diferentes funções incorporadas para ajudar a manipular ou controlar os dados, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, dos formatadores de tempo de data e até mesmo construções de loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Pode criar um mapa/transformação com o Visual Studio [SDK de integração do Enterprise](https://aka.ms/vsmapsandschemas). Quando tiver terminado de criar e testar a transformação, vai carregar a transformação para a sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de carregar o mapa/transformação para a sua conta de integração, pode usá-lo para criar uma aplicação lógica. A aplicação lógica executa as transformações, sempre que a aplicação lógica é acionada (e não existe conteúdo de entrada que precisa ser transformado).

**Aqui estão as etapas para usar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de integração e adicionar-lhe um mapa  

Agora que já tratámos dos pré-requisitos, é hora de criar a sua aplicação lógica:  

1. Criar uma aplicação lógica e [ligá-lo à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md "aprender a ligar uma conta de integração para uma aplicação lógica") que contém o mapa.
2. Adicionar uma **pedir** acionador à sua aplicação lógica  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicionar a **transformar XML** ação selecionando primeiro **adicionar uma ação**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Introduza a palavra *transformar* na caixa de pesquisa para filtrar todas as ações para aquele que pretende utilizar  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione o **transformar XML** ação   
6. Adicionar o XML **conteúdo** que transformar. Pode utilizar quaisquer dados XML que recebe na solicitação HTTP como o **conteúdo**. Neste exemplo, selecione o corpo da solicitação HTTP que disparou a aplicação lógica.

   > [!NOTE]
   > Certifique-se de que o conteúdo para o **transformar XML** é XML. Se o conteúdo não está em XML ou é codificada em base64, tem de especificar uma expressão que processa o conteúdo. Por exemplo, pode usar [funções](logic-apps-workflow-definition-language.md#functions), como ```@base64ToBinary``` à decodificação de conteúdo ou ```@xml``` para processar o conteúdo como XML.
 

7. Selecione o nome da **mapa** que deseja usar para executar a transformação. O mapa já deve estar na sua conta de integração. Num passo anterior, já deu o acesso de aplicação lógica à sua conta de integração que contém seu mapa.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Guarde o seu trabalho  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste ponto, tiver concluído a configuração de seu mapa. Num aplicativo do mundo real, pode querer armazenar os dados transformados num aplicativo de LOB, como o SalesForce. Pode facilmente como uma ação para enviar a saída da transformação para Salesforce. 

Agora pode testar a sua transformação fazendo uma solicitação para o ponto final HTTP.  


## <a name="features-and-use-cases"></a>Recursos e casos de utilização
* A transformação criada num mapa pode ser simples, como copiar um nome e endereço de um documento para outro. Em alternativa, pode criar transformações mais complexas, usando as operações de mapa de out-of-the-box.  
* Várias operações de mapa ou as funções estão prontamente disponíveis, incluindo cadeias de caracteres, funções de tempo de data e assim por diante.  
* Pode fazer uma cópia de dados direta entre os esquemas. O mapeador de pontos incluída no SDK, isso é tão simples como desenhar a linha que conecta os elementos no esquema de origem às suas contrapartes no esquema de destino.  
* Ao criar um mapa, exibir uma representação gráfica do mapa, que mostra todas as relações e ligações que cria.
* Utilize a funcionalidade de mapa de teste para adicionar uma mensagem XML de exemplo. Com um simples clique, pode testar o mapa que criou e ver o resultado gerado.  
* Carregar maps existentes  
* Inclui suporte para o formato XML.

## <a name="advanced-features"></a>Funcionalidades avançadas

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referência assembly ou um código personalizado de mapas 
A ação de transformação também oferece suporte a maps ou transforma com referência ao assembly externo. Esta capacidade permite chamadas para o código do .NET personalizado diretamente a partir de mapas XSLT. Seguem-se os pré-requisitos para utilizar o assembly no maps.

* O mapa e o assembly referenciado a partir das necessidades de mapa seja [carregou para a conta de integração](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapa e o assembly são necessários para ser carregado por uma ordem específica. Tem de carregar o assembly antes de carregar o mapa que faça referência ao assembly.

* O map também tem de ter estes atributos e uma seção CDATA que contém a chamada para o código de assembly:

    * **nome** é o nome de assembly personalizado.
    * **espaço de nomes** é o espaço de nomes em seu assembly que inclui o código personalizado.

  Este exemplo mostra um mapa que faz referência a um assembly denominado "XslUtilitiesLib" e chama o `circumreference` método do assembly.

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
Por predefinição, a resposta da transformação inicia com marca de ordem de Byte (BOM). Pode aceder a esta funcionalidade apenas enquanto trabalha no editor de vista de código. Para desativar esta funcionalidade, especifique `disableByteOrderMark` para o `transformOptions` propriedade:

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





## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre o maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre o maps de integração do enterprise")  

