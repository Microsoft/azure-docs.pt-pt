---
title: Segurança Azure Stack Edge Pro R | Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem os seus dispositivos Azure Stack Edge Pro R e Azure Stack Edge Mini R, serviço e dados no local e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: bd90a16c09dce65115cea2f097d18f2e0ced931a
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632038"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Segurança e proteção de dados para Azure Stack Edge Pro R e Azure Stack Edge Mini R

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

A segurança é uma grande preocupação quando se está a adotar uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. Azure Stack Edge Pro R e Azure Stack Edge Mini R ajudam-no a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve as funcionalidades de segurança Azure Stack Edge Pro R e Azure Stack Edge Mini R que ajudam a proteger cada um dos componentes da solução e os dados armazenados nos mesmos.

A solução consiste em quatro componentes principais que interagem entre si:

- **Serviço Azure Stack Edge, hospedado na nuvem pública de Azure ou Azure Government**. O recurso de gestão que utiliza para criar a ordem do dispositivo, configurar o dispositivo e, em seguida, rastrear a ordem para a conclusão.
- **Dispositivo robusto Azure Stack Edge**. O dispositivo físico robusto que lhe é enviado para que possa importar os seus dados no local para o público do Azure ou para a nuvem do Governo Azure. O dispositivo pode ser Azure Stack Edge Pro R ou Azure Stack Edge Mini R.
- **Clientes/anfitriões ligados ao dispositivo**. Os clientes da sua infraestrutura que se ligam ao dispositivo e contêm dados que precisam de ser protegidos.
- **Armazenamento em nuvem**. A localização na plataforma cloud Azure onde os dados são armazenados. Esta localização é tipicamente a conta de armazenamento ligada ao recurso Azure Stack Edge que cria.

## <a name="service-protection"></a>Proteção de serviços

O serviço Azure Stack Edge é um serviço de gestão que está hospedado no Azure. O serviço é utilizado para configurar e gerir o dispositivo.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Proteção de dispositivos

