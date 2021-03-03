---
title: Criar um recurso Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como criar um recurso Azure Cognitive Services Tradutor e obter uma chave de subscrição e URL de ponto final.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712934"
---
# <a name="create-a-translator-resource"></a>Criar um recurso Tradutor

Neste artigo, você vai aprender a criar um recurso Tradutor no portal Azure. [Azure Tradutor](translator-info-overview.md) é um serviço de tradução automática baseado em nuvem que faz parte da família [Azure Cognitive Services](../what-are-cognitive-services.md) da REST APIs. Os recursos Azure são casos de serviços que cria. Todos os pedidos da API aos serviços da Azure requerem um URL **de ponta** e uma chave de **subscrição** apenas de leitura para autenticar o acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de uma [**conta Azure**](https://azure.microsoft.com/free/cognitive-services/)ativa.  Se não tiver uma, pode [**criar uma subscrição gratuita de 12 meses.**](https://azure.microsoft.com/free/)

## <a name="translator-resource-types"></a>Tipos de recursos de tradutor

O serviço Tradutor pode ser acedido através de dois tipos diferentes de recursos:

* **Os** tipos de recursos de serviço único permitem o acesso a uma única chave API de serviço e ponto final.  

* Os tipos de recursos **multi-serviço** permitem o acesso a múltiplos Serviços Cognitivos utilizando uma única chave API e ponto final. O recurso Serviços Cognitivos está atualmente disponível para os seguintes serviços:
  * Língua[(Tradutor,](../translator/translator-info-overview.md) [Compreensão de Línguas (LUIS)](../luis/what-is-luis.md), [Análise de Texto)](../text-analytics/overview.md)  
  * Visão[(Visão](../computer-vision/overview.md)Computacional),[(Face)](../face/overview.md)  
  * Decisão[(Moderador de Conteúdo)](../content-moderator/overview.md)  

## <a name="create-your-resource"></a>Crie o seu recurso

* Navegue diretamente para a página [**Criar Tradutor**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) no portal Azure para completar os detalhes do seu projeto.

* Navegue diretamente para a página [**Criar Serviços Cognitivos**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) no portal Azure para completar os detalhes do seu projeto.

>[!TIP]
>Se preferir, pode iniciar na página inicial do Portal Azure para iniciar o processo **Criar** da seguinte forma:
>
> 1. Navegue para a página inicial do [**Portal Azure.**](https://ms.portal.azure.com/#home)
> 1. Selecione ➕**Crie um recurso**  a partir do menu de serviços Azure.
>1. Na caixa de pesquisa **do Marketplace,** introduza e selecione **Tradutor** (recurso de serviço único) ou **Serviços Cognitivos** (recurso multi-serviço).  *Consulte* [Escolha o seu tipo de recurso,](#create-your-resource)acima.
> 1. **Selecione Criar** e será levado para a página de detalhes do projeto.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Complete os detalhes do seu projeto e instância

1. **Subscrição**. Selecione uma das suas subscrições Azure disponíveis.

1. **Grupo de Recursos**. O grupo de recursos Azure que escolhe serve como recipiente virtual para o seu novo recurso. Pode criar um novo grupo de recursos ou adicionar o seu recurso a um grupo de recursos pré-existente que partilhe o mesmo ciclo de vida, permissões e políticas.

1. **Região de Recursos**. Escolha **a Global** a menos que o seu negócio ou aplicação exija uma região específica. O tradutor é um serviço não regional — não há dependência de uma região específica de Azure. *Ver* [Regiões e Zonas de Disponibilidade em Azure.](../../availability-zones/az-overview.md)

1. **Nome**. Insira o nome que escolheu para o seu recurso. O nome que escolher deve ser único dentro de Azure.

> [!NOTE]
> Se estiver a utilizar uma função De Tradutor que exija um ponto final de domínio personalizado, o valor que introduz no campo Nome será o parâmetro de nome de domínio personalizado para o ponto final.

5. **Escalão de preço**. Selecione um [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator) que satisfaça as suas necessidades:

   * Cada subscrição tem um nível livre.
   * O nível livre tem as mesmas funcionalidades e funcionalidades que os planos pagos e não expira.
   * É permitido apenas uma subscrição gratuita por conta.</li></ul>

1. Se criou um recurso multi-serviço, terá de confirmar detalhes adicionais de utilização através das caixas de verificação.

1. Selecione **Rever + Criar**.

1. Reveja os termos de serviço e **selecione Criar** para implementar o seu recurso.

1. Depois de o seu recurso ter sido implementado com sucesso, selecione **Ir para o recurso**.

### <a name="authentication-keys-and-endpoint-url"></a>Chaves de autenticação e URL de ponto final

Todos os pedidos de API de Serviços Cognitivos requerem um URL de ponto final e uma chave de leitura apenas para a autenticação.

* **Chaves de autenticação**. A sua chave é uma corda única que é transmitida em todos os pedidos ao serviço de Tradução. Pode passar a sua chave através de um parâmetro de cadeia de consulta ou especificando-a no cabeçalho de pedido HTTP.

* **URL de ponto final.** Utilize o ponto final Global no seu pedido de API a menos que precise de uma região específica do Azure. *Ver* [URLs de base.](reference/v3-0-reference.md#base-urls) O URL global de ponto final é `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Obtenha as suas chaves de autenticação e ponto final

1. Depois de o seu novo recurso ser implementado, selecione **Ir para o recurso** ou navegar diretamente para a sua página de recursos.
1. No trilho esquerdo, sob *Gestão de Recursos,* selecione **Keys e Endpoint**.
1. Copie e cole as suas chaves de subscrição e URL de ponto final num local conveniente, como *o Microsoft Notepad*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Pegue a chave e o ponto final.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Como eliminar um grupo de recursos ou recursos

> [!Warning]
> A eliminação de um grupo de recursos também elimina todos os recursos contidos no grupo.

Para remover um recurso de Serviços Cognitivos ou Tradutor, pode **eliminar o recurso** ou eliminar o grupo de **recursos**.

Para eliminar o recurso:

1. Navegue para o seu Grupo de Recursos no portal Azure.
1. Selecione os recursos a eliminar selecionando a caixa de verificação adjacente.
1. **Selecione Excluir** do menu superior perto da borda direita.
1. Digite *sim* na caixa de diálogo **de recursos eliminados.**
1. Selecione **Eliminar**.

Para eliminar o grupo de recursos:

1. Navegue para o seu Grupo de Recursos no portal Azure.
1. Selecione o grupo de **recursos Delete** da barra de menu superior perto da borda esquerda.
1. Confirme o pedido de eliminação introduzindo o nome do grupo de recursos e selecionando **Delete**.

## <a name="how-to-get-started-with-translator"></a>Como começar com o Tradutor

No nosso quickstart, você aprenderá a usar o serviço de Tradutor com APIs REST.

> [!div class="nextstepaction"]
> [Começar com tradutor](quickstart-translator.md)

## <a name="more-resources"></a>Mais recursos

* [Amostras de código do Microsoft Tradutor](https://github.com/MicrosoftTranslator).  As amostras de código tradutor multi-linguísticos estão disponíveis no GitHub.
* [Microsoft Tradutor Support Forum](https://www.aka.ms/TranslatorForum)
* [Começar com Azure (vídeo de 3 minutos)](https://azure.microsoft.com/get-started/?b=16.24)