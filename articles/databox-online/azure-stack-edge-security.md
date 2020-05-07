---
title: Segurança Azure Stack Edge [ segurança Azure Stack Edge ] Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo, serviço e dados Azure Stack Edge no local e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570073"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Segurança e proteção de dados Azure Stack Edge

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. O Azure Stack Edge ajuda-o a garantir que apenas entidades autorizadas podem visualizar, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança Azure Stack Edge que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

Azure Stack Edge é composto por quatro componentes principais que interagem entre si:

- **Serviço Azure Stack Edge, hospedado em Azure.** O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem até à conclusão.
- **Dispositivo Azure Stack Edge**. O dispositivo de transferência que lhe foi enviado para que possa importar os seus dados no local para o Azure.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo Azure Stack Edge e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem.** A localização na plataforma de nuvem Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Azure Stack Edge que cria.

## <a name="azure-stack-edge-service-protection"></a>Proteção de serviço Azure Stack Edge

O serviço Azure Stack Edge é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Proteção do dispositivo Azure Stack Edge

O dispositivo Azure Stack Edge é um dispositivo no local que ajuda a transformar os seus dados processando-os localmente e enviando-os depois para o Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Azure Stack Edge.
- Está sempre protegido por uma senha do dispositivo.
- É um dispositivo fechado. O dispositivo BMC e BIOS estão protegidos por palavra-passe. O BIOS está protegido por acesso limitado ao utilizador.
- Tem uma bota segura ativada.
- Executa o Guarda de Dispositivos do Windows Defender. O Device Guard permite-lhe executar apenas aplicações fidedignas que define nas suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo Azure Stack Edge autorizado é autorizado a aderir ao serviço Azure Stack Edge que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para mais informações, consulte [Obter uma tecla de ativação](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Azure Stack Edge arrancam em estado fechado.

Pode:

- Ligue-se à Web UI local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sessão no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo através de HTTP. A gestão remota é ativada por defeito. Em seguida, pode fornecer a senha do dispositivo para iniciar sessão no dispositivo. Para mais informações, consulte [Connect remotamente ao seu dispositivo Azure Stack Edge](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilize a UI web local para [alterar a palavra-passe](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.

## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança Azure Stack Edge que protegem dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A encriptação bitLocker XTS-AES de 256 bits é usada para proteger os dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteja os dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rode e, em seguida, [sincronize](azure-stack-edge-manage-shares.md#sync-storage-keys) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Azure Stack Edge recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou apagar uma parte, siga os passos em [Gerir as ações no Azure Stack Edge](azure-stack-edge-manage-shares.md).

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Azure Stack Edge](azure-stack-edge-deploy-prep.md)
