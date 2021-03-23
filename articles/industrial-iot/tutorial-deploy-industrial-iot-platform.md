---
title: Implementar a Plataforma Azure Industrial IoT
description: Neste tutorial, aprende-se a implantar a Plataforma IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787870"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Tutorial: Implementar a Plataforma Azure Industrial IoT

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Sobre os principais componentes da Plataforma IIoT
> * Sobre os diferentes tipos de instalação
> * Como implantar a Plataforma Industrial IoT

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure deve ser criada
- Baixar [Git](https://git-scm.com/downloads)
- Os registos de aplicações Azure Ative (AAD) utilizados para a autenticação requerem direitos de administrador global, administrador de aplicações ou administrador de aplicação em nuvem para fornecer consentimento administrativo em todo o inquilino (ver abaixo para mais opções)
- Os sistemas operativos suportados para implementação são Windows, Linux e Mac
- IoT Edge suporta Windows 10 IoT Enterprise LTSC e Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Componentes Principais

- Dependências mínimas: IoT Hub, Cosmos DB, Service Bus, Event Hub, Key Vault, Storage
- Dependências padrão: Serviço mínimo + SignalR, registos de aplicações AAD, Serviço de Provisionamento de Dispositivos, Insights de Séries De Tempo, Livro, Análise de Registos, Insights de Aplicações
- Microserviços: Plano de Serviço de Aplicações, Serviço de Aplicações
- UI (web app): Plano de Serviço de Aplicações (partilhado com microserviços), Serviço de Aplicações
- Simulação: Máquina virtual, rede virtual, Borda IoT
- Azure Kubernetes Service

## <a name="installation-types"></a>Tipos de instalação

- Mínimo: Dependências mínimas
- Local: Mínimo e as dependências padrão
- Serviços: Locais e os microserviços
- Simulação: Dependências mínimas e componentes de simulação
- App: Serviços e a UI
- Tudo (predefinido): App e a simulação

## <a name="deployment"></a>Implementação

1. Para começar com a implantação da Plataforma IIoT, clone o repositório a partir do pedido de comando ou terminal.

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Inicie a implementação guiada, o script recolherá as informações necessárias, tais como conta Azure, subscrição, recurso alvo e nome de grupo e aplicação.

No Windows:
    ```
    .\deploy
    ```

Em Linux ou Mac:
    ```
    ./deploy.sh
    ```

3. Os microserviços e a UI são aplicações web que requerem autenticação, isto requer três registos de aplicações na AAD. Se os direitos necessários estiverem em falta, existem duas soluções possíveis:

- Peça ao administrador da AAD que conceda consentimento administrativo a todo o inquilino para o pedido
- Um administrador AAD pode criar as aplicações AAD. A pasta de implementação/scripts contém o script aad-register.ps1 para executar o registo AAD separadamente da implementação. A saída do script é um ficheiro que contém as informações relevantes a serem utilizadas como parte da implementação e deve ser passado para o deploy.ps1 script na mesma pasta utilizando o argumento - aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

Para implementações de produção que requerem encenação, reversão, escala e resiliência, a plataforma pode ser implantada no [Serviço Azure Kubernetes (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Referências:
- [Implantação da Plataforma IoT Industrial Azure](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Como implementar tudo-em-um](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Como implementar a plataforma em AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Passos seguintes
Agora que implementou a Plataforma IIoT, pode aprender a personalizar a configuração dos componentes:

> [!div class="nextstepaction"]
> [Personalizar a configuração dos componentes](tutorial-configure-industrial-iot-components.md)
