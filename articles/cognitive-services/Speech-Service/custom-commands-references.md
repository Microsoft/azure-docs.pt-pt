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
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307673"
---
# <a name="custom-commands-concepts-and-definitions"></a>Conceitos e definições de Comandos Personalizados

Este artigo serve como referência para conceitos e definições para aplicações de Comandos Personalizados.

## <a name="commands-configuration"></a>Configuração de comandos
Os comandos são os blocos básicos de construção de uma aplicação de Comandos Personalizados. Um comando é conjunto de configurações necessárias para completar uma tarefa específica definida por um utilizador.

### <a name="example-sentences"></a>Frases de exemplo
As palavras de exemplo são os exemplos definidos que o utilizador pode dizer para desencadear um determinado comando. Note que precisa fornecer apenas uma amostra de expressões e não uma lista exaustiva. 

### <a name="parameters"></a>Parâmetros
Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="completion-rules"></a>Regras de conclusão
Série de regras a executar uma vez que o comando esteja pronto para ser cumprido, isto é, quando todas as condições das regras estiverem preenchidas.

### <a name="interaction-rules"></a>Regras de interação
Regras adicionais para lidar com situações mais específicas ou complexas. Pode adicionar validações adicionais ou configurar funcionalidades avançadas, como confirmações ou correção de um passo. Também pode construir as suas próprias regras de interação personalizadas.

## <a name="parameters-configuration"></a>Configuração de parâmetros

Os parâmetros são informações necessárias pelos comandos para completar uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que desencadeiam ações personalizadas.

### <a name="name"></a>Name
Um parâmetro é identificado pela propriedade do nome. Deve sempre dar um nome descritivo a um parâmetro. Um parâmetro pode ser referido em diferentes secções, como - enquanto constrói condições, respostas da fala ou outras ações.
 
### <a name="isglobal"></a>IsGlobal
Caixa de verificação indicando se o âmbito deste parâmetro é partilhado em todos os comandos da aplicação. Se um parâmetro for global, o seu valor pode potencialmente ser fornecido a partir de qualquer âmbito de comando e uma vez atribuído um valor - pode ser referido a partir de qualquer um dos comandos. 

### <a name="required"></a>Necessário
Caixa de verificação indicando se é necessário um valor para este parâmetro para a realização/conclusão do comando. Tem de configurar respostas para solicitar ao utilizador que forneça valor se um parâmetro for marcado conforme necessário.

### <a name="type"></a>Tipo
Comandos personalizados suportam os seguintes tipos de parâmetros...


* DateTime
* Geografia
* Número
* String

Todos estes tipos de parâmetros suportam a configuração de valor predefinido que pode configurar a partir do portal.

### <a name="configuration"></a>Configuração
Configuração é uma propriedade de parâmetro definida apenas para o tipo: String. Abaixo estão os valores suportados
* Nenhum
* Aceite a entrada completa: Ativando esta opção, o parâmetro aceita qualquer expressão de entrada, isto é útil quando o utilizador precisa de um parâmetro com a expressão completa. por exemplo, endereços postais.
* Aceite valores de entrada predefinidos do catálogo externo: usado para configurar parâmetros que podem assumir uma grande variedade de valores. por exemplo, catálogo de vendas. Neste caso, o catálogo é hospedado num ponto final externo da web e pode ser configurado de forma independente.
* Aceite valores de entrada predefinidos do catálogo interno: usado para configurar parâmetros que podem assumir alguns valores. Neste caso, os valores devem ser configurados no Estúdio da Fala.


### <a name="validation"></a>Validação
As validações são construções aplicáveis a certos tipos de parâmetros que permitem configurar restrições no valor do parâmetro. Atualmente, os Comandos Personalizados suportam validações em seguintes tipos de parâmetros:
* DateTime
* Número

## <a name="rules-configuration"></a>Configuração de regras
Uma regra em Comandos Personalizados é definida por um conjunto de **condições,** que quando cumpridos, executará um conjunto de **ações**. A regra também permite configurar o seu **estado pós-execução** e **as expectativas** para a próxima volta.

### <a name="types"></a>Tipos
Suporte de comandos personalizados seguindo categorias de regras:
* Regras de conclusão

    Lista de regras a executar após o cumprimento do comando. Todas as regras configuradas nesta secção para as quais as condições são verdadeiras serão executadas.
    
* Regras de interação

    As regras de interação podem ser usadas para configurar validações personalizadas adicionais, confirmações, correção de um passo ou para realizar qualquer outra lógica de diálogo personalizado. Estas regras são avaliadas em cada turno de processamento e podem ser usadas para desencadear regras de conclusão.

As diferentes ações configuradas como parte de uma regra são executadas na ordem em que aparecem no portal de autoria.
    
### <a name="conditions"></a>Condições
Conjunto de condições que devem ser cumpridas para que uma regra seja executada. A condição das regras pode ser dos seguintes tipos:
* O valor do parâmetro é igual: o valor do parâmetro configurado é igual a um valor específico.
* Sem valor de parâmetro: os parâmetros configurados não devem ter qualquer valor.
* Parâmetros necessários: o parâmetro configurado tem um valor.
* Todos os parâmetros necessários: todos os parâmetros que foram marcados conforme necessário têm um valor.
* Parâmetros atualizados: um ou mais valores de parâmetros foram atualizados como resultado do processamento da entrada atual (expressão/atividade).
* A confirmação foi bem sucedida: a expressão/atividade de entrada foi uma confirmação bem sucedida (sim).
* A confirmação foi negada: a expressão/atividade de entrada foi uma confirmação bem sucedida (não).
* O comando anterior tem de ser atualizado: esta condição é utilizada em casos em que pretender obter uma confirmação negada juntamente com uma atualização. Nos bastidores, isto é configurado para quando o motor de diálogo deteta uma confirmação negativa onde a intenção é a mesma da curva anterior, e o utilizador respondeu com uma atualização.

### <a name="actions"></a>Ações
* Envie resposta de discurso: envie uma resposta de discurso ao cliente.
* Valor do parâmetro de atualização: valor de atualização de um parâmetro de comando para um valor especificado.
* Limpar o valor do parâmetro desmarcar o valor do parâmetro de comando.
* Ligue para o ponto final da web: faça uma chamada para um ponto final web.
* Enviar atividade ao cliente: envie uma atividade personalizada ao cliente.

### <a name="expectations"></a>Expectativas
As expectativas são usadas para configurar pistas para o processamento da próxima entrada do utilizador. Os seguintes tipos são suportados:
* Esperando a confirmação do utilizador: especifica que a aplicação está à espera de uma confirmação (sim/não) para a próxima entrada do utilizador.
* Entrada de parâmetros do utilizador: especifique um ou mais parâmetros de comando que a aplicação espera da entrada do utilizador.

### <a name="post-execution-state"></a>Estado pós-execução
Estado de diálogo após o processamento da entrada atual (expressão/atividade). É dos seguintes tipos:
* Comando concluído: preencha o comando e não serão processadas quaisquer regras adicionais do comando.
* Executar regras de conclusão: execute todas as regras de conclusão válidas.
* Aguarde a entrada do utilizador: aguarde a próxima entrada do utilizador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)
