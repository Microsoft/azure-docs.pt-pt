---
title: Conceitos e definições de Comandos Personalizados - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende sobre conceitos e definições para aplicações de Comandos Personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94561102"
---
# <a name="custom-commands-concepts-and-definitions"></a>Conceitos e definições de Comandos Personalizados

Este artigo serve como referência para conceitos e definições para aplicações de Comandos Personalizados.

## <a name="commands-configuration"></a>Configuração de comandos
Os comandos são os blocos básicos de construção de uma aplicação de Comandos Personalizados. Um comando é um conjunto de configurações necessárias para completar uma tarefa específica definida por um utilizador.

### <a name="example-sentences"></a>Frases de exemplo
As palavras de exemplo são os exemplos definidos que o utilizador pode dizer para desencadear um determinado comando. Só precisa fornecer uma amostra de expressões e não uma lista exaustiva. 

### <a name="parameters"></a>Parâmetros
Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="completion-rules"></a>Regras de conclusão
As regras de conclusão são uma série de regras a executar depois de o comando estar pronto para ser cumprido, por exemplo, quando todas as condições das regras estiverem preenchidas.

### <a name="interaction-rules"></a>Regras de interação
As regras de interação são regras adicionais para lidar com situações mais específicas ou complexas. Pode adicionar validações adicionais ou configurar funcionalidades avançadas, como confirmações ou uma correção de um passo. Também pode construir as suas próprias regras de interação personalizadas.

## <a name="parameters-configuration"></a>Configuração de parâmetros

Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="name"></a>Name
Um parâmetro é identificado pela propriedade do nome. Deve sempre dar um nome descritivo a um parâmetro. Um parâmetro pode ser referido em diferentes secções, por exemplo, quando se constrói condições, respostas da fala ou outras ações.
 
### <a name="isglobal"></a>IsGlobal
Esta caixa de verificação indica se o âmbito deste parâmetro é partilhado em todos os comandos da aplicação. Se um parâmetro for global, o seu valor pode potencialmente ser fornecido a partir de qualquer âmbito de comando. Depois de atribuído um valor, pode ser encaminhado a partir de qualquer um dos comandos. 

### <a name="required"></a>Necessário
Esta caixa de verificação indica se é necessário um valor para este parâmetro para a realização ou conclusão do comando. Tem de configurar respostas para solicitar ao utilizador que forneça um valor se um parâmetro for marcado conforme necessário.

Note que, se configurar um **parâmetro necessário** para ter um **valor Padrão,** o sistema ainda irá explicitamente solicitar o valor do parâmetro.

### <a name="type"></a>Tipo
Os Comandos Personalizados suportam os seguintes tipos de parâmetros:

* DateTime
* Geografia
* Número
* String

Todos estes tipos de parâmetros, com exceção da Geografia, suportam a configuração do valor predefinido, que pode configurar a partir do portal.

### <a name="configuration"></a>Configuração
Configuração é uma propriedade de parâmetro definida apenas para o tipo String. São suportados os seguintes valores:

* **Nenhuma.**
* **Aceite a entrada completa**: Quando ativado, um parâmetro aceita qualquer expressão de entrada. Esta opção é útil quando o utilizador precisa de um parâmetro com a expressão completa. Um exemplo são os endereços postais.
* **Aceite valores de entrada predefinidos a partir de um catálogo externo**: Este valor é utilizado para configurar um parâmetro que pode assumir uma grande variedade de valores. Um exemplo é um catálogo de vendas. Neste caso, o catálogo está hospedado num ponto final externo da web e pode ser configurado de forma independente.
* **Aceite valores de entrada predefinidos do catálogo interno**: Este valor é utilizado para configurar um parâmetro que pode assumir alguns valores. Neste caso, os valores devem ser configurados no Estúdio da Fala.


### <a name="validation"></a>Validação
As validações são construções aplicáveis a certos tipos de parâmetros que permitem configurar restrições no valor de um parâmetro. Atualmente, os Comandos Personalizados suportam validações nos seguintes tipos de parâmetros:

