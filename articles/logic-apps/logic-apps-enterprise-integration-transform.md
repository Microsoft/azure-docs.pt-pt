---
title: Transforme o XML entre formatos
description: Crie transformações ou mapas que convertam XML entre formatos em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792392"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Crie mapas que transformem xML entre formatos em Aplicações Lógicas Azure com Pacote de Integração Empresarial

A integração enterprise Transform converte dados de um formato para outro formato. Por exemplo, pode ter uma mensagem de entrada que contenha a data atual no formato YearMonthDay. Você pode usar uma transformação para reformar na data para estar no formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
A Transform, que também é conhecida como um mapa, consiste num esquema Source XML (a entrada) e num esquema Target XML (a saída). Pode utilizar diferentes funções incorporadas para ajudar a manipular ou controlar os dados, incluindo manipulações de cordas, atribuições condicionais, expressões aritméticas, tempo de data para assuntos e até mesmo construções em loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Pode criar uma transformação/mapa utilizando o Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). Quando terminar de criar e testar a transformação, faça o upload da transformação na sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de fazer o upload da transformação/mapa na sua conta de integração, pode usá-la para criar uma aplicação Logic. A aplicação Logic executa as suas transformações sempre que a aplicação Logic é desencadeada (e há conteúdo de entrada que precisa de ser transformado).

**Aqui estão os passos para usar uma transformação:**

### <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de integração e adicionar-lhe um mapa  

Agora que já tratou dos pré-requisitos, é hora de criar a sua aplicação Logic:  

1. Crie uma aplicação Lógica e [ligue-a à sua conta](../logic-apps/logic-apps-enterprise-integration-accounts.md "Aprenda a ligar uma conta de integração a uma aplicação Lógica") de integração que contenha o mapa.
2. Adicione um gatilho **de Pedido** à sua aplicação Lógica  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicione a ação **Transform XML** selecionando primeiro **Adicionar uma ação**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Introduza a palavra *transforme* na caixa de pesquisa para filtrar todas as ações para aquela que pretende usar  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação **Transform XML**   
6. Adicione o **conteúdo** XML que transforma. Pode utilizar todos os dados XML que receber no pedido http como **CONTEÚDO**. Neste exemplo, selecione o corpo do pedido HTTP que desencadeou a aplicação Lógica.

   > [!NOTE]
   > Certifique-se de que o conteúdo do Transform XML é **XML.** Se o conteúdo não estiver no XML ou estiver codificado com base64, deve especificar uma expressão que processa o conteúdo. Por exemplo, pode utilizar [funções,](logic-apps-workflow-definition-language.md#functions)como ```@base64ToBinary``` ```@xml``` para descodificar conteúdos ou para processar o conteúdo como XML.
 

7. Selecione o nome do **MAP** que pretende utilizar para realizar a transformação. O mapa já deve estar na sua conta de integração. Num passo anterior, já deu à sua app Logic acesso à sua conta de integração que contém o seu mapa.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Guardar o trabalho  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste momento, está sem preparar o mapa. Numa aplicação no mundo real, pode querer armazenar os dados transformados numa aplicação LOB como salesForce. Pode facilmente como ação para enviar a saída da transformação para a Salesforce. 

Agora pode testar a sua transformação fazendo um pedido para o ponto final http.  


## <a name="features-and-use-cases"></a>Características e casos de utilização
* A transformação criada num mapa pode ser simples, como copiar um nome e endereço de um documento para outro. Ou, pode criar transformações mais complexas usando as operações de mapa fora da caixa.  
* Várias operações ou funções do mapa estão prontamente disponíveis, incluindo cordas, funções de data, e assim por diante.  
* Pode fazer uma cópia de dados diretos entre os esquemas. No Mapper incluído no SDK, isto é tão simples como desenhar uma linha que liga os elementos no esquema de origem com os seus homólogos no esquema de destino.  
* Ao criar um mapa, vê-se uma representação gráfica do mapa, que mostra todas as relações e links que cria.
* Utilize a função Test Map para adicionar uma mensagem XML de amostra. Com um simples clique, pode testar o mapa que criou e ver a saída gerada.  
* Carregar mapas existentes  
* Inclui suporte para o formato XML.

## <a name="advanced-features"></a>Funcionalidades avançadas

### <a name="reference-assembly-or-custom-code-from-maps"></a>Montagem de referência ou código personalizado a partir de mapas 
A ação de transformação também suporta mapas ou transforma-se com referência a montagem externa. Esta capacidade permite que as chamadas personalizem o código .NET diretamente dos mapas XSLT. Aqui estão os pré-requisitos para usar o conjunto em mapas.

* O mapa e a montagem referenciada a partir do mapa precisam de ser enviados para a conta de [integração.](./logic-apps-enterprise-integration-maps.md) 

  > [!NOTE]
  > O mapa e a montagem devem ser carregados numa ordem específica. Tem de fazer o upload da montagem antes de fazer o upload do mapa que faz referência à montagem.

* O mapa também deve ter estes atributos e uma secção CDATA que contenha a chamada para o código de montagem:

    * **nome** é o nome de montagem personalizado.
    * **nomeespaço** é o espaço de nome na sua montagem que inclui o código personalizado.

  Este exemplo mostra um mapa que faz referência a um conjunto `circumreference` chamado "XslUtilitiesLib" e chama o método da montagem.

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


### <a name="byte-order-mark"></a>Marca de ordem byte
Por padrão, a resposta da transformação começa com a Marca de Ordem Byte (BOM). Só pode aceder a esta funcionalidade enquanto trabalha no editor do Code View. Para desativar esta `disableByteOrderMark` funcionalidade, especifique para a `transformOptions` propriedade:

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
* [Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Conheça mapas de integração empresarial")  

