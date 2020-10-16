---
title: Segurança Azure Stack Edge Pro Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo, serviço e dados Azure Stack Edge Pro e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b5c5143c7bc92487aeabc3cdc1afea5061466027
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903166"
---
# <a name="azure-stack-edge-pro-security-and-data-protection"></a>Segurança e proteção de dados Azure Stack Edge Pro

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. O Azure Stack Edge Pro ajuda-o a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança Azure Stack Edge Pro que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

Azure Stack Edge Pro é composto por quatro componentes principais que interagem entre si:

- **Serviço Azure Stack Edge, hospedado em Azure**. O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem para a conclusão.
- **Dispositivo Azure Stack Edge Pro**. O dispositivo de transferência que lhe foi enviado para que possa importar os seus dados no local para o Azure.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo Azure Stack Edge Pro e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem**. A localização na plataforma cloud Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Azure Stack Edge que cria.

## <a name="azure-stack-edge-service-protection"></a>Proteção de serviço Azure Stack Edge

O serviço Azure Stack Edge é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-pro-device-protection"></a>Proteção do dispositivo Azure Stack Edge Pro

O dispositivo Azure Stack Edge Pro é um dispositivo no local que ajuda a transformar os seus dados processando-os localmente e enviando-os depois para o Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Azure Stack Edge.
- Está sempre protegido por uma palavra-passe do dispositivo.
- É um dispositivo fechado. O dispositivo BMC e BIOS estão protegidos por palavra-passe. O BIOS está protegido por acesso limitado ao utilizador.
- Tem a bota segura ativada.
- Executa o Windows Defender Device Guard. O Device Guard permite-lhe executar apenas aplicações fidedignas que define nas suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo autorizado Azure Stack Edge Pro é autorizado a aderir ao serviço Azure Stack Edge que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, [consulte obter uma chave de ativação.](azure-stack-edge-deploy-prep.md#get-the-activation-key)

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Azure Stack Edge Pro iniciam-se num estado bloqueado.

Pode:

- Ligue-se à UI web local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sôm no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo em HTTP. A gestão remota é ativada por defeito. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sôm no dispositivo. Para obter mais informações, consulte [Ligar-se remotamente ao seu dispositivo Azure Stack Edge Pro](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilize o UI web local para [alterar a palavra-passe.](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access) Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.

## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança Azure Stack Edge Pro que protegem os dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A encriptação BitLocker XTS-AES de 256 bits é usada para proteger os dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rode e, em seguida, [sincronize](azure-stack-edge-manage-shares.md#sync-storage-keys) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Azure Stack Edge recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou apagar uma partilha, siga os passos em [Gerir ações no Azure Stack Edge Pro](azure-stack-edge-manage-shares.md).

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
