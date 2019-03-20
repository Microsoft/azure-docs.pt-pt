---
title: Implementar a solução de monitorização remota localmente - Docker – Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local com o Docker para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: c00e62e237fe263f54926c8e74fb6211a2e5a4e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993057"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Implementar o monitorização remota acelerador de soluções localmente - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra-lhe como implementar o acelerador de solução de monitorização remota no seu computador local para teste e desenvolvimento. Saiba como implementar os microsserviços em contentores do Docker locais. Uma implementação de microsserviços local utiliza os seguintes serviços de nuvem: IoT Hub, Cosmos DB, a análise de transmissão em fluxo do Azure e Azure Time Series Insights serviços na cloud.

Se quiser executar o acelerador de solução de monitorização remota num IDE no seu computador local, veja [implementar o monitorização remota acelerador de soluções localmente - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implementar os serviços do Azure utilizados pelo acelerador de solução de monitorização remota, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração de máquina

Para concluir a implementação de local, terá das seguintes ferramentas instaladas no seu computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - se de que pretende efetuar alterações para os microsserviços.
* [NODE. js v8](https://nodejs.org/) -este software é um pré-requisito para a CLI de PCS que os scripts usam para criar recursos do Azure. Não utilize a v10 de node. js.

> [!NOTE]
> Estas ferramentas estão disponíveis em muitas plataformas, incluindo Windows, Linux e iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Execute os microsserviços no Docker

Abra uma nova linha de comandos para ter certeza de ter acesso às variáveis de ambiente definidas pelos **start.cmd** script. No Windows, pode verificar que as variáveis de ambiente são definidas, executando o seguinte comando:

```cmd
set PCS
```

O comando mostra todas as variáveis de ambiente definidas pelos **start.cmd** script.

Certifique-se de que o Docker está em execução no seu computador local.
> [!NOTE]
> Tem de executar o docker [contentores de Linux](https://docs.docker.com/docker-for-windows/) se ele está em execução no Windows.

Os microsserviços em execução nos contentores do Docker locais tem de aceder aos serviços cloud do Azure. Pode testar a conectividade de internet do seu ambiente do Docker com o seguinte comando para enviar um ping de um endereço de internet a partir de dentro de um contentor:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Para executar o solution accelerator, navegue para o **serviços\\scripts\\local** pasta no seu ambiente de linha de comandos e execute o seguinte comando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Certifique-se de que [partilhar uma unidade local](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) com o Docker antes de executar `docker-compose up`.

Na primeira vez que executar este comando, o Docker transfere as imagens de microsserviços do hub do Docker para criar os contentores localmente. Nos seguintes execuções, Docker executa os contentores imediatamente.

> [!TIP]
> Com freqüência, a Microsoft publica novas imagens do Docker com a nova funcionalidade. Pode utilizar o seguinte conjunto de comandos para limpeza seus contentores do Docker locais e as imagens correspondentes antes de extrair os mais recentes:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Pode utilizar um shell separado para ver os registos do contentor. Primeiro, encontrar o ID de contentor com o `docker ps` comando. Em seguida, utilizar `docker logs {container-id} --tail 1000` para ver as últimas 1 000 entradas para o contentor especificado.

### <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de Stream Analytics

Siga estes passos para iniciar a tarefa de Stream Analytics:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue para o **grupo de recursos** criado para a sua solução. O nome do grupo de recursos é o nome que escolheu para a sua solução quando executou o **start.cmd** script.
1. Clique nas **tarefa do Stream Analytics** na lista de recursos.
1. Na tarefa de Stream Analytics **descrição geral** página, clique nas **iniciar** botão. Em seguida, clique em **iniciar** para iniciar a tarefa agora.

### <a name="connect-to-the-dashboard"></a>Ligar ao dashboard

Para aceder ao dashboard de solução de monitorização remota, navegue para `http://localhost:8080` no seu browser. Agora, pode utilizar a IU da Web e os microsserviços locais.

## <a name="clean-up"></a>Limpeza

Para evitar desnecessários custos, quando tiver concluído os testes remover os serviços em nuvem da sua subscrição do Azure. Para remover os serviços, navegue para o [portal do Azure](https://ms.portal.azure.com) e eliminar o recurso de grupo que o **start.cmd** script criado.

Utilize o `docker-compose down --rmi all` comando para remover as imagens do Docker e liberte espaço no seu computador local. Também pode eliminar a cópia local do repositório de monitorização remota criado quando clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou a solução de monitorização remota, a próxima etapa é [explore os recursos do dashboard da solução](quickstart-remote-monitoring-deploy.md).
