---
title: Hospedagem de site estática no armazenamento do Azure
description: Hospedagem de sites estáticos do armazenamento do Azure, fornecendo uma solução econômica e escalonável para hospedar aplicativos Web modernos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 8de36ea9f7bb77443b22e038172ee69bb8435b29
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311218"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedagem de site estática no armazenamento do Azure

Você pode fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner de armazenamento chamado *$Web*. Hospedar seu conteúdo no armazenamento do Azure permite que você use arquiteturas sem servidor que incluem [Azure Functions](/azure/azure-functions/functions-overview) e outros serviços de PaaS (plataforma como serviço).

> [!NOTE]
> Se o seu site depende do código do servidor, use [Azure app serviço](/azure/app-service/overview) em vez disso.

## <a name="setting-up-a-static-website"></a>Configurando um site estático

A hospedagem de site estática é um recurso que você precisa habilitar na conta de armazenamento.

Para habilitar a hospedagem de site estático, selecione o nome do arquivo padrão e, opcionalmente, forneça um caminho para uma página personalizada do 404. Se um contêiner de armazenamento de BLOBs chamado **$Web** ainda não existir na conta, um será criado para você. Adicione os arquivos do seu site a esse contêiner.

Para obter orientações passo a passo, consulte [hospedar um site estático no armazenamento do Azure](storage-blob-static-website-how-to.md).

![Métrica de métricas de sites estáticos do armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Os arquivos no contêiner **$Web** diferenciam maiúsculas de minúsculas, são atendidos por meio de solicitações de acesso anônimo e estão disponíveis somente por meio de operações de leitura.

## <a name="uploading-content"></a>Carregando conteúdo

Você pode usar qualquer uma dessas ferramentas para carregar conteúdo no contêiner de **$Web** :

> [!div class="checklist"]
> * [CLI do Azure](storage-blob-static-website-how-to.md#cli)
> * [Módulo Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador do Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão de Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Exibindo conteúdo

Os usuários podem exibir o conteúdo do site de um navegador usando a URL pública do site. Você pode encontrar a URL usando o portal do Azure, o CLI do Azure ou o PowerShell. Use esta tabela como um guia.

|Ferramenta| Orientações |
|----|----|
|**Portal do Azure** | [Localize a URL do site usando o portal do Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**CLI do Azure** | [Localize a URL do site usando o CLI do Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Módulo Azure PowerShell** | [Localizar a URL do site usando o PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

A URL do seu site contém um código regional. Por exemplo, a URL `https://contosoblobaccount.z22.web.core.windows.net/` contém código regional `z22`.

Embora esse código deva permanecer na URL, ele é apenas para uso interno, e você não precisará usar esse código de nenhuma outra maneira.

O documento de índice especificado quando você habilita a hospedagem de site estático é exibido quando os usuários abrem o site e não especificam um arquivo específico (por exemplo: `https://contosoblobaccount.z22.web.core.windows.net`).  

Se o servidor retornar um erro 404 e você não tiver especificado um documento de erro ao habilitar o site, uma página 404 padrão será retornada ao usuário.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da configuração do nível de acesso público do contêiner da Web

Você pode modificar o nível de acesso público do contêiner de **$Web** , mas isso não tem impacto sobre o ponto de extremidade de site estático primário porque esses arquivos são servidos por meio de solicitações de acesso anônimo. Isso significa acesso público (somente leitura) a todos os arquivos.

A captura de tela a seguir mostra a configuração de nível de acesso público no portal do Azure:

![Captura de tela mostrando como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Embora o ponto de extremidade de site estático primário não seja afetado, uma alteração no nível de acesso público afeta o ponto de extremidade do serviço blob primário.

Por exemplo, se você alterar o nível de acesso público do contêiner de **$Web** de **privado (sem acesso anônimo)** para **BLOB (acesso de leitura anônimo para BLOBs somente)** , o nível de acesso público ao ponto de extremidade de site estático primário `https://contosoblobaccount.z22.web.core.windows.net/index.html` Não é alterado.

No entanto, o acesso público ao ponto de extremidade do serviço blob primário `https://contosoblobaccount.blob.core.windows.net/$web/index.html` muda de particular para público. Agora, os usuários podem abrir esse arquivo usando qualquer um desses dois pontos de extremidade.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Suporte à CDN (rede de distribuição de conteúdo) e ao Secure Socket Layer (SSL)

Para disponibilizar seus arquivos de site estáticos em seu domínio personalizado e HTTPS, consulte [usando a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md). Como parte desse processo, você precisa apontar sua CDN para o ponto de extremidade de *site estático* primário em oposição ao ponto de extremidade do *serviço blob* primário. Talvez seja necessário aguardar alguns minutos para que o conteúdo fique visível, pois a configuração da CDN não é executada imediatamente.

Ao atualizar seu site estático, certifique-se de limpar o conteúdo armazenado em cache nos servidores de borda da CDN limpando o ponto de extremidade da CDN. Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> O HTTPS tem suporte nativo por meio do ponto de extremidade da Web da conta, portanto, o ponto de extremidade da Web pode ser acessado via HTTP e HTTPS. No entanto, se a conta de armazenamento estiver configurada para exigir transferência segura por HTTPS, os usuários deverão usar o ponto de extremidade HTTPS. Para obter mais informações, consulte [exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md).
>
> O uso de domínios personalizados por HTTPS requer o uso da CDN do Azure no momento.

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Você pode tornar seu site estático disponível por meio de um domínio personalizado. Para saber mais, consulte [configurar um nome de domínio personalizado para sua conta de armazenamento do Azure](storage-custom-domain-name.md).

Para obter uma análise detalhada sobre como hospedar seu domínio no Azure, confira [hospedar seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Preços

Você pode habilitar a hospedagem de sites estáticos gratuitamente. Você é cobrado apenas pelo armazenamento de BLOBs que seu site utiliza e pelos custos de operações. Para obter mais detalhes sobre os preços do armazenamento de BLOBs do Azure, confira a [página de preços do armazenamento de BLOBs do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Você pode habilitar métricas em páginas de site estáticos. Depois de habilitar as métricas, as estatísticas de tráfego nos arquivos no contêiner de **$Web** são relatadas no painel de métricas.

Para habilitar as métricas em suas páginas de site estáticos, consulte [habilitar métricas em páginas de site estático](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passos seguintes

* [Hospedar um site estático no armazenamento do Azure](storage-blob-static-website-how-to.md)
* [Usar a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para seu BLOB ou ponto de extremidade da Web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [App Service do Azure](/azure/app-service/overview)
* [Crie seu primeiro aplicativo Web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: hospedar seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
