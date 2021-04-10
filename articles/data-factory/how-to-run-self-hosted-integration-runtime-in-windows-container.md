---
title: Como executar Self-Hosted tempo de execução de integração no recipiente Windows
description: Saiba como executar Self-Hosted tempo de execução de integração no recipiente Windows.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: e34e1f589442be41e2a4eac3ac893f377675e4b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379510"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Como executar Self-Hosted tempo de execução de integração no recipiente Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo irá explicar como executar Self-Hosted tempo de execução de integração no recipiente Windows.
A Azure Data Factory está a fornecer o suporte oficial do contentor do vidro do Self-Hosted o tempo de execução da integração. Você pode baixar o código fonte de construção do estivador e combinar o processo de construção e execução no seu próprio pipeline de entrega contínua. 

## <a name="prerequisites"></a>Pré-requisitos 
- [Requisitos do contentor do windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker Versão 2.3 e mais tarde 
- versão de prazo de execução de integração Self-Hosted 4.11.7512.1 e posterior 
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
Atualmente não suportamos funcionalidades abaixo quando executamos Self-Hosted tempo de execução de integração no contentor Windows:
- Procuração HTTP 
- Comunicação de nó de nó encriptado com certificado TLS/SSL 
- Gerar e importar backup 
- Serviço Daemon 
- Atualização automática 

### <a name="next-steps"></a>Passos seguintes
- Rever [conceitos de tempo de execução de integração na Azure Data Factory.](./concepts-integration-runtime.md)
- Saiba como [criar um tempo de integração auto-hospedado no portal Azure](./create-self-hosted-integration-runtime.md).