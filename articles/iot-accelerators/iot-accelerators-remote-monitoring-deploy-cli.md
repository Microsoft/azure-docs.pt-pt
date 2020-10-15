---
title: Implementar a solução de Monitorização Remota utilizando o CLI - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como providenciar o acelerador de solução de monitorização remota utilizando o CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: f9dcf19f5318021df5d9fdde777b8786942e33d8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072259"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Implementar o acelerador de solução de monitorização remota utilizando o CLI

Este guia de como fazer mostra-lhe como implantar o acelerador de solução de monitorização remota. Utilize a solução utilizando o CLI. Também pode implementar a solução utilizando a UI baseada na web em azureiotsolutions.com, para saber sobre esta opção ver o acelerador [de solução de monitorização remota.](quickstart-remote-monitoring-deploy.md)

## <a name="prerequisites"></a>Pré-requisitos

Para implementar o acelerador de solução de monitorização remota, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para executar o CLI, precisa de [Node.js](https://nodejs.org/) instalado na sua máquina local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar o CLI, executar o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Inscreva-se no CLI

Antes de poder implantar o acelerador de solução, tem de iniciar sação na sua assinatura Azure utilizando o CLI:

```cmd/sh
pcs login
```

Siga as instruções no ecrã para completar o processo de inscrição.

## <a name="deployment-options"></a>Opções de implementação

Quando implementa o acelerador de solução, existem várias opções que configuram o processo de implantação:

| Opção | Valores | Descrição |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Uma implantação _básica_ destina-se a testes e demonstrações, implanta todos os microserviços para uma única máquina virtual. Uma implantação _padrão_ destina-se à produção, implanta os microserviços em várias máquinas virtuais. Uma implantação _local_ configura um recipiente Docker para executar os microserviços na sua máquina local, e utiliza serviços de nuvem Azure, como armazenamento e Cosmos DB. |
| Runtime | `dotnet`, `java` | Seleciona a implementação linguística dos microserviços. |

Para aprender a utilizar a opção de implementação local, consulte [a solução de monitorização remota localmente.](iot-accelerators-remote-monitoring-deploy-local.md)

## <a name="basic-and-standard-deployments"></a>Implementações básicas e padrão

Esta secção resume as principais diferenças entre uma implantação básica e padrão.

### <a name="basic"></a>Básico

Pode fazer uma implementação básica a partir de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) ou utilizar o CLI.

A implementação básica está orientada para mostrar a solução. Para reduzir custos, todos os microserviços são implantados numa única máquina virtual. Esta implantação não usa uma arquitetura pronta para a produção.

Uma implementação básica cria os seguintes serviços na sua subscrição Azure:

| Contagem | Recurso                       | Tipo         | Usado para |
|-------|--------------------------------|--------------|----------|
| 1     | [Máquina virtual Linux](https://azure.microsoft.com/services/virtual-machines/) | Padrão D1 V2  | Hospedar microserviços |
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Nível standard | Gestão e comunicação de dispositivos |
| 1     | [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Armazenar dados de configuração, regras, alertas e outros armazenamentos a frio |  
| 1     | [Conta de Armazenamento do Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)  | Standard        | Armazenamento para postos de controlo de VM e streaming |
| 1     | [Aplicação Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hospedar aplicação web frontal |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestão das identidades e segurança dos utilizadores |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualização de localizações de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Permitir análises em tempo real |
| 1     | [Serviço de fornecimento de dispositivos Azure](../iot-dps/index.yml)        |       S1          | Dispositivos de provisionamento à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para dados de mensagens e permite análise de telemetria de mergulho profundo |

### <a name="standard"></a>Standard

Pode fazer uma implantação padrão apenas utilizando o CLI.

Uma implementação padrão é uma implementação pronta à produção que um desenvolvedor pode personalizar e estender. Use a opção de implementação padrão quando estiver pronto para personalizar uma arquitetura pronta à produção, construída para escala e extensibilidade. Os microserviços de aplicação são construídos como recipientes Docker e implantados usando o Serviço Azure Kubernetes. O orquestrador Kubernetes implanta, escala e gere os microserviços.

Uma implementação padrão cria os seguintes serviços na sua subscrição Azure:

| Contagem | Recurso                                     | SKU / Tamanho      | Usado para |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Use um serviço de orquestração de contentores Kubernetes totalmente gerido, incumprimentos para 3 agentes|
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Nível standard | Gestão, comando e controlo de dispositivos |
| 1     | [BD do Cosmos para o Azure](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Armazenar dados de configuração e telemetria do dispositivo como regras, alertas e mensagens |
| 5     | [Contas de armazenamento Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)    | Standard        | 4 para armazenamento em VM e 1 para os postos de controlo de streaming |
| 1     | [Serviço de Aplicações](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway de aplicação sobre TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestão das identidades e segurança dos utilizadores |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualização de localizações de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Permitir análises em tempo real |
| 1     | [Serviço de fornecimento de dispositivos Azure](../iot-dps/index.yml)        |       S1          | Dispositivos de provisionamento à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para dados de mensagens e permite análise de telemetria de mergulho profundo |

> [!NOTE]
> Pode encontrar informações sobre preços para estes serviços em [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) . Pode encontrar detalhes de utilização e faturação para a sua subscrição no [Portal Azure.](https://portal.azure.com/)

## <a name="deploy-the-solution-accelerator"></a>Implementar o acelerador de soluções

Exemplos de implantação:

### <a name="example-deploy-net-version"></a>Exemplo: implementar a versão .NET

O exemplo a seguir mostra como implementar a versão básica, .NET do acelerador de solução de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implementar a versão Java

O exemplo a seguir mostra como implementar a versão padrão, Java, do acelerador de solução de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>opções de comando pcs

Quando se dirige o `pcs` comando para implementar uma solução, é-lhe pedido:

- Um nome para a sua solução. Este nome tem de ser exclusivo.
- A subscrição do Azure que deve utilizar.
- Uma localização.
- Credenciais para as máquinas virtuais que acolhem os microserviços. Pode utilizar estas credenciais para aceder às máquinas virtuais para resolução de problemas.

Quando o `pcs` comando termina, apresenta o URL do seu novo acelerador de solução. O `pcs` comando também cria um ficheiro que contém `{deployment-name}-output.json` informações como o nome do Hub IoT que criou.

Para obter mais informações sobre os parâmetros da linha de comando, corra:

```cmd/sh
pcs -h
```

Para obter mais informações sobre o CLI, consulte [Como utilizar o CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de como guiar, aprendeu a:

> [!div class="checklist"]
> * Configurar o acelerador de soluções
> * Implementar o acelerador de soluções
> * Inscreva-se no acelerador de solução

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de instrumentos de solução](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->