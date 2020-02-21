---
title: Hospedar um site estático no Armazenamento Azure
description: Saiba como servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente de um recipiente numa conta Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 35b5a85ea6fba87e785b581a7a20d0c28f312820
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484150"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar um site estático no Armazenamento Azure

Pode servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente de um recipiente numa conta Azure Storage GPv2. Para saber mais, consulte site [estático hospedado no Armazenamento Azure.](storage-blob-static-website.md)

Este artigo mostra-lhe como permitir a hospedagem de websites estáticos utilizando o portal Azure, o Azure CLI ou powerShell.

<a id="portal" />

## <a name="portal"></a>[Portal](#tab/azure-portal)

Para um tutorial passo a passo, consulte [Tutorial: Hospedar um site estático no Armazenamento blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Depois de ativar o alojamento em sites estáticos, pode ver as páginas do seu site a partir de um browser utilizando o URL público do site.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Encontre o URL do site utilizando o portal Azure

No painel que aparece ao lado da página geral da conta da sua conta de armazenamento, selecione **Site Estático**. O URL do seu site aparece no campo **final primário.**

![Métrica de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode ativar o alojamento em sites estáticos utilizando a Interface de [Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra a [Shell Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então detete a sua subscrição ativa para a subscrição da conta de armazenamento que irá acolher o seu website estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o valor do espaço reservado `<subscription-id>` pelo ID da sua subscrição.

3. Ativar o alojamento em sites estáticos.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o valor `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que aparecerá para os utilizadores quando um navegador solicitar uma página no seu site que não existe.

   * Substitua o espaço reservado `<index-document-name>` com o nome do documento do índice. Este documento é comumente "index.html".

4. Faça upload de objetos para o recipiente *$web* a partir de um diretório de origem.

   > [!NOTE]
   > Se estiver a utilizar a Azure Cloud Shell, certifique-se de adicionar um personagem de fuga `\` ao referir-se ao recipiente `$web` (por exemplo: `\$web`). Se estiver a usar uma instalação local do Azure CLI, então não terá de usar o personagem de fuga.

   Este exemplo pressupõe que está a executar comandos da sessão Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
   ```

   * Substitua o valor `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua o espaço reservado `<source-path>` por um caminho para a localização dos ficheiros que pretende carregar.

   > [!NOTE]
   > Se estiver a utilizar uma instalação de localização do Azure CLI, então pode utilizar o caminho para qualquer local no seu computador local (por exemplo: `C:\myFolder`.
   >
   > Se estiver a usar a Azure Cloud Shell, terá de fazer referência a uma partilha de ficheiros que seja visível para a Cloud Shell. Esta localização pode ser a parte de ficheiro da Cloud partilhar-se ou uma partilha de ficheiros existente que montas a partir da Cloud Shell. Para aprender a fazer isto, consulte [ficheiros Persist em Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Encontre o URL do site utilizando o Azure CLI

Pode ver conteúdo a partir de um browser utilizando o URL público do site.

Encontre o URL utilizando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o valor `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o valor `<resource-group-name>` espaço reservado pelo nome do seu grupo de recursos.

<a id="powershell" />

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

   Substitua o valor do espaço reservado `<subscription-id>` pelo ID da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o valor `<resource-group-name>` espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o valor `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

6. Ativar o alojamento em sites estáticos.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que aparecerá para os utilizadores quando um navegador solicitar uma página no seu site que não existe.

   * Substitua o espaço reservado `<index-document-name>` com o nome do documento do índice. Este documento é comumente "index.html".

7. Faça upload de objetos para o recipiente *$web* a partir de um diretório de origem.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Properties @{ ContentType = "text/html; charset=utf-8";} `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Substitua o valor do espaço reservado `<path-to-file>` pelo caminho totalmente qualificado para o ficheiro que pretende carregar (por exemplo: `C:\temp\index.html`).

   * Substitua o valor do espaço reservado `<blob-name>` pelo nome que pretende dar à bolha resultante (por exemplo: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Encontre o URL do site usando powerShell

Pode ver conteúdo a partir de um browser utilizando o URL público do site.

Encontre o URL utilizando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o valor `<resource-group-name>` espaço reservado pelo nome do seu grupo de recursos.

* Substitua o valor `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>Ativar métricas em páginas de site estático

Uma vez ativado as métricas, as estatísticas de tráfego de ficheiros no contentor **$web** são reportadas no painel de métricas.

1. Clique em **Definições** > **monitorizar** **métricas** > .

   Os dados das métricas são gerados ligando-se a diferentes APIs métricas. O portal apenas apresenta membros DaPI utilizados dentro de um determinado período de tempo de forma a concentrar-se apenas nos membros que devolvem dados. De forma a garantir que é capaz de selecionar o membro API necessário, o primeiro passo é expandir o prazo.

2. Clique no botão de tempo e selecione **Last 24 horas** e, em seguida, clique **em Aplicar**.

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

## <a name="next-steps"></a>Passos Seguintes

* Aprenda a configurar um domínio personalizado com o seu site estático. Consulte um domínio personalizado para um ponto final de [armazenamento De Blob Azure](storage-custom-domain-name.md).

