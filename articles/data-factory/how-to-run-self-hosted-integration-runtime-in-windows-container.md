---
title: Como executar o tempo de execução de integração auto-hospedado no recipiente Windows
description: Saiba como executar o tempo de funcionação de integração auto-hospedado no recipiente Windows.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927581"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Como executar o tempo de execução de integração auto-hospedado no recipiente Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como executar o tempo de execução de integração auto-hospedado no recipiente Windows.
A Azure Data Factory está a fornecer o suporte oficial do contentor do vidro do tempo de funcionamento da integração auto-hospedada. Você pode baixar o código fonte de construção do estivador e combinar o processo de construção e execução no seu próprio pipeline de entrega contínua. 

## <a name="prerequisites"></a>Pré-requisitos 
- [Requisitos do contentor do windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker Versão 2.3 e mais tarde 
- Versão de runtime de integração auto-hospedada 4.11.7512.1 e posterior 
## <a name="get-started"></a>Introdução 
1.  Instale o Docker e ative o recipiente do Windows 
2.  Transferir o código fonte de https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Descarregue a versão mais recente SHIR na pasta 'SHIR' 
4.  Abra a pasta na concha: 
```console
cd "yourFolderPath"
```

5.  Construa a imagem do estivador das janelas: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Recipiente de estivador de funcionação: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY é obrigatório para este comando. NODE_NAME, ENABLE_HA e HA_PORT são opcionais. Se não definir o valor, o comando utilizará valores predefinidos. O valor padrão do ENABLE_HA é falso e HA_PORT é de 8060.

## <a name="container-health-check"></a>Verificação sanitária do contentor 
Após 120 segundos de arranque, o verificador de saúde funcionará periodicamente a cada 30 segundos. Fornecerá o estado de saúde do IR ao motor do contentor. 

## <a name="limitations"></a>Limitações
Atualmente não suportamos funcionalidades abaixo ao executar o tempo de execução de integração auto-hospedada no recipiente Windows:
- Procuração HTTP 
- Comunicação de nó de nó encriptado com certificado TLS/SSL 
- Gerar e importar backup 
- Serviço Daemon 
- Atualização automática 

### <a name="next-steps"></a>Passos seguintes
- Rever [conceitos de tempo de execução de integração na Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)
- Saiba como [criar um tempo de integração auto-hospedado no portal Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


