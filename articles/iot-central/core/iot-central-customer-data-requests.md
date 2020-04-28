---
title: Funcionalidades de pedido de dados de clientes no Azure IoT Central [ Central De IoT] Microsoft Docs
description: Este artigo descreve a identificação, a pagando e exportando dados dos clientes na aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023757"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados dos clientes

A Azure IoT Central é uma solução de software de Internet das Coisas (IoT) totalmente gerida que facilita a ligação, monitorização e gestão dos seus ativos IoT em escala, cria insights profundos a partir dos seus dados IoT e toma medidas informadas.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificação dos dados dos clientes

Os IDs de Objeto sintetismo Ativo Azure são utilizados para identificar utilizadores e atribuir funções. O portal Azure IoT Central exibe endereços de e-mail de utilizadores para atribuições de funções, mas apenas o Azure Ative Directory Object-ID está armazenado, o endereço de e-mail é dinamicamente consultado do Diretório Ativo azure. Os administradores da Azure IoT Central podem visualizar, exportar e excluir utilizadores de aplicações na secção de administração de utilizadores de uma aplicação Azure IoT Central.

Dentro da aplicação, os endereços de e-mail podem ser configurados para receber alertas. Neste caso, os endereços de e-mail são armazenados no IoT Central e devem ser geridos a partir da página de administração da conta na aplicação.

No que diz respeito aos dispositivos, a Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam ao dispositivo a correlação do utilizador. Muitos dos dispositivos geridos no Azure IoT Central não são dispositivos pessoais, por exemplo uma máquina de venda automática ou máquina de café. Os clientes podem, no entanto, considerar alguns dispositivos pessoalmente identificáveis e, a seu critério, podem manter os seus próprios sistemas de rastreio de ativos ou inventários que ligam dispositivos a indivíduos. A Azure IoT Central gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais, e não estão relacionados com a atividade do utilizador. Os registos gerados pelo sistema Azure IoT Central não são acessíveis ou exportáveis pelos administradores de aplicações.

## <a name="deleting-customer-data"></a>Apagar dados dos clientes

A capacidade de eliminar os dados dos utilizadores só é fornecida através da página de administração IoT Central. Os administradores de aplicação podem selecionar o utilizador a ser eliminado e selecionar **Apagar** no canto superior direito da aplicação para apagar o registo. Os administradores de aplicações também podem remover contas individuais que já não estejam associadas à aplicação em questão.

Depois de um utilizador ser eliminado, não são enviados mais alertas por e-mail. No entanto, o seu endereço de e-mail deve ser removido individualmente de cada alerta configurado.

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

A capacidade de exportar dados só é fornecida através da página da administração IoT Central. Os dados do cliente, incluindo as funções atribuídas, podem ser selecionados, copiados e colados por um administrador de aplicação.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter mais informações sobre a administração de contas, incluindo definições de papéis, consulte [como administrar a sua aplicação](howto-administer.md).
