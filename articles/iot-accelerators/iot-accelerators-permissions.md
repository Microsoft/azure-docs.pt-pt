---
title: Utilize o site Azure IoT Solutions - Azure | Microsoft Docs
description: Descreve como usar o website AzureIoTSolutions.com para implementar o seu acelerador de solução.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: e3c9296a909ba23ee8a3475149911af629a01b1a
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167469"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Utilize o site azureiotsolutions.com para implantar o seu acelerador de solução

Pode colocar os aceleradores de solução Azure IoT na sua subscrição Azure a partir de [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com acolhe tanto os aceleradores de fonte aberta da Microsoft como os aceleradores de soluções parceiras. Estes aceleradores de solução alinham-se com a [Arquitetura de Referência Azure IoT](/azure/architecture/reference-architectures/iot). Pode utilizar o site para implementar rapidamente um acelerador de solução como um ambiente de demonstração ou de produção.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="Página inicial de soluções IoT":::

> [!TIP]
> Se precisar de mais controlo sobre o processo de implantação, pode utilizar o CLI para implementar um acelerador de solução.

Pode implantar os aceleradores de solução nas seguintes configurações:

* **Standard**: Uma infraestrutura alargada para o desenvolvimento de um ambiente de produção. O Serviço de Contentores Azure implanta os microserviços em várias máquinas virtuais Azure. O Kubernetes orquestra os contentores do Docker que alojam os microsserviços individuais.
* **Base**: Versão de custo reduzido para uma demonstração ou para testar uma implantação. Todos os microsserviços são implementados numa máquina virtual do Azure individual.
* **Local**: Uma colocação de máquina local para testes e desenvolvimento. Esta abordagem implanta os microserviços para um contentor local do Docker e liga-se aos serviços de armazenamento IoT Hub, Azure Cosmos DB e Azure na nuvem.

Cada um dos aceleradores de solução usa uma combinação diferente de serviços Azure, tais como IoT Hub, Azure Stream Analytics e Cosmos DB. Para mais informações, visite [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) e selecione um acelerador de soluções.

## <a name="sign-in-at-azureiotsolutionscom"></a>Inscreva-se na azureiotsolutions.com

Antes de poder implementar um acelerador de solução, tem de iniciar sação no AzureIoTSolutions.com utilizando credenciais associadas a uma subscrição do Azure. Se a sua conta estiver associada a mais de um inquilino do Microsoft Azure Ative Directory (AD), pode utilizar a **retirada da seleção de Conta** para escolher o diretório a utilizar.

As suas permissões para implementar aceleradores de solução, gerir utilizadores e gerir os serviços Azure dependem do seu papel no diretório selecionado. Os papéis comuns da AD Azure associados aos aceleradores de solução incluem:

* **Administrador global**: Pode haver [muitos administradores globais](../active-directory/roles/permissions-reference.md) por inquilino AZure AD:

  * Quando cria um inquilino AZure AD, é por defeito o administrador global desse inquilino.
  * O administrador global pode implementar aceleradores de solução básicos e padrão.

* **Utilizador de domínio**: Pode haver muitos utilizadores de domínio por inquilino Azure AD. Um utilizador de domínio pode implementar um acelerador de solução básico.

* **Utilizador convidado**: Pode haver muitos utilizadores convidados por inquilino Azure AD. Os utilizadores convidados não podem implementar um acelerador de solução no inquilino AZure AD.

Para obter mais informações sobre utilizadores e funções em Azure AD, consulte os seguintes recursos:

* [Criar utilizadores no Azure Ative Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Atribuir utilizadores a apps](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Escolha o seu dispositivo

O site AzureIoTSolutions.com ligações ao [catálogo de dispositivos Azure Certified for IoT.](https://devicecatalog.azure.com/)

O catálogo lista centenas de dispositivos de hardware IoT certificados que pode ligar aos seus aceleradores de solução para começar a construir a sua solução IoT.

![Catálogo de dispositivos](media/iot-accelerators-permissions/devicecatalog.png)

Se for um fabricante de hardware, clique em **Tornar-se um Parceiro** para aprender a fazer parceria com a Microsoft no programa Certified for IoT.

## <a name="next-steps"></a>Passos seguintes

Para experimentar um dos aceleradores de solução IoT, confira o arranque rápido: [Experimente uma solução de fábrica conectada](quickstart-connected-factory-deploy.md).
