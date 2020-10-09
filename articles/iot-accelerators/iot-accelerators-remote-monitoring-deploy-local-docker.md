---
title: Implementar solução de Monitorização Remota localmente - Docker - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como implantar o acelerador de solução de monitorização remota para a sua máquina local usando o Docker para testes e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73888824"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Implementar o acelerador de solução de monitorização remota localmente - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implantar o acelerador de solução de Monitorização Remota na sua máquina local para testes e desenvolvimento. Aprende-se a implantar os microserviços em contentores locais do Docker. Uma implementação local de microserviços utiliza os seguintes serviços em nuvem: IoT Hub, Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights na nuvem.

Se pretender executar o acelerador de solução de monitorização remota num IDE na sua máquina local, consulte [implementar localmente o acelerador de solução de monitorização remota - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração da máquina

Para completar a implantação local, necessita das seguintes ferramentas instaladas na sua máquina de desenvolvimento local:

* [Rio Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - se planeia fazer alterações nos microserviços.
* [Node.js v8](https://nodejs.org/) - este software é um pré-requisito para o PCS CLI que os scripts usam para criar recursos Azure. Não use Node.js v10.

> [!NOTE]
> Estas ferramentas estão disponíveis em muitas plataformas, incluindo Windows, Linux e iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Executar os microserviços em Docker

Abra um novo pedido de comando para ter certeza de ter acesso às variáveis ambientais definidas pelo script **start.cmd.** No Windows, pode verificar se as variáveis ambientais são definidas executando o seguinte comando:

```cmd
set PCS
```

O comando mostra todas as variáveis ambientais definidas pelo script **start.cmd.**

Certifica-te que o Docker está a trabalhar na tua máquina local.
> [!NOTE]
> O Docker deve estar a executar [contentores Linux](https://docs.docker.com/docker-for-windows/) se estiver a funcionar no Windows.

Os microserviços que estão a funcionar nos contentores locais do Docker precisam de aceder aos serviços de nuvem Azure. Pode testar a conectividade da internet do seu ambiente Docker utilizando o seguinte comando para obter um endereço de internet a partir de dentro de um recipiente:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Para executar o acelerador de solução, navegue para as ** \\ pastas \\ locais de scripts** de serviços no seu ambiente de linha de comando e execute o seguinte comando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Certifique-se de [que partilha uma unidade local](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) com o Docker antes de `docker-compose up` correr.

A primeira vez que dirige este comando, o Docker descarrega as imagens de microserviço do centro do Docker para construir os contentores localmente. Nas seguintes corridas, Docker dirige os contentores imediatamente.

> [!TIP]
> A Microsoft publica frequentemente novas imagens docker com nova funcionalidade. Pode utilizar o seguinte conjunto de comandos para limpar os seus contentores e imagens locais do Docker antes de retirar os mais recentes:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Pode utilizar uma concha separada para visualizar os troncos do recipiente. Primeiro encontre a identificação do contentor usando o `docker ps` comando. Em seguida, utilize `docker logs {container-id} --tail 1000` para visualizar as últimas 1000 entradas para o recipiente especificado.

### <a name="start-the-stream-analytics-job"></a>Inicie o trabalho stream analytics

Siga estes passos para iniciar o trabalho stream Analytics:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Navegue para o **grupo de Recursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o script **start.cmd.**
1. Clique no **trabalho stream Analytics** na lista de recursos.
1. Na página **de visão geral** do trabalho stream Analytics, clique no botão **Iniciar.** Em seguida, clique **em Começar** a trabalhar agora.

### <a name="connect-to-the-dashboard"></a>Ligue-se ao painel de instrumentos

Para aceder ao painel de solução de monitorização remota, navegue `http://localhost:8080` no seu browser. Agora pode utilizar o Web UI e os microserviços locais.

## <a name="clean-up"></a>Limpeza

Para evitar encargos desnecessários, quando terminar os seus testes remova os serviços de cloud da sua subscrição Azure. Para remover os serviços, navegue no [portal Azure](https://ms.portal.azure.com) e elimine o grupo de recursos que o script **start.cmd** criou.

Utilize o `docker-compose down --rmi all` comando para remover as imagens do Docker e liberte espaço na sua máquina local. Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código fonte do GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de instrumentos de solução](quickstart-remote-monitoring-deploy.md).
