---
title: Hospedar um site estático no Armazenamento Azure
description: Saiba como servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente de um recipiente numa conta Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247015"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar um site estático no Armazenamento Azure

Pode servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente de um recipiente numa conta Azure Storage GPv2. Para saber mais, consulte site [estático hospedado no Armazenamento Azure.](storage-blob-static-website.md)

Este artigo mostra-lhe como permitir a hospedagem de websites estáticos utilizando o portal Azure, o Azure CLI ou powerShell.

## <a name="enable-static-website-hosting"></a>Ativar hospedagem de site estático

O alojamento em sites estáticos é uma funcionalidade que você tem que ativar na conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

2. Localize a sua conta de armazenamento e exiba a visão geral da conta.

3. Selecione **site estático** para exibir a página de configuração para sites estáticos.

4. Selecione **Habilitado** para permitir o alojamento estático do site para a conta de armazenamento.

5. No campo de nome do **documento Index,** especifique uma página de índice predefinido (por exemplo: *index.html*). 

   A página de índice predefinido é apresentada quando um utilizador navega para a raiz do seu website estático.  

6. No campo de trajetória do **erro,** especifique uma página de erro predefinido (por exemplo: *404.html*). 

   A página de erro predefinida é apresentada quando um utilizador tenta navegar para uma página que não existe no seu site estático.

