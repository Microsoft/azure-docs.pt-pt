---
title: Segurança Azure Data Box Edge [ segurança De Dados De Dados ] Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo, serviço e dados Azure Data Box Edge e dados no local e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69970890"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Proteção de dados e segurança do Azure Data Box Edge

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. O Azure Data Box Edge ajuda-o a garantir que apenas entidades autorizadas podem visualizar, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança data Box Edge que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

O Azure Data Box Edge é composto por quatro componentes principais que interagem entre si:

- **Serviço Data Box Edge, hospedado em Azure.** O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem até à conclusão.
- **Dispositivo Data Box Edge**. O dispositivo de transferência que lhe foi enviado para que possa importar os seus dados no local para o Azure.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo Data Box Edge e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem.** A localização na plataforma de nuvem Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Data Box Edge que cria.

## <a name="data-box-edge-service-protection"></a>Proteção de serviço Data Box Edge

O serviço Data Box Edge é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Proteção do dispositivo Data Box Edge

O dispositivo Data Box Edge é um dispositivo no local que ajuda a transformar os seus dados processando-os localmente e enviando-os depois para o Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Data Box Edge.
- Está sempre protegido por uma senha do dispositivo.
- É um dispositivo fechado. O dispositivo BMC e BIOS estão protegidos por palavra-passe. O BIOS está protegido por acesso limitado ao utilizador.
- Tem uma bota segura ativada.
- Executa o Guarda de Dispositivos do Windows Defender. O Device Guard permite-lhe executar apenas aplicações fidedignas que define nas suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo Data Box Edge autorizado é autorizado a aderir ao serviço Data Box Edge que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para mais informações, consulte [Obter uma tecla de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Data Box Edge arrancam em estado fechado.

Pode:

- Ligue-se à Web UI local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sessão no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo através de HTTP. A gestão remota é ativada por defeito. Em seguida, pode fornecer a senha do dispositivo para iniciar sessão no dispositivo. Para mais informações, consulte [Connect remotamente ao seu dispositivo Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilize a UI web local para [alterar a palavra-passe](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.

## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança data Box Edge que protegem os dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A encriptação bitLocker XTS-AES de 256 bits é usada para proteger os dados locais.


### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteja os dados através de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rode e, em seguida, [sincronize](data-box-edge-manage-shares.md#sync-storage-keys) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Data Box Edge recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para visualizar a lista de utilizadores que podem aceder ou apagar uma parte, siga os passos em [Gerir as ações no Data Box Edge](data-box-edge-manage-shares.md).

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
