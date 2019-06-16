---
title: Utilize o site de soluções de IoT do Azure - Azure | Documentos da Microsoft
description: Descreve como utilizar o site de AzureIoTSolutions.com para implementar o acelerador de soluções.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447459"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Utilizar o site de azureiotsolutions.com para implementar o acelerador de soluções

Pode implementar os Aceleradores de solução de IoT do Azure à sua subscrição do Azure partir [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com hospeda o código aberto da Microsoft e os Aceleradores de solução de parceiro. Estes aceleradores de solução que se alinham com o [arquitetura de referência do Azure IoT](https://aka.ms/iotrefarchitecture). Pode utilizar o site para implementar rapidamente um acelerador de solução como um ambiente de demonstração ou de produção.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Se precisar de mais controlo sobre o processo de implementação, pode utilizar o [CLI para implementar um acelerador de solução](iot-accelerators-remote-monitoring-deploy-cli.md).

Pode implantar Aceleradores de soluções nas seguintes configurações:

* **Padrão**: Uma implementação de infraestrutura expandida para o desenvolvimento de um ambiente de produção. O Azure Container Service implementa os microsserviços em várias máquinas virtuais do Azure. O Kubernetes orquestra os contentores do Docker que alojam os microsserviços individuais.
* **Básico**: Uma versão de custo reduzido para demonstração ou para testar uma implementação. Todos os microsserviços são implementados numa máquina virtual do Azure individual.
* **Local**: Uma implementação de computador local para teste e desenvolvimento. Esta abordagem implementa os microsserviços para um contentor do Docker local e liga-se para o IoT Hub do Azure Cosmos DB e os serviços de armazenamento do Azure na cloud.

Cada um dos Aceleradores de solução utiliza uma combinação diferente de serviços do Azure, como o IoT Hub, o Azure Stream Analytics e o Cosmos DB. Para obter mais informações, visite [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) e selecione um acelerador de solução.

## <a name="sign-in-at-azureiotsolutionscom"></a>Inicie sessão em azureiotsolutions.com

Antes de poder implementar um acelerador de soluções, tem de iniciar sessão em AzureIoTSolutions.com utilizando credenciais associadas uma subscrição do Azure. Se a sua conta estiver associada a mais do que um inquilino do Microsoft Azure Active Directory (AD), pode utilizar o **lista pendente de seleção de conta** para escolher o diretório a utilizar.

As suas permissões Aceleradores de solução de implementar, gerir utilizadores e gerir serviços do Azure dependem de sua função no diretório selecionado. As funções do Azure AD comuns associadas com os Aceleradores de solução incluem:

* **Administrador global**: Podem existir muitas [os administradores globais](../active-directory/users-groups-roles/directory-assign-admin-roles.md) por inquilino do Azure AD:

  * Quando cria um inquilino do Azure AD, é por predefinição o administrador global desse inquilino.
  * O administrador global pode implementar os Aceleradores de solução de básico e standard.

* **Utilizador de domínio**: Podem existir vários usuários de domínio por inquilino do Azure AD. Um utilizador de domínio pode implementar um acelerador de solução básica.

* **Utilizador convidado**: Podem existir vários utilizadores convidados por inquilino do Azure AD. Os utilizadores convidados não é possível implementar um acelerador de solução no inquilino do Azure AD.

Para obter mais informações sobre utilizadores e funções no Azure AD, consulte os seguintes recursos:

* [Criar utilizadores no Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Atribuir utilizadores a aplicações](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Escolha o seu dispositivo

O site de AzureIoTSolutions.com contém ligações para o [certificação do Azure para o catálogo de dispositivos de IoT](https://catalog.azureiotsolutions.com/).

O catálogo de lista centenas de dispositivos de hardware de IoT certificados que pode ligar a seus Aceleradores de solução para começar a criar a sua solução de IoT.

![Catálogo de dispositivos](media/iot-accelerators-permissions/devicecatalog.png)

Se for um fabricante de hardware, clique em **me tornar um parceiro** para saber mais sobre a nossa parceria com a Microsoft sobre o certificado para o programa de IoT.

## <a name="next-steps"></a>Passos Seguintes

Para experimentar um dos aceleradores de soluções de IoT, veja os inícios rápidos:

* [Try a remote monitoring solution](quickstart-remote-monitoring-deploy.md) (Experimentar uma solução de monitorização remota)
* [Try a connected factory solution](quickstart-connected-factory-deploy.md) (Experimentar uma solução de fábrica ligada)
* [Try a predictive maintenance solution](quickstart-predictive-maintenance-deploy.md) (Experimentar uma solução de manutenção preditiva)
* [Try a device simulation solution](quickstart-device-simulation-deploy.md) (Testar uma solução de simulação de dispositivos)
