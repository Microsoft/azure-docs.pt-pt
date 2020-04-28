---
title: Segurança Azure Data Box Gateway [ Segurança De Porta de Dados Azure ] Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo virtual, serviço e dados do Azure Data Box Gateway, no local e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69900604"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Segurança e proteção de dados da Caixa de Dados Azure

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. O Portal da Caixa de Dados Azure ajuda-o a garantir que apenas entidades autorizadas podem visualizar, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança do Portal de Dados Azure que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

A solução Data Box Gateway consiste em quatro componentes principais que interagem entre si:

- **Serviço Data Box Gateway, hospedado em Azure.** O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem até à conclusão.
- **Dispositivo Gateway data Box**. O dispositivo virtual que fornece no hipervisor do sistema que fornece. Este dispositivo virtual é utilizado para importar os seus dados no local para o Azure.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo Data Box Gateway e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem.** A localização na plataforma de nuvem Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Data Box Gateway que cria.


## <a name="data-box-gateway-service-protection"></a>Proteção de serviço Data Box Gateway

O serviço Data Box Gateway é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Proteção do dispositivo Data Box Gateway

O dispositivo Data Box Gateway é um dispositivo virtual que é aprovisionado no hipervisor de um sistema no local que fornece. O dispositivo ajuda a enviar dados para o Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Data Box Edge/Data Box Gateway.
- Está sempre protegido por uma senha do dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo Data Box Gateway autorizado é autorizado a aderir ao serviço Data Box Gateway que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Para mais informações, consulte [Obter uma tecla de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Data Box Gateway arrancam em estado fechado.

Pode:

- Ligue-se à Web UI local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sessão no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo através do HTTP. A gestão remota é ativada por defeito. Em seguida, pode fornecer a senha do dispositivo para iniciar sessão no dispositivo. Para mais informações, consulte [Connect remotamente ao seu dispositivo Data Box Gateway](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilize a UI web local para [alterar a palavra-passe](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.


## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança data Box Gateway que protegem os dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteja os dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rode e, em seguida, [sincronize](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Data Box Gateway recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para visualizar a lista de utilizadores que podem aceder ou apagar uma parte, siga os passos em [Gerir as ações no Portal da Caixa](data-box-gateway-manage-shares.md)de Dados .

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Gateway data box](data-box-gateway-deploy-prep.md)
