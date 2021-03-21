---
author: baanders
description: incluir arquivo para tutoriais Azure Digital Twins - configurar o projeto de amostra
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463909"
---
## <a name="configure-the-sample-project"></a>Configure o projeto da amostra

Em seguida, crie uma aplicação de cliente de amostra que irá interagir com a sua instância Azure Digital Twins.

Navegue na sua máquina para o arquivar mais cedo a partir de amostras de ponta a ponta da [*Azure Digital Twins*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (e desaperte-as se ainda não o fez).

Uma vez dentro da pasta, navegue no _AdtSampleApp_. Open _**AdtE2ESample.sln**_ no Visual Studio 2019. 

No Estúdio Visual, selecione o _SampleClientApp > **appsettings.jsno**_ ficheiro para o abrir na janela de edição. Isto servirá como um ficheiro JSON pré-definido com as variáveis de configuração necessárias para executar o projeto.

No corpo do ficheiro, mude o para o `instanceUrl` seu exemplo Azure Digital Twins *hostName URL* (adicionando **_https://_** em frente ao *nome anfitrião,* como mostrado abaixo).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Guarde e feche o ficheiro. 

Em seguida, configurar o *appsettings.jsno* ficheiro a ser copiado para o diretório de saída quando construir o *SampleClientApp*. Para isso, selecione o *appsettings.jsno* ficheiro e escolha **Propriedades**. No inspetor **imobiliário,** procure a *propriedade Copy to Output Directory.* Altere o valor para **Copiar se** já não estiver definido para tal.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Excerto da janela do Estúdio Visual mostrando o painel do Solution Explorer com appsettings.jsem destaque, e o painel de propriedades com a propriedade 'Copy to Output Directory' definido para 'Copy if new'" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
