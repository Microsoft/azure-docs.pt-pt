---
title: Exportar ou eliminar definições do portal Azure
description: Saiba como pode exportar ou eliminar as definições do utilizador, dashboards privados e configurações personalizadas no portal Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: how-to
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: 248c643c5f1865451abbe201e7033efedb8a7e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763946"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou eliminar as definições de utilizador

Pode utilizar configurações e funcionalidades no portal Azure para criar uma experiência personalizada. As informações sobre as suas definições personalizadas são armazenadas no Azure. Pode exportar ou eliminar os seguintes dados do utilizador:

* Dashboards privados no portal Azure
* Definições de utilizadores como subscrições ou diretórios favoritos e último diretório iniciado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e rever as suas definições antes de as apagar. Reconstruir painéis ou refazer as definições personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou eliminar as definições do seu portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No cabeçalho do portal, selecione ![ definições de ](media/azure-portal-export-delete-settings/settings-icon.png) **ícones de definições**.

1. Selecione **Exporte todas as definições** ou **elimine todas as definições e dashboards privados**.

    ![Definições e configurações do portal Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A tabela que se segue descreve estas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um ficheiro *.json* que contém as definições do utilizador como o seu tema de cor, favoritos e dashboards privados.|
      | **Eliminar todas as definições e dashboards privados** | Elimina todos os links para dashboards privados e outras definições personalizadas que fez ao portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações do utilizador e ao risco de corrupção de dados, não é possível importar configurações a partir do ficheiro *.json.*
>
>

## <a name="next-steps"></a>Próximos passos

* [Partilhar dashboards do Azure mediante a utilização do Controlo de Acesso Baseado em Funções](azure-portal-dashboard-share-access.md)
* [Adicione, remova e reorganize os favoritos](azure-portal-add-remove-sort-favorites.md)
