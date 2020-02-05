---
title: Access Azure VMware Solutions (AVS) - Portal
description: Descreve como aceder à Azure VMware Solutions (AVS) do portal Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015955"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Access Azure VMware Solutions (AVS) do portal Azure

O único sinal é suportado para acesso ao portal AVS. Depois de iniciar sessão no portal Azure, pode aceder ao portal AVS sem voltar a entrar. A primeira vez que acede ao portal AVS é solicitado a autorizar o pedido de Autorização de [Serviço AVS.](#consent-to-avs-service-authorization-application) Autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os utilizadores com funções de **Proprietário** e **Contribuinte** incorporados podem aceder ao portal AVS. As funções devem ser configuradas no grupo de recursos onde o serviço AVS é implantado. As funções também podem ser configuradas no objeto de serviço AVS. Para obter mais informações sobre a verificação do seu papel, consulte o artigo de atribuição de [papéis da View.](https://docs.microsoft.com/azure/role-based-access-control/check-access) Apenas os utilizadores com funções de **Proprietário** e **Colaborador** incorporados podem aceder ao portal AVS. As funções devem ser configuradas na subscrição. Para obter mais informações sobre a verificação do seu papel, consulte o artigo de atribuição de [papéis da View.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Se estiver a utilizar funções personalizadas, a função deve ter qualquer uma das seguintes operações em ```Actions```.  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Se alguma das operações fizer parte de ```NotActions```, o utilizador não pode aceder ao portal AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Aceda ao portal AVS

1. Selecione **Todos os serviços**.

2. Pesquisa de **Serviços AVS**.

3. Selecione o serviço AVS no qual pretende criar a sua Cloud Privada.

4. Na página **'Visão Geral',** clique **em ir ao portal AVS**. Se estiver a aceder ao portal AVS do portal Azure pela primeira vez, será solicitado a autorizar o pedido de Autorização de [Serviço DaVs.](#consent-to-avs-service-authorization-application) 

    ![Lançar portal AVS](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se selecionar uma operação Cloud Privada (como criar ou expandir uma Nuvem Privada) diretamente a partir do portal Azure, o portal AVS abre-se para a página indicada.

No portal AVS, selecione **Home** no menu lateral para mostrar informações sumárias sobre a sua Nuvem Privada AVS. Os recursos e capacidade da sua Nuvem Privada AVS são mostrados, juntamente com alertas e tarefas que requerem atenção. Para tarefas comuns, clique nos ícones nomeados no topo da página.

![Página Inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Consentimento para pedido de autorização de serviço AVS

O lançamento do portal AVS do portal Azure pela primeira vez requer o seu consentimento para o pedido de Autorização de Serviço DaVs. Selecione **Aceitar** conceder permissões solicitadas e aceder ao portal AVS.

![Consentimento para Autorização de Serviço AVS - administradores](media/cloudsimple-azure-consent.png)

Se tiver privilégio de administrador global, pode consentir pela sua organização. Selecione **O Consentimento em nome da sua organização.**

![Consentimento para Autorização de Serviço AVS - administração global](media/cloudsimple-azure-consent-global-admin.png)

Se as suas permissões não permitirem o acesso ao portal AVS, contacte o administrador global do seu inquilino para conceder as permissões necessárias. Um administrador global pode consentir em nome da sua organização.

![Consentimento para Autorização de Serviço AVS - requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
