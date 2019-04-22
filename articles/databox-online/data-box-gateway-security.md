---
title: Segurança de Gateway de caixa de dados do Azure | Documentos da Microsoft
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo virtual de Gateway de caixa de dados do Azure, serviço e dados no local e na cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685904"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Proteção do Azure de segurança e os dados de Gateway de caixa de dados

A segurança é uma preocupação importante quando uma nova tecnologia, especialmente se a tecnologia é utilizada com dados confidenciais ou proprietários. Solução de Gateway de caixa de dados do Microsoft Azure ajuda a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve os recursos de segurança do Gateway de caixa de dados do Azure que ajudam a proteger todos os componentes da solução e os dados armazenados nos mesmos.

A solução de Gateway de caixa de dados consiste em quatro componentes principais que interagem entre si:

- **Serviço de Gateway de caixa de dados alojado no Azure** – o recurso de gestão que utiliza para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até à conclusão.
- **Dispositivo de Gateway de caixa de dados** – o dispositivo virtual que aprovisionou no hipervisor do sistema que forneceu. Este dispositivo virtual é utilizado para importar os dados no local para o Azure.
- **Os clientes/anfitriões ligados ao dispositivo** – os clientes na sua infraestrutura de que se ligam ao dispositivo de Gateway de caixa de dados e contêm dados que precisam ser protegido.
- **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados. Esta localização é, normalmente, a conta de armazenamento ligada para o recurso de Gateway de caixa de dados que criou.


## <a name="data-box-gateway-service-protection"></a>Proteção de serviço de Gateway de caixa de dados

O serviço de Gateway de caixa de dados é um serviço de gestão alojado no Microsoft Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Proteção de dispositivos de Gateway de caixa de dados

O dispositivo de Gateway de caixa de dados é um dispositivo virtual aprovisionado no hipervisor de um sistema no local que indicar. O dispositivo ajuda a enviar dados para o Azure. O dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço de Gateway de caixa de Edge/dados de caixa de dados.
- É protegida sempre por uma palavra-passe do dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo através de chave de ativação

Apenas um dispositivo de Gateway de caixa de dados autorizado tem permissão para associar o serviço de Gateway de caixa de dados que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de utilizar uma chave de ativação para ativar o dispositivo com o serviço de Gateway de caixa de dados.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, aceda a [obter uma chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo através de palavra-passe

As palavras-passe Certifique-se de que os dados estão acessíveis aos utilizadores autorizados apenas. Dados caixa Gateway dispositivos arranque no estado bloqueado.

Pode:

- Ligar à IU do dispositivo através de um browser da web local e, em seguida, forneça uma palavra-passe para iniciar sessão no dispositivo.
- Ligar remotamente à interface do PowerShell do dispositivo através de HTTP. Gestão remota está ativada por predefinição. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sessão no dispositivo. Para obter mais informações, aceda a [ligar remotamente ao seu dispositivo de Gateway de caixa de dados](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilização da interface do Usuário para o local da web [alterar a palavra-passe](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de que todos os utilizadores de acesso remoto de notificar para que eles não se deparam com um falha de início de sessão.


## <a name="protect-the-data"></a>Proteger os dados

Esta secção descreve as funcionalidades de segurança do Gateway de caixa de dados que protegem os dados em trânsito e dados armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rodar e, em seguida [sincronizar as chaves de conta de armazenamento](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço de Gateway de caixa de dados recolhe as informações pessoais nas seguintes instâncias principais:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou eliminar uma partilha, siga os passos em [gerir partilhas no Gateway de caixa de dados](data-box-gateway-manage-shares.md).

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos Seguintes

[Implementar o dispositivo de Gateway de caixa de dados](data-box-gateway-deploy-prep.md).
