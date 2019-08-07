---
title: Acessar a solução VMware do Azure por CloudSimple-Portal
description: Descreve como acessar a solução VMware pelo portal do CloudSimple da portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5bb1a4dd9d652481dfe1a2727ee0e5fe7601e96a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812771"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Acessando a solução VMware pelo portal CloudSimple da portal do Azure

O logon único tem suporte para acesso ao portal do CloudSimple. Depois de entrar no portal do Azure, você poderá acessar o portal do CloudSimple sem entrar novamente. Na primeira vez que você acessar o portal do CloudSimple, você será solicitado a autorizar o aplicativo de [autorização de serviço do CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Usuários com funções de **colaborador** e **proprietário** interno podem acessar o portal do CloudSimple.  As funções devem ser configuradas no grupo de recursos em que o serviço CloudSimple está implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre como verificar sua função, consulte o artigo [exibir atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Se você estiver usando funções personalizadas, a função deverá ter qualquer uma das operações a seguir ```Actions```em.  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se qualquer uma das operações fizer parte do, ```NotActions```o usuário não poderá acessar o portal do CloudSimple. 

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

2. Procure **Serviços CloudSimples**.

3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.

4. Na página **visão geral** , clique em **ir para o portal do CloudSimple**.  Se estiver acessando o portal do CloudSimple da portal do Azure pela primeira vez, você será solicitado a autorizar o aplicativo de [autorização de serviço do CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Iniciar o portal do CloudSimple](media/launch-cloudsimple-portal.png)

> [!TIP]
> Se você selecionar uma operação de nuvem privada (como criar ou expandir uma nuvem privada) diretamente da portal do Azure, o portal do CloudSimple será aberto na página indicada.

No portal do CloudSimple, selecione **página inicial** no menu lateral para exibir informações de resumo sobre suas nuvens privadas. Os recursos e a capacidade de suas nuvens privadas são mostrados, juntamente com alertas e tarefas que exigem atenção. Para tarefas comuns, clique nos ícones nomeados na parte superior da página.

![Página inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento para o aplicativo de autorização do serviço CloudSimple

Iniciar o portal do CloudSimple da portal do Azure pela primeira vez requer seu consentimento para o aplicativo de autorização do serviço CloudSimple.  Selecione **aceitar** para conceder permissões solicitadas e acessar o portal do CloudSimple. 

![Consentimento para a autorização do serviço CloudSimple-administradores](media/cloudsimple-azure-consent.png)

Se você tiver um privilégio de administrador global, poderá consentir para sua organização.  Selecione **consentimento em nome da sua organização**.

![Consentimento para a autorização do serviço CloudSimple-administrador global](media/cloudsimple-azure-consent-global-admin.png)

Se suas permissões não permitirem acesso ao portal do CloudSimple, entre em contato com o administrador global do seu locatário para conceder as permissões necessárias.  Um administrador global pode consentir em nome de sua organização.

![Consentimento para a autorização do serviço CloudSimple-requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
