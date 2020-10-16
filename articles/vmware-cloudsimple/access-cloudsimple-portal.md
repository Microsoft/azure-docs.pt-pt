---
title: Access Azure VMware Solution by CloudSimple - Portal
description: Descreve como aceder ao portal da VMware Solution by CloudSimple no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea178655646f7f130476acaffc35c60181968ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87058708"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Aceda à Solução VMware por CloudSimple a partir do portal Azure

O sôm-on único é suportado para acesso ao portal CloudSimple. Depois de iniciar sessão no portal Azure, pode aceder ao portal CloudSimple sem voltar a iniciar sessão. A primeira vez que acede ao portal CloudSimple é solicitado a autorizar a aplicação [de Autorização de Serviço CloudSimple.](#consent-to-cloudsimple-service-authorization-application)  A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os utilizadores com funções **de Proprietário** e **Contribuinte** podem aceder ao portal CloudSimple.  As funções devem ser configuradas no grupo de recursos onde o serviço CloudSimple é implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre a verificação da sua função, consulte [artigo de atribuições de funções.](../role-based-access-control/check-access.md) Apenas os utilizadores com funções **incorporadas proprietário** e **contribuinte** podem aceder ao portal CloudSimple.  As funções devem ser configuradas na subscrição.  Para obter mais informações sobre a verificação da sua função, consulte [artigo de atribuições de funções.](../role-based-access-control/check-access.md)

Se estiver a utilizar funções personalizadas, a função deverá ter qualquer uma das seguintes operações em ```Actions``` .  Para obter mais informações sobre funções personalizadas, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md).  Se alguma das operações fizer parte ```NotActions``` , o utilizador não poderá aceder ao portal CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.

4. Na página **'Vista Geral',** clique **em Ir ao portal CloudSimple**.  Se estiver a aceder ao portal CloudSimple a partir do portal Azure pela primeira vez, será solicitado que autorize a aplicação [de Autorização de Serviço CloudSimple.](#consent-to-cloudsimple-service-authorization-application) 

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se selecionar uma operação Private Cloud (como criar ou expandir uma Nuvem Privada) diretamente a partir do portal Azure, o portal CloudSimple abre para a página indicada.

No portal CloudSimple, selecione **Home** no menu lateral para apresentar informações sumárias sobre as suas Nuvens Privadas. Os recursos e capacidade das suas Nuvens Privadas são mostrados, juntamente com alertas e tarefas que requerem atenção. Para tarefas comuns, clique nos ícones nomeados no topo da página.

![Página Inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento para pedido de Autorização de Serviço CloudSimple

O lançamento do portal CloudSimple a partir do portal Azure pela primeira vez requer o seu consentimento para a aplicação de Autorização de Serviço CloudSimple.  **Selecione Aceitar** conceder permissões solicitadas e aceder ao portal CloudSimple.

![Consentimento para Autorização de Serviço CloudSimple - administradores](media/cloudsimple-azure-consent.png)

Se tiver privilégios de administrador global, pode consentir com a sua organização.  Selecione **Consentimento em nome da sua organização**.

![Consentimento para a Autorização de Serviço CloudSimple - administrador global](media/cloudsimple-azure-consent-global-admin.png)

Se as suas permissões não permitirem o acesso ao portal CloudSimple, contacte o administrador global do seu inquilino para conceder as permissões necessárias.  Um administrador global pode consentir em nome da sua organização.

![Consentimento para a Autorização de Serviço CloudSimple - requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar uma nuvem privada](./create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
