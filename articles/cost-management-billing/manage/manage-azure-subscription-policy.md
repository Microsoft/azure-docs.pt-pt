---
title: Gerir políticas de subscrição do Azure
description: Saiba como gerir as políticas de subscrição do Azure para controlar o movimento de subscrições do Azure de e para diretórios.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631877"
---
# <a name="manage-azure-subscription-policies"></a>Gerir políticas de subscrição do Azure

>[!NOTE]
>Esta funcionalidade encontra-se atualmente em pré-visualização e encontra-se a ser lançada de forma gradual, pelo que nem todos podem ver esta experiência ainda no portal Azure.

Este artigo ajuda-o a configurar as políticas de subscrição do Azure para operações de subscrição para controlar o movimento de subscrições do Azure de e para diretórios.

## <a name="prerequisites"></a>Pré-requisitos

- Apenas [administradores globais de diretórios](../../active-directory/roles/permissions-reference.md#global-administrator) podem editar políticas de subscrição. Antes de editar as políticas de subscrição, o administrador global deve elevar o [acesso para gerir todas as subscrições e grupos de gestão do Azure.](../../role-based-access-control/elevate-access-global-admin.md) Depois podem editar as políticas de subscrição.
- Todos os outros utilizadores só podem ler a definição de política atual.

## <a name="available-subscription-policy-settings"></a>Definições de política de subscrição disponíveis

Utilize as seguintes definições de política para controlar o movimento de subscrições Azure de e para diretórios.

### <a name="subscriptions-leaving-aad-directory"></a>Assinaturas que saem do diretório AAD

A política permite ou impede os utilizadores de retirarem as subscrições do diretório atual. [Os proprietários](../../role-based-access-control/built-in-roles.md#owner) de subscrições podem [alterar o diretório de uma assinatura Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) para outra onde são membros. Coloca desafios de governação, para que os administradores globais possam permitir ou proibir os utilizadores de diretórios de alterar o diretório.

### <a name="subscriptions-entering-aad-directory"></a>Assinaturas que entram no diretório da AAD

A política permite ou impede os utilizadores de outros diretórios, que têm acesso no diretório atual, a moverem as subscrições para o diretório atual. [Os proprietários](../../role-based-access-control/built-in-roles.md#owner) de subscrições podem [alterar o diretório de uma assinatura Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) para outra onde são membros. Coloca desafios de governação, para que os administradores globais possam permitir ou proibir os utilizadores de diretórios de alterar o diretório.

### <a name="exempted-users"></a>Utilizadores Isentos

Por razões de governação, os administradores globais podem bloquear todos os movimentos de diretório de subscrição - para o nosso fora do atual diretório. No entanto, podem querer permitir que utilizadores específicos façam qualquer uma das operações. Para qualquer uma das situações, podem configurar uma lista de utilizadores isentos que permite que os utilizadores contornem a definição de política que se aplica a todos os outros.

## <a name="setting-subscription-policy"></a>Definição da política de subscrição

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegar para **subscrições**. **As Políticas de Gestão** são mostradas na barra de comando.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Screenshot mostrando Manage Polices em Subscrições." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Selecione **Políticas de Gestão** para visualizar detalhes sobre as políticas de subscrição atuais definidas para o diretório. Um administrador global com [permissões elevadas](../../role-based-access-control/elevate-access-global-admin.md) pode fazer edições para as definições, incluindo adicionar ou remover utilizadores isentos.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Screenshot mostrando definições de política específicas e utilizadores isentos." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. **Selecione Guardar as alterações** na parte inferior para guardar alterações. As alterações são eficazes imediatamente.

## <a name="read-subscription-policy"></a>Ler política de subscrição

Os administradores não globais ainda podem navegar na área da política de subscrição para ver as definições políticas do diretório. Não podem fazer edições. Não podem ver a lista de utilizadores isentos por razões de privacidade. Podem ver os seus administradores globais a apresentar pedidos de alterações de política, desde que as definições de diretório o permitam.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Screenshot mostrando as políticas de Gestão em Subscrições como leitor." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Passos seguintes

- Leia a [documentação de Gestão de Custos + Faturação](../index.yml)