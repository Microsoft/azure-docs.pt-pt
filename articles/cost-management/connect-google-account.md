---
title: Ligar a uma conta do Google Cloud Platform ao Cloudyn no Azure | Documentos da Microsoft
description: Ligar uma conta do Google Cloud Platform para exibir dados de custo e a utilização em relatórios do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230138"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ligar uma conta do Google Cloud Platform

Pode ligar a sua conta do Google Cloud Platform existente ao Cloudyn. Depois de ligar a sua conta ao Cloudyn, dados de utilização e custos estão disponíveis nos relatórios do Cloudyn. Este artigo ajuda-o a configurar e ligar a sua conta Google no Cloudyn.


## <a name="collect-project-information"></a>Recolher informações do projeto

Comece a coleta de informações sobre o seu projeto.

1. Entre no console do Google Cloud Platform em [https://console.cloud.google.com](https://console.cloud.google.com).
2. Examine as informações do projeto que você deseja integrar ao Cloudyn e observe o **nome do projeto** e a **ID do projeto**. Manter as informações úteis para os passos seguintes.  
    ![nome do projeto e a ID do projeto mostrados no console do Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se a faturação não está ativada e ligada ao seu projeto, crie uma conta de cobrança. Para obter mais informações, consulte [criar uma nova conta de cobrança](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Ativar a exportação de faturação de bucket de armazenamento

Cloudyn obtém seus dados de faturação do Google a partir de um bucket de armazenamento. Mantenha o **nome do Bucket** e as informações de **prefixo do relatório** úteis para uso posterior durante o registro do Cloudyn.

Utilizar o Google Cloud Storage para armazenar os relatórios de utilização, incorre em taxas mínimas. Para obter mais informações, consulte [preços de armazenamento em nuvem](https://cloud.google.com/storage/pricing).

1. Se você não tiver habilitado a exportação de cobrança para um arquivo, siga as instruções em [como habilitar a exportação de cobrança para um arquivo](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Pode utilizar o formato de exportação de faturação JSON ou CSV.
2. Caso contrário, no console do Google Cloud Platform, navegue até **cobrança** > **exportação de cobrança**. Anote o **nome do Bucket** de cobrança e o **prefixo do relatório**.  
    ![informações de exportação de cobrança mostradas na página de exportação de cobrança](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Ativar as APIs do Google Cloud Platform

Para recolher informações de utilização e ativos, Cloudyn tem as seguintes APIs de plataforma de Cloud Google ativada:

- API de BigQuery
- Google Cloud SQL
- API do arquivo de dados do Google Cloud
- Google Cloud Storage
- API de JSON do armazenamento de Google Cloud
- API do motor de computação do Google

### <a name="enable-or-verify-apis"></a>Ativar ou certifique-se de APIs

1. Na consola do Google Cloud Platform, selecione o projeto que pretende registar no Cloudyn.
2. Navegue até **APIs & serviços** > **biblioteca**.
3. Utilize a pesquisa para encontrar que cada listados anteriormente apresentados pela API.
4. Para cada API, verifique se a **API habilitada** é mostrada. Caso contrário, clique em **habilitar**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adicionar uma conta do Google Cloud ao Cloudyn

1. Abra o portal do Cloudyn no portal do Azure ou navegue até [https://azure.cloudyn.com](https://azure.cloudyn.com/) e entre.
2. Clique em **configurações** (símbolo de engrenagem) e selecione **contas de nuvem**.
3. Em **Gerenciamento de contas**, selecione a guia **contas do Google** e clique em **Adicionar novo +** .
4. No **nome da conta do Google**, insira o endereço de email para a conta de cobrança e clique em **Avançar**.
5. Na caixa de diálogo de autenticação do Google, selecione ou insira uma conta do Google e, em seguida, **permita** o acesso cloudyn.com à sua conta.
6. Adicionar informações de pedidos de projeto que tinha anterior indicado. Eles incluem **ID do projeto**, nome do **projeto** , nome do Bucket de **cobrança** e prefixo do relatório do **arquivo de cobrança** e, em seguida, clique em **salvar**.  
    ![adicionar o Google Project ao Cloudyn Account](./media/connect-google-account/add-project.png)

Sua conta do Google aparece na lista de contas e deve dizer **autenticada**. Sob a mesma, o nome do projeto Google e o ID devem aparecer e ter um símbolo de marca de verificação verde. O status da conta deve ser **concluído**.

Em algumas horas, os relatórios do Cloudyn mostram informações de custo e a utilização do Google.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o Cloudyn, continue no tutorial [analisar o uso e os custos](./tutorial-review-usage.md) do Cloudyn.
