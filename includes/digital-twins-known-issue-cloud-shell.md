---
author: baanders
description: incluir arquivo para Azure Digital Twins - cita edição conhecida com autenticação Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290107"
---
>[!NOTE]
>Existe atualmente um **problema conhecido** na Cloud Shell que afeta estes grupos de comando: . . . . . . . . . . . . `az dt route` . `az dt model` `az dt twin` .
>
>Para resolver, ou corra `az login` em Cloud Shell antes de executar o comando, ou use o [CLI local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em vez de Cloud Shell. Para mais detalhes sobre este assunto, consulte [*Troubleshooting: Questões conhecidas em Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).