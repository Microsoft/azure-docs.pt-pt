---
title: Implementar solução de monitorização remota localmente - Docker - Azure [ Microsoft Docs
description: Este guia de como fazer mostra como implantar o acelerador de solução de monitorização remota para a sua máquina local utilizando o Docker para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888824"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Implementar o acelerador de solução de monitorização remota localmente - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota na sua máquina local para testes e desenvolvimento. Aprende-se a implantar os microserviços nos contentores locais do Docker. Uma implantação local de microserviços utiliza os seguintes serviços na nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Serviços Azure Time Series Insights na nuvem.

Se pretender executar o acelerador de solução de monitorização remota num IDE na sua máquina local, consulte a implementação do acelerador de soluções de [monitorização remota localmente - Estúdio Visual](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de soluções de monitorização remota, necessita de uma subscrição azure ativa.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implementação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Estivador](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - se planeia fazer alterações nos microserviços.
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o CLI PCS que os scripts usam para criar recursos Azure. Não use node.js v10.

> [!NOTE]
> Estas ferramentas estão disponíveis em muitas plataformas, incluindo Windows, Linux e iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Gereação dos microserviços em Docker

Abra uma nova solicitação de comando para ter certeza de ter acesso às variáveis ambientais definidas pelo script **start.cmd.** No Windows, pode verificar se as variáveis ambientais são definidas executando o seguinte comando:

```cmd
set PCS
```

O comando mostra todas as variáveis ambientais definidas pelo script **start.cmd.**

Certifica-te que o Docker está a trabalhar na tua máquina local.
> [!NOTE]
> O Docker deve estar a executar [contentores Linux](https://docs.docker.com/docker-for-windows/) se estiver a funcionar no Windows.

Os microserviços que estão a funcionar nos contentores locais do Docker precisam de aceder aos serviços de nuvem Azure. Pode testar a conectividade da Internet do seu ambiente Docker utilizando o seguinte comando para pingar um endereço de internet a partir de dentro de um recipiente:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Para executar o acelerador de solução, navegue para as scripts de **serviços\\\\** da pasta local no seu ambiente de linha de comando e execute o seguinte comando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Certifique-se de [partilhar uma unidade](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) `docker-compose up`local com o Docker antes de correr.

A primeira vez que dirige este comando, Docker descarrega as imagens de microserviço do centro de Docker para construir os contentores localmente. Nas seguintes corridas, Docker dirige os contentores imediatamente.

> [!TIP]
> A Microsoft publica frequentemente novas imagens do Docker com novas funcionalidades. Pode utilizar o seguinte conjunto de comandos para limpar os seus contentores Docker locais e imagens correspondentes antes de puxar os mais recentes:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Pode utilizar uma concha separada para ver os troncos do recipiente. Primeiro encontre o ID `docker ps` do recipiente usando o comando. Em `docker logs {container-id} --tail 1000` seguida, utilize para visualizar as últimas 1000 entradas para o recipiente especificado.

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho de Stream Analytics

Siga estes passos para iniciar o trabalho de Stream Analytics:

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue para o **grupo Derecursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Clique no **trabalho do Stream Analytics** na lista de recursos.
1. Na página de **visão geral** do trabalho do Stream Analytics, clique no botão **Iniciar.** Em seguida, clique **em Começar** a trabalhar agora.

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao dashboard de solução de monitorização remota, navegue `http://localhost:8080` no seu navegador. Agora pode utilizar a Web UI e os microserviços locais.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes, remova os serviços de nuvem da sua subscrição Azure. Para remover os serviços, navegue para o [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Use `docker-compose down --rmi all` o comando para remover as imagens do Docker e libertar espaço na sua máquina local. Também pode eliminar a cópia local do repositório de Monitorização Remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de soluções](quickstart-remote-monitoring-deploy.md).
