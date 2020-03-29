---
title: Exportar ou excluir configurações do portal Azure
description: Saiba como pode exportar ou eliminar as definições de utilizador, dashboards privados e configurações personalizadas no portal Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900739"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou eliminar as definições de utilizador

Pode utilizar configurações e funcionalidades no portal Azure para criar uma experiência personalizada. As informações sobre as suas configurações personalizadas são armazenadas no Azure. Pode exportar ou eliminar os seguintes dados do utilizador:

* Dashboards privados no portal Azure
* Configurações de utilizador como subscrições ou diretórios favoritos, e último diretório sessão
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e rever as suas definições antes de as apagar. Reconstruir dashboards ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou eliminar as definições do portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No cabeçalho do portal, ![](media/azure-portal-export-delete-settings/settings-icon.png) selecione definições de ícone **Definições**.

1. **Selecione Exportar todas as definições** ou **elimine todas as definições e dashboards privados**.

    ![Opções de definições e definições do portal Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A tabela que se segue descreve estas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um ficheiro *.json* que contém as definições do utilizador, como o tema da cor, os favoritos e os dashboards privados.|
      | **Eliminar todas as definições e dashboards privados** | Elimina todas as ligações para dashboards privados e outras configurações personalizadas que fez para o portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações dos utilizadores e ao risco de corrupção de dados, não é possível importar definições do ficheiro *.json.*
>
>

## <a name="next-steps"></a>Passos seguintes

* [Partilhar dashboards do Azure mediante a utilização do Controlo de Acesso Baseado em Funções](azure-portal-dashboard-share-access.md)
* [Adicione, remova e reorganize os favoritos](azure-portal-add-remove-sort-favorites.md)
