---
title: Exportar ou eliminar as definições do portal do Azure | Documentos da Microsoft
description: Saiba como exportar e eliminar as definições de utilizador, dashboards privados e definições personalizadas no portal do Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361947"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou eliminar as definições de utilizador

Pode utilizar as definições e funcionalidades no portal do Azure para criar uma experiência personalizada. Informações sobre as definições personalizadas são armazenadas no Azure. Pode exportar ou eliminar os seguintes dados de utilizador:

* Dashboards privados no portal do Azure
* Definições de utilizador como favoritas subscrições ou diretórios e última sessão iniciada no diretório
* Temas e outras definições do portal personalizadas

É uma boa idéia para exportar e rever as suas definições antes de os eliminar. A reconstrução de dashboards ou Refazer definições personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou eliminar as definições do portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No cabeçalho do portal, selecione **definições**.

    ![Captura de ecrã que mostra o ícone de engrenagem de definições do portal](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Selecione **exportar todas as definições** ou **eliminar todas as definições e dashboards privados**.

    ![Captura de ecrã que mostra o portal de exportar e eliminar definições](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      A tabela seguinte descreve estas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as definições** | Cria um ficheiro. JSON que contém as definições de utilizador, como o tema de cores, Favoritos e dashboards privados.|
      | **Eliminar todas as definições e dashboards privados** | Elimina todas as ligações para dashboards privados e outras definições personalizadas efetuadas no portal. |

> [!NOTE]
> Devido a da natureza dinâmica de definições de utilizador e o risco de danos nos dados, não é possível importar as definições do ficheiro. JSON.
>
>


## <a name="next-steps"></a>Passos Seguintes

* [Criar e partilhar dashboards do Azure](azure-portal-dashboard-share-access.md)
* [Adicionar, remover e ordenar favoritos](azure-portal-add-remove-sort-favorites.md)
