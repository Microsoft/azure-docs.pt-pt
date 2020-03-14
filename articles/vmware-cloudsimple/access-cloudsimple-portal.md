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
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244735"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Aceda à Solução VMware por CloudSimple do portal Azure

O único sinal é suportado para acesso ao portal CloudSimple. Depois de iniciar sessão no portal Azure, pode aceder ao portal CloudSimple sem voltar a entrar. A primeira vez que acede ao portal CloudSimple é solicitado a autorizar o pedido de Autorização de [Serviço CloudSimple.](#consent-to-cloudsimple-service-authorization-application)  Autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os utilizadores com funções de **Proprietário** e **Contribuinte** incorporados podem aceder ao portal CloudSimple.  As funções devem ser configuradas no grupo de recursos onde o serviço CloudSimple é implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre a verificação do seu papel, consulte o artigo de atribuição de [papéis Da View.](https://docs.microsoft.com/azure/role-based-access-control/check-access) Apenas os utilizadores com funções de **Proprietário** e **Colaborador** incorporados podem aceder ao portal CloudSimple.  As funções devem ser configuradas na subscrição.  Para obter mais informações sobre a verificação do seu papel, consulte o artigo de atribuição de [papéis Da View.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Se estiver a utilizar funções personalizadas, a função deve ter qualquer uma das seguintes operações em ```Actions```.  Para obter mais informações sobre papéis personalizados, consulte [funções personalizadas para os recursos Do Azure.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)  Se alguma das operações fizer parte de ```NotActions```, o utilizador não pode aceder ao portal CloudSimple.

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

2. Pesquisa por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.

4. Na página **'Overview',** clique em **ir ao portal CloudSimple**.  Se estiver a aceder ao portal CloudSimple do portal Azure pela primeira vez, será solicitado a autorizar o pedido de Autorização de [Serviço CloudSimple.](#consent-to-cloudsimple-service-authorization-application) 

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se selecionar uma operação Cloud Privada (como criar ou expandir uma Nuvem Privada) diretamente a partir do portal Azure, o portal CloudSimple abre-se para a página indicada.

No portal CloudSimple, selecione **Home** no menu lateral para mostrar informações sumárias sobre as suas Nuvens Privadas. Os recursos e capacidade das suas Nuvens Privadas são mostrados, juntamente com alertas e tarefas que requerem atenção. Para tarefas comuns, clique nos ícones nomeados no topo da página.

![Home Page](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento para pedido de autorização de serviço CloudSimple

O lançamento do portal CloudSimple do portal Azure pela primeira vez requer o seu consentimento para a aplicação de Autorização de Serviço CloudSimple.  Selecione **Aceitar** conceder permissões solicitadas e aceder ao portal CloudSimple.

![Consentimento para CloudSimple Service Authorization - administradores](media/cloudsimple-azure-consent.png)

Se tiver privilégio de administrador global, pode consentir pela sua organização.  Selecione **O Consentimento em nome da sua organização.**

![Consentimento para CloudSimple Service Authorization - administração global](media/cloudsimple-azure-consent-global-admin.png)

Se as suas permissões não permitirem o acesso ao portal CloudSimple, contacte o administrador global do seu inquilino para conceder permissões necessárias.  Um administrador global pode consentir em nome da sua organização.

![Consentimento para A Autorização de Serviço CloudSimple - requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
