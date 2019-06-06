---
title: Alojamento de Web site estático no armazenamento do Azure
description: Armazenamento estático Web site do Azure que aloja, fornecendo uma solução económica e escalável para alojar aplicações web modernas.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427977"
---
# <a name="static-website-hosting-in-azure-storage"></a>Alojamento de Web site estático no armazenamento do Azure

Pode servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente a partir de um contentor de armazenamento com o nome *$web*. Hospedagem de seu conteúdo no armazenamento do Azure permite utilizar arquiteturas sem servidor, que incluem [as funções do Azure](/azure/azure-functions/functions-overview) e outra plataforma como um serviço de serviço (PaaS).

> [!NOTE]
> Se o seu site depende do código do lado do servidor, utilize [App Service do Azure](/azure/app-service/overview) em vez disso.

## <a name="setting-up-a-static-website"></a>Como configurar um Web site estático

Alojamento de Web site estático é uma funcionalidade que tem de ativar na conta de armazenamento.

Para ativar o alojamento de Web sites estáticos, selecione o nome do seu ficheiro predefinido e, em seguida, opcionalmente, indique um caminho para uma página 404 personalizada. Se um contentor de armazenamento de BLOBs denominado **$web** já não existe na conta, é criada uma para. Adicione os ficheiros do seu site para este contentor.

Para obter orientações passo a passo, consulte [alojar um Web site estático no armazenamento do Azure](storage-blob-static-website-how-to.md).

![Métrica de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Ficheiros nos **$web** contentor diferenciam maiúsculas de minúsculas, atendidos por meio de solicitações de acesso anônimo e estão disponíveis apenas através de operações de leitura.

## <a name="uploading-content"></a>A carregar conteúdo

Pode utilizar qualquer uma destas ferramentas para carregar conteúdo para o **$web** contentor:

> [!div class="checklist"]
> * [CLI do Azure](storage-blob-static-website-how-to.md#cli)
> * [Módulo Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador do Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão do Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Visualizar o conteúdo

Os utilizadores podem ver o conteúdo do site num browser utilizando o URL pública do site. Pode encontrar o URL ao utilizar o portal do Azure, a CLI do Azure ou o PowerShell. Utilize esta tabela como guia.

|Ferramenta| Orientação |
|----|----|
|**Portal do Azure** | [Encontrar o URL de Web site com o portal do Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**CLI do Azure** | [Encontrar o URL do Web site com a CLI do Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Módulo Azure PowerShell** | [Encontrar o URL de Web site com o PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

O URL do seu site contém um código regional. Por exemplo, o URL `https://contosoblobaccount.z22.web.core.windows.net/` contém código regional `z22`.

Embora esse código deve permanecer a URL, é apenas para utilização interna e não precisará usar esse código em qualquer outra forma.

O documento de índice que especificou quando ativa a hospedagem de Web site estático, é apresentada quando os utilizadores, abra o site e não especificarem um ficheiro específico (por exemplo: `https://contosoblobaccount.z22.web.core.windows.net`).  

Se o servidor retorna um erro 404, e não especificou um documento de erro quando ativou o Web site, uma página 404 padrão é devolvido ao usuário.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da definição de nível de acesso público do contentor web

Pode modificar o nível de acesso público do **$web** contentor, mas isso não tem impacto sobre o ponto de extremidade do Web site estático principal porque estes ficheiros são atendidos por meio de solicitações de acesso anônimo. Isso significa que o acesso de público (só de leitura) para todos os ficheiros.

Captura de ecrã seguinte mostra a definição de nível de acesso público no portal do Azure:

![Captura de ecrã que mostra como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Enquanto o ponto de extremidade do Web site estático principal não é afetado, uma alteração para o nível de acesso público impacto sobre o ponto de final de serviço blob primário.

Por exemplo, se alterar o nível de acesso público do **$web** contentor a partir do **privado (sem acesso anónimo)** para **Blob (acesso de leitura anónimo apenas para blobs)** , em seguida, o nível de acesso público para o ponto de final do Web site estático principal `https://contosoblobaccount.z22.web.core.windows.net/index.html` não é alterado.

No entanto, o ponto final de serviço de Blobs do acesso público para o primário `https://contosoblobaccount.blob.core.windows.net/$web/index.html` alterar entre particular e público. Agora os utilizadores podem abrir esse ficheiro, utilizando qualquer um desses dois pontos de extremidade.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Suporte de Secure Socket Layer (SSL) e de rede de entrega de conteúdos (CDN)

Para disponibilizar os ficheiros do Web site estático ao longo do seu domínio personalizado e HTTPS, consulte [utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md). Como parte deste processo, tem de apontar o CDN para o primário *Web site estático* ponto final em vez da primária *serviço blob* ponto final. Poderá ter de aguardar alguns minutos até que seu conteúdo está visível à medida que a configuração de CDN não é executada imediatamente.

Ao atualizar o seu Web site estático, certifique-se de que limpar o conteúdo em cache em servidores edge CDN ao remover o ponto final da CDN. Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS é suportado nativamente através do ponto de final de web de conta, pelo que o ponto final web é acessível através de HTTP e HTTPS. No entanto, se a conta de armazenamento está configurada para requerer transferência segura através de HTTPS, em seguida, os utilizadores podem utilizar o ponto final HTTPS. Para obter mais informações, consulte [requer transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md).
>
> A utilização de domínios personalizados através de HTTPS requer a utilização da CDN do Azure neste momento.

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Pode tornar seu Web site estático disponível por meio de um domínio personalizado. Para obter mais informações, consulte [configurar um nome de domínio personalizado para a sua conta de armazenamento do Azure](storage-custom-domain-name.md).

Para um exame minucioso sobre alojar o seu domínio no Azure, consulte [alojar o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Preços

Pode ativar o alojamento de Web site estático sem encargos. É cobrado apenas para o armazenamento de BLOBs que utiliza o seu site e os custos de operações. Para obter mais detalhes sobre os preços para o armazenamento de Blobs do Azure, consulte a [página de preços do Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Pode ativar métricas nas páginas de Web site estático. Assim que tive ativado as métricas, estatísticas de ficheiros de tráfego a **$web** contentor são apresentadas no dashboard de indicadores.

Para ativar as métricas sobre suas páginas de Web site estático, consulte [ativar as métricas nas páginas de Web site estático](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passos Seguintes

* [Alojar um Web site estático no armazenamento do Azure](storage-blob-static-website-how-to.md)
* [Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para o ponto final do blob ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicações do Azure](/azure/app-service/overview)
* [Crie seu primeiro aplicativo da web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Aloje o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
