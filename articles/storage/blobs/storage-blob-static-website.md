---
title: Static website hosting in Azure Storage (Alojamento de sites estáticos no Armazenamento do Microsoft Azure)
description: Hospedagem estática de website Azure Storage, fornecendo uma solução rentável e escalável para hospedar aplicações web modernas.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 952d0acb00a25fe7d84738825cbad017e5b18029
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892698"
---
# <a name="static-website-hosting-in-azure-storage"></a>Static website hosting in Azure Storage (Alojamento de sites estáticos no Armazenamento do Microsoft Azure)

Pode servir conteúdo estático (HTML, CSS, JavaScript e ficheiros de imagem) diretamente a partir de um recipiente de armazenamento denominado *$web*. Hospedar o seu conteúdo no Azure Storage permite-lhe utilizar arquiteturas sem servidor que incluam [Funções Azure](/azure/azure-functions/functions-overview) e outras plataformas como serviços (PaaS). O alojamento estático do website Azure Storage é uma ótima opção nos casos em que não é necessário um servidor web para renderizar conteúdo.

[App Service Static Web Apps](https://azure.microsoft.com/services/app-service/static/) é uma ótima alternativa para hospedar websites estáticos Azure Storage e também é apropriado em casos em que você não precisa de um servidor web para renderizar conteúdo. As Aplicações Web Estáticas do Serviço de Aplicações fornecem-lhe um fluxo de trabalho de integração contínua e entrega contínua (CI/CD) totalmente gerido da fonte do GitHub para a implementação global.

Se precisar de um servidor web para renderizar conteúdo, pode utilizar [o Azure App Service](https://azure.microsoft.com/services/app-service/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Certifique-se de criar uma conta de armazenamento v2 Standard para fins gerais . Os sites estáticos não estão disponíveis em qualquer outro tipo de conta de armazenamento.

## <a name="setting-up-a-static-website"></a>Criação de um website estático

O alojamento estático do site é uma funcionalidade que tem de ativar na conta de armazenamento.

Para ativar o alojamento estático do site, selecione o nome do seu ficheiro predefinido e, em seguida, opcionalmente, forneça um caminho para uma página personalizada de 404. Se um recipiente de armazenamento blob chamado **$web** já não existir na conta, um é criado para si. Adicione os ficheiros do seu site a este recipiente.

Para obter orientação passo a passo, consulte [o Host a estática no Azure Storage](storage-blob-static-website-how-to.md).

![Métricas de métricas de websites estáticos de armazenamento Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Os ficheiros no **$web** contentor são sensíveis a casos, servidos através de pedidos de acesso anónimos e só estão disponíveis através de operações de leitura.

## <a name="uploading-content"></a>Conteúdo de upload

Pode utilizar qualquer uma destas ferramentas para enviar conteúdo para o recipiente **$web:**

> [!div class="checklist"]
> * [CLI do Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Módulo do Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão do Visual Studio Code](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualização de conteúdos

Os utilizadores podem ver o conteúdo do site a partir de um navegador usando o URL público do site. Pode encontrar o URL utilizando o portal Azure CLI ou PowerShell. Ver [Localizar o URL do site.](storage-blob-static-website-how-to.md#portal-find-url)

Se o servidor retornar um erro 404 e não tiver especificado um documento de erro quando ativou o website, então uma página 404 predefinida é devolvida ao utilizador.

> [!NOTE]
> [O suporte de partilha de recursos de origem cruzada (CORS) para armazenamento de Azure](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) não é suportado com website estático.

### <a name="regional-codes"></a>Códigos regionais

O URL do seu site contém um código regional. Por exemplo, o URL `https://contosoblobaccount.z22.web.core.windows.net/` contém código `z22` regional.

Embora esse código tenha de permanecer no URL, é apenas para uso interno, e não terá de usar esse código de outra forma.

O documento de índice que especifica quando ativa o alojamento estático do site, aparece quando os utilizadores abrem o site e não especificam um ficheiro específico (por exemplo: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>Pontos finais secundários

Se [configurar redundância numa região secundária,](../common/storage-redundancy.md#redundancy-in-a-secondary-region)também pode aceder ao conteúdo do site utilizando um ponto final secundário. Como os dados são replicados para regiões secundárias de forma assíncrona, os ficheiros que estão disponíveis no ponto final secundário nem sempre estão em sintonia com os ficheiros disponíveis no ponto final primário.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da definição do nível de acesso público do recipiente web

Pode modificar o nível de acesso público do recipiente **$web,** mas isso não tem impacto no ponto final do site estático primário porque estes ficheiros são servidos através de pedidos de acesso anónimos. Isto significa acesso público (apenas para leitura) a todos os ficheiros.

A imagem que se segue mostra a definição do nível de acesso público no portal Azure:

![Screenshot mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Embora o principal ponto final do site estático não seja afetado, uma alteração no nível de acesso público tem impacto no ponto final do serviço blob primário.

Por exemplo, se alterar o nível de acesso público do **$web** contentor de **Private (sem acesso anónimo)** ao **Blob (acesso anónimo de leitura apenas para bolhas),** então o nível de acesso público ao principal ponto final do site estático `https://contosoblobaccount.z22.web.core.windows.net/index.html` não muda.

No entanto, o acesso do público ao principal ponto final do serviço blob `https://contosoblobaccount.blob.core.windows.net/$web/index.html` muda de privado para público. Agora os utilizadores podem abrir esse ficheiro utilizando qualquer um destes dois pontos finais.

Desativar o acesso público a uma conta de armazenamento não afeta sites estáticos que estão hospedados nessa conta de armazenamento. Para mais informações, consulte o público [anónimo Configure acesso para contentores e bolhas.](anonymous-read-access-configure.md)

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapear um domínio personalizado para um URL estático do site

Pode disponibilizar o seu website estático através de um domínio personalizado.

É mais fácil ativar o acesso HTTP para o seu domínio personalizado, porque o Azure Storage suporta-o de forma nativa. Para ativar HTTPS, terá de utilizar o Azure CDN porque o Azure Storage ainda não suporta https de forma nativa com domínios personalizados. ver [Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md) para uma orientação passo a passo.

Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) sobre HTTPS, então os utilizadores devem utilizar o ponto final HTTPS.

> [!TIP]
> Considere hospedar o seu domínio em Azure. Para obter mais informações, consulte [Host your domain in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adicionar cabeçalhos HTTP

Não há como configurar cabeçalhos como parte da funcionalidade estática do site. No entanto, pode utilizar o Azure CDN para adicionar cabeçalhos e valores de cabeçalho (ou substituição). Consulte [a referência do motor standard para Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Se pretender utilizar cabeçalhos para controlar o caching, consulte [o comportamento do caching do Control Azure CDN com regras de caching](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="multi-region-website-hosting"></a>Hospedagem de websites multi-regiões

Se planeia hospedar um website em várias geografias, recomendamos que utilize uma [Rede de Entrega de Conteúdos](https://docs.microsoft.com/azure/cdn/) para caching regional. Utilize [a Porta frontal Azure](https://docs.microsoft.com/azure/frontdoor/) se quiser servir conteúdos diferentes em cada região. Também fornece capacidades de failover. [O Gestor de Tráfego Azure](https://docs.microsoft.com/azure/traffic-manager/) não é recomendado se pretender utilizar um domínio personalizado. Os problemas podem surgir devido à forma como o Azure Storage verifica nomes de domínio personalizados.


## <a name="pricing"></a>Preços

Pode ativar o alojamento estático do site gratuitamente. É cobrado apenas pelo armazenamento de bolhas que o seu site utiliza e os custos de operações. Para obter mais detalhes sobre os preços do Azure Blob Storage, consulte a [página de preços de armazenamento Azure Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Pode ativar métricas em páginas estáticas do site. Uma vez ativadas as métricas, as estatísticas de tráfego sobre ficheiros no **$web** contentor são reportadas no painel de métricas.

Para ativar métricas nas páginas estáticas do site, consulte [Ativar as métricas nas páginas estáticas do site](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passos seguintes

* [Hospedar um site estático no Azure Storage](storage-blob-static-website-how-to.md)
* [Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicações do Azure](/azure/app-service/overview)
* [Construa o seu primeiro aplicativo web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Alojar o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
