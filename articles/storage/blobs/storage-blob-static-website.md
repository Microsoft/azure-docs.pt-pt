---
title: Alojamento de Web site estático no armazenamento do Azure
description: Armazenamento estático Web site do Azure que aloja, fornecendo uma solução económica e escalável para alojar aplicações web modernas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1794aa26fc725207c4a901c11c345eeaa3d2f65d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867746"
---
# <a name="static-website-hosting-in-azure-storage"></a>Alojamento de Web site estático no armazenamento do Azure
Contas de armazenamento GPv2 do Azure permitem-lhe servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente a partir de um contentor de armazenamento com o nome *$web*. Tirar partido de hospedagem no armazenamento do Azure permite utilizar arquiteturas sem servidor, incluindo [as funções do Azure](/azure/azure-functions/functions-overview) e outros serviços PaaS.

Ao contrário da hospedagem de Web site estático, locais dinâmicos que dependem de código do lado do servidor são melhor alojados com [App Service do Azure](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Como funciona?
Quando ativar o Web site estático de alojamento na sua conta de armazenamento, selecione o nome do seu ficheiro de padrão e, opcionalmente, indique um caminho para uma página 404 personalizada. Como a funcionalidade está ativada, um contentor com o nome *$web* é criada, se ainda não exista.

Ficheiros nos *$web* contentor são:

- atendidos por meio de solicitações de acesso anónimo
- disponível apenas por meio de operações de leitura do objeto
- diferencia maiúsculas de minúsculas
- disponível na Web pública, esse padrão:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- disponível através de um ponto de extremidade de armazenamento do Blob seguindo este padrão:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Utilizar o ponto final de armazenamento de BLOBs para carregar ficheiros. Por exemplo, o ficheiro carregado nesta localização:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

está disponível no navegador num local como este:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

O nome de ficheiro predefinido selecionado é utilizado na raiz e todos os subdiretórios, quando não for fornecido um nome de ficheiro. Se o servidor retorna um 404, e não fornecer um caminho de documento de erro, uma página 404 padrão é devolvido ao usuário.

## <a name="cdn-and-ssl-support"></a>Suporte CDN e SSL

Para tornar sua estático site ficheiros disponível através de HTTPS, consulte [utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md). Como parte deste processo, precisa *apontar o CDN para o ponto final web* em vez do ponto final do blob. Terá de aguardar alguns minutos até que seu conteúdo está visível à medida que a configuração de CDN não é executada imediatamente.

Ao atualizar o seu Web site estático, certifique-se de que limpar o conteúdo em cache em servidores edge CDN ao remover o ponto final da CDN. Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../../cdn/cdn-purge-endpoint.md).

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Pode [configurar um nome de domínio personalizado para a sua conta de armazenamento do Azure](storage-custom-domain-name.md) para tornar o seu Web site estático disponível por meio de um domínio personalizado. Para alojar o seu domínio num exame minucioso sobre [do Azure, consulte alojar o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Preços
Alojamento de Web site estático é fornecido sem custos adicionais. Para obter mais detalhes sobre os preços para o armazenamento de Blobs do Azure, consulte a [página de preços do Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Início Rápido

### <a name="azure-portal"></a>Portal do Azure
Comece por abrir o portal do Azure em https://portal.azure.com e execute os seguintes passos na sua conta de armazenamento GPv2:

1. Clique em **definições**
2. Clique em **Web site estático**
3. Introduza um *nome do documento de índice*. (É um valor comum *Index. HTML)*
4. Opcionalmente, introduza um *caminho do documento de erro* para uma página 404 personalizada. (É um valor comum *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Em seguida, carregar seus recursos para o *$web* contentor através do portal do Azure ou com o [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) a carregar diretórios de todos. Certifique-se de incluir um ficheiro que corresponda a *nome do documento de índice* que selecionou quando ativar a funcionalidade.

Por fim, navegue para o ponto final de web para testar o seu Web site.

### <a name="azure-cli"></a>CLI do Azure
Instale a extensão de pré-visualização de armazenamento:

```azurecli-interactive
az extension add --name storage-preview
```
No caso de várias subscrições, defina o CLI para a subscrição da conta de armazenamento GPv2 que pretende ativar:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Ative a funcionalidade. Certifique-se substituir todos os valores de marcador de posição, incluindo os parênteses, com seus próprios valores:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Consulta para o URL de ponto final web:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Carregar objetos para o *$web* contentor a partir de um diretório de origem. Certifique-se de que a referência de escape corretamente os *$web* contentor no comando. Por exemplo, se estiver a utilizar o CLI do Azure partir CloudShell no portal do Azure, de escape a *$web* contentor, conforme mostrado:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Implementação

Métodos disponíveis para implementar o conteúdo para um contentor de armazenamento incluem o seguinte:

- [AZCopy](../common/storage-use-azcopy.md)
- [Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/)
- [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/)
- [Extensão do Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

Em todos os casos, certifique-se de que copiar ficheiros para o *$web* contentor.

## <a name="metrics"></a>Métricas

Para ativar as métricas nas páginas da Web site estático, clique em **configurações** > **monitorização** > **métricas**.

Dados de métricas são gerados por conexão com métricas diferentes APIs. O portal apresenta apenas os membros da API utilizados num determinado período de tempo para manter o foco somente em membros que retornam dados. Para se certificar de que pode selecionar o membro da API necessário, a primeira etapa é expandir o intervalo de tempo.

Clique no botão de período de tempo e selecione **últimas 24 horas** e, em seguida, clique em **aplicar**

![Intervalo de tempo de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Em seguida, selecione **Blob** partir do *espaço de nomes* menu pendente.

![Namespace de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Em seguida, selecione o **saída** métrica.

![Métrica de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Selecione **soma** partir do *agregação* Seletor.

![Agregação de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Em seguida, clique a **Adicionar filtro** botão e escolha **nome da API** do *propriedade* Seletor.

![Nome de API de métricas de Web sites estáticos de armazenamento do Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Por fim, a caixa de verificação junto a **GetWebContent** no *valores* Seletor para preencher o relatório de métrica.

![Métricas de Web sites estáticos de armazenamento do Azure GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Estatísticas em arquivos de tráfego de uma vez ativada, o *$web* contentor são apresentadas no dashboard de indicadores.

## <a name="faq"></a>FAQ

**A funcionalidade de Web sites estáticos está disponível para todos os tipos de conta de armazenamento?**  
Não, o alojamento de Web site estático apenas está disponível nas contas de armazenamento padrão de GPv2.

**São armazenamento VNET e regras de firewall suportadas no novo ponto final web?**  
Sim, o novo ponto final da web obedeça as regras VNET e de firewall configuradas para a conta de armazenamento.

**O ponto final web diferencia maiúsculas de minúsculas?**  
Sim, o ponto final web diferencia maiúsculas de minúsculas tal como o ponto final do blob. 

## <a name="next-steps"></a>Passos Seguintes
* [Utilizar a CDN do Azure para aceder a blobs com domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para o ponto final do blob ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicações do Azure](/azure/app-service/overview)
* [Crie seu primeiro aplicativo da web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Aloje o seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
