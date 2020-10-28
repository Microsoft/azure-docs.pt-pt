---
title: Ligar uma conta do Google Cloud Platform à Cloudyn no Azure
description: Ligue uma conta do Google Cloud Platform para ver os custos e os dados de utilização nos relatórios da Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 8d549a8fd7c8b03f98fe2b11c94531ed323e0e3a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92526462"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ligar uma conta do Google Cloud Platform

Pode ligar a sua conta do Google Cloud Platform existente à Cloudyn. Após ligar a conta à Cloudyn, os custos e os dados de utilização ficam disponíveis nos relatórios da Cloudyn. Este artigo ajuda-o a configurar e a ligar a sua conta Google à Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="collect-project-information"></a>Recolher informações do projeto

Comece por recolher informações sobre o projeto.

1. Inicie sessão na consola do Google Cloud Platform em [https://console.cloud.google.com](https://console.cloud.google.com).
2. Analise as informações do projeto que quer integrar na Cloudyn e aponte o **Project name** (Nome do projeto) e o **Project ID** (ID do Projeto). Mantenha a informação disponível para os passos posteriores.  
    ![Project name (Nome do projeto) e Project ID (ID do projeto) apresentados na consola do Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se a faturação não estiver ativada e ligada ao projeto, crie uma conta de faturação. Para obter mais informações, veja [Create a new billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account) (Criar uma nova conta de faturação).

## <a name="enable-storage-bucket-billing-export"></a>Ativar a exportação da faturação dos registos de armazenamento

A Cloudyn obtém os dados de faturação da Google a partir de um registo de armazenamento. Mantenha as informações do **Bucket name** (Nome do registo) e do **Report prefix** (Prefixo do relatório) disponível para utilização posterior durante o registo da Cloudyn.

A utilização do Google Cloud Storage para armazenar os relatórios de utilização está sujeita ao pagamento de taxas mínimas. Para obter mais informações, veja [Cloud Storage Pricing](https://cloud.google.com/storage/pricing) (Preços do Cloud Storage).

1. Se não tiver ativado a exportação da faturação para um ficheiro, siga as instruções em [How to enable billing export to a file](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file) (Como ativar a exportação da faturação para um ficheiro). Pode utilizar o formato de exportação de faturação JSON ou CSV.
2. Caso contrário, na consola do Google Cloud Platform, navegue até **Billing** > **Billing export** (Faturação > Exportação da faturação). Anote o **Bucket name** (Nome do registo) e o **Report prefix** (Prefixo do relatório) de faturação.  
    ![Informações sobre a exportação da faturação apresentadas na página Billing export (Exportação da faturação)](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Ativar as APIs do Google Cloud Platform

Para recolher informações sobre a utilização e os recursos, a Cloudyn precisa das seguintes APIs do Google Cloud Platform ativadas:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Ativar ou verificar as APIs

1. Na consola do Google Cloud Platform, selecione o projeto que quer registar com a Cloudyn.
2. Navegue para **APIs & Services** > **Library** (APIs e Serviços > Biblioteca).
3. Utilize a pesquisa para localizar cada uma das APIs anteriormente listadas.
4. Em cada API, verifique se é apresentado **API enabled** (API ativada). Caso contrário, clique em **ENABLE** (ATIVAR).

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adicionar uma conta Google Cloud à Cloudyn

1. Abra o portal da Cloudyn a partir do portal do Azure ou navegue para [https://azure.cloudyn.com](https://azure.cloudyn.com/) e inicie sessão.
2. Clique em **Definições** (símbolo de engrenagem) e, em seguida, selecione **Contas Cloud** .
3. Em **Gestão de Contas** , selecione o separador **Contas Google** e, em seguida, clique em **Adicionar nova +** .
4. Em **Nome da Conta Google** , introduza um endereço de e-mail para a conta de faturação e, em seguida, clique em **Seguinte** .
5. No diálogo de autenticação da Google, selecione ou introduza uma conta Google e, em seguida, **PERMITA** o acesso de cloudyn.com à sua conta.
6. Adicione as informações do projeto pedido que tinha anotado anteriormente. As informações incluem o **ID de Projeto** , o nome do **Projeto** , o nome do registo de **faturação** e o Prefixo do relatório do **ficheiro de faturação** . Quando terminar, clique em **Guardar** .  
    ![Adicionar o projeto Google à conta Cloudyn](./media/connect-google-account/add-project.png)

A sua conta Google aparece na lista de contas e deve indicar **Autenticada** . Sob a mesma, deve ver o ID e o nome do projeto Google e ter um símbolo de marca de verificação verde. O Estado da Conta deve indicar **Concluído** .

Em poucas horas, os relatórios da Cloudyn mostram informações sobre os custos e a utilização do Google.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a Cloudyn, continue para o tutorial [Rever a utilização e os custos](tutorial-review-usage.md) da Cloudyn.
