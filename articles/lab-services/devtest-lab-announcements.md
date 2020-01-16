---
title: Postar uma apresentação em um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um comunicado a um laboratório no Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976454"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Postar um comunicado em um laboratório no Azure DevTest Labs

Como administrador de um laboratório, você pode postar um comunicado personalizado em um laboratório existente para notificar os usuários sobre alterações recentes ou adições ao laboratório. Por exemplo, talvez você queira informar os usuários sobre:

- Novos tamanhos de VM que estão disponíveis
- Imagens que estão inutilizáveis no momento
- Atualizações para políticas de laboratório

Depois de Postado, o comunicado é exibido na página de visão geral do laboratório e o usuário pode selecioná-lo para obter mais detalhes.

O recurso de anúncio deve ser usado para notificações temporárias.  Você pode desabilitar um anúncio com facilidade depois que ele não for mais necessário.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Etapas para postar um comunicado em um laboratório existente

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista. (Seu laboratório já pode ser mostrado no painel em **todos os recursos**).
1. Na lista de laboratórios, selecione o laboratório no qual você deseja postar um comunicado.
1. Na área **visão geral** do laboratório, selecione **configuração e políticas**.

    ![Botão configuração e políticas](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. À esquerda em **configurações**, selecione **comunicado de laboratório**.

    ![Botão comunicado de laboratório](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Para criar uma mensagem para os usuários neste laboratório, defina **habilitado** como **Sim**.

1. Você pode inserir uma **data de validade** para especificar uma data e hora após a qual o anúncio não é mais exibido aos usuários. Se você não inserir uma data de expiração, o comunicado permanecerá até você desabilitá-lo.

   > [!NOTE]
   > Depois que o comunicado expirar, ele não será mais exibido aos usuários, mas ainda existirá no painel **comunicado de laboratório** . Você pode fazer edições nele e habilitá-la novamente para ativá-la novamente.
   >
   >

1. Insira um **título de comunicado** e o **texto do comunicado**.

   O título pode ter até 100 caracteres e é mostrado para o usuário na página de visão geral do laboratório. Se o usuário selecionar o título, o texto do comunicado será exibido.

   O texto do comunicado aceita redução. Ao inserir o texto do comunicado, você pode exibir a mensagem na área de visualização na parte inferior da tela.

    ![Tela de comunicado do laboratório para criar a mensagem.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selecione **salvar** quando o comunicado estiver pronto para ser publicado.

Quando você não quiser mais mostrar este comunicado aos usuários do laboratório, retorne à página **comunicado do laboratório** e defina **habilitado** como **não**. Se você especificou uma data de expiração, o comunicado será desabilitado automaticamente nessa data e hora.

## <a name="steps-for-users-to-view-an-announcement"></a>Etapas para os usuários exibirem um comunicado

1. No [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. Se o laboratório tiver um comunicado Postado para ele, um aviso de informações será mostrado na parte superior da página de visão geral do laboratório. Esse aviso de informação é o título do comunicado que foi especificado quando o comunicado foi criado.

    ![Comunicado de laboratório na página Visão geral](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. O usuário pode selecionar a mensagem para exibir o anúncio inteiro.

    ![Mais informações para o comunicado do laboratório](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Você pode especificar um comunicado como parte de um modelo de Azure Resource Manager, conforme mostrado no exemplo a seguir:

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

Você pode implantar um modelo de Azure Resource Manager usando uma das seguintes maneiras:

- [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
- [O Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
- [API REST](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Passos seguintes
* Se você alterar ou definir uma política de laboratório, talvez queira postar um comunicado para informar os usuários. [Definir políticas e agendas](devtest-lab-set-lab-policy.md) fornece informações sobre a aplicação de restrições e convenções em sua assinatura usando políticas personalizadas.
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
