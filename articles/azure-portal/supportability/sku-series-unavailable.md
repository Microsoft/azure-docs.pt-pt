---
title: Região ou série SKU indisponível - Azure
description: Algumas séries SKU não estão disponíveis para a subscrição selecionada para esta região, o que pode exigir pedido de apoio de gestão de subscrição.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843647"
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU indisponível

Este artigo descreve como resolver a questão de uma subscrição azure não ter acesso a uma região ou a um VM SKU.

## <a name="symptoms"></a>Sintomas

Ao implantar uma máquina virtual, recebe uma das seguintes mensagens de erro:

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

Ao adquirir Instâncias de Máquina Virtual Reservadas, recebe uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Ao criar um pedido de apoio para aumentar a quota central da computação, uma região ou uma família SKU não está disponível para seleção.

## <a name="solution"></a>Solução

Recomendamos primeiro que considere uma região alternativa ou SKU que atenda às suas necessidades de negócio.

Se não conseguir encontrar uma região adequada ou SKU, crie um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) à gestão de **subscrição** seguindo estas etapas:

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**. Em seguida, selecione **Novo pedido de suporte**.

1. No **Básico,** para **tipo de Emissão,** selecione **gestão de subscrição.**

1. Selecione uma **Subscrição** e introduza uma breve descrição em **Resumo**.

   ![Separador básico de novo pedido de suporte](./media/SKU-series-unavailable/support-request-basics.png)

1. Para **o tipo de problema,** escolha Selecionar o tipo de **problema**.

1. Para selecionar tipo de **problema,** escolha uma opção, por exemplo, **Incapaz de aceder à minha subscrição ou recurso** > **O meu problema não está listado acima .** Selecione **Guardar**.

   ![Especificar um problema para o pedido](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecione **Seguinte: Soluções** para explorar possíveis soluções. Se necessário, selecione **Next: Detalhes** para continuar.

1. Insira qualquer informação adicional que possa fornecer, juntamente com as suas informações de contacto.

1. Selecione **Rever + criar**. Depois de verificar as suas informações, selecione **Criar** para criar o pedido.

## <a name="send-us-your-suggestions"></a>Envie-nos as suas sugestões

Estamos sempre abertos a feedback e sugestões! Envie-nos as suas [sugestões.](https://feedback.azure.com/forums/266794-support-feedback) Além disso, pode envolver-se connosco no [Twitter](https://twitter.com/azuresupport) ou nos fóruns da [MSDN.](https://social.msdn.microsoft.com/Forums/azure)

## <a name="learn-more"></a>Saiba mais

[FaQ de suporte azure](https://azure.microsoft.com/support/faq)
