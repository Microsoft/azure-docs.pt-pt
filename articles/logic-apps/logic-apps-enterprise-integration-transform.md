---
title: Transformar XML entre formatos
description: Criar transformações ou mapas que convertam XML entre formatos em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654137"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Criar mapas que transformam XML entre formatos no Azure Logic Apps com o Enterprise Integration Pack

O conector de Transformação do Enterprise Integration converte dados de um formato para outro. Por exemplo, pode receber uma mensagem com data atual no formato AnoMêsDia. Pode utilizar uma transformação para reformatar a data para estar no formato MêsDiaAno.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
A Transform, que também é conhecida como um mapa, consiste num esquema Source XML (a entrada) e num esquema Target XML (a saída). Pode utilizar diferentes funções incorporadas para ajudar a manipular ou controlar os dados, incluindo manipulações de cordas, atribuições condicionais, expressões aritméticas, formadores de tempo de data e até construções em loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Pode criar uma transformação/mapa utilizando o Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). Quando terminar de criar e testar a transformação, faça o upload da transformação na sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de fazer o upload da transformação/mapa para a sua conta de integração, pode usá-lo para criar uma aplicação Logic. A aplicação Logic executa as suas transformações sempre que a aplicação Logic é ativada (e há conteúdo de entrada que precisa de ser transformado).

**Aqui estão os passos para usar uma transformação:**

### <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de integração e adicione-lhe um mapa  

Agora que já tratou dos pré-requisitos, está na hora de criar a sua aplicação Logic:  

1. Crie uma aplicação Lógica e [ligue-a à sua conta de integração](./logic-apps-enterprise-integration-create-integration-account.md "Aprenda a ligar uma conta de integração a uma aplicação Logic") que contenha o mapa.
2. Adicione um gatilho **de pedido** à sua aplicação Logic  
   ![Screenshot do dropdown "Show Microsoft managed APIs" com o gatilho Request selecionado. O dropdown está numa aplicação Lógica criada usando o Visual Studio Enterprise Integration SDK.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicione a ação **Transform XML** selecionando pela primeira vez **Adicione uma ação**   
   ![Screenshot mostrando o botão "Adicionar uma ação" selecionado no ecrã de gatilho 'Pedido'.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Introduza a palavra *transformar* na caixa de pesquisa para filtrar todas as ações para a que pretende usar  
   ![Screenshot mostrando como procurar a ação Transform XML no dropdown "Show Microsoft managed APIs" para que possa ser adicionado ao gatilho Request.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação **Transform XML**   
6. Adicione o **conteúdo** XML que transforma. Pode utilizar todos os dados XML que receber no pedido HTTP como **conteúdo.** Neste exemplo, selecione o corpo do pedido HTTP que desencadeou a aplicação Logic.

   > [!NOTE]
   > Certifique-se de que o conteúdo do **Transform XML** é XML. Se o conteúdo não estiver em XML ou estiver codificado com base64, deve especificar uma expressão que processe o conteúdo. Por exemplo, pode utilizar [funções](logic-apps-workflow-definition-language.md#functions), como para ```@base64ToBinary``` descodição de conteúdos ou ```@xml``` para o processamento do conteúdo como XML.
 

7. Selecione o nome do **MAP** que pretende utilizar para realizar a transformação. O mapa já deve estar na sua conta de integração. Num passo anterior, já deu acesso à sua app Logic à sua conta de integração que contém o seu mapa.      
   ![Screenshot mostrando os campos de Conteúdo e Mapa no ecrã Transform XML para o gatilho Request.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Guardar o trabalho  
    ![Screenshot mostrando o botão Guardar no Design de Aplicações Lógicas.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste momento, já terminou de preparar o seu mapa. Numa aplicação real, poderá querer armazenar os dados transformados numa aplicação LOB como a SalesForce. Pode facilmente como uma ação para enviar a saída da transformação para a Salesforce. 

Agora pode testar a sua transformação fazendo um pedido ao ponto final HTTP.  


## <a name="features-and-use-cases"></a>Características e casos de utilização
* A transformação criada num mapa pode ser simples, como copiar um nome e um endereço de um documento para outro. Ou, pode criar transformações mais complexas usando as operações de mapa fora da caixa.  
* Várias operações ou funções do mapa estão prontamente disponíveis, incluindo cordas, funções de hora de data, e assim por diante.  
* Pode fazer uma cópia de dados direta entre os esquemas. No Mapper incluído no SDK, isto é tão simples como desenhar uma linha que liga os elementos no esquema de origem com os seus homólogos no esquema de destino.  
* Ao criar um mapa, você vê uma representação gráfica do mapa, que mostra todas as relações e ligações que cria.
* Utilize a função de Mapa de Teste para adicionar uma amostra de mensagem XML. Com um simples clique, pode testar o mapa que criou e ver a saída gerada.  
* Upload mapas existentes  
* Inclui suporte para o formato XML.

## <a name="advanced-features"></a>Funcionalidades avançadas

### <a name="reference-assembly-or-custom-code-from-maps"></a>Conjunto de referência ou código personalizado a partir de mapas 
A ação de transformação também suporta mapas ou transforma-se com referência à montagem externa. Esta capacidade permite chamadas para código .NET personalizado diretamente a partir de mapas XSLT. Aqui estão os pré-requisitos para usar a montagem em mapas.

* O mapa e a montagem referenciados a partir do mapa precisam de ser [carregados para conta de integração.](./logic-apps-enterprise-integration-maps.md) 

  > [!NOTE]
  > O mapa e a montagem são necessários para serem carregados numa ordem específica. Tem de fazer o upload da montagem antes de fazer o upload do mapa que faz referência à montagem.

* O mapa também deve ter estes atributos e uma secção CDATA que contenha a chamada para o código de montagem:

    * **nome** é o nome de montagem personalizado.
    * **namespace** é o espaço de nome no seu conjunto que inclui o código personalizado.

  Este exemplo mostra um mapa que faz referência a um conjunto chamado "XslUtilitiesLib" e chama o `circumreference` método da montagem.

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


### <a name="byte-order-mark"></a>Marca de Ordem Byte
Por defeito, a resposta da transformação começa com a Marca byte Order (BOM). Só pode aceder a esta funcionalidade enquanto estiver a trabalhar no editor Code View. Para desativar esta funcionalidade, especifique `disableByteOrderMark` para a `transformOptions` propriedade:

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
* [Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Conheça os mapas de integração empresarial")  