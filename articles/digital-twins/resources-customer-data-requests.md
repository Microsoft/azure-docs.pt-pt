---
title: Recursos de pedido de dados do cliente para Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Este artigo mostra processos de exportação e eliminação de dados pessoais em Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461764"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Funcionalidades de pedido de dados do cliente da Azure Digital Twins

A Azure Digital Twins é uma plataforma de desenvolvimento para criar representações digitais seguras de um ambiente de negócios. As representações são impulsionadas por dados do estado vivo de fontes de dados selecionadas pelos utilizadores.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

As representações digitais chamadas *gémeas digitais* em Azure Digital Twins representam entidades em ambientes reais, e estão associadas a identificadores. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam que os identificadores sejam correlacionados com os utilizadores. 

Muitos dos gémeos digitais em Azure Digital Twins não representam diretamente entidades pessoais — objetos típicos representados podem ser uma sala de reuniões de escritório, ou um piso de fábrica. No entanto, os utilizadores podem considerar que algumas entidades são pessoalmente identificáveis, e a seu critério podem manter o seu próprio ativo ou métodos de rastreio de inventário que ligam gémeos digitais a indivíduos. A Azure Digital Twins gere e armazena todos os dados associados a gémeos digitais como se fossem dados pessoais.

Para visualizar, exportar e eliminar dados pessoais que possam ser referenciados num pedido de assunto de dados, um administrador da Azure Digital Twins pode utilizar o [**portal Azure**](https://portal.azure.com/) para utilizadores e funções, ou as [**APIs REST de Gémeos Digitais Azure**](/rest/api/azure-digitaltwins/) para gémeos digitais. O portal Azure e as APIs REST fornecem diferentes métodos para os utilizadores servirem esses pedidos de tratamento.

## <a name="identifying-customer-data"></a>Identificar dados do cliente

A Azure Digital Twins considera que *os dados pessoais* são dados associados aos seus administradores e utilizadores. 

A Azure Digital Twins armazena o *ID* do objeto [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) dos utilizadores com acesso ao ambiente. O Azure Digital Twins no portal Azure exibe endereços de e-mail dos utilizadores, mas estes endereços de e-mail não são armazenados dentro do Azure Digital Twins. São analisados dinamicamente no Azure Ative Directory, utilizando o ID do objeto Azure Ative Directory.

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

Os administradores da Azure Digital Twins podem utilizar o portal Azure para eliminar dados relacionados com os utilizadores. Também é possível realizar operações de eliminação em gémeos digitais individuais usando as APIs REST dos Gémeos Digitais Azure. Para obter mais informações sobre as APIs disponíveis, consulte [a documentação do Azure Digital Twins REST APIs](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

A Azure Digital Twins armazena dados relacionados com gémeos digitais. Os utilizadores podem recuperar e visualizar estes dados através de APIs REST, e exportar estes dados usando cópia e pasta. 

Os dados do cliente, incluindo as funções do utilizador e as atribuições de funções, podem ser selecionados, copiados e colados a partir do portal Azure. 

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter uma lista completa das APIs de serviço Azure Digital Twins, consulte a documentação do [Azure Digital Twins REST APIs](/rest/api/azure-digitaltwins/).