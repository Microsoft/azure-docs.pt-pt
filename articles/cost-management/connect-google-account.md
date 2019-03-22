---
title: Ligar a uma conta do Google Cloud Platform ao Cloudyn no Azure | Documentos da Microsoft
description: Ligar uma conta do Google Cloud Platform para exibir dados de custo e a utilização em relatórios do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: b22d98c079a87943d3078fbe41e76074543c260a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002617"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ligar uma conta do Google Cloud Platform

Pode ligar a sua conta do Google Cloud Platform existente ao Cloudyn. Depois de ligar a sua conta ao Cloudyn, dados de utilização e custos estão disponíveis nos relatórios do Cloudyn. Este artigo ajuda-o a configurar e ligar a sua conta Google no Cloudyn.

> [!NOTE]
> Google modificou a segurança de conta, o que impede que novas ligações de que está a ser estabelecido entre o Cloudyn e Google. Cloudyn continua a recolher dados do Google para os utilizadores que já têm Cloudyn ligado para a Google. No entanto, é possível adicionar novas contas do Google ao Cloudyn atualmente. A equipe do Cloudyn não sabe quando o suporte para adicionar novas contas do Google ao Cloudyn será retomada. Vamos remover esta nota quando sai de suporte.

## <a name="collect-project-information"></a>Recolher informações do projeto

Comece a coleta de informações sobre o seu projeto.

1. Inicie sessão na consola do Google Cloud Platform em [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Reveja as informações de projeto que pretende integrar ao Cloudyn e tenha em atenção a **nome do projeto** e o **ID do projeto**. Manter as informações úteis para os passos seguintes.  
    ![Nome do projeto e o ID de projeto, mostrado na consola do Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se a faturação não está ativada e ligada ao seu projeto, crie uma conta de cobrança. Para obter mais informações, consulte [criar uma nova conta de faturação](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Ativar a exportação de faturação de bucket de armazenamento

Cloudyn obtém seus dados de faturação do Google a partir de um bucket de armazenamento. Manter o **nome do Bucket** e **prefixo de relatório** informações úteis para utilizar mais tarde durante o registo do Cloudyn.

Utilizar o Google Cloud Storage para armazenar os relatórios de utilização, incorre em taxas mínimas. Para obter mais informações, consulte [preços de armazenamento na Cloud](https://cloud.google.com/storage/pricing).

1. Se não tiver ativado a exportação de faturação para um ficheiro, siga as instruções em [como ativar a exportação de faturação para um ficheiro](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Pode utilizar o formato de exportação de faturação JSON ou CSV.
2. Caso contrário, na consola do Google Cloud Platform, navegue até **faturação** > **exportação de faturação**. Tenha em atenção a sua cobrança **nome do Bucket** e **prefixo de relatório**.  
    ![Informações de exportação de faturação mostradas na página de exportação de faturação](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Ativar as APIs do Google Cloud Platform

Para recolher informações de utilização e ativos, Cloudyn tem as seguintes APIs de plataforma de Cloud Google ativada:

- API de BigQuery
- Google Cloud SQL
- API do arquivo de dados do Google Cloud
- Armazenamento na Cloud do Google
- API de JSON do armazenamento de Google Cloud
- API do motor de computação do Google

### <a name="enable-or-verify-apis"></a>Ativar ou certifique-se de APIs

1. Na consola do Google Cloud Platform, selecione o projeto que pretende registar no Cloudyn.
2. Navegue para **APIs e serviços** > **biblioteca**.
3. Utilize a pesquisa para encontrar que cada listados anteriormente apresentados pela API.
4. Para cada API, certifique-se de que **API ativada** é mostrado. Caso contrário, clique em **ATIVAR**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adicionar uma conta do Google Cloud ao Cloudyn

1. Abrir o portal da Cloudyn a partir do portal do Azure ou navegue até [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) e iniciar sessão.
2. Clique em **configurações** (símbolo de engrenagem) e, em seguida, selecione **contas da Cloud**.
3. Na **gestão de contas**, selecione a **contas do Google** separador e, em seguida, clique em **adicionar novo +**.
4. Na **nome da conta Google**, introduza o endereço de e-mail para a conta de cobrança, em seguida, clique em **próxima**.
5. Na caixa de diálogo de autenticação de Google, selecione ou introduza uma conta do Google e, em seguida **permitir** cloudyn.com acesso à sua conta.
6. Adicionar informações de pedidos de projeto que tinha anterior indicado. Eles incluem **ID do projeto**, **projeto** nome, **faturação** nome do registo, e **ficheiro faturação** prefixo de relatório, em seguida, clique em  **Guardar**.  
    ![Adicionar o projeto Google à conta do Cloudyn](./media/connect-google-account/add-project.png)

Sua conta Google aparece na lista de contas e deverá indicar **autenticado**. Sob a mesma, o nome do projeto Google e o ID devem aparecer e ter um símbolo de marca de verificação verde. Estado da conta deverá indicar **concluído**.

Em algumas horas, os relatórios do Cloudyn mostram informações de custo e a utilização do Google.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o Cloudyn, avance para o [rever a utilização e custos](./tutorial-review-usage.md) tutorial da Cloudyn.
