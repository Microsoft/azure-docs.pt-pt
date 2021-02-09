---
title: Gerir uma Nuvem Confluente - Soluções parceiras Azure
description: Este artigo descreve a gestão de uma Nuvem Confluente no portal Azure. Como configurar um único senting, eliminar uma organização Confluent e obter apoio.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989115"
---
# <a name="manage-the-confluent-cloud-resource"></a>Gerir o recurso Cloud Confluent

Este artigo descreve como gerir o seu exemplo de Apache Kafka para Nuvem Confluente em Azure. Mostra como configurar um único sign-on (SSO), eliminar uma organização Confluent e criar um pedido de apoio.

## <a name="single-sign-on"></a>Início de sessão único

Para implementar sSO para a sua organização, o seu administrador de inquilino pode importar o pedido de galeria. Este passo é opcional. Para obter informações que importem uma aplicação, consulte [Quickstart: Adicione uma aplicação ao seu inquilino Azure Ative Directory (Azure AD).](../../active-directory/manage-apps/add-application-portal.md) Quando o administrador do inquilino importa a aplicação, os utilizadores não precisam de consentir explicitamente para permitir o acesso ao portal Cloud Confluent.

Para ativar o SSO, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para a **Visão Geral** para o seu exemplo do recurso Cloud Confluent.
1. Selecione o link para **Gerir na Nuvem Confluente**.

   :::image type="content" source="media/sso-link.png" alt-text="Entrada única no portal confluente.":::

1. Se o administrador do inquilino não importar o pedido de consentimento da galeria, conceda permissões e consentimento. Este passo só é necessário na primeira vez que aceda ao link para **Gerir na Nuvem Confluente.**
1. Escolha uma conta AD Azure para um único sinal de acesso ao portal Cloud Confluent.
1. Após o consentimento ser fornecido, você é redirecionado para o portal Cloud Confluent.

## <a name="set-up-cluster"></a>Configurar cluster

Para obter informações sobre a configuração do seu cluster, consulte [Criar um Cluster em Nuvem Confluente - Documentação Confluente](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Excluir organização confluente

Quando já não precisar do seu recurso Cloud Confluente, elimine o recurso em Azure e Cloud Confluente.

Para eliminar os recursos em Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os recursos** e filtro pelo **nome** do recurso Cloud Confluent ou da organização De _Confluente_ **tipo de recurso.** Ou no portal Azure, procure o nome do recurso.
1. Na **visão geral** do recurso selecione **Excluir**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ícone de exclusão de recursos.":::

1. Para confirmar a eliminação, digite o nome do recurso e selecione **Delete**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Solicita para confirmar a eliminação de recursos.":::

Para eliminar o recurso na Nuvem Confluente, consulte a documentação para [Ambientes de Nuvem Confluente - Documentação Confluente](https://docs.confluent.io/current/cloud/using/environments.html) e [Bases de Nuvem Confluente - Documentação Confluente](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

O cluster e todos os dados do cluster são permanentemente eliminados. Se o seu contrato incluir uma cláusula de retenção de dados, a Confluent mantém os seus dados durante o período de tempo especificado nos [Termos de Serviço - Documentação Confluente.](https://www.confluent.io/confluent-cloud-tos)

És cobrado pelo uso prostado até ao momento da eliminação do agrupamento. Depois de o seu cluster ser permanentemente eliminado, a Confluent envia-lhe uma confirmação de e-mail.

## <a name="get-support"></a>Obter suporte

Para submeter um pedido de apoio à Confluent, contacte o [suporte da Confluent](https://support.confluent.io) ou envie um pedido através do portal, como mostrado abaixo.

> [!NOTE]
> Pela primeira vez, os utilizadores reiniciem a sua palavra-passe antes de iniciar sence no portal de suporte Confluent. Se não tiver uma conta com a Confluent Cloud, envie um e-mail `cloud-support@confluent.io` para mais assistência.

No portal, pode submeter um pedido através da Ajuda e Suporte Azure, ou diretamente da sua instância de Apache Kafka para Nuvem Confluente em Azure.

Para submeter um pedido através da Ajuda e Apoio Azure:

1. Selecione **Ajuda + suporte**.
1. **Selecione Criar um pedido de suporte**.
1. No formulário, selecione **Técnico** para **o tipo de Emissão**. Selecione a sua subscrição. Na lista de serviços, selecione **Confluent on Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Crie um pedido de apoio de ajuda.":::

Para submeter um pedido a partir do seu recurso, siga estes passos:

1. No portal Azure, selecione a sua organização Confluente.
1. A partir do menu do lado esquerdo do ecrã, selecione **Novo pedido de suporte**.
1. Para criar um pedido de suporte, selecione o link para o **portal Confluent**.

    :::image type="content" source="media/support-request.png" alt-text="Crie um pedido de apoio a partir de exemplo.":::

## <a name="next-steps"></a>Passos seguintes

Para obter ajuda na resolução de problemas, consulte [a Resolução de Problemas apache Kafka para soluções De Nuvem Confluente](troubleshoot.md).
