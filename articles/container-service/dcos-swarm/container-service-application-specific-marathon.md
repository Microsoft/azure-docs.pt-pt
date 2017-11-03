---
title: "Serviço Marathon específico para aplicações ou utilizadores | Microsoft Docs"
description: "Criar um serviço Marathon específico para aplicações ou utilizadores"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contentores, Marathon, Microserviços, DC/OS, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: b265763fb5dad240edd710cd8d0fb1079e3a7b51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-or-user-specific-marathon-service"></a>Criar um serviço Marathon específico para aplicações ou utilizadores
O Serviço de Contentor do Azure fornece um conjunto de servidores principais no qual podemos pré-configurar o Apache Mesos e o Marathon. Estes podem ser utilizados para orquestrar as aplicações no cluster, mas é melhor não utilizar os servidores mestres para esta finalidade. Por exemplo, para ajustar a configuração do Marathon, necessita de iniciar sessão nos próprios servidores mestres e efetuar as alterações – isto encoraja servidores mestre exclusivos, que são ligeiramente diferentes do padrão e têm de ser tratados e geridos de forma independente. Para além disso, a configuração necessária para uma equipa pode não ser a configuração ideal para outra equipa.

Neste artigo, explicaremos como adicionar um serviço Marathon específico para utilizadores ou aplicações.

Visto que este serviço pertencerá a um utilizador único ou equipa, estes são livres de configurá-lo da forma que pretendam. Além disso, o Serviço de Contentor do Azure irá garantir que o serviço continua a ser executado. Se o serviço falhar, o Serviço de Contentor do Azure reiniciá-lo-á por si. Na maioria das vezes, nem se aperceberá de eventuais períodos de indisponibilidade.

## <a name="prerequisites"></a>Pré-requisitos
[Implemente uma instância do Serviço de Contentor do Azure](container-service-deployment.md) com o tipo de orquestrador DC/SO e [certifique-se de que o cliente pode ligar ao cluster](../container-service-connect.md). Além disso, execute os passos seguintes.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Criar um serviço Marathon específico para aplicações ou utilizadores
Comece por criar um ficheiro de configuração JSON que define o nome do serviço de aplicação que pretende criar. Aqui, utilizamos `marathon-alice` como o nome da estrutura. Guarde o ficheiro com um nome semelhante a `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Em seguida, utilize o CLI DC/OS para instalar a instância do Marathon com as opções definidas no ficheiro de configuração:

```bash
dcos package install --options=marathon-alice.json marathon
```

Agora, deverá ver o serviço `marathon-alice` em execução no separador de Serviços da IU do DC/OS. A IU será `http://<hostname>/service/marathon-alice/` se pretender aceder diretamente à mesma.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Definir o CLI DC/OS para aceder ao serviço
Opcionalmente, pode configurar o CLI DC/OS para aceder a este novo serviço definindo a propriedade `marathon.url` para apontar para a instância `marathon-alice` da seguinte forma:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Pode verificar com que instância do Marathon o CLI está a trabalhar contra com o `dcos config show` comando. Pode reverter para a utilização do serviço Marathon principal com o comando `dcos config unset marathon.url`.

