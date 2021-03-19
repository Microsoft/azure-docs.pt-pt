---
title: Projetar funções de Azure para entrada idêntica
description: Construção Azure Funções para ser idempotente
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74226865"
---
# <a name="designing-azure-functions-for-identical-input"></a>Projetar funções de Azure para entrada idêntica

A realidade da arquitetura baseada em eventos e baseada em mensagens dita a necessidade de aceitar pedidos idênticos, preservando simultaneamente a integridade dos dados e a estabilidade do sistema.

Para ilustrar, considere um botão de chamada de elevador. Ao carregar no botão, acende-se e um elevador é enviado para o seu andar. Alguns momentos depois, alguém se junta a si no átrio. Esta pessoa sorri para si e carrega no botão iluminado uma segunda vez. Sorris para trás e ris-te enquanto te lembras que a ordem para chamar um elevador é idempotente.

Premir um botão de chamada de elevador uma segunda, terceira ou quarta vez não tem influência no resultado final. Quando carrega no botão, independentemente do número de vezes, o elevador é enviado para o seu andar. Sistemas idempotentes, como o elevador, resultam no mesmo resultado, não importa quantas vezes sejam emitidos comandos idênticos.

No que diz respeito à construção de aplicações, considere os seguintes cenários:

- O que acontece se a sua aplicação de controlo de inventário tentar eliminar o mesmo produto mais de uma vez?
- Como se comporta a sua aplicação de recursos humanos se há mais de um pedido para criar um registo de empregado para a mesma pessoa?
- Para onde vai o dinheiro se a sua aplicação bancária recebe 100 pedidos para fazer o mesmo levantamento?

Existem muitos contextos em que os pedidos para uma função podem receber comandos idênticos. Algumas situações incluem:

- Reformular políticas enviando o mesmo pedido muitas vezes
- Comandos em cache reproduzidos à aplicação
- Erros de aplicação enviando vários pedidos idênticos

Para proteger a integridade dos dados e a saúde do sistema, uma aplicação idempotente contém lógica que pode conter os seguintes comportamentos:

- Verificação da existência de dados antes de tentar executar uma eliminação
- Verificar se os dados já existem antes de tentar executar uma ação de criação
- Conciliar lógica que cria uma eventual consistência nos dados
- Controlos de concordância
- Deteção de duplicação
- Validação da frescura de dados
- Guarde a lógica para verificar os dados de entrada

Em última análise, a idempoteência é conseguida garantindo que uma determinada ação é possível e só é executada uma vez.
