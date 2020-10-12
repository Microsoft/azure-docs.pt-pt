---
title: Hospedar um site estático no Azure Storage
description: Saiba como servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente de um recipiente numa conta GPv2 de armazenamento Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 052a28dc69bf5c758133ca98366efc63105f4a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289876"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar um site estático no Azure Storage

Pode servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente a partir de um recipiente numa conta GPv2 de armazenamento Azure. Para saber mais, consulte [o site Static hospedado no Azure Storage.](storage-blob-static-website.md)

Este artigo mostra-lhe como permitir hospedagem de websites estáticos utilizando o portal Azure, o Azure CLI ou PowerShell.

> [!NOTE]
> Certifique-se de criar uma conta de armazenamento v2 Standard para fins gerais . Os sites estáticos não estão disponíveis em qualquer outro tipo de conta de armazenamento.

## <a name="enable-static-website-hosting"></a>Ativar hospedagem estática no site

O alojamento estático do site é uma funcionalidade que tem de ativar na conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

2. Localize a sua conta de armazenamento e apresente a visão geral da conta.

3. Selecione **site estático** para exibir a página de configuração para sites estáticos.

4. Selecione **Ativado** para permitir hospedagem estática do site para a conta de armazenamento.

5. No campo **de nome do documento Index,** especifique uma página de índice predefinido (por exemplo:index.htm* l*). 

   A página de índice predefinido é apresentada quando um utilizador navega para a raiz do seu website estático.  

6. No campo de trajetória do **documento Erro,** especifique uma página de erro por defeito (Por exemplo: *404.html*). 

   A página de erro predefinida é exibida quando um utilizador tenta navegar para uma página que não existe no seu website estático.

