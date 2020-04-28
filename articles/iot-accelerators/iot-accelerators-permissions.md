---
title: Utilize o site Azure IoT Solutions - Azure [ Azure ] Microsoft Docs
description: Descreve como usar o website AzureIoTSolutions.com para implementar o seu acelerador de soluções.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61447459"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Utilize o site azureiotsolutions.com para implementar o seu acelerador de soluções

Pode implantar aceleradores de soluções Azure IoT para a sua assinatura Azure a partir de [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com acolhe tanto os aceleradores de código aberto da Microsoft como os aceleradores de soluções de parceiro. Estes aceleradores de solução alinham-se com a [Arquitetura de Referência Azure IoT.](https://aka.ms/iotrefarchitecture) Pode utilizar o site para implementar rapidamente um acelerador de soluções como uma demonstração ou ambiente de produção.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Se necessitar de mais controlo sobre o processo de implantação, pode utilizar o [CLI para implantar um acelerador](iot-accelerators-remote-monitoring-deploy-cli.md)de solução .

Pode implantar os aceleradores de solução nas seguintes configurações:

* **Norma**: Uma implantação de infraestruturas alargada para o desenvolvimento de um ambiente de produção. O Serviço de Contentores Azure implanta os microserviços em várias máquinas virtuais Azure. O Kubernetes orquestra os contentores do Docker que alojam os microsserviços individuais.
* **Básico**: Uma versão de custo reduzido para uma demonstração ou para testar uma implantação. Todos os microsserviços são implementados numa máquina virtual do Azure individual.
* **Local**: Uma implantação local de máquinas para testes e desenvolvimento. Esta abordagem implanta os microserviços para um contentor local do Docker e liga-se aos serviços de armazenamento IoT Hub, Azure Cosmos DB e Azure na nuvem.

Cada um dos aceleradores de solução usa uma combinação diferente de serviços Azure como IoT Hub, Azure Stream Analytics e Cosmos DB. Para mais informações, visite [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) e selecione um acelerador de soluções.

## <a name="sign-in-at-azureiotsolutionscom"></a>Inscreva-se no azureiotsolutions.com

Antes de poder implementar um acelerador de soluções, tem de iniciar sessão em AzureIoTSolutions.com utilizando credenciais associadas a uma subscrição azure. Se a sua conta estiver associada a mais de um inquilino do Microsoft Azure Ative Directory (AD), pode utilizar a desistência da **seleção da Conta** para escolher o diretório para utilizar.

As suas permissões para implementar aceleradores de soluções, gerir utilizadores e gerir os serviços do Azure dependem do seu papel no diretório selecionado. As funções da AD Azure Comum associadas aos aceleradores de solução incluem:

* **Administrador global**: Pode haver muitos [administradores globais](../active-directory/users-groups-roles/directory-assign-admin-roles.md) por inquilino da AD Azure:

  * Quando se cria um inquilino da AD Azure, é por defeito o administrador global desse inquilino.
  * O administrador global pode implementar aceleradores básicos e de soluções padrão.

* **Utilizador de domínio**: Pode haver muitos utilizadores de domínio por inquilino Azure AD. Um utilizador de domínio pode implementar um acelerador de solução básica.

* **Utilizador convidado**: Pode haver muitos utilizadores convidados por inquilino Da Azure AD. Os utilizadores convidados não podem implementar um acelerador de soluções no inquilino da AD Azure.

Para obter mais informações sobre utilizadores e funções em Azure AD, consulte os seguintes recursos:

* [Criar utilizadores no Diretório Ativo do Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Atribuir utilizadores a apps](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Escolha o seu dispositivo

O site AzureIoTSolutions.com liga-se ao catálogo de [dispositivos Azure Certified para IoT](https://catalog.azureiotsolutions.com/).

O catálogo lista centenas de dispositivos de hardware IoT certificados que pode ligar aos seus aceleradores de soluções para começar a construir a sua solução IoT.

![Catálogo de dispositivos](media/iot-accelerators-permissions/devicecatalog.png)

Se for um fabricante de hardware, clique em **Tornar-se um Parceiro** para aprender sobre a parceria com a Microsoft no programa Certified for IoT.

## <a name="next-steps"></a>Passos seguintes

Para experimentar um dos aceleradores de soluções de IoT, veja os inícios rápidos:

* [Tentar uma solução de monitorização remota](quickstart-remote-monitoring-deploy.md)
* [Tentar uma solução de fábrica ligada](quickstart-connected-factory-deploy.md)
* [Try a predictive maintenance solution](quickstart-predictive-maintenance-deploy.md) (Experimentar uma solução de manutenção preditiva)
* [Try a device simulation solution](quickstart-device-simulation-deploy.md) (Testar uma solução de simulação de dispositivos)