* DateTime
* Número

## <a name="rules-configuration"></a>Configuração de regras
Uma regra nos Comandos Personalizados é definida por um conjunto de *condições* que, quando satisfeitas, executam um conjunto de *ações*. As regras também permitem configurar *o estado pós-execução* e *as expectativas* para a próxima volta.

### <a name="types"></a>Tipos
Os Comandos Personalizados suportam as seguintes categorias de regras:

* **Regras de conclusão**: Estas regras devem ser executadas mediante o cumprimento do comando. Todas as regras configuradas nesta secção para as quais as condições são verdadeiras serão executadas. 
* **Regras de interação**: Estas regras podem ser usadas para configurar validações personalizadas adicionais, confirmações e uma correção de um passo, ou para realizar qualquer outra lógica de diálogo personalizado. As regras de interação são avaliadas a cada momento no processamento e podem ser usadas para desencadear regras de conclusão.

As diferentes ações configuradas como parte de uma regra são executadas na ordem em que aparecem no portal de autoria.

### <a name="conditions"></a>Condições
As condições são os requisitos que devem ser cumpridos para que uma regra seja executada. As condições de regras podem ser dos seguintes tipos:

* **Valor do parâmetro é igual:** O valor do parâmetro configurado é igual a um valor específico.
* **Sem valor de parâmetro:** Os parâmetros configurados não devem ter qualquer valor.
* **Parâmetros necessários**: O parâmetro configurado tem um valor.
* **Todos os parâmetros necessários**: Todos os parâmetros que foram marcados conforme necessário têm um valor.
* **Parâmetros atualizados**: Um ou mais valores de parâmetros foram atualizados como resultado do processamento da entrada atual (expressão ou atividade).
* **Confirmação foi bem sucedida**: A expressão de entrada ou atividade foi uma confirmação bem sucedida (sim).
* **Confirmação foi negada**: A expressão ou atividade de entrada não foi uma confirmação bem sucedida (não).
* **O comando anterior tem de ser atualizado**: Esta condição é utilizada em casos em que pretender obter uma confirmação negada juntamente com uma atualização. Nos bastidores, esta condição é configurada para quando o motor de diálogo deteta uma confirmação negativa onde a intenção é a mesma da curva anterior, e o utilizador respondeu com uma atualização.

### <a name="actions"></a>Ações
* **Enviar resposta de discurso**: Envie uma resposta de discurso de volta ao cliente.
* **Valor do parâmetro de atualização**: Atualize o valor de um parâmetro de comando para um valor especificado.
* **Valor do parâmetro claro**: Limpe o valor do parâmetro de comando.
* **Ligue para o ponto final da web**: Faça uma chamada para um ponto final web.
* **Enviar atividade ao cliente:** Envie uma atividade personalizada ao cliente.

### <a name="expectations"></a>Expectativas
As expectativas são usadas para configurar pistas para o processamento da próxima entrada do utilizador. São suportados os seguintes tipos:

* **Esperando confirmação do utilizador**: Esta expectativa especifica que a aplicação está à espera de uma confirmação (sim/não) para a próxima entrada do utilizador.
* **À espera de uma entrada de parâmetros do utilizador**: Esta expectativa especifica um ou mais parâmetros de comando que a aplicação espera da entrada do utilizador.

### <a name="post-execution-state"></a>Estado pós-execução
O estado pós-execução é o estado de diálogo após o processamento da entrada atual (expressão ou atividade). É dos seguintes tipos:

* **Mantenha o estado atual**: Mantenha o estado atual apenas.
* **Preencha o comando**: Preencha o comando e não serão processadas quaisquer regras adicionais do comando.
* **Executar regras de conclusão**: Execute todas as regras de conclusão válidas.
* **Aguarde a entrada do utilizador**: Aguarde a próxima entrada do utilizador.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)
