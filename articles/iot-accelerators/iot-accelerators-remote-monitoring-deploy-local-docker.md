---
title: Implantar solução de monitoramento remoto localmente-Docker-Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local usando o Docker para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888824"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Implantar o acelerador de solução de monitoramento remoto localmente-Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local para teste e desenvolvimento. Você aprende a implantar os microserviços em contêineres do Docker local. Uma implantação de microserviços locais usa os seguintes serviços de nuvem: Hub IoT, Cosmos DB, análise de streaming do Azure e serviços de Azure Time Series Insights na nuvem.

Se você quiser executar o acelerador de solução de monitoramento remoto em um IDE em seu computador local, consulte [implantar o acelerador de solução de monitoramento remoto localmente-Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de monitoramento remoto, você precisa de uma assinatura ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisará das seguintes ferramentas instaladas em seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) -se você planeja fazer alterações nos microserviços.
* [Node. js V8](https://nodejs.org/) – este software é um pré-requisito para a CLI de PCs que os scripts usam para criar recursos do Azure. Não use V10 node. js.

> [!NOTE]
> Essas ferramentas estão disponíveis em várias plataformas, incluindo Windows, Linux e iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Executar os microserviços no Docker

Abra um novo prompt de comando para ter certeza de ter acesso às variáveis de ambiente definidas pelo script **Start. cmd** . No Windows, você pode verificar se as variáveis de ambiente estão definidas executando o seguinte comando:

```cmd
set PCS
```

O comando mostra todas as variáveis de ambiente definidas pelo script **Start. cmd** .

Verifique se o Docker está em execução no computador local.
> [!NOTE]
> O Docker deve estar executando [contêineres do Linux](https://docs.docker.com/docker-for-windows/) se estiver em execução no Windows.

Os microserviços em execução nos contêineres do Docker local precisam acessar os serviços de nuvem do Azure. Você pode testar a conectividade com a Internet do ambiente do Docker usando o seguinte comando para executar o ping de um endereço de Internet de dentro de um contêiner:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Para executar o Solution Accelerator, navegue até os **serviços\\scripts\\pasta local** em seu ambiente de linha de comando e execute o seguinte comando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Certifique-se [de compartilhar uma unidade local com o](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) Docker antes de executar `docker-compose up`.

Na primeira vez que você executar esse comando, o Docker baixará as imagens de microserviço do Hub do Docker para criar os contêineres localmente. Nas execuções a seguir, o Docker executa os contêineres imediatamente.

> [!TIP]
> A Microsoft publica com frequência novas imagens do Docker com novas funcionalidades. Você pode usar o seguinte conjunto de comandos para limpar os contêineres do Docker local e as imagens correspondentes antes de extrair os mais recentes:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Você pode usar um shell separado para exibir os logs do contêiner. Primeiro, localize a ID do contêiner usando o comando `docker ps`. Em seguida, use `docker logs {container-id} --tail 1000` para exibir as últimas 1000 entradas para o contêiner especificado.

### <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho de Stream Analytics

Siga estas etapas para iniciar o trabalho de Stream Analytics:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até o **grupo de recursos** criado para sua solução. O nome do grupo de recursos é o nome escolhido para a solução quando você executou o script **Start. cmd** .
1. Clique no **trabalho de Stream Analytics** na lista de recursos.
1. Na página **visão geral** do trabalho de Stream Analytics, clique no botão **Iniciar** . Em seguida, clique em **Iniciar** para iniciar o trabalho agora.

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel da solução de monitoramento remoto, navegue até `http://localhost:8080` em seu navegador. Agora você pode usar a interface do usuário da Web e os microserviços locais.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando você concluir o teste, remova os serviços de nuvem da sua assinatura do Azure. Para remover os serviços, navegue até a [portal do Azure](https://ms.portal.azure.com) e exclua o grupo de recursos criado pelo script **Start. cmd** .

Use o comando `docker-compose down --rmi all` para remover as imagens do Docker e liberar espaço no computador local. Você também pode excluir a cópia local do repositório de monitoramento remoto criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel da solução](quickstart-remote-monitoring-deploy.md).
