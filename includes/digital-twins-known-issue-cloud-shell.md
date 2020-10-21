---
author: baanders
description: incluir arquivo para Azure Digital Twins - cita edição conhecida com autenticação Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92322669"
---
>[!NOTE]
>Existe atualmente um **problema conhecido** na Cloud Shell que afeta estes grupos de comando quando são executados a partir *https://shell.azure.com* de: . . `az dt route` `az dt model` `az dt twin` .
>
>Para resolver, pode fazer qualquer um dos seguintes:
> * Corra `az login` em Cloud Shell antes de executar o comando.
> * Abra o painel Cloud Shell no portal Azure e complete o seu trabalho cloud Shell a partir daí.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Vista do portal Azure com o ícone 'Cloud Shell' em destaque, e a Cloud Shell aparecendo na parte inferior da janela do portal":::
> * Use o [CLI local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em vez de Cloud Shell.
>
>Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).