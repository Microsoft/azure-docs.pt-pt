---
title: Mova uma aplicação para outra região
description: Aprenda a mover recursos do Serviço app de uma região para outra.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925712"
---
# <a name="move-an-app-service-app-to-another-region"></a>Mova uma app de Serviço de Aplicações para outra região

Este artigo descreve como mover recursos do Serviço app para uma região azure diferente. Pode transferir os seus recursos para outra região por uma série de razões. Por exemplo, aproveitar uma nova região do Azure, implementar recursos ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e governação, ou em resposta aos requisitos de planeamento de capacidades.

Os recursos do Serviço de Aplicações são específicos da região e não podem ser movidos por regiões. Você deve criar uma cópia dos seus recursos de Serviço de Aplicações existentes na região alvo, mover o seu conteúdo para a nova app. Se a sua aplicação de origem utilizar um domínio personalizado, pode [migrar para a nova aplicação na região alvo](manage-custom-dns-migrate-domain.md) quando terminar.

Para facilitar a cópia da sua aplicação, pode [clonar uma aplicação individual](app-service-web-app-cloning.md) do App Service num plano de Serviço de Aplicações noutra região, mas tem [limitações](app-service-web-app-cloning.md#current-restrictions)– especialmente que não suporta aplicações Linux.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que a aplicação App Service está na região do Azure, da qual pretende mover-se.
- Certifique-se de que a região alvo suporta o Serviço de Aplicações e qualquer serviço relacionado, cujos recursos pretende mover.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Preparação

Identifique todos os recursos do Serviço de Aplicações que está a utilizar. Por exemplo:

- Aplicações do Serviço de Aplicações
- [Planos do Serviço de Aplicações](overview-hosting-plans.md)
- [Ranhuras de implantação](deploy-staging-slots.md)
- [Domínios personalizados adquiridos em Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração da Rede Virtual Azure](web-sites-integrate-with-vnet.md)
- [Ligações híbridas.](app-service-hybrid-connections.md)
- [Identidades geridas](overview-managed-identity.md)
- [Definições de backup](manage-backup.md)

Certos recursos, tais como certificados importados ou ligações híbridas, contêm integração com outros serviços Azure. Para obter informações sobre como movimentar esses recursos através das regiões, consulte a documentação para os respetivos serviços.

## <a name="move"></a>Mover

1. [Crie um back up da aplicação de origem.](manage-backup.md)
1. Crie uma aplicação num novo plano de Serviço de [Aplicações, na região alvo.](app-service-plan-manage.md#create-an-app-service-plan)
2. [Restaurar a parte de trás na app alvo](web-sites-restore.md)
2. Se utilizar um domínio personalizado, [ligue-o preventivamente à aplicação alvo](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) com `awverify.` e [ative o domínio na aplicação alvo](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configure tudo o resto na sua aplicação alvo para ser o mesmo que a aplicação de origem e verifique a sua configuração.
4. Quando estiver pronto para que o domínio personalizado aponte para a aplicação alvo, [remape o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

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

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Elimine a aplicação de origem e o plano de serviço de aplicações. [Um plano de Serviço de Aplicações no nível não gratuito tem uma taxa, mesmo que nenhuma aplicação esteja a funcionar nele.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Passos seguintes

[Clonagem de aplicativos de aplicativo sinuoso usando powershell](app-service-web-app-cloning.md)