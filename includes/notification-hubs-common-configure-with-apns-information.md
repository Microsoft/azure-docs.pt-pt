---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081548"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Configurar um Hub de Notificação com informações de APNs

Nos **Serviços de Notificação**, selecione **a Apple** e siga os passos adequados com base na abordagem que escolheu anteriormente na secção Criar um Certificado para Centros de [Notificação.](#creating-a-certificate-for-notification-hubs)  

> [!NOTE]
> Utilize o Modo **De Produção** para **Aplicação** apenas se pretender enviar notificações push para utilizadores que compraram a sua aplicação na loja.

### <a name="option-1-using-a-p12-push-certificate"></a>OPÇÃO 1: Utilização de um certificado de pressão .p12

1. Selecione **Certificado**.

1. Selecione o ícone de ficheiro.

1. Selecione o ficheiro .p12 que exportou anteriormente e, em seguida, selecione **Abrir**.

1. Se necessário, especifique a palavra-passe correta.

1. Selecione o modo **Sandbox**.

1. Selecione **Guardar**.

### <a name="option-2-using-token-based-authentication"></a>OPÇÃO 2: Utilização da autenticação baseada em fichas

1. Selecione **Token**.
1. Insira os seguintes valores que adquiriu anteriormente:

    - **ID chave**
    - **ID do Pacote**
    - **ID da equipa**
    - **Token**

1. Escolha **a Caixa de Areia.**
1. Selecione **Guardar**.
