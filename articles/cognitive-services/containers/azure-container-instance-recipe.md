---
title: Receita de instância de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implementar contentores de serviços cognitivos na instância de contentor do Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207493"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Implementar e executar o contentor na instância de contentor do Azure (ACI)

Com os seguintes passos, dimensione as aplicações de serviços cognitivos do Azure na cloud facilmente com o Azure [instância de contentor](https://docs.microsoft.com/azure/container-instances/) (ACI). Esta ajuda que se concentrar na criação de seus aplicativos em vez de gerir a infraestrutura.

## <a name="prerequisites"></a>Pré-requisitos

Esta solução funciona com qualquer contentor de serviços cognitivos. O recurso de serviço cognitivo tem de ser criado no portal do Azure antes de utilizar essa receita. Cada serviço cognitivos que suporte contentores tem um documento de "Como instalar" especificamente para instalar e configurar o serviço para um contentor. Uma vez que alguns serviços requerem um ficheiro ou conjunto de arquivos como entrada para o contentor, é importante que entenda e ter usado o contentor com êxito antes de utilizar esta solução.

* Um recurso de serviço cognitivo, criado no portal do Azure.
* Serviço cognitivo **URL de ponto final** -rever seu serviço específico "como instalar o" para o contentor, para localizar onde o URL de ponto final é a partir do portal do Azure e o que um exemplo correto do URL é semelhante. O formato exato pode alterar a partir de serviços.
* Serviço cognitivo **chave** -as chaves são sobre o **chaves** página para o recurso do Azure. Só precisa de uma das duas chaves. A chave é uma cadeia de caracteres de 32 carateres de alfanuméricos.
* Um único cognitivos serviços de contentor no seu anfitrião local (o seu computador). Certifique-se de que pode:
  * Obter a imagem com um `docker pull` comando.
  * Executar o contentor local com êxito com todas as definições de configuração necessárias com um `docker run` comando.
  * Chame o ponto de extremidade do contentor, voltando uma resposta de 2xx e uma resposta JSON.

Todas as variáveis em colchetes angulares, `<>`, precisam ser substituídos pelos seus próprios valores. Essa substituição inclui dos parênteses.

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>Passo 2: Iniciar o contentor no Azure Container Instances (ACI)

**Criar o recurso de instância de contentor do Azure (ACI).**

1. Vá para o [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) página do Container Instances.

1. Sobre o **Noções básicas** separador, introduza os seguintes detalhes:

    |Página|Definição|Value|
    |--|--|--|
    |Noções básicas|Subscrição|Selecione a sua subscrição.|
    |Noções básicas|Grupo de recursos|Selecione o grupo de recursos disponíveis ou criar um novo como `cognitive-services`.|
    |Noções básicas|Nome do contentor|Introduza um nome como `cognitive-container-instance`. Este nome tem de estar no limites inferiores.|
    |Noções básicas|Location|Selecione uma região para a implementação.|
    |Noções básicas|Tipo de imagem|`Public`|
    |Noções básicas|Nome da imagem|Introduza a localização do contentor de serviços cognitivos. Isso pode ser a mesma localização que utilizou no `docker pull` comando, _por exemplo_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Noções básicas|Tipo de SO|`Linux`|
    |Noções básicas|Tamanho|Alterar o tamanho para as recomendações sugeridas para o seu contentor específico do serviço cognitivo.:<br>2 núcleos<br>4GB
    ||||
  
1. Sobre o **redes** separador, introduza os seguintes detalhes:

    |Página|Definição|Value|
    |--|--|--|
    |Redes|Portas|Edite a porta de existente para TCP partir `80` para `5000`. Isso significa que estão a expor o contentor na porta 5000.|
    ||||

1. Sobre o **avançadas** separador, introduza os seguintes detalhes para passar através do contêiner necessárias definições para o recurso de instância de contentor de faturação:

    |Chave de página Avançadas|Valor de página Avançadas|
    |--|--|
    |`apikey`|Copiado do **chaves** página do recurso. Precisa apenas uma das duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado do **descrição geral** página do recurso. |
    |`eula`|`accept`|

    Se o seu contentor tiver outras definições de configuração como monta entrada, saída monta ou Registro em log, essas definições também tem de ser adicionado.

1. Selecione **reveja e criar**.
1. Depois de passar a validação, selecione **criar** para concluir o processo de criação.
1. Selecione o ícone de sino na parte superior da navegação. Esta é a janela de notificação. Isso exibirá um azul **Ir para recurso** botão quando o recurso é criado. Selecione o botão Ir novo recurso.

## <a name="use-the-container-instance"></a>Utilizar a instância de contentor

1. Selecione o **descrição geral** e copie o endereço IP. É um endereço IP numérico como `55.55.55.55`.
1. Abra um novo separador do browser e utilizar o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Verá a homepage do contentor, permitindo-lhe saber que o contentor está em execução.

1. Selecione **descrição do serviço de API** para ver a página de swagger para o contentor.

1. Selecione qualquer uma da **POST** APIs e selecione **experimentá-lo**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **Execute** para enviar o pedido para a instância do contentor.

    Criou com êxito e utilizado contentores de serviços cognitivos na instância de contentor do Azure.