O dispositivo robusto é um dispositivo no local que ajuda a transformar os seus dados processando-os localmente e enviando-os para a Azure. O seu dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço Azure Stack Edge.
- Está sempre protegido por uma palavra-passe do dispositivo.
- É um dispositivo fechado. O controlador de gestão do quadro de base do dispositivo (BMC) e o BIOS estão protegidos por palavra-passe. O BMC está protegido por acesso limitado ao utilizador.
- Tem uma bota segura ativada que garante que o dispositivo arranca apenas utilizando o software fidedigno fornecido pela Microsoft.
- Executa o Controlo de Aplicações do Windows Defender (WDAC). O WDAC permite-lhe executar apenas aplicações fidedignas que define nas suas políticas de integridade de código.
- Possui um Módulo de Plataforma Fidedigna (TPM) que executa funções baseadas em hardware e relacionadas com a segurança. Especificamente, o TPM gere e protege segredos e dados que precisam de ser persistidos no dispositivo.
- Apenas as portas necessárias são abertas no aparelho e todas as outras portas estão bloqueadas. Para mais informações, consulte a lista dos requisitos do [Porto para o dispositivo.](azure-stack-edge-pro-r-system-requirements.md)
- Todo o acesso ao hardware do dispositivo, bem como ao software, está registado. 
    - Para o software do dispositivo, são recolhidos registos de firewall predefinidos para o tráfego de entrada e saída do dispositivo. Estes registos estão agregados no pacote de suporte.
    - Para o hardware do dispositivo, todos os eventos do chassis do dispositivo, como a abertura e fecho do chassis do dispositivo, estão registados no dispositivo.

    Para obter mais informações sobre os registos específicos que contêm os eventos de intrusão de hardware e software e como obter os registos, vá a [Recolher registos de segurança avançados](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Proteja o dispositivo através da chave de ativação

Apenas um dispositivo autorizado Azure Stack Edge Pro R ou Azure Stack Edge Mini R é autorizado a aderir ao serviço Azure Stack Edge que cria na sua subscrição Azure. Para autorizar um dispositivo, é necessário utilizar uma chave de ativação para ativar o dispositivo com o serviço Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Para obter mais informações, [consulte obter uma chave de ativação.](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)

### <a name="protect-the-device-via-password"></a>Proteja o dispositivo através de senha

As palavras-passe garantem que apenas os utilizadores autorizados podem aceder aos seus dados. Os dispositivos Azure Stack Edge Pro R iniciam-se num estado bloqueado.

Pode:

- Ligue-se à UI web local do dispositivo através de um browser e, em seguida, forneça uma senha para iniciar sôm no dispositivo.
- Ligue-se remotamente à interface PowerShell do dispositivo em HTTP. A gestão remota é ativada por defeito. A gestão remota também está configurada para usar a Just Enough Administration (JEA) para limitar o que os utilizadores podem fazer. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sôm no dispositivo. Para obter mais informações, consulte [Ligar-se remotamente ao seu dispositivo.](azure-stack-edge-gpu-connect-powershell-interface.md)
- O utilizador local edge do dispositivo tem acesso limitado ao dispositivo para configuração inicial e resolução de problemas. As cargas de trabalho computacional que estão a decorrer no dispositivo, a transferência de dados e o armazenamento podem ser acedidos a partir do portal público ou governamental do Azure para o recurso na nuvem.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Utilize o UI web local para [alterar a palavra-passe.](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password) Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não tenham problemas em iniciar sessão.

### <a name="establish-trust-with-the-device-via-certificates"></a>Estabelecer confiança com o dispositivo através de certificados

O dispositivo robusto Azure Stack Edge permite-lhe trazer os seus próprios certificados e instalá-los para serem utilizados para todos os pontos finais públicos. Para mais informações, aceda ao [Upload do seu certificado.](azure-stack-edge-gpu-manage-certificates.md#upload-certificates) Para obter uma lista de todos os certificados que podem ser instalados no seu dispositivo, aceda a [Obter certificados no seu dispositivo](azure-stack-edge-gpu-manage-certificates.md).

- Quando configurar o cálculo no seu dispositivo, é criado um dispositivo IoT e um dispositivo IoT Edge. Estes dispositivos são automaticamente atribuídos chaves de acesso simétrico. Como uma boa prática de segurança, estas chaves são rodadas regularmente através do serviço IoT Hub.

## <a name="protect-your-data"></a>Proteger os seus dados

Esta secção descreve as funcionalidades de segurança que protegem os dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger os dados inativos

Todos os dados em repouso no dispositivo são encriptados duplamente, o acesso aos dados é controlado e uma vez que o dispositivo é desativado, os dados são apagados de forma segura dos discos de dados.

#### <a name="double-encryption-of-data"></a>Dupla encriptação de dados

Os dados dos seus discos estão protegidos por duas camadas de encriptação:

- A primeira camada de encriptação é a encriptação BitLocker XTS-AES de 256 bits nos volumes de dados.
- A segunda camada são os discos rígidos que têm uma encriptação incorporada.
- O volume DE SO tem BitLocker como a única camada de encriptação.

> [!NOTE]
> O disco OS tem uma única camada bitLocker XTS-AES-256 encriptação de software.

Quando o dispositivo é ativado, é-lhe pedido que guarde um ficheiro chave que contenha chaves de recuperação que ajudam a recuperar os dados do dispositivo se o dispositivo não arrancar. Há duas chaves no ficheiro:

- Uma chave recupera a configuração do dispositivo nos volumes de SO.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- A segunda chave desbloqueia a encriptação de hardware nos discos de dados.

> [!IMPORTANT]
> Guarde o ficheiro chave num local seguro fora do próprio dispositivo. Se o dispositivo não arrancar e não tiver a chave, pode potencialmente resultar em perda de dados.

- Certos cenários de recuperação irão levá-lo para o ficheiro chave que guardou. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Acesso restrito aos dados

O acesso aos dados armazenados em ações e contas de armazenamento é restrito.

- Os clientes SMB que acedam a dados de partilha precisam de credenciais de utilizador associadas à partilha. Estas credenciais são definidas quando a partilha é criada.
- Os clientes NFS que acedam a uma ação precisam de ter o seu endereço IP adicionado explicitamente quando a ação é criada.
- As contas de armazenamento Edge criadas no dispositivo são locais e estão protegidas pela encriptação nos discos de dados. As contas de armazenamento Azure para as contas de armazenamento Edge para as que estas contas de armazenamento Edge estão mapeadas estão protegidas por subscrição e duas chaves de acesso ao armazenamento de 512 bits associadas à conta de armazenamento Edge (estas chaves são diferentes das associadas às suas contas de Armazenamento Azure). Para obter mais informações, consulte [Protect datas nas contas de armazenamento.](#protect-data-in-storage-accounts)
- A encriptação BitLocker XTS-AES de 256 bits é usada para proteger os dados locais.

#### <a name="secure-data-erasure"></a>Apagamento de dados seguros

Quando o dispositivo sofre um reset duro, é executada uma limpeza segura no dispositivo. A limpeza segura executa o apagamento de dados nos discos utilizando a purga NIST SP 800-88r1.

### <a name="protect-data-in-flight"></a>Proteger dados em voo

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Proteger dados em contas de armazenamento

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Rode e, em seguida, [sincronize](azure-stack-edge-gpu-manage-storage-accounts.md) regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

## <a name="manage-personal-information"></a>Gerir informações pessoais

O serviço Azure Stack Edge recolhe informações pessoais nos seguintes cenários:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Para ver a lista de utilizadores que podem aceder ou apagar uma partilha, siga os passos em [Gerir ações no Azure Stack Edge](azure-stack-edge-gpu-manage-shares.md).

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-prep.md)
