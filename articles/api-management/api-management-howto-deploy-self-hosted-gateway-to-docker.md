---
title: Implantar o gateway de gerenciamento de API do Azure auto-hospedado no Docker | Microsoft Docs
description: Saiba como implantar um gateway de gerenciamento de API do Azure auto-hospedado para o Docker
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768508"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implantar um gateway auto-hospedado do gerenciamento de API do Azure para o Docker

Este artigo fornece as etapas para implantar o gateway de gerenciamento de API do Azure auto-hospedado em um ambiente do Docker.

> [!NOTE]
> O recurso de gateway auto-hospedado está em versão prévia. Durante a visualização, o gateway auto-hospedado está disponível apenas nas camadas desenvolvedor e Premium sem custo adicional. A camada de desenvolvedor é limitada a uma única implantação de gateway de hospedagem interna.

## <a name="prerequisites"></a>Pré-requisitos

- Concluir o início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
- Crie um ambiente do Docker. O [Docker for Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte a [documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, seus recursos e a documentação abrangente do Docker em si.
- [Provisionar um recurso de gateway em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> O gateway auto-hospedado é empacotado como um contêiner do Docker baseado em Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Implantar o gateway auto-hospedado no Docker

1. Selecione **gateways** em **configurações**.
2. Selecione o recurso de gateway que você pretende implantar.
3. Selecione **implantação**.
4. Observe que um novo token na caixa de texto de **token** foi gerado automaticamente para você usando os valores padrão de **expiração** e **chave secreta** . Ajuste um ou ambos, se desejado, e selecione **gerar** para criar um novo token.
4. Verifique se o **Docker** está selecionado em **scripts de implantação**.
5. Selecione o link **env. conf** do arquivo ao lado do **ambiente** para baixar o arquivo.
6. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **executar** para salvar o comando Docker na área de transferência.
7. Cole o comando na janela de terminal (ou comando). Ajuste os mapeamentos de porta e o nome do contêiner conforme necessário. Observe que o comando espera que o arquivo de ambiente baixado esteja presente no diretório atual.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Execute o comando. O comando instrui o ambiente do Docker a executar o contêiner, usando a imagem do gateway de hospedagem interna baixada do registro de contêiner da Microsoft e mapeando as portas HTTP (8080) e HTTPS (8081) do contêiner para as portas 80 e 443 no host.
9. Execute o comando abaixo para verificar se o pod de gateway está em execução:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Volte para portal do Azure e confirme se o nó de gateway que você acabou de implantar está relatando o status Íntegro.

![status do gateway](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Use <code>console docker container logs <gateway-name></code> comando para exibir um instantâneo do log de gateway auto-hospedado.
>
> Use <code>docker container logs --help</code> comando para ver todas as opções de exibição de log.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md).
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
