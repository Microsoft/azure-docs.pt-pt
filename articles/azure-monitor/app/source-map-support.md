---
title: Suporte de mapa de origem para aplicações JavaScript - Azure Monitor Application Insights
description: Saiba como enviar mapas de origem para a sua própria conta de armazenamento O recipiente Blob utilizando o Application Insights.
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933942"
---
# <a name="source-map-support-for-javascript-applications"></a>Suporte de mapa de origem para aplicações JavaScript

O Application Insights suporta o upload de mapas de origem para o seu próprio recipiente blob de conta de armazenamento.
Os mapas de origem podem ser usados para desminaminar as pilhas de chamadas encontradas na página de detalhes de transação final. Qualquer exceção enviada pelo [JavaScript SDK][ApplicationInsights-JS] ou [ peloNode.js SDK][ApplicationInsights-Node.js] pode ser inindificado com mapas de origem.

![Desindifique uma Pilha de Chamadas ligando-a com uma conta de armazenamento](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Criar uma nova conta de armazenamento e recipiente Blob

Se já tiver uma conta de armazenamento ou recipiente de bolhas existente, pode saltar este passo.

1. [Criar uma nova conta de armazenamento][create storage account]
2. [Crie um recipiente de bolhas][create blob container] dentro da sua conta de armazenamento. Certifique-se de definir o "nível de acesso público" `Private` para garantir que os seus mapas de origem não são acessíveis ao público.

> [!div class="mx-imgBorder"]
>![O seu nível de acesso ao contentor deve ser definido para Privado](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Empurre os seus mapas de origem para o seu recipiente Blob

Deverá integrar o seu gasoduto de implantação contínua com a sua conta de armazenamento, configurando-o para enviar automaticamente os seus mapas de origem para o recipiente Blob configurado.

Os mapas de origem podem ser enviados para o seu Recipiente de Armazenamento Blob com a mesma estrutura de pasta com a mesma que foram compilados & implementados. Um caso de utilização comum é pré-fixo de uma pasta de implantação com a sua versão, por `1.2.3/static/js/main.js` exemplo. Ao não se ligar através de um contentor Azure Blob `sourcemaps` chamado, tentará obter um mapa de origem localizado em `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Enviar mapas de origem através de Gasodutos Azure (recomendado)

Se estiver a utilizar os Pipelines Azure para construir e implementar continuamente a sua aplicação, adicione uma tarefa [de Cópia de Ficheiros Azure][azure file copy] ao seu pipeline para carregar automaticamente os seus mapas de origem.

> [!div class="mx-imgBorder"]
> ![Adicione uma tarefa de cópia de ficheiroS Azure ao seu Pipeline para enviar os seus mapas de origem para o armazenamento de blob Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configure o seu recurso Application Insights com uma conta de armazenamento de Mapa de Origem

### <a name="from-the-end-to-end-transaction-details-page"></a>Da página de detalhes de transações de ponta a ponta

A partir do separador detalhes de transações de ponta a ponta, pode clicar em *Unminify* e apresentará um pedido de configuração se o seu recurso não estiver configurado.

1. No Portal, veja os detalhes de uma exceção que é minificada.
2. Clique em *Unminify*
3. Se o seu recurso não tiver sido configurado, aparecerá uma mensagem, levando-o a configurar.

### <a name="from-the-properties-page"></a>A partir da página de propriedades

Se quiser configurar ou alterar a conta de armazenamento ou o recipiente Blob que está ligado ao seu Recurso De Insights de Aplicação, pode fazê-lo visualizando o separador *Propriedades* do recurso Application Insights.

1. Navegue para o *separador Propriedades* do seu recurso Application Insights.
2. Clique no *recipiente de bolha de mapa de origem Change*.
3. Selecione um recipiente Blob diferente como o seu recipiente de mapas de origem.
4. Clique em `Apply`.

> [!div class="mx-imgBorder"]
> ![Reconfigure o seu recipiente Azure Blob selecionado navegando para a Lâmina de Propriedades](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Regulação necessária do controlo de acesso baseado em funções (Azure RBAC) no seu recipiente Blob

Qualquer utilizador no Portal que utilize esta função deve ser, pelo menos, designado como Leitor de [Dados blob de armazenamento][storage blob data reader] ao seu recipiente Blob. Deve atribuir este papel a qualquer pessoa que utilize os mapas de origem através desta funcionalidade.

> [!NOTE]
> Dependendo da forma como o contentor foi criado, este pode não ter sido automaticamente atribuído a si ou à sua equipa.

### <a name="source-map-not-found"></a>Mapa de origem não encontrado

1. Verifique se o mapa de origem correspondente é enviado para o recipiente blob correto
2. Verifique se o ficheiro do mapa de origem tem o nome do ficheiro JavaScript para o qual mapeia, sufixado com `.map` .
    - Por exemplo, `/static/js/main.4e2ca5fa.chunk.js` procurará a bolha chamada `main.4e2ca5fa.chunk.js.map`
3. Verifique a consola do seu navegador para ver se estão a ser registados erros. Inclua isto em qualquer bilhete de apoio.

## <a name="next-steps"></a>Passos Seguintes

* [Tarefa de cópia de arquivo Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
