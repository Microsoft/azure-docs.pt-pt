---
title: Alojar um Web site estático no armazenamento do Azure
description: Aprenda a servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente a partir de um contentor numa conta GPv2 de armazenamento do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5ab24a99b22fae172b5308ba7477953f27ecfd44
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435943"
---
# <a name="host-a-static-website-in-azure-storage"></a>Alojar um Web site estático no armazenamento do Azure

Pode servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente a partir de um contentor numa conta GPv2 de armazenamento do Azure. Para obter mais informações, consulte [alojar o Web site estático no armazenamento do Azure](storage-blob-static-website.md).

Este artigo mostra-lhe como permitir o alojamento de Web site estático com o portal do Azure, a CLI do Azure ou o PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para obter um tutorial passo a passo, consulte [Tutorial: Alojar um Web site estático no armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Depois de ativar o alojamento de Web sites estáticos, pode ver as páginas do seu site num browser utilizando o URL pública do site.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Encontrar o URL de Web site com o portal do Azure

No painel que aparece ao lado da página de descrição geral da conta da sua conta de armazenamento, selecione **Web site estático**. O URL do seu site é apresentado na **ponto final primário** campo.

![Métrica de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Pode ativar o alojamento de Web site estático, utilizando o [Interface de linha de comandos (CLI do Azure)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se tiver [instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma assinatura, em seguida, defina seu Active Directory subscrição a subscrição da conta de armazenamento que irá alojar o seu Web site estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do marcador de posição com o ID da sua subscrição.

3. Permitir o alojamento de Web site estático.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o `<storage-account-name>` valor do marcador de posição pelo nome da sua conta de armazenamento.

   * Substitua o `<error-document-name>` marcador de posição pelo nome do documento de erro que será apresentado aos utilizadores quando um navegador solicita uma página no site que não existe.

   * Substitua o `<index-document-name>` marcador de posição pelo nome do documento de índice. Este documento geralmente é "Index".

4. Carregar objetos para o *$web* contentor a partir de um diretório de origem.

   > [!NOTE]
   > Se estiver a utilizar o Azure Cloud Shell, certifique-se de adicionar uma `\` carácter de escape quando nos Referimos ao `$web` contentor (por exemplo: `\$web`). Se estiver a utilizar uma instalação local da CLI do Azure, não terá de utilizar o caráter de escape.

   Este exemplo assume que está a executar comandos da sessão do Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Substitua o `<storage-account-name>` valor do marcador de posição pelo nome da sua conta de armazenamento.

   * Substitua o `<source-path>` marcador de posição com um caminho para a localização dos ficheiros que pretende carregar.

   > [!NOTE]
   > Se estiver a utilizar uma instalação local da CLI do Azure, em seguida, pode utilizar o caminho para qualquer localização no seu computador local (por exemplo: `C:\myFolder`.
   >
   > Se estiver a utilizar o Azure Cloud Shell, terá de fazer referência a uma partilha de ficheiros visível para o Cloud Shell. Esta localização pode ser a partilha de ficheiros de Cloud partilhar em si ou uma partilha de ficheiros existente, monte a partir do Cloud Shell. Para saber como fazer isso, consulte [manter os ficheiros no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Encontrar o URL do Web site com a CLI do Azure

Pode ver o conteúdo a partir de um navegador usando a URL pública do site.

Encontre o URL com o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o `<storage-account-name>` valor do marcador de posição pelo nome da sua conta de armazenamento.

* Substitua o `<resource-group-name>` valor do marcador de posição pelo nome do seu grupo de recursos.

<a id="powershell" />

## <a name="use-powershell"></a>Utilizar o PowerShell

Pode ativar o alojamento de Web site estático com o módulo Azure PowerShell.

1. Abra uma janela de comando do Windows PowerShell.

2. Certifique-se de que tem o Azure PowerShell versão do módulo Az 0,7 ou posterior.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

3. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

4. Se a sua identidade estiver associada a mais de uma assinatura, em seguida, defina seu Active Directory subscrição a subscrição da conta de armazenamento que irá alojar o seu Web site estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do marcador de posição com o ID da sua subscrição.

5. Obtenha o contexto de conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do marcador de posição pelo nome do seu grupo de recursos.

   * Substitua o `<storage-account-name>` valor do marcador de posição pelo nome da sua conta de armazenamento.

6. Permitir o alojamento de Web site estático.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o `<error-document-name>` marcador de posição pelo nome do documento de erro que será apresentado aos utilizadores quando um navegador solicita uma página no site que não existe.

   * Substitua o `<index-document-name>` marcador de posição pelo nome do documento de índice. Este documento geralmente é "Index".

7. Carregar objetos para o *$web* contentor a partir de um diretório de origem.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Substitua a `<path-to-file>` valor de espaço reservado com o caminho totalmente qualificado para o ficheiro que pretende carregar (por exemplo: `C:\temp\index.html`).

   * Substitua a `<blob-name>` valor de marcador de posição pelo nome que pretende conceder o blob resultante (por exemplo: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Encontrar o URL de Web site com o PowerShell

Pode ver o conteúdo a partir de um navegador usando a URL pública do site.

Encontre o URL com o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o `<resource-group-name>` valor do marcador de posição pelo nome do seu grupo de recursos.

* Substitua o `<storage-account-name>` valor do marcador de posição pelo nome da sua conta de armazenamento.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Ativar as métricas nas páginas de Web site estático

Assim que tive ativado as métricas, estatísticas de ficheiros de tráfego a **$web** contentor são apresentadas no dashboard de indicadores.

1. Clique em **configurações** > **monitorização** > **métricas**.

   Dados de métricas são gerados por conexão com métricas diferentes APIs. O portal apresenta apenas os membros da API utilizados num determinado período de tempo para manter o foco somente em membros que retornam dados. Para garantir que pode selecionar o membro da API necessário, a primeira etapa é expandir o intervalo de tempo.

2. Clique no botão de período de tempo e selecione **últimas 24 horas** e, em seguida, clique em **aplicar**.

   ![Intervalo de tempo de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **Blob** partir a *espaço de nomes* menu pendente.

   ![Namespace de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione o **saída** métrica.

   ![Métrica de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **soma** partir do *agregação* Seletor.

   ![Agregação de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique nas **Adicionar filtro** botão e escolha **nome da API** do *propriedade* Seletor.

   ![Nome de API de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Veri a caixa junto a **GetWebContent** no *valores* Seletor para preencher o relatório de métrica.

   ![Métricas de Web sites estáticos de armazenamento do Azure GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Passos Seguintes

* [Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)
* [Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para o ponto final do blob ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicações do Azure](/azure/app-service/overview)
* [Crie seu primeiro aplicativo da web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Aloje o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
