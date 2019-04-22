---
title: Segurança de borda de caixa de dados do Azure | Documentos da Microsoft
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo Edge de caixa de dados do Azure, serviço e dados no local e na cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682105"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Proteção do Azure de dados e segurança de borda de caixa de dados

A segurança é uma preocupação importante quando uma nova tecnologia, especialmente se a tecnologia é utilizada com dados confidenciais ou proprietários. Solução de ponta de caixa de dados do Microsoft Azure ajuda a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve os recursos de segurança de borda de caixa de dados que ajudam a proteger todos os componentes da solução e os dados armazenados nos mesmos.

A solução de borda de caixa de dados do Azure consiste em quatro componentes principais que interagem entre si:

- **Serviço de borda de caixa de dados alojado no Azure** – o recurso de gestão que utiliza para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até à conclusão.
- **Dispositivo de limite de caixa de dados** – o dispositivo de transferência que é enviado para si para importar os dados no local para o Azure.
- **Os clientes/anfitriões ligados ao dispositivo** – os clientes na sua infraestrutura de que se ligam para o dispositivo de limite de caixa de dados e contêm dados que precisam ser protegido.
- **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados. Esta localização é, normalmente, a conta de armazenamento ligada para o recurso de borda de caixa de dados que criou.

## <a name="data-box-edge-service-protection"></a>Proteção de serviço de borda de caixa de dados

O serviço de dados caixa Edge é um serviço de gestão alojado no Microsoft Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Proteção de dispositivos do Edge de caixa de dados

O dispositivo de limite de caixa de dados é um dispositivo no local que ajuda a transformar os dados por processá-lo localmente e, em seguida, enviá-la para o Azure. O dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço de borda de caixa de dados.
- É protegida sempre por uma palavra-passe do dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS estiver protegido por palavra-passe com acesso de usuário limitado para o BIOS.
- Tem o arranque seguro ativado.
- Executa o Windows Defender o Device Guard. O Device Guard permite que execute apenas aplicações fidedignas que define nas suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo através de chave de ativação

Apenas um dispositivo de limite de caixa de dados autorizado tem permissão para associar o serviço de borda de caixa de dados que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de utilizar uma chave de ativação para ativar o dispositivo com o serviço de borda de caixa de dados.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, aceda a [obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo através de palavra-passe

As palavras-passe Certifique-se de que os dados estão acessíveis aos utilizadores autorizados apenas. Dados caixa Edge dispositivos arranque no estado bloqueado.

Pode:

- Ligar à IU do dispositivo através de um browser da web local e, em seguida, forneça uma palavra-passe para iniciar sessão no dispositivo.
- Ligar remotamente à interface do PowerShell do dispositivo através de HTTP. Gestão remota está ativada por predefinição. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sessão no dispositivo. Para obter mais informações, aceda a [ligar remotamente ao seu dispositivo Edge de caixa de dados](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilização da interface do Usuário para o local da web [alterar a palavra-passe](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de que todos os utilizadores de acesso remoto de notificar para que eles não se deparam com um falha de início de sessão.

## <a name="protect-the-data"></a>Proteger os dados

Esta secção descreve as funcionalidades de segurança de borda de caixa de dados que protegem os dados em trânsito e dados armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rodar e, em seguida [sincronizar as chaves de conta de armazenamento](data-box-edge-manage-shares.md#sync-storage-keys) regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço de borda de caixa de dados recolhe as informações pessoais nas seguintes instâncias principais:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou eliminar uma partilha, siga os passos em [gerir partilhas no limite da caixa de dados](data-box-edge-manage-shares.md).

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos Seguintes

[Implementar o dispositivo do Edge de caixa de dados](data-box-edge-deploy-prep.md).
