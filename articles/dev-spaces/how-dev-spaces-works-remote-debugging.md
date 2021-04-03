---
title: Como funciona a depuração remota do seu código com a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de depuração remota no Serviço Azure Kubernetes com Azure Dev Spaces
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975047"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Como funciona a depuração remota do seu código com a Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces fornece-lhe múltiplas formas de iteração rápida e depuração rápida de aplicações kubernetes e colaborar com a sua equipa num cluster Azure Kubernetes Service (AKS). Uma vez que o seu projeto está em execução em um espaço dev, Azure Dev Spaces fornece uma maneira de anexar e depurar uma aplicação de execução em AKS.

Este artigo descreve como funciona a depuragem remota com dev Spaces.

## <a name="debug-your-code"></a>Depurar o seu código

Para aplicações Java, .NET Core e Node.js, pode desordiá-la diretamente no seu espaço dev utilizando o Visual Studio Code ou o Visual Studio. Visual Studio Code e Visual Studio fornecem ferramentas para ligar ao seu espaço dev, lançar a sua aplicação e anexar um depurante. Depois de `azds prep` correr, pode abrir o seu projeto no Visual Studio Code ou no Visual Studio. Visual Studio Code ou Visual Studio gerarão os seus próprios ficheiros de configuração para a ligação que está separada da execução `azds prep` . A partir do Código do Estúdio Visual ou do Estúdio Visual, pode definir pontos de rutura e lançar a sua aplicação para o seu espaço dev.

![Depurando o seu código](media/get-started-node/debug-configuration-nodejs2.png)

Quando lança a sua aplicação utilizando o Visual Studio Code ou o Visual Studio para depurar, eles manuseiam o lançamento e a ligação ao seu espaço dev da mesma forma que correr `azds up` . Além disso, a ferramenta do lado do cliente no Visual Studio Code e no Visual Studio cada um fornece um parâmetro adicional com informações específicas para depurar. O parâmetro contém o nome da imagem de depurador, a localização do depurador dentro da imagem do depurador e a localização de destino dentro do contentor da aplicação para montar a pasta de depurador.

A imagem de depurar é automaticamente determinada pela ferramenta do lado do cliente. Utiliza um método semelhante ao utilizado durante o gráfico Dockerfile e Helm durante a execução `azds prep` . Depois de o depurar ser montado na imagem da aplicação, é executado utilizando `azds exec` .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)
