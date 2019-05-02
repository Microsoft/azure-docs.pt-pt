---
title: Dados do cliente solicitar recursos no Azure IoT Central | Documentos da Microsoft
description: Dados do cliente solicitar recursos no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4c63ac8d6b16b2a0476600f67c66b2ccc8dc2aab
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685560"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente

O Azure IoT Central é uma solução de software-como-serviço de Internet das coisas (IoT) totalmente gerida que torna mais fácil ligar, monitorizar, gerir os seus recursos de IoT em escala, criar informações aprofundadas dos seus dados de IoT e tomar ações informadas.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificar os dados do cliente

O Azure Active Directory-IDs de objeto são utilizados para identificar os utilizadores e atribuir funções. Os endereços de e-mail de utilizador do Azure IoT Central portal apresenta para atribuições de funções, mas apenas o Azure Active Directory-ID de objeto é armazenado, o endereço de e-mail dinamicamente é consultado do Azure Active Directory. Os administradores do Centro de IoT do Azure podem ver, exportar e eliminar utilizadores da aplicação na secção de administração de utilizador de uma aplicação do Azure IoT Central.

Dentro do aplicativo, os endereços de e-mail podem ser configurados para receber alertas. Neste caso, os endereços de e-mail são armazenados no Centro de IoT e tem de ser gerenciados a partir da página de administração da conta na aplicação.

Sobre dispositivos, a Microsoft não mantém nenhuma informação e não tem acesso aos dados que permite que o dispositivo para a correlação de utilizador. Muitos dos dispositivos geridos no Azure IoT Central não são dispositivos pessoais, por exemplo, uma máquina de venda automática ou café. Os clientes podem, no entanto, a considerar alguns dos dispositivos de identificação pessoal e a seu critério podem manter seus próprios recursos ou sistemas que associe os dispositivos aos indivíduos de controle de inventário. O Azure IoT Central gerencia e armazena todos os dados associados a dispositivos como se fosse dados pessoais.

Ao utilizar serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Estes registos constituem ações factuais realizadas dentro do serviço e os dados de diagnóstico relacionadas com dispositivos individuais e não estão relacionadas com a atividade do utilizador. Registos gerados pelo sistema para Centro de IoT do Azure não são acessíveis ou exportável pelos administradores do aplicativo.

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

A capacidade de eliminar dados de utilizador só é fornecida através da página de administração do Centro de IoT. Administradores da aplicação, podem selecionar o utilizador que pretende eliminar e selecione **eliminar** no canto superior direito da aplicação para eliminar o registo. Os administradores do aplicativo também podem remover as contas individuais que já não estão associadas com o aplicativo em questão.

Depois de um utilizador é eliminado, não existem alertas adicionais são enviadas por e-mail aos mesmos. No entanto, o respetivo endereço de e-mail deve ser individualmente removido de cada alerta configurada.

Para obter mais informações, consulte [configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Exportar dados do cliente

A capacidade de exportar os dados só é fornecida através da página de administração do Centro de IoT. Dados do cliente, incluindo funções atribuídas, podem ser selecionados, copiados e colados por um administrador da aplicação.

## <a name="links-to-additional-documentation"></a>Ligações para documentação adicional

Para obter mais informações sobre a administração de conta, incluindo definições de funções, consulte [como administrar a sua aplicação](howto-administer.md).
