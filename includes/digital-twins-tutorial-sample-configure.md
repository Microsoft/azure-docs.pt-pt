---
author: baanders
description: incluir arquivo para tutoriais Azure Digital Twins - configurar o projeto de amostra
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: d442c5782af1f55cd5d894823945d517dbc48091
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92323480"
---
## <a name="configure-the-sample-project"></a>Configure o projeto da amostra

Em seguida, crie uma aplicação de cliente de amostra que irá interagir com a sua instância Azure Digital Twins.

Navegue na sua máquina para o arquivar mais cedo a partir de amostras de ponta a ponta da [*Azure Digital Twins*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (e desaperte-as se ainda não o fez).

Uma vez dentro da pasta, navegue no _AdtSampleApp_. Open _**AdtE2ESample.sln**_ in Visual Studio 2019. 

No Visual Studio, utilize o painel *Solution Explorer* para criar uma cópia do _SampleClientApp > **serviceConfig.jsligado. Ficheiro MODELO** _ (pode utilizar os menus de seleção à direita para copiar e colar). Mude o nome da cópia *serviceConfig.js.* Isto servirá como um ficheiro JSON pré-definido com as variáveis de configuração necessárias para executar o projeto.

Selecione a *serviceConfig.jsno* ficheiro para abri-la na janela de edição. Altere o `instanceUrl` para o seu exemplo Azure Digital Twins *hostName* URL (com *https://* à sua frente, como mostrado abaixo).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Guarde e feche o ficheiro. 

Em seguida, configurar o *serviceConfig.jsno* ficheiro a ser copiado para o diretório de saída quando construir o *SampleClientApp*. Para isso, selecione o *serviceConfig.jsno* ficheiro e escolha *Propriedades.* No inspetor *de Imóveis,* altere o valor da *propriedade Copy para Output Directory* para Copy se for mais *recente*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Excerto da janela do Estúdio Visual mostrando o painel do Solution Explorer com serviceConfig.jsem destaque, e o painel de propriedades com a propriedade 'Copy to Output Directory' definido para 'Copy if new'" border="false":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

