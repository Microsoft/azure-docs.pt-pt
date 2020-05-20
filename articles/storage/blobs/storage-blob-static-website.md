---
title: Alojamento de site estático no Armazenamento do Azure
description: Hospedagem de websites estáticos do Azure Storage, fornecendo uma solução rentável e escalável para hospedar aplicações web modernas.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.openlocfilehash: 6a007525f8402bb163195b623173d665f9721bff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648501"
---
# <a name="static-website-hosting-in-azure-storage"></a>Alojamento de site estático no Armazenamento do Azure

Pode servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente a partir de um recipiente de armazenamento chamado *$web*. Hospedar o seu conteúdo no Armazenamento Azure permite-lhe utilizar arquiteturas sem servidores que incluem [funções Azure](/azure/azure-functions/functions-overview) e outras plataformas como serviços de serviço (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Se o seu site depender do código do lado do servidor, utilize o Serviço de [Aplicações Azure.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Criação de um site estático

O alojamento em sites estáticos é uma funcionalidade que você tem que ativar na conta de armazenamento.

Para ativar o alojamento estático do site, selecione o nome do seu ficheiro predefinido e, em seguida, opcionalmente, forneça um caminho para uma página personalizada de 404. Se um recipiente de armazenamento de blob chamado **$web** não existe já na conta, um é criado para si. Adicione os ficheiros do seu site a este recipiente.

Para obter orientação passo a passo, consulte [Host um site estático no Armazenamento Azure.](storage-blob-static-website-how-to.md)

![Métrica de sites estáticos de armazenamento azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Os ficheiros no contentor **$web** são sensíveis a casos, servidos através de pedidos de acesso anónimos e só estão disponíveis através de operações de leitura.

## <a name="uploading-content"></a>Carregar conteúdo

Pode utilizar qualquer uma destas ferramentas para fazer o upload de conteúdo para **o** $web recipiente:

> [!div class="checklist"]
> * [CLI do Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Módulo do Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão do Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualização de conteúdos

Os utilizadores podem ver o conteúdo do site a partir de um browser utilizando o URL público do website. Pode encontrar o URL utilizando o portal Azure, Azure CLI ou PowerShell. Ver [Encontrar o URL do site](storage-blob-static-website-how-to.md#portal-find-url).

Se o servidor devolver um erro de 404 e não tiver especificado um documento de erro quando ativou o website, então uma página padrão 404 é devolvida ao utilizador.

> [!NOTE]
> [O CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) não é suportado com um website estático.

### <a name="regional-codes"></a>Códigos regionais

O URL do seu site contém um código regional. Por exemplo, o URL `https://contosoblobaccount.z22.web.core.windows.net/` contém código regional `z22` .

Embora esse código tenha de permanecer no URL, é apenas para uso interno, e não terá de usar esse código de outra forma.

O documento de índice que especifica quando ativa o alojamento estático do website, aparece quando os utilizadores abrem o site e não especificam um ficheiro específico (por exemplo: `https://contosoblobaccount.z22.web.core.windows.net` ).  

### <a name="secondary-endpoints"></a>Pontos finais secundários

Se configurar [o despedimento numa região secundária,](../common/storage-redundancy.md#redundancy-in-a-secondary-region)também pode aceder ao conteúdo do website utilizando um ponto final secundário. Uma vez que os dados são replicados para regiões secundárias assincronicamente, os ficheiros disponíveis no ponto final secundário nem sempre estão em sintonia com os ficheiros disponíveis no ponto final primário. 

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da definição do nível de acesso público do contentor web

Pode modificar o nível de acesso público **do** $web contentor, mas isso não tem impacto no ponto final do site estático primário porque estes ficheiros são servidos através de pedidos de acesso anónimos. Isto significa acesso público (apenas para leitura) a todos os ficheiros.

A imagem que se segue mostra a definição do nível de acesso público no portal Azure:

![Screenshot mostrando como definir o nível de acesso do público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Embora o ponto final do site estático primário não seja afetado, uma alteração ao nível de acesso público afeta o ponto final do serviço de blob primário.

Por exemplo, se alterar o nível de acesso público do contentor **$web** do **Private (sem acesso anónimo)** ao **Blob (acesso de leitura anónima apenas para blobs)**, então o nível de acesso público ao ponto final do site estático primário `https://contosoblobaccount.z22.web.core.windows.net/index.html` não muda.

No entanto, o acesso público ao ponto final do serviço de blob primário `https://contosoblobaccount.blob.core.windows.net/$web/index.html` muda de privado para público. Agora os utilizadores podem abrir esse ficheiro utilizando qualquer um destes dois pontos finais.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapear um domínio personalizado para um URL do site estático

Pode disponibilizar o seu website estático através de um domínio personalizado. 

É mais fácil permitir o acesso http para o seu domínio personalizado, porque o Azure Storage apoia-o de forma nativa. Para ativar https, terá de utilizar o Azure CDN porque o Armazenamento Azure ainda não suporta de forma nativa HTTPS com domínios personalizados. ver Mapear um domínio personalizado para um ponto final de [armazenamento De Blob Azure](storage-custom-domain-name.md) para orientação passo a passo.

Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) sobre HTTPS, então os utilizadores devem utilizar o ponto final HTTPS. 

> [!TIP]
> Considere hospedar o seu domínio no Azure. Para mais informações, consulte [Host your domain in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adicionar cabeçalhos HTTP

Não há como configurar os cabeçalhos como parte da funcionalidade do site estático. No entanto, pode utilizar o Azure CDN para adicionar cabeçalhos e anexar (ou substituir) valores de cabeçalho. Consulte a referência do [motor standard regras para o Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Se quiser usar cabeçalhos para controlar o cache, consulte o comportamento de cache do [Control Azure CDN com regras](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)de cache .

## <a name="pricing"></a>Preços

Pode ativar o site estático hospedando gratuitamente. Você é cobrado apenas para o armazenamento blob que o seu site usa e custos de operações. Para mais detalhes sobre os preços do Armazenamento De Blob Azure, consulte a página de preços de [armazenamento de Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Pode ativar métricas em páginas de site estática. Uma vez ativado as métricas, as estatísticas de tráfego de ficheiros no contentor **$web** são reportadas no painel de métricas.

Para permitir métricas nas páginas do seu site estático, consulte [métricas de ativação nas páginas do site estático](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passos seguintes

* [Hospedar um site estático no Armazenamento Azure](storage-blob-static-website-how-to.md)
* [Mapear um domínio personalizado para um ponto final de armazenamento de Blob Azure](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicações do Azure](/azure/app-service/overview)
* [Construa a sua primeira aplicação web sem servidores](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Alojar o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
