---
title: Os livros de trabalho do Azure Monitor trazem o seu próprio armazenamento
description: Saiba como proteger o seu livro, guardando o conteúdo do livro para o seu armazenamento
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618942"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Traga o seu próprio armazenamento para guardar livros

Há momentos em que pode ter uma consulta ou alguma lógica de negócio que quer assegurar. Os livros de trabalho oferecem uma opção para proteger os livros, guardando o conteúdo dos livros para o seu armazenamento. A conta de armazenamento pode então ser encriptada com as teclas geridas pela Microsoft ou pode gerir a encriptação fornecendo as suas próprias chaves. [Consulte a documentação do Azure sobre encriptação do serviço de armazenamento.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Livro de poupança com identidades geridas

1. Antes de guardar o livro para o seu armazenamento, terá de criar uma identidade gerida (Todos os Serviços -> Identidades Geridas) e dar-lhe `Storage Blob Data Contributor` acesso à sua conta de armazenamento. [Consulte a documentação do Azure sobre Identidades Geridas.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Screenshot mostrando a adição de uma atribuição de função](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Criar um novo livro.
3. Selecione o botão **Guardar** para guardar o livro.
4. Existe uma opção para `Save content to an Azure Storage Account` , selecionar a caixa de verificação para guardar para uma Conta de Armazenamento Azure.

    ![Screenshot mostrando um diálogo guardado](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Selecione a conta de armazenamento de desejo e o Recipiente. A lista de conta de Armazenamento é da Subscrição selecionada acima.

    ![Screenshot mostrando um diálogo de salvamento com opção de armazenamento](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Em seguida, selecione **Alterar** para selecionar uma identidade gerida previamente criada.

    [![Screenshot mostrando o diálogo de identidade de mudança](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Depois de ter selecionado as suas opções de armazenamento, prima **Guardar** para guardar o seu livro.

## <a name="limitations"></a>Limitações

- Ao poupar para armazenamento personalizado, não é possível fixar partes individuais do livro num painel de instrumentos, uma vez que os pinos individuais contêm informações protegidas no próprio painel de instrumentos. Ao utilizar o armazenamento personalizado, só é possível fixar ligações ao próprio livro de instrumentos aos dashboards.
- Uma vez guardado um livro para armazenamento personalizado, este será sempre guardado para armazenamento personalizado e este não pode ser desligado. Para guardar em outro lugar, pode usar "Save As" e optar por não guardar a cópia para armazenamento personalizado.
- Os livros de trabalho no recurso Application Insights são livros "legados" e não suportam armazenamento personalizado. Os mais recentes livros de trabalho no recurso Application Insights são os "... Mais" seleção. Os livros antigos não têm opções de Subscrição ao poupar.

   ![Screenshot mostrando livro legado](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar uma visualização [de mapas](workbooks-map-visualizations.md) em livros de trabalho.
- Saiba como utilizar [grupos em livros de trabalho.](../visualize/workbooks-groups.md)