7. Clique em **Guardar**. O portal Azure apresenta agora o seu ponto final estático do site. 

    ![Ativar hospedagem estática no site para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

<a id="cli"></a>

Pode ativar o alojamento estático do site utilizando a [Interface Azure Command-Line (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

3. Ativar o alojamento estático do site.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que aparecerá aos utilizadores quando um navegador solicita uma página no seu site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é comumente "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Pode ativar o alojamento estático do site utilizando o módulo Azure PowerShell.

1. Abra uma janela de comando Windows PowerShell.

2. Verifique se tem a versão Azure PowerShell Az ou mais tarde.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

3. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

4. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

6. Ativar o alojamento estático do site.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que aparecerá aos utilizadores quando um navegador solicita uma página no seu site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é comumente "index.html".

---

## <a name="upload-files"></a>Carregar ficheiros 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Estas instruções mostram-lhe como fazer o upload de ficheiros utilizando a versão do Storage Explorer que aparece no portal Azure. No entanto, também pode utilizar a versão do [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) que corre fora do portal Azure. Pode utilizar [AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI ou qualquer aplicação personalizada que possa enviar ficheiros para **o** $web recipiente da sua conta. Para obter um tutorial passo a passo que faça upload de ficheiros utilizando o código Visual Studio, consulte [Tutorial: Hospedar um website estático no Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selecione **Explorador de Armazenamento (pré-visualização)**.

2. Expanda o nó **BLOB CONTAINERS** e, em seguida, selecione **o** $web recipiente.

3. Escolha o botão **Upload** para carregar ficheiros.

   ![Carregar ficheiros](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se pretender que o navegador exiba o conteúdo do ficheiro, certifique-se de que o tipo de conteúdo desse ficheiro está definido para `text/html` . 

   ![Verifique tipos de conteúdo](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > O Storage Explorer define automaticamente esta propriedade `text/html` para extensões geralmente reconhecidas, tais como `.html` . No entanto, em alguns casos, terá que definir isto por si mesmo. Caso não desafie esta propriedade, o navegador irá solicitar aos `text/html` utilizadores que descarreguem o ficheiro em vez de renderizarem o conteúdo. Para definir esta propriedade, clique com o botão direito no ficheiro e, em seguida, clique em **Propriedades**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Faça o upload de objetos para o contentor *$web* de um diretório de origem.

Este exemplo pressupõe que está a executar comandos a partir da sessão Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Se o navegador pedir aos utilizadores que descarreguem o ficheiro em vez de renderizarem o conteúdo, pode anexar `--content-type 'text/html; charset=utf-8'` o comando. 

* Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o `<source-path>` espaço reservado por um caminho para a localização dos ficheiros que pretende carregar.

> [!NOTE]
> Se estiver a utilizar uma instalação de localização do Azure CLI, então pode utilizar o caminho para qualquer local do seu computador local (por exemplo: `C:\myFolder` .
>
> Se estiver a usar a Azure Cloud Shell, terá de fazer referência a uma partilha de ficheiros visível para a Cloud Shell. Esta localização pode ser a parte de ficheiros da partilha da Cloud ou uma partilha de ficheiros existente que monta a partir da Cloud Shell. Para aprender a fazê-lo, consulte [os ficheiros Persist em Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Faça o upload de objetos para o contentor *$web* de um diretório de origem.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Se o navegador pedir aos utilizadores que descarreguem o ficheiro em vez de renderizarem o conteúdo, pode anexar `-Properties @{ ContentType = "text/html; charset=utf-8";}` o comando.

* Substitua o `<path-to-file>` valor do espaço reservado pelo caminho totalmente qualificado para o ficheiro que pretende carregar (Por exemplo: `C:\temp\index.html` ).

* Substitua o `<blob-name>` valor do espaço reservado pelo nome que pretende dar à bolha resultante (Por exemplo: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Encontre o URL do site

Pode ver as páginas do seu site a partir de um browser utilizando o URL público do site.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No painel que aparece ao lado da página geral da conta da sua conta de armazenamento, selecione **Estática Website**. O URL do seu site aparece no campo **do ponto final primário.**

![Métricas de métricas de websites estáticos de armazenamento Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Encontre o URL público do seu website estático utilizando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Encontre o URL público do seu website estático utilizando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Ativar métricas em páginas estáticas do site

Uma vez ativadas as métricas, as estatísticas de tráfego sobre ficheiros no **$web** contentor são reportadas no painel de métricas.

1. Clique **em Métricas** na secção **Monitor** do menu da conta de armazenamento.

   > [!div class="mx-imgBorder"]
   > ![Ligação métrica](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Os dados das métricas são gerados ligando-se a diferentes métricas APIs. O portal apenas apresenta membros da API utilizados dentro de um determinado prazo para se concentrar apenas nos membros que devolvem dados. Para garantir que é capaz de selecionar o membro da API necessário, o primeiro passo é expandir o prazo.

2. Clique no botão de tempograma, escolha um prazo e, em seguida, clique em **Aplicar**.

   ![Intervalo de tempo de métricas de métricas de websites estáticos de armazenamento Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **Blob** do *Namespace* drop down.

   ![Azure Storage static websites métricas espaço de nome](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egress.**

   ![Métricas de métricas de websites estáticos de armazenamento Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Soma** no seletor *de agregação.*

   ![Agregação de métricas de websites estáticos de armazenamento Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **adicionar filtro** e escolha o **nome API** no seletor *de propriedade.*

   ![Azure Storage estático websites métricas nome API](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Verifique a caixa ao lado **do GetWebContent** no seletor *valores* para preencher o relatório de métricas.

   ![Métricas de websites estáticos de armazenamento Azure GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A caixa de verificação **GetWebContent** só aparece se o membro da API for utilizado dentro de um determinado prazo. O portal apenas apresenta membros da API utilizados dentro de um determinado prazo para se concentrar apenas nos membros que devolvem dados. Se não encontrar um membro específico da API nesta lista, expanda o prazo.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a configurar um domínio personalizado com o seu website estático. Consulte [um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md).

