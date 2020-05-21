---
title: Compor um modelo com o Unity
description: Quickstart que guia o utilizador através dos passos para renderizar um modelo
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: be5bc8ec4e8f363d9b6079d22f3bba1af2d4d5fb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682564"
---
# <a name="quickstart-render-a-model-with-unity"></a>Quickstart: Render um modelo com Unidade

Este quickstart cobre como executar uma amostra de Unidade que torna um modelo incorporado remotamente, utilizando o serviço de renderização remota Azure (ARR).

Não entraremos em detalhes sobre a própria ARR API ou como criar um novo projeto de Unidade. Estes tópicos são abordados no [Tutorial: Criação de um projeto de Unidade do zero.](../tutorials/unity/project-setup.md)

Neste arranque rápido, aprenderá a:
> [!div class="checklist"]
>
>* Instale o seu ambiente de desenvolvimento local
>* Obtenha e construa a app de amostras ARR Quickstart para a Unidade
>* Render um modelo na aplicação de amostra seletiva ARR Quickstart

## <a name="prerequisites"></a>Pré-requisitos

Para ter acesso ao serviço de renderização remota Azure, primeiro precisa [de criar uma conta](../how-tos/create-an-account.md).

Deve ser instalado o seguinte software:

* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unidade 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Instale estes módulos na Unidade:
    * **UWP** - Suporte universal para construir plataforma Windows
    * **IL2CPP** - Suporte para construção de janelas (IL2CPP)

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Abra um pedido de comando (digite `cmd` no menu inicial do Windows) e mude para um diretório onde pretende armazenar o projeto de amostra ARR.

Execute os seguintes comandos:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

O último comando cria um subdiretório no diretório ARR contendo os vários projetos de amostra para a renderização remota azure.

A aplicação de amostra sintetizada para unidade é encontrada no subdiretório *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Renderizando um modelo com o projeto de amostra de unidade

Abra o Unity Hub e adicione o projeto de amostra, que é a pasta *ARR\azure-remote-rendering\Unity\Quickstart.*
Abra o projeto. Se necessário, permita que a Unidade atualize o projeto para a sua versão instalada.

O modelo padrão que prestamos é um [modelo de amostra incorporado.](../samples/sample-model.md) Mostraremos como converter um modelo personalizado utilizando o serviço de conversão ARR no [próximo quickstart](convert-model.md).

### <a name="enter-your-account-info"></a>Insira as informações da sua conta

1. No navegador de ativos da Unidade, navegue para a pasta *Scenes* e abra a cena **Quickstart.**
1. A partir da *Hierarquia, selecione*o objeto de jogo **RemoteRendering.**
1. No *Inspetor,* insira [as credenciais da](../how-tos/create-an-account.md)sua conta. Se ainda não tem uma conta, [crie uma.](../how-tos/create-an-account.md)

![Informação da conta ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> O Portal Azure exibe o domínio da sua conta apenas *mixedreality.azure.com*. Isto é insuficiente para uma ligação com sucesso.
> Definir **AccountDomain** `<region>.mixedreality.azure.com` para, onde `<region>` está uma das [regiões disponíveis perto de si.](../reference/regions.md)

Mais tarde queremos implementar este projeto para um HoloLens e ligar-nos ao serviço de renderização remota a partir desse dispositivo. Uma vez que não temos uma maneira fácil de introduzir as credenciais no dispositivo, a amostra de arranque rápido **irá salvar as credenciais na cena da Unidade**.

> [!WARNING]
> Certifique-se de não verificar o projeto com as suas credenciais guardadas em algum repositório onde iria vazar informações secretas de login!

### <a name="create-a-session-and-view-the-default-model"></a>Crie uma sessão e veja o modelo padrão

Prima o botão **Play** da Unidade para iniciar a sessão. Você deve ver uma sobreposição com texto de estado, na parte inferior do viewport no painel *game.* A sessão será submetida a uma série de transições estatais. No estado **de partida,** o VM remoto é fiado, o que leva vários minutos. Após o sucesso, transita para o estado **pronto.** Agora a sessão entra no estado **de Ligação,** onde tenta atingir o tempo de execução da renderização naquele VM. Quando bem sucedida, a amostra transita para o estado **conectado.** Neste momento, começará a descarregar o modelo para renderização. Devido ao tamanho do modelo, o download pode demorar mais alguns minutos. Em seguida, aparecerá o modelo telecomandado.

![Saída da amostra](media/arr-sample-output.png)

Parabéns! Está agora a ver um modelo remotamente renderizado!

## <a name="inspecting-the-scene"></a>Inspecionando a cena

Uma vez em funcionamento a ligação de renderização remota, o painel do Inspetor atualiza com informações adicionais sobre o estado:

![Amostra de unidade jogando](./media/arr-sample-configure-session-running.png)

Agora pode explorar o gráfico de cena selecionando o novo nó e clicando em **mostrar às crianças** no Inspetor.

![Hierarquia da Unidade](./media/unity-hierarchy.png)

Há um objeto de [avião cortado](../overview/features/cut-planes.md) no local. Tente capacitá-lo nas suas propriedades e movê-lo ao redor:

![Mudar o avião de corte](media/arr-sample-unity-cutplane.png)

Para sincronizar transformações, clique em **Sync agora** ou verifique o Sync cada opção **de quadro.** Para propriedades componentes, apenas mudá-las é suficiente.

## <a name="next-steps"></a>Próximos passos

No próximo arranque rápido, vamos implantar a amostra num HoloLens para ver o modelo remotamente renderizado no seu tamanho original.

> [!div class="nextstepaction"]
> [Quickstart: Implementar amostra de unidade para HoloLens](deploy-to-hololens.md)

Em alternativa, a amostra também pode ser implantada para um pc de ambiente de trabalho.

> [!div class="nextstepaction"]
> [Quickstart: Implementar amostra de unidade para desktop](deploy-to-desktop.md)