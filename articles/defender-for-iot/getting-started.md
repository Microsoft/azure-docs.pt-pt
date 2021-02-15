---
title: Introdução
description: Começa por compreender o fluxo básico de trabalho para o Defender para a implantação de IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 06cf8b5f35bdebed0ebaa9e6949261d7c0d516f2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522568"
---
# <a name="get-started-with-defender-for-iot"></a>Começa com o Defender para o IoT

Este artigo fornece uma visão geral dos passos que você vai tomar para configurar o Azure Defender para IoT. O processo requer que:

- Registe a sua subscrição e sensores no portal Azure Defender para IoT.
- Instale o software de consola de gestão de sensores e instalações no local.
- Realize a ativação inicial do sensor e da consola de gestão.

## <a name="permission-requirements"></a>Requisitos de permissão

Algumas das etapas de configuração requerem permissões específicas do utilizador.

As permissões administrativas do utilizador são necessárias para ativar o sensor e consola de gestão, carregar certificados SSL/TLS e gerar novas palavras-passe.

A tabela que se segue descreve permissões de acesso ao Azure Defender para ferramentas do portal IoT:

| Permissão | Leitor de segurança | Administrador de segurança | Colaborador de subscrição | Proprietário de assinatura |
|--|--|--|--|--|
| Ver todos os ecrãs e dados do Defender para IoT | ✓ | ✓ | ✓ | ✓ |
| A bordo de um sensor  |  |  ✓ | ✓ | ✓ |
| Atualizar preços  |  |  ✓ | ✓ | ✓ |
| Recuperar senha  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. Identificar a infraestrutura de solução

**Clarifique as necessidades de configuração da sua rede**

Pesse a arquitetura da sua rede, largura de banda monitorizada e outros detalhes da rede. Para obter mais informações, consulte [Sobre o Azure Defender para a configuração da rede IoT](how-to-set-up-your-network.md).

**Clarificar quais os sensores e aparelhos de consola de gestão necessários para lidar com a carga da rede**

O Azure Defender for IoT suporta implementações físicas e virtuais. Para as implementações físicas, pode adquirir vários aparelhos certificados. Para obter mais informações, consulte [Identificar os aparelhos necessários.](how-to-identify-required-appliances.md)

Recomendamos que calcule o número aproximado de dispositivos que serão monitorizados. Mais tarde, quando registar a sua subscrição Azure no portal, ser-lhe-á pedido que introduza este número. Os números podem ser adicionados em intervalos de 1.000 segundos. O número de dispositivos monitorizados chama-se *dispositivos comprometidos.*

## <a name="2-register-with-azure-defender-for-iot"></a>2. Registe-se com o Azure Defender para ioT

As inscrições incluem:

- A bordo das suas assinaturas Azure para Defender para IoT.
- Definindo dispositivos comprometidos.
- Descarregando um ficheiro de ativação para a consola de gestão no local.

Para se registar:

1. Vá ao Azure Defender para o portal IoT.
1. Selecione **a subscrição a bordo**.
1. Na página **de Preços,** selecione uma subscrição ou crie uma nova e adicione o número de dispositivos comprometidos.
1. Selecione **o separador de consola de gestão de gestão de descarregamento e** guarde o ficheiro de ativação descarregado. Este ficheiro contém os dispositivos agregados comprometidos que definiu. O ficheiro será enviado para a consola de gestão após a iniciação inicial.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. Instale e instale a consola de gestão no local

Depois de adquirir o seu aparelho de consola de gestão no local:

- Descarregue o pacote ISO do portal Azure Defender para IoT.
- Instale o software.
- Ative e realize a configuração inicial da consola de gestão.

Para instalar e configurar:

1. **Selecione Começar** a partir do Portal Defender para IoT.
1. Selecione o **separador consola de gestão on-in.**
1. Escolha uma versão e selecione **Download**.
1. Instale o software de consola de gestão no local. Para mais informações, consulte [defender para a instalação IoT](how-to-install-software.md).
1. Ative e crie a consola de gestão. Para mais informações, consulte [Ativar e configurar a sua consola de gestão no local.](how-to-activate-and-set-up-your-on-premises-management-console.md)

## <a name="4-onboard-a-sensor"></a>4. A bordo de um sensor

A bordo de um sensor registando-o com o Azure Defender para IoT e descarregando um ficheiro de ativação de sensores:

1. Defina um nome de sensor e associe-o a uma subscrição.
1. Escolha um modo de gestão de sensores:

   - **Sensores ligados à nuvem**: A informação que os sensores detetam é apresentada na consola do sensor. Além disso, a informação de alerta é entregue através de um hub IoT e pode ser partilhada com outros serviços Azure, como a Azure Sentinel.

   - **Sensores geridos localmente**: A informação que os sensores detetam é exibida na consola do sensor. Se estiver a trabalhar numa rede com lacunas de ar e quiser uma visão unificada de toda a informação detetada por vários sensores geridos localmente, trabalhe com a consola de gestão no local. 

1. Descarregue um ficheiro de ativação de sensores.

Para obter mais informações, consulte [a bordo e gere os sensores no portal Defender para IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. Instale e instale o sensor

Descarregue o pacote ISO do portal Azure Defender para IoT, instale o software e instale o sensor.

1. **Selecione Começar** a partir do Portal Defender para IoT.
1. Selecione **Configurar o sensor**.
1. Escolha uma versão e selecione **Download**.
1. Instale o software do sensor. Para mais informações, consulte [defender para a instalação IoT](how-to-install-software.md).
1. Ative e instale o seu sensor. Para obter mais informações, consulte [iniciar sção e ativar um sensor.](how-to-activate-and-set-up-your-sensor.md)

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. Ligar os sensores a uma consola de gestão no local

Ligue os sensores à consola de gestão para garantir que:

- Os sensores enviam informações de alerta e inventário de dispositivos para a consola de gestão no local.

- A consola de gestão no local pode realizar backups de sensores, gerir alertas que os sensores detetam, investigar desconexões de sensores e realizar outras atividades em sensores conectados.

Recomendamos que agrupe vários sensores que monitorizem as mesmas redes numa zona. Ao fazê-lo, irá colidír informações recolhidas por vários sensores.

Para obter mais informações, consulte [os sensores de ligação à consola de gestão no local.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. Populate Azure Sentinel com informações de alerta (opcional)

Envie informações de alerta ao Azure Sentinel, configurando Azure Sentinel. Consulte [os seus dados do Defender para IoT ao Azure Sentinel](how-to-configure-with-sentinel.md).
## <a name="next-steps"></a>Passos seguintes

[Bem-vindo ao Azure Defender para ioT](overview.md)

[Azure Defender para arquitetura IoT](architecture.md)
