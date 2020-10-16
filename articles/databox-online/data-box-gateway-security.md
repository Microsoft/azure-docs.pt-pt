---
title: Segurança Azure Data Box Gateway / Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o dispositivo virtual, serviço e dados do Azure Data Box Gateway, no local e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 45506b87bf13b44e035655dbdf0fac03ea50612c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893867"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Segurança e proteção de dados da Caixa de Dados Azure

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. O Azure Data Box Gateway ajuda-o a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança Azure Data Box Gateway que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

A solução Data Box Gateway consiste em quatro componentes principais que interagem entre si:

- **Serviço Data Box Gateway, hospedado em Azure**. O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem para a conclusão.
- **Dispositivo Data Box Gateway**. O dispositivo virtual que fornece no hipervisor do sistema que fornece. Este dispositivo virtual é utilizado para importar os seus dados no local para o Azure.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo Data Box Gateway e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem**. A localização na plataforma cloud Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Data Box Gateway que cria.

## <a name="data-box-gateway-service-protection"></a>Proteção de serviço gateway de caixa de dados

O serviço Data Box Gateway é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Proteção do dispositivo Data Box Gateway

O dispositivo Data Box Gateway é um dispositivo virtual que está previsto no hipervisor de um sistema no local que fornece. O dispositivo ajuda a enviar dados para o Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Azure Stack Edge Pro/Data Box Gateway.
- Está sempre protegido por uma palavra-passe do dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo autorizado Data Box Gateway pode aderir ao serviço Data Box Gateway que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, [consulte obter uma chave de ativação.](data-box-gateway-deploy-prep.md#get-the-activation-key)

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Data Box Gateway iniciam-se num estado bloqueado.

Pode:

- Ligue-se à UI web local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sôm no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo em HTTP. A gestão remota é ativada por defeito. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sôm no dispositivo. Para obter mais informações, consulte [Ligar-se remotamente ao seu dispositivo Data Box Gateway](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utilize o UI web local para [alterar a palavra-passe.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.

## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança do Data Box Gateway que protegem os dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Proteger dados usando contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]

- Rode e, em seguida, [sincronize](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

### <a name="protect-the-device-data-using-bitlocker"></a>Proteja os dados do dispositivo utilizando o BitLocker

Para proteger os discos virtuais na sua máquina virtual Data Box Gateway, recomendamos que ative o BitLocker. Por predefinição, o BitLocker não está ativado. Para obter mais informações, consulte:

- [Definições de suporte de encriptação no Hyper-V Manager](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [Suporte BitLocker em uma máquina virtual](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Data Box Gateway recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou apagar uma partilha, siga os passos em [Gerir ações no Portal da Caixa de Dados.](data-box-gateway-manage-shares.md)

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Data Box Gateway](data-box-gateway-deploy-prep.md)
