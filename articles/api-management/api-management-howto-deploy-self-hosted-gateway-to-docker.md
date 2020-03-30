---
title: Implementar porta de entrada de gestão da API Azure auto-hospedada para Docker [ Microsoft Docs
description: Saiba como implementar uma porta de entrada de gestão de API Azure auto-hospedada para Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768508"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implementar uma porta de entrada auto-hospedada da Azure API Management para Docker

Este artigo fornece os passos para implantar a porta de entrada de gestão de API Azure auto-hospedada num ambiente Docker.

> [!NOTE]
> A funcionalidade de gateway auto-hospedada está em pré-visualização. Durante a pré-visualização, o gateway auto-hospedado só está disponível nos níveis Developer e Premium sem custos adicionais. O nível de desenvolvimento está limitado a uma única implementação de gateway auto-hospedada.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
- Criar um ambiente Docker. [Docker for Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte a [documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, as suas características e documentação completa sobre o próprio Docker.
- [Fornecer um recurso gateway na sua instância de Gestão API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Gateway auto-hospedado é embalado como um recipiente X86-64 Baseado em Linux Docker.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Desloque a porta de entrada auto-hospedada para Docker

1. Selecione **Gateways** a partir de **definições**.
2. Selecione o recurso de gateway que pretende implementar.
3. Selecione **Implementação**.
4. Note que um novo símbolo na caixa de texto **Token** foi autogerado para si usando os valores padrão **de Expiração** e **Chave Secreta.** Ajuste ambos ou ambos se desejarem e selecione **Generate** para criar um novo símbolo.
4. Certifique-se de que **o Docker** é selecionado em **scripts de implantação**.
5. Selecione link de ficheiro **env.conf** ao lado do **Ambiente** para descarregar o ficheiro.
6. Selecione o ícone **de cópia** localizado na extremidade direita da caixa de texto **Executar** para salvar o comando Do Docker para a área de recortes.
7. Colar o comando à janela do terminal (ou comando). Ajuste os mapeamentos da porta e o nome do contentor conforme necessário. Note que o comando espera que o ficheiro ambiental descarregado esteja presente no atual diretório.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Execute o comando. O comando instrui o seu ambiente Docker a executar o contentor, utilizando a imagem auto-hospedada do gateway descarregada do Registo de Contentores da Microsoft, e a mapear as portas HTTP (8080) e HTTPS (8081) do contentor para as portas 80 e 443 no hospedeiro.
9. Executar o comando abaixo para verificar se a cápsula de gateway está em execução:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Volte ao portal Azure e confirme que o nó de gateway que acabou de implementar está a reportar um estado saudável.

![estado de gateway](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Utilize <code>console docker container logs <gateway-name></code> o comando para visualizar uma imagem do registo de gateway auto-hospedado.
>
> Utilize <code>docker container logs --help</code> o comando para ver todas as opções de visualização de registo.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management .](self-hosted-gateway-overview.md)
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
