---
title: Implementar porta de entrada auto-hospedada para Docker [ Microsoft Docs
description: Saiba como implementar um componente de gateway auto-hospedado da Azure API Management para Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205095"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implementar uma porta de entrada auto-hospedada da Azure API Management para Docker

Este artigo fornece os passos para a implementação da componente de gateway auto-hospedada da Azure API Management para um ambiente Docker.

> [!NOTE]
> Hospedar gateway auto-hospedado em Docker é o mais adequado para casos de avaliação e uso de desenvolvimento. As kubernetes são recomendadas para uso da produção. Consulte [este](how-to-deploy-self-hosted-gateway-kubernetes.md) documento para aprender a implementar porta de entrada auto-hospedada para Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
- Criar um ambiente Docker. [Docker for Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte a [documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, as suas características e documentação completa sobre o próprio Docker.
- [Fornecer um recurso gateway na sua instância de Gestão API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Gateway auto-hospedado é embalado como um recipiente X86-64 Baseado em Linux Docker.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Desloque a porta de entrada auto-hospedada para Docker

1. Selecione **Gateways** a partir de baixo **da implantação e infraestrutura**.
2. Selecione o recurso de gateway que pretende implementar.
3. Selecione **Implementação**.
4. Note que um token de acesso na caixa de texto **Token** foi autogerado para si usando os **valores-chave** de **Validade** e Segredo predefinidos. Se necessário, escolha os valores desejados em ambos os controlos para gerar um novo símbolo.
4. Certifique-se de que **o Docker** é selecionado em **scripts de implantação**.
5. Selecione link de ficheiro **env.conf** ao lado do **Ambiente** para descarregar o ficheiro.
6. Selecione o ícone **de cópia** localizado na extremidade direita da caixa de texto **Executar** para copiar o comando Do Docker para a área de sobre-gravação.
7. Colar o comando à janela do terminal (ou comando). Ajuste os mapeamentos da porta e o nome do contentor conforme necessário. Note que o comando assume que o ficheiro ambiental descarregado está presente no diretório atual.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Execute o comando. O comando instrui o seu ambiente Docker a executar o contentor utilizando [a imagem](https://aka.ms/apim/sputnik/dhub) do contentor descarregada do Registo de Contentores da Microsoft e a mapear as portas HTTP (8080) e HTTPS (8081) do contentor para as portas 80 e 443 no hospedeiro.
9. Executar o comando abaixo para verificar se o recipiente de porta de entrada está em funcionamento:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Volte ao portal Azure, clique em **Overview** e confirme que o contentor de gateway auto-hospedado que acaba de implementar está a reportar um estado saudável.

![estado de gateway](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Utilize <code>console docker container logs <gateway-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.
>
> Utilize <code>docker container logs --help</code> o comando para ver todas as opções de visualização de registo.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management .](self-hosted-gateway-overview.md)
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
