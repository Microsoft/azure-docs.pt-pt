---
title: Recursos de solicitação de dados do cliente no Azure IoT Central | Microsoft Docs
description: Recursos de solicitação de dados do cliente no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a1775c8680b2e0d931c623d2e8ced2224205a767
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050482"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente

O Azure IoT Central é uma solução de software como serviço de IoT (Internet das Coisas) totalmente gerenciada que facilita a conexão, o monitoramento e o gerenciamento de seus ativos de IoT em escala, a criação de informações aprofundadas de seus dados de IoT e a ação informada.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificando dados do cliente

Azure Active Directory objeto-IDs são usadas para identificar usuários e atribuir funções. O portal de IoT Central do Azure exibe endereços de email do usuário para atribuições de função, mas somente o Azure Active Directory ID do objeto é armazenado, o endereço de email é consultado dinamicamente de Azure Active Directory. Os administradores de IoT Central do Azure podem exibir, exportar e excluir usuários de aplicativos na seção de administração de usuário de um aplicativo de IoT Central do Azure.

No aplicativo, os endereços de email podem ser configurados para receber alertas. Nesse caso, os endereços de email são armazenados no IoT Central e devem ser gerenciados na página de administração da conta no aplicativo.

Em relação a dispositivos, a Microsoft não mantém nenhuma informação e não tem acesso aos dados que permitem a correlação do dispositivo com o usuário. Muitos dos dispositivos gerenciados no Azure IoT Central não são dispositivos pessoais, por exemplo, um computador de venda ou um café. No entanto, os clientes podem considerar que alguns dispositivos sejam identificáveis pessoalmente e, a seu critério, podem manter seus próprios ativos ou sistemas de controle de estoque que unem dispositivos a indivíduos. O Azure IoT Central gerencia e armazena todos os dados associados a dispositivos como se eles fossem dados pessoais.

Quando você usa os serviços corporativos da Microsoft, a Microsoft gera algumas informações, conhecidas como logs gerados pelo sistema. Esses logs constituem ações reais executadas no serviço e nos dados de diagnóstico relacionados a dispositivos individuais e não estão relacionadas à atividade do usuário. Os logs gerados pelo sistema IoT Central do Azure não estão acessíveis ou exportáveis por administradores de aplicativos.

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

A capacidade de excluir dados do usuário é fornecida somente por meio da página de administração do IoT Central. Os administradores de aplicativos podem selecionar o usuário a ser excluído e selecionar **excluir** no canto superior direito do aplicativo para excluir o registro. Os administradores de aplicativos também podem remover contas individuais que não estão mais associadas ao aplicativo em questão.

Depois que um usuário é excluído, nenhum alerta adicional é enviado a ele. No entanto, seu endereço de email deve ser removido individualmente de cada alerta configurado.

Para obter mais informações, consulte [configurar regras e ações para seu dispositivo](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Exportar dados do cliente

A capacidade de exportar dados é fornecida apenas por meio da página de administração de IoT Central. Os dados do cliente, incluindo as funções atribuídas, podem ser selecionados, copiados e colados por um administrador de aplicativos.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter mais informações sobre administração de conta, incluindo definições de função, consulte [como administrar seu aplicativo](howto-administer.md).
