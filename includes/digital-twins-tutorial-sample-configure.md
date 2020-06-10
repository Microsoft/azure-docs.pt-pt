---
author: baanders
description: incluir arquivo para tutoriais Azure Digital Twins - configurar o projeto de amostra
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613537"
---
## <a name="configure-the-sample-project"></a>Configure o projeto da amostra

Em seguida, crie uma aplicação de cliente de amostra que irá interagir com a sua instância Azure Digital Twins. Se ainda não descarregou o projeto da amostra, [obtenha-o agora, descarregando o repositório de amostras Azure Digital Twins como um ficheiro ZIP](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Navegue para o ficheiro descarregado na sua máquina e desaperte-o.

Uma vez dentro da pasta desapertado, navegue em _digital-twins-samples-master/AdtSampleApp/_. Open _**AdtE2ESample.sln**_ in Visual Studio 2019. 

No Visual Studio, utilize o painel *Solution Explorer* para criar uma cópia do ficheiro _SampleClientApp > **serviceConfig.json.TEMPLATE** _ (pode utilizar os menus selecionados à direita para copiar e colar). Mude o nome do serviço de *cópiaConfig.json*. Isto servirá como um ficheiro JSON pré-definido com as variáveis de configuração necessárias para executar o projeto.

Selecione o novo ficheiro para o abrir na janela de edição. Altere o `tenantId` ID do seu *Diretório,* `clientId` para o seu *ID de aplicação,* e `instanceUrl` para o seu exemplo Azure Digital Twins *hostName* URL (com *https://* à sua frente, como mostrado abaixo).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Guarde e feche o ficheiro. 

Em seguida, configurar o ficheiro *conig.json* do serviço a ser copiado para o diretório de saída quando construir o *SampleClientApp*. Para isso, selecione à direita o *ficheiro Conig.json* do serviço e escolha *Propriedades.* No inspetor *de Imóveis,* altere o valor da *propriedade Copy para Output Directory* para Copy se for mais *recente*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Excerto da janela do Estúdio Visual mostrando o painel do Solution Explorer com o serviçoConfig.json em destaque, e o painel de propriedades com a propriedade 'Copy to Output Directory' definido para 'Copy if newer'" border="false":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

