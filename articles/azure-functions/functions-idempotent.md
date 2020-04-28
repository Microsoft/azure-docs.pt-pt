---
title: Conceber funções Azure para entrada idêntica
description: Construção de Funções Azure para ser idempotente
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74226865"
---
# <a name="designing-azure-functions-for-identical-input"></a>Conceber funções Azure para entrada idêntica

A realidade da arquitetura baseada em eventos e baseada em mensagens dita a necessidade de aceitar pedidos idênticos, preservando simultaneamente a integridade dos dados e a estabilidade do sistema.

Para ilustrar, considere um botão de chamada de elevador. Ao carregar no botão, acende-se e um elevador é enviado para o seu andar. Alguns momentos depois, alguém se junta a si no átrio. Esta pessoa sorri para ti e carrega no botão iluminado uma segunda vez. Sorris para trás e ries-te para ti mesmo quando te lembras que o comando de chamar um elevador é idempotente.

Premir um botão de chamada de elevador uma segunda, terceira ou quarta vez não tem influência no resultado final. Quando carrega no botão, independentemente do número de vezes, o elevador é enviado para o seu andar. Sistemas idempotentes, como o elevador, resultam no mesmo resultado, não importa quantas vezes sejam emitidos comandos idênticos.

No que diz respeito às aplicações de construção, considere os seguintes cenários:

- O que acontece se a sua aplicação de controlo de inventário tentar apagar o mesmo produto mais de uma vez?
- Como se comporta a sua aplicação de recursos humanos se há mais de um pedido para criar um registo de empregados para a mesma pessoa?
- Para onde vai o dinheiro se a sua aplicação bancária recebe 100 pedidos para fazer o mesmo levantamento?

Existem muitos contextos em que os pedidos a uma função podem receber comandos idênticos. Algumas situações incluem:

- Políticas de retry enviando o mesmo pedido muitas vezes
- Comandos cached reproduzidos para a aplicação
- Erros de aplicação enviando vários pedidos idênticos

Para proteger a integridade dos dados e a saúde do sistema, uma aplicação idempotente contém lógica que pode conter os seguintes comportamentos:

- Verificar a existência de dados antes de tentar executar um aeliminar
- Verificar se os dados já existem antes de tentar executar uma ação de criação
- Conciliar lógica que cria uma eventual consistência nos dados
- Controlos de condivisões
- Deteção de duplicação
- Validação da frescura de dados
- Lógica da guarda para verificar dados de entrada

Em última análise, a idempotency é alcançada garantindo que uma determinada ação é possível e só é executada uma vez.
