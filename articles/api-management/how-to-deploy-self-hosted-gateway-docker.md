---
title: Implementar porta de entrada auto-hospedada para Docker | Microsoft Docs
description: Saiba como implementar um componente de gateway auto-hospedado da Azure API Management para Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725615"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implementar uma porta de entrada auto-hospedada da AZure API Management para Docker

Este artigo fornece os passos para a implementação da componente de gateway auto-hospedada da Azure API Management para um ambiente Docker.

> [!NOTE]
> Hospedar porta de entrada auto-hospedada em Docker é o mais adequado para casos de avaliação e uso de desenvolvimento. Kubernetes é recomendado para uso de produção. Consulte [este](how-to-deploy-self-hosted-gateway-kubernetes.md) documento para aprender a implantar porta de entrada auto-hospedada para Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)
- Criar um ambiente docker. [Docker para Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte [a documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, as suas características e documentação completa sobre o próprio Docker.
- [Fornecimento de um recurso de gateway na sua instância de Gestão de API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Gateway auto-hospedado é embalado como um recipiente estivado x86-64 baseado em Linux.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Desdobre a porta de entrada auto-hospedada para Docker

1. Selecione **Gateways** a partir de **implementação e infraestrutura**.
2. Selecione o recurso gateway que pretende implementar.
3. Selecione **Implantação**.
4. Note que um token de acesso na caixa de texto **Token** foi autogerido para si usando os valores de **validade** e **segredo** padrão. Se necessário, escolha os valores desejados em ambos os controlos para gerar um novo token.
5. Certifique-se de que **o Docker** está selecionado nos **scripts de Implementação.**
6. Selecione o link **de ficheiros env.conf** ao lado **do Ambiente** para descarregar o ficheiro.
7. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **Run** para copiar o comando Docker para a área de transferência.
8. Cole o comando até à janela do terminal (ou comando). Ajuste os mapeamentos da porta e o nome do recipiente conforme necessário. Note que o comando assume que o ficheiro ambiente descarregado está presente no diretório atual.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Execute o comando. O comando instrui o seu ambiente Docker a executar o contentor utilizando a imagem do [contentor](https://aka.ms/apim/sputnik/dhub) descarregada do Registo de Contentores da Microsoft e a mapear as portas HTTP (8080) e HTTPS (8081) do contentor para as portas 80 e 443 do hospedeiro.
10. Verifique o comando abaixo para verificar se o contentor do gateway está em funcionamento:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Volte ao portal Azure, clique em **Overview** e confirme que o recipiente de gateway auto-hospedado que acabou de implantar está a reportar um estado saudável.

    ![estado de gateway](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Use <code>console docker container logs <gateway-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.
>
> Utilize <code>docker container logs --help</code> o comando para ver todas as opções de visualização de registos.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a Azure API Management auto-hospedada gateway overview](self-hosted-gateway-overview.md).
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
