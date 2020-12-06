---
title: Séries regional ou SKU indisponíveis
description: Algumas séries SKU não estão disponíveis para a subscrição selecionada para esta região, o que pode exigir um pedido de suporte de gestão de subscrição.
author: stevendotwang
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 2bbe1dff6bbf8fed481300db95482c46b0e8a7b7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745286"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU indisponível

Este artigo descreve como resolver a questão de uma subscrição Azure não ter acesso a uma região ou a um VM SKU.

## <a name="symptoms"></a>Sintomas

Ao utilizar uma máquina virtual, recebe uma das seguintes mensagens de erro:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Ao adquirir instâncias de máquina virtual reservadas, recebe uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Ao criar um pedido de apoio para aumentar a quota principal do cálculo, uma região ou uma família SKU não está disponível para seleção.

## <a name="solution"></a>Solução

Recomendamos primeiro que considere uma região alternativa ou SKU que atenda às suas necessidades comerciais.

Se não conseguir encontrar uma região adequada ou SKU, crie um pedido de [apoio à](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **gestão de subscrição** seguindo estes passos:

1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**. Em seguida, selecione **Novo pedido de suporte**.

1. Em **Básicos**, para **tipo de Emissão,** selecione **gestão de subscrição**.

1. Selecione uma **Subscrição** e introduza uma breve descrição em **Resumo**.

   ![Separador básico do novo pedido de apoio](./media/SKU-series-unavailable/support-request-basics.png)

1. Para **o tipo de problema,** escolha **Selecione o tipo de problema**.

1. Para **Selecionar tipo de problema**, escolha uma opção, por exemplo, Não pode aceder à minha **subscrição ou recurso** O meu problema não está listado  >  **acima**. Selecione **Guardar**.

   ![Especificar um problema para o pedido](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecione **Seguinte: Soluções** para explorar possíveis soluções. Se necessário, selecione **Seguinte: Detalhes** a continuar.

1. Insira qualquer informação adicional que possa fornecer, juntamente com as suas informações de contacto.

1. Selecione **Rever + criar**. Depois de verificar as suas informações, selecione **Criar** para criar o pedido.

## <a name="send-us-your-suggestions"></a>Envie-nos as suas sugestões

Estamos sempre abertos a feedback e sugestões! Envie-nos as suas [sugestões.](https://feedback.azure.com/forums/266794-support-feedback) Além disso, pode envolver-se connosco no [Twitter](https://twitter.com/azuresupport) ou na [página de perguntas do Microsoft Q&A](/answers/products/azure).

## <a name="learn-more"></a>Saber mais

[FAQ de suporte Azure](https://azure.microsoft.com/support/faq)