7. Clique em **Guardar**. O portal Azure apresenta agora o seu ponto final do site estático. 

    ![Ativar hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

Pode ativar o alojamento em sites estáticos utilizando a Interface de [Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra a [Shell Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então detete a sua subscrição ativa para a subscrição da conta de armazenamento que irá acolher o seu website estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pela identificação da sua subscrição.

3. Ativar o alojamento em sites estáticos.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua `<storage-account-name>` o valor do espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua `<error-document-name>` o espaço reservado pelo nome do documento de erro que aparecerá para os utilizadores quando um navegador solicitar uma página no seu site que não existe.

   * Substitua `<index-document-name>` o espaço reservado com o nome do documento do índice. Este documento é comumente "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

Pode ativar o alojamento estático do site utilizando o módulo PowerShell Azure.

1. Abra uma janela de comando Windows PowerShell.

2. Verifique se tem o módulo Azure PowerShell Az versão 0.7 ou posterior.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

3. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

4. Se a sua identidade estiver associada a mais de uma subscrição, então detete a sua subscrição ativa para a subscrição da conta de armazenamento que irá acolher o seu website estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pela identificação da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua `<storage-account-name>` o valor do espaço reservado pelo nome da sua conta de armazenamento.

6. Ativar o alojamento em sites estáticos.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua `<error-document-name>` o espaço reservado pelo nome do documento de erro que aparecerá para os utilizadores quando um navegador solicitar uma página no seu site que não existe.

   * Substitua `<index-document-name>` o espaço reservado com o nome do documento do índice. Este documento é comumente "index.html".

---

## <a name="upload-files"></a>Carregar ficheiros 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Estas instruções mostram-lhe como carregar ficheiros utilizando a versão do Storage Explorer que aparece no portal Azure. No entanto, também pode utilizar a versão do [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) que corre fora do portal Azure. Pode utilizar [a AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI ou qualquer aplicação personalizada que possa enviar ficheiros para **o** $web contentor da sua conta. Para um tutorial passo a passo que faz upload de ficheiros utilizando o código do Estúdio Visual, consulte [Tutorial: Hospedar um site estático no Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selecione **Storage Explorer (pré-visualização)**.

2. Expanda o nó **BLOB CONTAINERS** e, em seguida, selecione o recipiente **$web.**

3. Escolha o botão **Upload** para carregar ficheiros.

   ![Carregar ficheiros](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se pretender que o navegador exiba o conteúdo do ficheiro, certifique-se de que o tipo de conteúdo desse ficheiro está definido para `text/html`. 

   ![Verificar os tipos de conteúdos](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > O Storage Explorer define automaticamente `text/html` esta propriedade para extensões comumente reconhecidas, tais como `.html`. No entanto, em alguns casos, terá que definir isto por si mesmo. Se não configurar esta `text/html`propriedade para , o navegador irá pedir aos utilizadores para descarregarem o ficheiro em vez de renderizarem o conteúdo. Para definir esta propriedade, clique no ficheiro e clique em **Propriedades**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Faça upload de objetos para o recipiente *$web* a partir de um diretório de origem.

> [!NOTE]
> Se estiver a utilizar a Casca de Nuvem `\` Azure, certifique-se de adicionar um carácter de fuga quando se refere ao `$web` recipiente (por exemplo: `\$web`). Se estiver a usar uma instalação local do Azure CLI, então não terá de usar o personagem de fuga.

Este exemplo pressupõe que está a executar comandos da sessão Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Substitua `<storage-account-name>` o valor do espaço reservado pelo nome da sua conta de armazenamento.

* Substitua `<source-path>` o espaço reservado por um caminho para a localização dos ficheiros que pretende carregar.

> [!NOTE]
> Se estiver a utilizar uma instalação de localização do Azure CLI, então pode `C:\myFolder`utilizar o caminho para qualquer local no seu computador local (por exemplo: .
>
> Se estiver a usar a Azure Cloud Shell, terá de fazer referência a uma partilha de ficheiros que seja visível para a Cloud Shell. Esta localização pode ser a parte de ficheiro da Cloud partilhar-se ou uma partilha de ficheiros existente que montas a partir da Cloud Shell. Para aprender a fazer isto, consulte [ficheiros Persist em Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Faça upload de objetos para o recipiente *$web* a partir de um diretório de origem.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Substitua `<path-to-file>` o valor do espaço reservado pelo caminho totalmente qualificado para `C:\temp\index.html`o ficheiro que pretende carregar (por exemplo: ).

* Substitua `<blob-name>` o valor do espaço reservado pelo nome que pretende `index.html`dar à bolha resultante (por exemplo: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Encontre o URL do site utilizando o portal Azure

Pode ver as páginas do seu site a partir de um browser utilizando o URL público do site.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

No painel que aparece ao lado da página geral da conta da sua conta de armazenamento, selecione **Site Estático**. O URL do seu site aparece no campo **final primário.**

![Métrica de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Encontre o URL público do seu website estático utilizando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua `<storage-account-name>` o valor do espaço reservado pelo nome da sua conta de armazenamento.

* Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Encontre o URL público do seu website estático utilizando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua `<resource-group-name>` o valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua `<storage-account-name>` o valor do espaço reservado pelo nome da sua conta de armazenamento.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Ativar métricas em páginas de site estático

Uma vez ativado as métricas, as estatísticas de tráfego de ficheiros no contentor **$web** são reportadas no painel de métricas.

1. Clique em **Métricas** na secção **Monitor** do menu da conta de armazenamento.

   > [!div class="mx-imgBorder"]
   > ![Ligação métrica](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Os dados das métricas são gerados ligando-se a diferentes APIs métricas. O portal apenas apresenta membros DaPI utilizados dentro de um determinado período de tempo de forma a concentrar-se apenas nos membros que devolvem dados. De forma a garantir que é capaz de selecionar o membro API necessário, o primeiro passo é expandir o prazo.

2. Clique no botão de tempo, escolha um quadro de tempo e, em seguida, clique em **Aplicar**.

   ![Gama de tempo métricas de métricas de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **Blob** a partir do *Namespace* drop down.

   ![Azure Storage site estática métricas nomespace](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egress.**

   ![Métrica de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Soma** do seletor de *agregação.*

   ![Agregação de métricas de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **'Adicionar'** e escolha o **nome API** do seletor *de propriedade.*

   ![Azure Storage site estática métricas Nome API](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Verifique a caixa ao lado do **GetWebContent** no seletor *de Valores* para preencher o relatório de métricas.

   ![Métricas de sites estáticos de armazenamento azure GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A caixa de verificação **GetWebContent** só aparece se esse membro da API for utilizado dentro de um determinado período de tempo. O portal apenas apresenta membros DaPI utilizados dentro de um determinado período de tempo de forma a concentrar-se apenas nos membros que devolvem dados. Se não encontrar um membro específico da API nesta lista, expanda o prazo.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a configurar um domínio personalizado com o seu site estático. Consulte um domínio personalizado para um ponto final de [armazenamento De Blob Azure](storage-custom-domain-name.md).

