---
title: Azure CDN governa referência do motor Microsoft Docs
description: Documentação de referência para as regras do Motor De CDN Azure condições e características.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 75633521474ec3bcbc35cea49ea7a2da6a271e01
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872515"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN da Verizon Premium regras referência do motor

Este artigo enumera descrições detalhadas das condições de jogo disponíveis e das características do motor de [regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

O motor de regras foi concebido para ser a autoridade final sobre como tipos específicos de pedidos são processados pelo CDN.

**Utilizações comuns:**

- Anular ou definir uma política de cache personalizada.
- Proteger ou negar pedidos de conteúdo sensível.
- Redirecionamento de pedidos.
- Armazenar dados de registo personalizados.
## <a name="key-concepts"></a>Conceitos-chave
Os conceitos-chave para a criação do Motor de Regras são descritos abaixo.
### <a name="draft"></a>Rascunho
Um projeto de política consiste numa ou mais regras destinadas a identificar pedidos e ao conjunto de ações que lhes serão aplicadas. Um rascunho é um trabalho em andamento que permite atualizações frequentes de configuração sem afetar o tráfego do site. Uma vez finalizado um projeto, este deve ser convertido numa política apenas para leitura.

### <a name="rule"></a>Regra
Uma regra identifica um ou mais tipos de pedidos e o conjunto de ações que lhes serão aplicadas.

Consiste em: 

- Um conjunto de expressões condicionais que definem a lógica através da qual os pedidos são identificados.
- Um conjunto de condições de correspondência que definem os critérios utilizados para identificar pedidos.
- Um conjunto de funcionalidades que definem como o CDN irá lidar com os pedidos acima referidos.
Estes elementos são identificados na seguinte ilustração.

![Fluxo de trabalho de implementação de políticas](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Política
Uma política, que consiste num conjunto de regras apenas de leitura, prevê os meios para:

- Crie, armazene e gere várias variantes das suas regras.
- Volte para uma versão previamente implantada.
- Preparar previamente regras específicas para eventos (por exemplo, uma regra que redireciona o tráfego em resultado de uma manutenção da origem do cliente.)

> [!NOTE]
> Embora seja permitida apenas uma política única por ambiente, as políticas podem ser implementadas se necessário.

### <a name="deploy-request"></a>Pedido de implantação
Um pedido de implantação fornece um procedimento simples e simplificado através do qual uma política pode ser rapidamente aplicada ao ambiente de encenação ou produção. É fornecido um histórico de pedidos de implantação para facilitar o acompanhamento das alterações aplicadas a esses ambientes.

> [!NOTE]
> Apenas pedidos que não passem no nosso sistema automatizado de validação e deteção de erros exigirão revisão manual e aprovação.

### <a name="rule-precedence"></a>Precedência de regras
As regras contidas numa Política são normalmente processadas na ordem em que estão listadas (isto é, de cima a baixo). Se o pedido corresponder às regras contraditórias, então a última regra a ser processada terá precedência.

### <a name="policy-deployment-workflow"></a>Fluxo de trabalho de implementação de políticas
O fluxo de trabalho através do qual uma política pode ser aplicada quer ao ambiente de produção quer a uma encenação é ilustrado abaixo.

![Fluxo de trabalho de implementação de políticas](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Passo |Descrição |
|---------|---------|
|[Criar Esboço](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Um projeto consiste num conjunto de regras que definem como os pedidos para o seu conteúdo devem ser tratados pelo CDN.     |
|Rascunho de bloqueio   |     Uma vez finalizado um projeto, este deve ser bloqueado e convertido numa política apenas para leitura.    |
|[Enviar Pedido de Implantação](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Um pedido de implantação permite que uma política seja aplicada ao tráfego de ensaios ou de produção.</br> <br>Envie um pedido de implantação para o ambiente de preparação ou produção.</br>     |
|Implementar revisão de pedidos   |    <br>Um pedido de implementação passa por validação automatizada e deteção de erros.</br><br>Embora a maioria dos pedidos de implementação sejam automaticamente aprovados, é necessária uma revisão manual para políticas mais complexas.</br>   |
|Implantação de políticas[(encenação)](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging)   |  <br> Após a aprovação de um pedido de implantação para o ambiente de preparação, será aplicada uma política ao ambiente de encenação. Este ambiente permite que uma política seja testada contra o tráfego do site simulado.</br><br>Uma vez que a política esteja pronta para ser aplicada ao tráfego do local ao vivo, deve ser apresentado um novo pedido de implantação para o ambiente de produção.</br>      |
|Implantação de Políticas[(Produção)](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti)   |  Após a aprovação de um pedido de implantação ao ambiente de produção, será aplicada uma política ao ambiente de produção. Este ambiente permite que uma política atue como a autoridade final para determinar como o CDN deve lidar com o tráfego vivo.     |
## <a name="syntax"></a>Sintaxe

A forma como os caracteres especiais são tratados varia de acordo com a forma como uma condição de correspondência ou característica lida com os valores de texto. Uma condição ou característica de correspondência pode interpretar texto de uma das seguintes formas:

- [**Valores literais**](#literal-values)
- [**Valores wildcard**](#wildcard-values)
- [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

O texto interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como parte do valor que deve ser igualado. Por outras palavras, uma condição de correspondência literal definida `\'*'\` apenas é satisfeita quando esse valor exato (isto é, `\'*'\` ) é encontrado.

Um símbolo percentual é usado para indicar codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores wildcard

Texto que é interpretado como um valor wildcard atribui significado adicional a caracteres especiais. A tabela seguinte descreve como o seguinte conjunto de caracteres é interpretado:

Caráter | Descrição
----------|------------
\ | Um backslash é usado para escapar de qualquer um dos caracteres especificados nesta tabela. Uma barra de costas deve ser especificada diretamente antes do carácter especial que deve ser escapado.<br/>Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | Um símbolo percentual é usado para indicar codificação de URL (por exemplo, `%20` ).
\* | Um asterisco é um wildcard que representa um ou mais caracteres.
Espaço | Um caracteres espaciais indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
'valor' | Uma única citação não tem um significado especial. No entanto, um conjunto de citações únicas é usado para indicar que um valor deve ser tratado como um valor literal. Pode ser usado das seguintes formas:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponda a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cordas: <br/><br/>/negócios/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/negócio/modelo. **ma**p<br /><br />- Permite que um personagem especial seja especificado como um personagem literal. Por exemplo, pode especificar um personagem de espaço literal, encerrando um personagem espacial dentro de um conjunto de citações individuais (ou seja, `' '` ou `'sample value'` ).<br/>- Permite especificar um valor em branco. Especifique um valor em branco especificando um conjunto de cotações individuais (isto é, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um wildcard, então é automaticamente considerado um valor literal, o que significa que não é necessário especificar um conjunto de cotações únicas.<br/>- Se um backslash não escapar a outro carácter nesta tabela, é ignorado quando é especificado num conjunto de citações únicas.<br/>- Outra forma de especificar um personagem especial como personagem literal é escapar-lhe usando um backslash (isto é, `\` ).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é procurado dentro de um valor de texto. Notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela seguinte indica como os caracteres especiais são tratados pelas condições de jogo e características que suportam expressões regulares.

Caráter Especial | Descrição
------------------|------------
\ | Um backslash escapa ao personagem o que o segue, o que faz com que esse personagem seja tratado como um valor literal em vez de assumir o seu significado de expressão regular. Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | O significado de um símbolo percentual depende da sua utilização.<br/><br/> `%{HTTPVariable}`: Esta sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: Esta sintaxe utiliza um símbolo percentual para identificar uma variável HTTP e como delimitador.<br />`\%`: Escapar de um símbolo percentual permite que seja utilizado como um valor literal ou indicar codificação de URL (por exemplo, `\%20` ).
\* | Um asterisco permite que o personagem anterior seja igualado zero ou mais vezes.
Espaço | Um personagem espacial é tipicamente tratado como um personagem literal.
'valor' | As citações individuais são tratadas como caracteres literais. Um conjunto de citações individuais não tem um significado especial.

As condições e características de correspondência que suportam expressões regulares aceitam padrões definidos por Expressões Regulares Compatíveis perl (PCRE).



## <a name="next-steps"></a>Passos seguintes

- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral do CDN azure](cdn-overview.md)
