---
title: Implementar a solução de monitorização remota utilizando o CLI - Azure  Microsoft Docs
description: Este guia de como fazer mostra como fornecer o acelerador de solução de monitorização remota utilizando o CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383074"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Implementar o acelerador de solução de monitorização remota utilizando o CLI

Este guia de como fazer mostra como implementar o acelerador de solução de monitorização remota. Implementa a solução utilizando o CLI. Também pode implementar a solução utilizando o UI baseado na web em azureiotsolutions.com, para saber sobre esta opção ver o acelerador de [solução de monitorização remota de implementar.](quickstart-remote-monitoring-deploy.md)

## <a name="prerequisites"></a>Pré-requisitos

Para implementar o acelerador de soluções de monitorização remota, necessita de uma subscrição azure ativa.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para executar o CLI, precisa de [Node.js](https://nodejs.org/) instalado na sua máquina local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar o CLI, execute o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Inscreva-se no CLI

Antes de poder implementar o acelerador de solução, tem de iniciar sessão na subscrição do Azure utilizando o CLI:

```cmd/sh
pcs login
```

Siga as instruções no ecrã para completar o processo de iniciar a inscrição.

## <a name="deployment-options"></a>Opções de implementação

Quando implementa o acelerador de soluções, existem várias opções que configuram o processo de implementação:

| Opção | Valores | Descrição |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Uma implantação _básica_ destina-se a testes e demonstrações, implanta todos os microserviços para uma única máquina virtual. Uma implantação _padrão_ destina-se à produção, implanta os microserviços em várias máquinas virtuais. Um destacamento _local_ configura um contentor Docker para executar os microserviços na sua máquina local, e utiliza serviços de nuvem Azure, como armazenamento e Cosmos DB. |
| Runtime | `dotnet`, `java` | Seleciona a implementação linguística dos microserviços. |

Para aprender a utilizar a opção de implementação local, consulte [Executar a solução de monitorização remota localmente](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Implantações básicas e standard

Esta secção resume as principais diferenças entre uma implantação básica e normalizada.

### <a name="basic"></a>Básica

Pode fazer uma implementação básica a partir de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) ou utilizando o CLI.

A implementação básica está orientada para mostrar a solução. Para reduzir custos, todos os microserviços são implantados numa única máquina virtual. Esta implantação não usa uma arquitetura pronta para a produção.

Uma implementação básica cria os seguintes serviços na sua subscrição Azure:

| Contagem | Recurso                       | Tipo         | Usado para |
|-------|--------------------------------|--------------|----------|
| 1     | [Máquina Virtual Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hospedagem de microserviços |
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                  | S1 - Nível standard | Gestão e comunicação de dispositivos |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Armazenar dados de configuração, regras, alertas e outros armazenamentos frios |  
| 1     | [Conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Armazenamento para VM e pontos de verificação de streaming |
| 1     | [Aplicação Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hospedar aplicação web frontal |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerir identidades e segurança dos utilizadores |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualização de localizações de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Habilitar a análise em tempo real |
| 1     | [Serviço de Provisionamento de Dispositivos Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Dispositivos de provisionamento à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento de dados de mensagens e permite análise de telemetria de mergulho profundo |

### <a name="standard"></a>Standard

Só pode fazer uma implementação padrão utilizando o CLI.

Uma implementação padrão é uma implementação pronta para a produção que um desenvolvedor pode personalizar e estender. Use a opção de implementação padrão quando estiver pronto para personalizar uma arquitetura pronta para a produção, construída para escala e extensibility. Os microserviços de aplicações são construídos como contentores Docker e implantados utilizando o Serviço Azure Kubernetes. O orquestrador Kubernetes implanta, escala e gere os microserviços.

Uma implementação padrão cria os seguintes serviços na sua subscrição Azure:

| Contagem | Recurso                                     | SKU / Tamanho      | Usado para |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Serviço Kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service)| Utilize um serviço de orquestração de contentores Kubernetes totalmente gerido, incumprimentos a 3 agentes|
| 1     | [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/)                     | S2 - Nível standard | Gestão, comando e controlo de dispositivos |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Armazenar dados de configuração e telemetria do dispositivo como regras, alertas e mensagens |
| 5     | [Contas de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 para armazenamento vm, e 1 para os pontos de verificação de streaming |
| 1     | [Serviço de Aplicações](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway de aplicação sobre SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerir identidades e segurança dos utilizadores |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualização de localizações de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Habilitar a análise em tempo real |
| 1     | [Serviço de Provisionamento de Dispositivos Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Dispositivos de provisionamento à escala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento de dados de mensagens e permite análise de telemetria de mergulho profundo |

> [!NOTE]
> Pode encontrar informações sobre preços para estes serviços em [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). Pode encontrar detalhes de utilização e faturação para a sua subscrição no [Portal Azure.](https://portal.azure.com/)

## <a name="deploy-the-solution-accelerator"></a>Implementar o acelerador de solução

Exemplos de implantação:

### <a name="example-deploy-net-version"></a>Exemplo: implementar versão .NET

O exemplo que se segue mostra como implementar a versão básica.NET do acelerador de soluções de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implementar versão Java

O exemplo que se segue mostra como implementar a versão padrão java do acelerador de solução de monitorização remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>opções de comando pcs

Quando executas o comando `pcs` para implementar uma solução, é-te pedido:

- Um nome para a sua solução. Este nome tem de ser exclusivo.
- A subscrição do Azure que deve utilizar.
- Um local.
- Credenciais para as máquinas virtuais que acolhem os microserviços. Pode usar estas credenciais para aceder às máquinas virtuais para resolução de problemas.

Quando o comando `pcs` termina, exibe o URL do seu novo acelerador de soluções. O comando `pcs` também cria um ficheiro `{deployment-name}-output.json` que contém informações como o nome do Hub IoT que criou.

Para mais informações sobre os parâmetros da linha de comando, corra:

```cmd/sh
pcs -h
```

Para obter mais informações sobre o CLI, consulte [Como utilizar o CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Passos Seguintes

Neste guia de como orientar, aprendeu a:

> [!div class="checklist"]
> * Configurar o acelerador de soluções
> * Implementar o acelerador de solução
> * Inscreva-se no acelerador de soluções

Agora que implementou a solução de Monitorização Remota, o próximo passo é [explorar as capacidades do painel de soluções](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
