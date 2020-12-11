---
title: Encontre iD do inquilino, ID de objeto e detalhes da associação de parceiros no Azure Marketplace
description: Como encontrar o ID do Inquilino, ID de Objeto e detalhes da associação de parceiros de um ID de subscrição no Mercado Azure.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109351"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Encontre iD do inquilino, ID de objeto e detalhes da associação de parceiros

Este artigo descreve como encontrar o ID do Inquilino, O ID do Objeto e detalhes da associação de parceiros, juntamente com os respetivos IDs de subscrição.

Se precisar de obter imagens destes itens em Azure Cloud Shell para usar para depurar assistência, salte para a [associação Find Tenant, Object e Partner ID para depuração.](#find-ids-for-debugging)

>[!Note]
> Apenas o proprietário de uma subscrição tem os privilégios de realizar estes passos.

## <a name="find-tenant-id"></a>Encontre o ID do inquilino

1. Aceda ao [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="O ícone do Diretório Ativo Azure no portal Azure.":::

3. Selecione **Descrição geral**. A sua identificação do inquilino deve aparecer sob **informações básicas.**

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Selecione Grupos no portal Azure.":::

## <a name="find-subscriptions-and-roles"></a>Encontrar subscrições e funções

1. Vá ao portal Azure e selecione **O Diretório Ativo Azure** como indicado nos passos 1 e 2 acima.
2. Selecione **Subscrições**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="O ícone de subscrições no portal Azure.":::

3. Ver subscrições e funções.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="O ecrã de subscrições no portal Azure.":::

## <a name="find-partner-id"></a>Encontre iD do parceiro

1. Navegue na página de Subscrições como descrito na secção anterior.
2. Selecione uma subscrição.
3. Em **Billing,** selecione **informações de Parceiros**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Informações do parceiro no menu de navegação à esquerda.":::

## <a name="find-user-object-id"></a>Encontre o utilizador (ID do objeto)

1. Inscreva-se no [Portal de Administração 365](https://portal.office.com/adminportal/home) com uma conta no arrendatário pretendido com os direitos administrativos adequados.
2. No menu do lado esquerdo, expanda a secção **Admin Centers** na parte inferior e, em seguida, selecione a opção Azure Ative Directory para lançar a consola de administração numa nova janela do navegador.
3. Selecione **Utilizadores**.
4. Navegue ou procure o utilizador pretendido e, em seguida, selecione o nome da conta para ver as informações de perfil da conta do utilizador.
5. O ID do objeto está localizado na secção identidade à direita.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Centro de Administração Azure Ative.":::

6. Encontre **atribuições de funções** selecionando **o controlo de acesso (IAM)** no menu esquerdo e, em seguida, **atribuições de funções**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Atribuições de funções para recursos Azure.":::

## <a name="find-ids-for-debugging"></a>Encontre IDs para depurar

Esta secção descreve como encontrar inquilino, objeto e associação de identificação parceira para fins de depuragem.

1. Aceda ao [portal do Azure](https://ms.portal.azure.com/).
2. Abra o Azure Cloud Shell selecionando o ícone PowerShell no canto superior direito.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Ícone PowerShell no topo direito do ecrã.":::

3. Selecione **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Selecione a ligação PowerShell.":::

4. Selecione a caixa **de Subscrição** para escolher a que o parceiro está ligado e, em seguida, **Crie Armazenamento**. Esta é uma ação única; se já tiver o armazenamento montado, proceda ao passo seguinte.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Selecione o botão Criar Armazenamento.":::

5. A criação (ou já tendo) armazenamento abre a janela Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="A janela Azure Cloud Shell.":::

6. Para detalhes da associação parceira, instale a extensão com este comando:<br>`az extension add --name managementpartner`.<br>A Azure Cloud Shell notará se a extensão já estiver instalada:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="A janela Azure Cloud Shell mostrando que a extensão já está instalada.":::

7. Verifique os detalhes do parceiro usando este comando:<br>`az managementpartner show --partner-id xxxxxx`<br>Exemplo: `az managementpartner show --partner-id 4760962`.<br>Tire uma imagem dos resultados do comando, que será semelhante a isto:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Ecrã de amostra mostrando os resultados do comando anterior para visualizar o ID parter.":::

>[!NOTE]
>Se várias subscrições necessitarem de uma imagem, utilize este comando para alternar entre subscrições:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Passos seguintes

- [Países e regiões suportados](sell-from-countries.md)