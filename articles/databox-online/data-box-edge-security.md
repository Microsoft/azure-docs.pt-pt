---
title: Segurança do Azure Data Box Edge | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem seu Azure Data Box Edge dispositivo, serviço e dados locais e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970890"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge segurança e proteção de dados

A segurança é uma preocupação importante quando você está adotando uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. Azure Data Box Edge ajuda a garantir que apenas entidades autorizadas possam exibir, modificar ou excluir seus dados.

Este artigo descreve os Data Box Edge recursos de segurança que ajudam a proteger cada um dos componentes da solução e os dados armazenados neles.

Azure Data Box Edge consiste em quatro componentes principais que interagem entre si:

- **Data Box Edge serviço, hospedado no Azure**. O recurso de gerenciamento que você usa para criar a ordem do dispositivo, configurar o dispositivo e acompanhar o pedido até a conclusão.
- **Data Box Edge dispositivo**. O dispositivo de transferência enviado para você para que você possa importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infraestrutura que se conectam ao dispositivo de Data Box Edge e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. O local na plataforma de nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de Data Box Edge que você cria.

## <a name="data-box-edge-service-protection"></a>Proteção de serviço do Data Box Edge

O serviço de Data Box Edge é um serviço de gerenciamento hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Proteção de dispositivo Data Box Edge

O dispositivo Data Box Edge é um dispositivo local que ajuda a transformar seus dados processando-os localmente e, em seguida, enviando-os para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço de Data Box Edge.
- O é protegido sempre por uma senha de dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS são protegidos por senha. O BIOS é protegido por acesso limitado ao usuário.
- Tem inicialização segura habilitada.
- Executa o Windows Defender Device Guard. A proteção de dispositivo permite que você execute somente aplicativos confiáveis que você define em suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo Data Box Edge autorizado tem permissão para ingressar no serviço de Data Box Edge que você cria em sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço de Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

As senhas garantem que somente usuários autorizados possam acessar seus dados. Data Box Edge dispositivos inicializam em um estado bloqueado.

Pode:

- Conecte-se à interface do usuário da Web local do dispositivo por meio de um navegador e forneça uma senha para entrar no dispositivo.
- Conecte-se remotamente à interface do dispositivo do PowerShell por HTTP. O gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, consulte [conectar-se remotamente ao dispositivo data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use a interface do usuário da Web local para [alterar a senha](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, lembre-se de notificar todos os usuários de acesso remoto para que eles não tenham problemas de entrada.

## <a name="protect-your-data"></a>Proteja os seus dados

Esta seção descreve os Data Box Edge recursos de segurança que protegem dados armazenados e em trânsito.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- O BitLocker XTS-criptografia de 256 bits é usado para proteger dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Gire e [sincronize suas chaves de conta de armazenamento](data-box-edge-manage-shares.md#sync-storage-keys) regularmente para ajudar a proteger sua conta de armazenamento de usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço de Data Box Edge coleta informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos no data Box Edge](data-box-edge-manage-shares.md).

Para obter mais informações, examine a política de privacidade da Microsoft na [central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos Seguintes

[Implantar o dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
