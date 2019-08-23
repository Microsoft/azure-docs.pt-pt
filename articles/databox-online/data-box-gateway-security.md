---
title: Segurança do Gateway do Azure Data Box | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem seu Gateway do Azure Data Box dispositivo virtual, serviço e dados, no local e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900604"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Gateway do Azure Data Box segurança e proteção de dados

A segurança é uma preocupação importante quando você está adotando uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. Gateway do Azure Data Box ajuda a garantir que apenas entidades autorizadas possam exibir, modificar ou excluir seus dados.

Este artigo descreve os Gateway do Azure Data Box recursos de segurança que ajudam a proteger cada um dos componentes da solução e os dados armazenados neles.

A solução Gateway do Data Box consiste em quatro componentes principais que interagem entre si:

- **Gateway do data box serviço, hospedado no Azure**. O recurso de gerenciamento que você usa para criar a ordem do dispositivo, configurar o dispositivo e acompanhar o pedido até a conclusão.
- **Gateway do data Box dispositivo**. O dispositivo virtual que você provisiona no hipervisor do sistema que você fornece. Esse dispositivo virtual é usado para importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infraestrutura que se conectam ao dispositivo de Gateway do Data Box e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. O local na plataforma de nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de Gateway do Data Box que você cria.


## <a name="data-box-gateway-service-protection"></a>Proteção de serviço do Gateway do Data Box

O serviço de Gateway do Data Box é um serviço de gerenciamento hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Proteção de dispositivo Gateway do Data Box

O dispositivo de Gateway do Data Box é um dispositivo virtual provisionado no hipervisor de um sistema local que você fornece. O dispositivo ajuda a enviar dados para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço de Data Box Edge/Gateway do Data Box.
- O é protegido sempre por uma senha de dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo Gateway do Data Box autorizado tem permissão para ingressar no serviço de Gateway do Data Box que você cria em sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço de Gateway do Data Box.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [obter uma chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

As senhas garantem que somente usuários autorizados possam acessar seus dados. Gateway do Data Box dispositivos inicializam em um estado bloqueado.

Pode:

- Conecte-se à interface do usuário da Web local do dispositivo por meio de um navegador e forneça uma senha para entrar no dispositivo.
- Conectar-se remotamente à interface do PowerShell do dispositivo por HTTP. O gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, consulte [conectar-se remotamente ao dispositivo gateway do data Box](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use a interface do usuário da Web local para [alterar a senha](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, lembre-se de notificar todos os usuários de acesso remoto para que eles não tenham problemas de entrada.


## <a name="protect-your-data"></a>Proteja os seus dados

Esta seção descreve os Gateway do Data Box recursos de segurança que protegem dados armazenados e em trânsito.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Gire e [sincronize suas chaves de conta de armazenamento](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente para ajudar a proteger sua conta de armazenamento de usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço de Gateway do Data Box coleta informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos no gateway do data Box](data-box-gateway-manage-shares.md).

Para obter mais informações, examine a política de privacidade da Microsoft na [central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

[Implantar o dispositivo Gateway do Data Box](data-box-gateway-deploy-prep.md)
