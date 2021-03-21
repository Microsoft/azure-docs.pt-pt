---
title: Publique um anúncio a um laboratório em Azure DevTest Labs | Microsoft Docs
description: Saiba como publicar um anúncio personalizado num laboratório existente para notificar os utilizadores sobre alterações ou adições recentes ao laboratório em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270772"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publique um anúncio a um laboratório em Azure DevTest Labs

Como administrador de laboratório, pode publicar um anúncio personalizado num laboratório existente para notificar os utilizadores sobre alterações ou adições recentes ao laboratório. Por exemplo, é melhor informar os utilizadores sobre:

- Novos tamanhos VM que estão disponíveis
- Imagens que são atualmente inutilizáveis
- Atualizações das políticas de laboratório

Uma vez publicado, o anúncio é apresentado na página geral do laboratório e o utilizador pode selecioná-lo para mais detalhes.

A funcionalidade de anúncio destina-se a ser utilizada para notificações temporárias.  Pode facilmente desativar um anúncio depois de já não ser necessário.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Passos para publicar um anúncio num laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista. (O seu laboratório pode já estar no Painel de Instrumentos em **Todos os Recursos**).
1. Da lista de laboratórios, selecione o laboratório no qual quer publicar um anúncio.
1. Na área de **visão geral** do laboratório, selecione **Configuração e políticas**.

    ![Botão de configuração e políticas](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. À esquerda em **DEFINIÇÕES**, selecione **anúncio de Laboratório**.

    ![Botão de anúncio de laboratório](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Para criar uma mensagem para os utilizadores deste laboratório, **desematado Ativado** para **Sim**.

1. Pode introduzir uma **data de expiração** para especificar uma data e hora após a qual o anúncio já não é mostrado aos utilizadores. Se não introduzir uma data de validade, o anúncio permanece até que o desative.

   > [!NOTE]
   > Após o anúncio expirar, já não é mostrado aos utilizadores, mas ainda existe no **painel de anúncios** do Laboratório. Pode fazer edições e reativá-la para torná-la ativa novamente.
   >
   >

1. Introduza um **título de Anúncio** e o texto de **Anúncio**.

   O título pode ter até 100 caracteres e é mostrado ao utilizador na página geral do laboratório. Se o utilizador selecionar o título, o texto de anúncio é apresentado.

   O texto do anúncio aceita a marcação. Ao introduzir o texto do anúncio, pode visualizar a mensagem na área de Pré-visualização na parte inferior do ecrã.

    ![Tela de anúncio do laboratório para criar a mensagem.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. **Selecione Save** assim que o seu anúncio estiver pronto para publicar.

Quando já não quiser mostrar este anúncio aos utilizadores do laboratório, volte à página de **anúncios** do Laboratório e desempate **ativado** para **o Nº**. Se especificou uma data de validade, o anúncio é automaticamente desativado nessa data e hora.

## <a name="steps-for-users-to-view-an-announcement"></a>Passos para os utilizadores visualizar um anúncio

1. A partir do [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)selecione um laboratório.

1. Se o laboratório tiver um anúncio publicado, um aviso de informação é mostrado no topo da página geral do laboratório. Este aviso de informação é o título de anúncio que foi especificado quando o anúncio foi criado.

    ![Anúncio de laboratório na página do resumo](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. O utilizador pode selecionar a mensagem para visualizar todo o anúncio.

    ![Mais informações para o anúncio do laboratório](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Pode especificar um anúncio como parte de um modelo do Gestor de Recursos Azure, como mostrado no seguinte exemplo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Pode implementar um modelo de Gestor de Recursos Azure utilizando uma das seguintes formas:

- [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
- [API REST](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Passos seguintes
* Se alterar ou definir uma política de laboratório, é melhor publicar um anúncio para informar os utilizadores. [As políticas e horários definidos](devtest-lab-set-lab-policy.md) fornecem informações sobre a aplicação de restrições e convenções em toda a sua subscrição, utilizando políticas personalizadas.
* Explore a [galeria de modelos QuickStart do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
