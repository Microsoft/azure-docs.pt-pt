---
title: Solução de VMware do Azure de acesso por CloudSimple - Portal
description: Descreve como aceder a solução da VMware CloudSimple portal a partir do portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544117"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>A solução de VMware a aceder ao portal CloudSimple a partir do portal do Azure

Início de sessão único é suportado para acesso ao portal do CloudSimple. Depois de iniciar sessão portal do Azure, pode aceder ao portal de CloudSimple sem iniciar sessão novamente. Na primeira vez que acessar o portal de CloudSimple lhe for pedido para autorizar o [autorização de serviço CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplicação.  Autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os utilizadores com builtin **proprietário** e **contribuinte** funções podem acessar o portal de CloudSimple.  As funções devem ser configuradas no grupo de recursos onde CloudSimple serviço está implementado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre como verificar a sua função, veja [ver atribuições de funções](https://docs.microsoft.com/azure/role-based-access-control/check-access) artigo.

Se estiver a utilizar funções personalizadas, deve ter a função de qualquer uma das seguintes operações em ```Actions```.  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se qualquer uma das operações é uma parte do ```NotActions```, o utilizador não é possível aceder ao CloudSimple portal. 

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

2. Procure **CloudSimple serviços**.

3. Selecione o serviço de CloudSimple no qual pretende criar a sua nuvem privada.

4. Sobre o **descrição geral** página, clique em **aceda ao portal do CloudSimple**.  Se está acessando o portal de CloudSimple do portal do Azure pela primeira vez, será solicitado para autorizar o [autorização de serviço CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplicação. 

    ![Iniciar CloudSimple portal](media/launch-cloudsimple-portal.png)

> [!TIP]
> Se selecionar uma operação de nuvem privada (como criar ou expandir uma nuvem privada) diretamente a partir do portal do Azure, o portal de CloudSimple abre-se para a página indicada.

No portal do CloudSimple, selecione **home page** no menu do lado para apresentar informações de resumo sobre as nuvens privadas. São apresentados os recursos e a capacidade de suas nuvens privadas, juntamente com alertas e tarefas que necessitam da atenção. Para tarefas comuns, clique nos ícones com nome na parte superior da página.

![Página inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Autorizar aplicação CloudSimple autorização de serviço

Iniciar o portal de CloudSimple do portal do Azure pela primeira vez requer o seu consentimento para a aplicação de autorização de serviço CloudSimple.  Selecione **Accept** para conceder permissões pedidas e aceder ao portal de CloudSimple. 

![Dar consentimento a autorização do serviço CloudSimple - os administradores](media/cloudsimple-azure-consent.png)

Se tiver o privilégio de administrador global, pode dar consentimento para a sua organização.  Selecione **consentir em nome da sua organização**.

![Dar consentimento a autorização do serviço CloudSimple - global admin](media/cloudsimple-azure-consent-global-admin.png)

Se as suas permissões não permitem o acesso ao portal do CloudSimple, contacte o administrador global do seu inquilino para conceder permissões necessárias.  Um administrador global pode consentir em nome de sua organização.

![Dar consentimento a autorização de serviço CloudSimple – requer que os administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
