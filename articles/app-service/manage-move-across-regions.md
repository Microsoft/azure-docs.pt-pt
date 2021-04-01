---
title: Mover uma app para outra região
description: Saiba como mover os recursos do Serviço de Aplicações de uma região para outra.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86524859"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Mover um recurso de Serviço de Aplicações para outra região

Este artigo descreve como mover recursos do Serviço de Aplicações para uma região de Azure diferente. Pode mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para implantar características ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e governação, ou em resposta aos requisitos de planeamento de capacidades.

Os recursos do Serviço de Aplicações são específicos da região e não podem ser movidos através de regiões. Tem de criar uma cópia dos recursos do Serviço de Aplicações existentes na região alvo e, em seguida, transferir o seu conteúdo para a nova aplicação. Se a sua aplicação de origem utilizar um domínio personalizado, pode [emigrá-la para a nova aplicação na região alvo](manage-custom-dns-migrate-domain.md) quando terminar.

Para facilitar a cópia da sua aplicação, pode [clonar uma aplicação individual do Serviço de Aplicações](app-service-web-app-cloning.md) num plano de Serviço de Aplicações noutra região, mas tem [limitações](app-service-web-app-cloning.md#current-restrictions)– especialmente porque não suporta aplicações Linux.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a aplicação do Serviço de Aplicações está na região Azure de onde pretende mover-se.
- Certifique-se de que a região alvo suporta o Serviço de Aplicações e qualquer serviço relacionado, cujos recursos pretende mover.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Preparação

Identifique todos os recursos do Serviço de Aplicações que está a utilizar. Por exemplo:

- Aplicações do Serviço de Aplicações
- [Planos do Serviço de Aplicações](overview-hosting-plans.md)
- [Blocos de implementação](deploy-staging-slots.md)
- [Domínios personalizados comprados em Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração da Rede Virtual Azure](web-sites-integrate-with-vnet.md)
- [Ligações híbridas.](app-service-hybrid-connections.md)
- [Identidades geridas](overview-managed-identity.md)
- [Definições de backup](manage-backup.md)

Certos recursos, tais como certificados importados ou ligações híbridas, contêm integração com outros serviços da Azure. Para obter informações sobre como mover esses recursos através das regiões, consulte a documentação dos respetivos serviços.

## <a name="move"></a>Mover

1. [Crie uma parte de trás da aplicação de origem.](manage-backup.md)
1. [Criar uma aplicação num novo plano de Serviço de Aplicações, na região alvo.](app-service-plan-manage.md#create-an-app-service-plan)
2. [Restaurar a re-volta na aplicação-alvo](web-sites-restore.md)
2. Se utilizar um domínio personalizado, [ligue-o preventivamente à aplicação-alvo](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` e ative o domínio na [aplicação-alvo](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configure tudo o resto na sua aplicação-alvo para ser o mesmo que a aplicação de origem e verifique a sua configuração.
4. Quando estiver pronto para o domínio personalizado apontar para a aplicação-alvo, [remapia o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Elimine a aplicação de origem e o plano de Serviço de Aplicações. [Um plano de Serviço de Aplicações no nível não gratuito tem uma taxa, mesmo que nenhuma aplicação esteja a ser executada no mesmo.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Passos seguintes

[Clonagem de aplicativos Azure App usando PowerShell](app-service-web-app-cloning.md)