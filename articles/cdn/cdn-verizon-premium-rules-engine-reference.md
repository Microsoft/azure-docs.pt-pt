---
title: Azure CDN regras referência do motor | Microsoft Docs
description: Documentação de referência para Azure CDN regras condições e funcionalidades de correspondência do motor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362585"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN da Verizon Premium regras referência do motor

Este artigo enumera descrições detalhadas das condições e funcionalidades de correspondência disponíveis para o motor de [regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

O motor de regras foi concebido para ser a autoridade final sobre a forma como os tipos específicos de pedidos são processados pela CDN.

**Usos comuns:**

- Sobrepor ou definir uma política de cache personalizada.
- Proteja ou negue pedidos de conteúdo sensível.
- Redirecione os pedidos.
- Armazenar dados de registo personalizados.
## <a name="key-concepts"></a>Conceitos-chave
Os conceitos-chave para a criação do Motor de Regras são descritos abaixo.
### <a name="draft"></a>Rascunho
Um rascunho de uma política consiste numa ou mais regras destinadas a identificar os pedidos e o conjunto de ações que lhes serão aplicadas. Um rascunho é um trabalho em andamento que permite atualizações frequentes de configuração sem afetar o tráfego do site. Uma vez que um projeto esteja pronto para ser finalizado, deve ser convertido numa política apenas de leitura.

### <a name="rule"></a>Regra
Uma regra identifica um ou mais tipos de pedidos e o conjunto de ações que lhes serão aplicadas.

Consiste em: 

- Um conjunto de expressões condicionais que definem a lógica através da qual os pedidos são identificados.
- Um conjunto de condições de correspondência que definem os critérios utilizados para identificar pedidos.
- Um conjunto de funcionalidades que definem como o CDN irá lidar com os pedidos acima.
Estes elementos são identificados na seguinte ilustração.

![Screenshot com etiquetas mostra a expressão condicional, o jogo e as características de uma regra.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Política
Uma política, que consiste num conjunto de regras apenas de leitura, fornece os meios para:

- Crie, armazene e gere várias variantes das suas regras.
- Volte para uma versão previamente implantada.
- Preparar previamente regras específicas para o evento (por exemplo, uma regra que redireciona o tráfego como resultado de uma manutenção de origem do cliente.)

> [!NOTE]
> Embora apenas seja permitida uma única política por ambiente, as políticas podem ser implementadas conforme necessário.

### <a name="deploy-request"></a>Pedido de implantação
Um pedido de implantação fornece um procedimento simples e simplificado através do qual uma política pode ser rapidamente aplicada ao ambiente de Encenação ou Produção. É fornecido um histórico de pedidos de implantação para facilitar o rastreio das alterações aplicadas a esses ambientes.

> [!NOTE]
> Apenas os pedidos que não passarem no nosso sistema automatizado de validação e deteção de erros exigirão revisão e aprovação manual.

### <a name="rule-precedence"></a>Precedência de regra
As regras contidas numa Política são normalmente processadas na ordem em que estão listadas (isto é, de cima para baixo). Se o pedido corresponder às regras contraditórias, então a última regra a ser processada terá precedência.

### <a name="policy-deployment-workflow"></a>Fluxo de trabalho de implementação de políticas
O fluxo de trabalho através do qual uma política pode ser aplicada quer ao ambiente de Produção quer à encenação, é ilustrado abaixo.

![Fluxo de trabalho de implementação de políticas](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Passo |Description |
|---------|---------|
|[Criar Rascunho](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Um rascunho consiste num conjunto de regras que definem como os pedidos para o seu conteúdo devem ser tratados pela CDN.     |
|Rascunho de bloqueio   |     Uma vez finalizado um projeto, este deve ser bloqueado e convertido numa política apenas de leitura.    |
|[Enviar Pedido de Implantação](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Um pedido de implantação permite aplicar uma política ao tráfego de teste ou de produção.</br> <br>Submeta um pedido de implantação para o ambiente de Encenação ou Produção.</br>     |
|Implementar revisão de pedidos   |    <br>Um pedido de implantação passa por validação automatizada e deteção de erros.</br><br>Embora a maioria dos pedidos de implementação sejam automaticamente aprovados, é necessária uma revisão manual para políticas mais complexas.</br>   |
|Implementação de políticas[(Encenação)](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging)   |  <br> Após a aprovação de um pedido de implantação para o ambiente de encenação, será aplicada uma política ao ambiente de encenação. Este ambiente permite que uma política seja testada contra o tráfego de sites falsos.</br><br>Uma vez que a política esteja pronta a ser aplicada ao tráfego em direto, deve ser apresentado um novo pedido de implantação para o ambiente de Produção.</br>      |
|Implementação de Políticas[(Produção)](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti)   |  Após a aprovação de um pedido de implantação para o ambiente de Produção, será aplicada uma política ao ambiente de Produção. Este ambiente permite que uma política atue como a autoridade final para determinar como a CDN deve lidar com o tráfego ao vivo.     |
## <a name="syntax"></a>Syntax

A forma como os caracteres especiais são tratados varia de acordo com a forma como uma condição de correspondência ou funcionalidade lida com os valores de texto. Uma condição ou característica de correspondência pode interpretar texto de uma das seguintes formas:

- [**Valores literais**](#literal-values)
- [**Valores wildcard**](#wildcard-values)
- [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

Texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como uma parte do valor que deve ser combinado. Por outras palavras, uma condição de correspondência literal definida `\'*'\` apenas é satisfeita quando esse valor exato (isto é, `\'*'\` ) é encontrado.

É utilizado um símbolo percentual para indicar a codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores wildcard

Texto que é interpretado como um valor wildcard atribui significado adicional a caracteres especiais. A tabela a seguir descreve como o seguinte conjunto de caracteres é interpretado:

Caráter | Descrição
----------|------------
\ | Uma pestana é usada para escapar a qualquer um dos caracteres especificados nesta tabela. Uma pestana deve ser especificada diretamente antes do carácter especial que deve ser escapado.<br/>Por exemplo, a seguinte sintaxe escapa a um asterisco: `\*`
% | É utilizado um símbolo percentual para indicar a codificação de URL (por exemplo, `%20` ).
\* | Um asterisco é um wildcard que representa um ou mais caracteres.
Espaço | Um caractere espacial indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
'valor' | Uma única citação não tem um significado especial. No entanto, um conjunto de cotações individuais é usado para indicar que um valor deve ser tratado como um valor literal. Pode ser utilizado das seguintes formas:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponda a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cordas: <br/><br/>/business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template. **ma** p<br /><br />- Permite que um carácter especial seja especificado como um carácter literal. Por exemplo, pode especificar um personagem de espaço literal, encerrando um personagem espacial dentro de um conjunto de citações individuais (isto é, `' '` ou `'sample value'` .<br/>- Permite especificar um valor em branco. Especifique um valor em branco especificando um conjunto de cotações individuais (isto é, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um wildcard, então é automaticamente considerado um valor literal, o que significa que não é necessário especificar um conjunto de cotações únicas.<br/>- Se uma lomassa não escapar a outro personagem desta tabela, é ignorada quando é especificada dentro de um conjunto de citações individuais.<br/>- Outra forma de especificar um personagem especial como personagem literal é escapar-lhe usando um backslash (isto é, `\` ).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é procurado dentro de um valor de texto. A notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela a seguir indica como os caracteres especiais são tratados por condições de correspondência e funcionalidades que suportam expressões regulares.

Personagem Especial | Description
------------------|------------
\ | Uma retrocesso escapa ao personagem que o segue, o que faz com que esse personagem seja tratado como um valor literal em vez de assumir o seu significado de expressão regular. Por exemplo, a seguinte sintaxe escapa a um asterisco: `\*`
% | O significado de um símbolo percentual depende da sua utilização.<br/><br/> `%{HTTPVariable}`: Esta sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: Esta sintaxe utiliza um símbolo percentual para identificar uma variável HTTP e como um delimiter.<br />`\%`: Escapar de um símbolo percentual permite-lhe ser utilizado como um valor literal ou para indicar codificação de URL (por exemplo, `\%20` ).
\* | Um asterisco permite que o personagem anterior seja igualado zero ou mais vezes.
Espaço | Um personagem do espaço é tipicamente tratado como um personagem literal.
'valor' | As citações individuais são tratadas como caracteres literais. Um conjunto de citações individuais não tem um significado especial.

Condições de correspondência e funcionalidades que suportam expressões regulares aceitam padrões definidos por Expressões Regulares Compatíveis perl (PCRE).



## <a name="next-steps"></a>Passos seguintes

- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Anular o comportamento http usando o motor de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral do Azure CDN](cdn-overview.md)
