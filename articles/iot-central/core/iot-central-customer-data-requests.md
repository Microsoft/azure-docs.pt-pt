---
title: Recursos de pedido de dados do cliente no Azure IoT Central Microsoft Docs
description: Este artigo descreve identificar, eliminar e exportar dados de clientes na aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dabcadea96f4ced5bdf73a35ef533e6d290595c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001881"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Funcionalidades de pedido de dados do cliente Azure IoT Central

O Azure IoT Central é uma solução totalmente gerida de software internet of things (IoT) que facilita a ligação, monitorização e gestão dos seus ativos IoT em escala, cria insights profundos a partir dos seus dados IoT e toma medidas informadas.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificar dados do cliente

O Azure Ative Directory Object-IDs são utilizados para identificar utilizadores e atribuir funções. O portal Azure IoT Central exibe endereços de e-mail do utilizador para atribuições de funções, mas apenas o ID do Objeto de Diretório Ativo Azure está armazenado, o endereço de e-mail é dinamicamente consultado a partir do Diretório Ativo Azure. Os administradores da Azure IoT Central podem ver, exportar e eliminar utilizadores de aplicações na secção de administração de utilizadores de uma aplicação Azure IoT Central.

Dentro da aplicação, os endereços de e-mail podem ser configurados para receber alertas. Neste caso, os endereços de e-mail são armazenados dentro da IoT Central e devem ser geridos a partir da página de administração da conta na aplicação.

No que diz respeito aos dispositivos, a Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam ao dispositivo a correlação entre os utilizadores. Muitos dos dispositivos geridos no Azure IoT Central não são dispositivos pessoais, por exemplo, uma máquina de venda automática ou uma máquina de café. Os clientes podem, no entanto, considerar que alguns dispositivos são pessoalmente identificáveis e, a seu critério, podem manter o seu próprio ativo ou sistemas de rastreio de inventário que liguem dispositivos a indivíduos. A Azure IoT Central gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais, e não estão relacionados com a atividade do utilizador. Os registos gerados pelo sistema Azure IoT Central não são acessíveis ou exportáveis pelos administradores de aplicações.

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

A capacidade de eliminar dados dos utilizadores é fornecida apenas através da página da administração central do IoT. Os administradores de aplicações podem selecionar o utilizador a eliminar e selecionar **Eliminar** no canto superior direito da aplicação para eliminar o registo. Os administradores de aplicações também podem remover contas individuais que já não estão associadas ao pedido em questão.

Depois de um utilizador ser eliminado, não são enviados mais alertas por e-mail. No entanto, o seu endereço de e-mail deve ser removido individualmente de cada alerta configurado.

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

A capacidade de exportação de dados só é fornecida através da página da administração central da IoT. Os dados do cliente, incluindo funções atribuídas, podem ser selecionados, copiados e colados por um administrador de aplicação.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter mais informações sobre a administração de conta, incluindo definições de funções, consulte [Como administrar a sua aplicação.](howto-administer.md)
