---
title: Implementar a solução de monitorização remota com a CLI - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como aprovisionar o acelerador de solução de monitorização remota com a CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: a1e65466bb409007a2f112422f393eee786d7b3c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182137"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Implementar o acelerador de solução de monitorização remota com a CLI

Este guia de procedimentos mostra como implementar o acelerador de solução de monitorização remota. Implementar a solução com a CLI. Também pode implementar a solução usando a interface do Usuário baseada na web em azureiotsolutions.com, para saber mais sobre esta opção, consulte a [implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md) início rápido.

## <a name="prerequisites"></a>Pré-requisitos

Para implementar o acelerador de solução de monitorização remota, terá de uma subscrição do Azure Active Directory.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para executar a CLI, precisa [node. js](https://nodejs.org/) instalado no seu computador local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar a CLI, execute o seguinte comando no seu ambiente de linha de comandos:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Entrar para a CLI

Antes de poder implementar o solution accelerator, tem de iniciar sessão sua subscrição do Azure com a CLI:

```cmd/sh
pcs login
```

Siga na tela instruções para concluir o processo de início de sessão.

## <a name="deployment-options"></a>Opções de implementação

Ao implementar o solution accelerator, existem várias opções que configuram o processo de implantação:

| Opção | Valores | Descrição |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | R _básica_ implementação destina-se para teste e demonstrações, implementa todos os microsserviços são uma única máquina virtual. R _padrão_ implementação destina-se para a produção, ele implementa os microsserviços em várias máquinas de virtuais. R _local_ implementação configura um contentor de Docker para executar os microsserviços no seu computador local e utiliza os serviços cloud do Azure, como o armazenamento e do Cosmos DB. |
| Runtime | `dotnet`, `java` | Seleciona a implementação de idioma dos microsserviços. |

Para saber como utilizar a opção de implementação de local, veja [a executar localmente a solução de monitorização remota](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Implementações de básicas e standard

Esta secção resume as principais diferenças entre uma implementação de básica e standard.

### <a name="basic"></a>Básica

A implementação Básica destina-se a demonstrar a solução. Para reduzir os custos, todos os microsserviços são implementados numa única máquina virtual. Esta implementação não usa uma arquitetura de prontos para produção.

Uma implementação básica cria os seguintes serviços na sua subscrição do Azure:

| Contagem | Recurso                       | Type         | Utilizado para |
|-------|--------------------------------|--------------|----------|
| 1     | [Máquina Virtual do Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Alojamento de microsserviços |
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                  | S1 – escalão Standard | Gestão de dispositivos e a comunicação |
| 1     | [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Armazenar dados de configuração, regras, alertas e outro armazenamento amovível |  
| 1     | [Conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Armazenamento de VM e os pontos de verificação de transmissão em fluxo |
| 1     | [Aplicação Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Aplicativo web de front-end de hospedagem |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerenciamento de identidades de utilizador e segurança |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Localizações de recurso de visualização |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Ativar a análise em tempo real |
| 1     | [Serviço de aprovisionamento de dispositivos no Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Aprovisionamento de dispositivos à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para análise de telemetria profunda de dados e permite que as mensagens |

### <a name="standard"></a>Standard

Uma implementação padrão é uma implementação de prontos para produção que um desenvolvedor pode personalizar e expandir. Utilize a opção de implementação standard quando estiver pronto para personalizar uma arquitetura de prontos para produção, criada para dimensionamento e extensibilidade. Microsserviços de aplicação são criados como contentores do Docker e implementados com o Azure Kubernetes Service. O orquestrador Kubernetes implementa, dimensiona e gere os microsserviços.

Uma implementação padrão cria os seguintes serviços na sua subscrição do Azure:

| Contagem | Recurso                                     | SKU / tamanho      | Utilizado para |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Serviço Kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service)| Utilizar um serviço totalmente gerido Kubernetes contentor orquestração, a predefinição é 3 agentes|
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                     | S2 – escalão Standard | Gestão de dispositivos, comando e controlo |
| 1     | [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Armazenar dados de configuração e a telemetria do dispositivo, como regras, alertas e mensagens |
| 5     | [Contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 para armazenamento de VM e 1 para os pontos de verificação de transmissão em fluxo |
| 1     | [Serviço de Aplicações](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway de aplicação através de SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerenciamento de identidades de utilizador e segurança |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Localizações de recurso de visualização |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Ativar a análise em tempo real |
| 1     | [Serviço de aprovisionamento de dispositivos no Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Aprovisionamento de dispositivos à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para análise de telemetria profunda de dados e permite que as mensagens |

> [!NOTE]
> Pode encontrar informações sobre preços desses serviços a [ https://azure.microsoft.com/pricing ](https://azure.microsoft.com/pricing). Pode encontrar detalhes da sua subscrição de faturação e utilização a [Portal do Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Implementar o acelerador de solução

Exemplos de implementação:

### <a name="example-deploy-net-version"></a>Exemplo: implementar a versão do .NET

O exemplo seguinte mostra como implementar a versão do .NET basic, do solution accelerator monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implementar a versão de Java

O exemplo seguinte mostra como implementar a versão de Java padrão, do solution accelerator monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>as opções do comando de PCs

Quando executa o `pcs` comando para implementar uma solução, lhe forem pedidas:

- Um nome para a sua solução. Este nome tem de ser exclusivo.
- A subscrição do Azure que deve utilizar.
- Uma localização.
- Credenciais para as máquinas virtuais que alojam os microsserviços. Pode utilizar estas credenciais para aceder as máquinas virtuais de resolução de problemas.

Quando o `pcs` comando estiver concluído, ele apresenta o URL do seu novo solution accelerator. O `pcs` comando também cria um ficheiro `{deployment-name}-output.json` que contém informações como o nome do IoT Hub que ele criou.

Para obter mais informações sobre os parâmetros da linha de comandos, execute:

```cmd/sh
pcs -h
```

Para obter mais informações sobre a CLI, veja [como utilizar a CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Passos Seguintes

Este guia de procedimentos, ficou a saber como:

> [!div class="checklist"]
> * Configurar o acelerador de soluções
> * Implementar o acelerador de solução
> * Inicie sessão no solution accelerator

Agora que implementou a solução de monitorização remota, a próxima etapa é [explore os recursos do dashboard da solução](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
