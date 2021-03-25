---
title: Compor um modelo com o Unity
description: Quickstart que guia o utilizador através dos passos para renderizar um modelo
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 3f565f456dde1d802a82faffb4a23f7a6e54d950
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031550"
---
# <a name="quickstart-render-a-model-with-unity"></a>Quickstart: Renderiza um modelo com Unidade

Este quickstart cobre como executar uma amostra de Unidade que torna um modelo incorporado remotamente, usando o serviço de renderização remota (ARR) do Azure.

Não entraremos em detalhes sobre a própria ARR API ou como criar um novo projeto de Unidade. Estes tópicos são abordados no [Tutorial: Visualização remota de modelos renderizados.](../tutorials/unity/view-remote-models/view-remote-models.md)

Neste início rápido, vai aprender a:
> [!div class="checklist"]
>
>* Configurar o seu ambiente de desenvolvimento local
>* Obter e construir a app de amostras ARR Quickstart para unidade
>* Rendereir um modelo na aplicação de amostra ARR Quickstart

## <a name="prerequisites"></a>Pré-requisitos

Para ter acesso ao serviço de renderização remota Azure, primeiro precisa [de criar uma conta.](../how-tos/create-an-account.md)

Deve ser instalado o seguinte software:

* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A mais recente versão do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Ferramentas de Estúdio Visual para Realidade Mista.](/windows/mixed-reality/install-the-tools) Especificamente, as seguintes instalações *de carga de trabalho* são obrigatórias:
  * **Desenvolvimento de desktop com C++**
  * **Desenvolvimento da Plataforma Universal windows (UWP)**
* GIT [(download)](https://git-scm.com/downloads)
* Unidade (ver [requisitos do sistema](../overview/system-requirements.md#unity) para versões suportadas)

## <a name="clone-the-sample-app"></a>Clonar a aplicação de exemplo

Abra uma solicitação de comando (escreva `cmd` no menu inicial do Windows) e mude para um diretório onde pretende armazenar o projeto de amostra ARR.

Execute os seguintes comandos:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
powershell azure-remote-rendering\Scripts\DownloadUnityPackages.ps1
```

O último comando cria uma subdiretória no diretório ARR contendo os vários projetos de amostragem para a renderização remota Azure.

A aplicação de amostra de arranque rápido para unidade encontra-se na unidade *subdireccional Unidade/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Renderização de um modelo com o projeto de amostra de unidade

Abra o Hub de Unidade e adicione o projeto de amostra, que é a *pasta ARR\azure-remote rendering\Unity\Quickstart.*
Abra o projeto. Se necessário, permita à Unidade atualizar o projeto para a sua versão instalada.

O modelo padrão que prestamos é um [modelo de amostra incorporado.](../samples/sample-model.md) Mostraremos como converter um modelo personalizado utilizando o serviço de conversão ARR no [próximo quickstart](convert-model.md).

### <a name="enter-your-account-info"></a>Insira a informação da sua conta

1. No navegador de ativos Unity, navegue na pasta *Cenas* e abra a cena **Quickstart.**
1. A partir da *Hierarquia,* selecione o objeto de jogo **RemoteRendering.**
1. No *Inspetor,* insira [as suas credenciais de conta.](../how-tos/create-an-account.md) Se ainda não tem uma conta, [crie uma.](../how-tos/create-an-account.md)

![Informação da Conta ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> **Desematado o RemoteRenderingDomain** `<region>.mixedreality.azure.com` para, onde está uma `<region>` das [regiões disponíveis perto de si](../reference/regions.md).\
> Desconfiem **do 'AccountDomain'** para [o domínio da conta,](../how-tos/create-an-account.md#retrieve-the-account-information) tal como mostrado no Portal Azure.

Mais tarde queremos implementar este projeto num HoloLens e conectar-nos ao serviço de renderização remota a partir desse dispositivo. Uma vez que não temos uma maneira fácil de introduzir as credenciais no dispositivo, a amostra de arranque rápido **salvará as credenciais na cena da Unidade.**

> [!WARNING]
> Certifique-se de que não verifica o projeto com as suas credenciais guardadas em algum repositório onde divulgaria informações secretas de login!

### <a name="create-a-session-and-view-the-default-model"></a>Crie uma sessão e veja o modelo padrão

Prima o botão **De Reprodução** da Unidade para iniciar a sessão. Você deve ver uma sobreposição com texto de estado, na parte inferior do viewport no painel *de jogo.* A sessão será submetida a uma série de transições estatais. No estado **inicial,** o servidor é girado, o que leva vários minutos. Após o sucesso, passa para o estado **Ready.** Agora a sessão entra no estado **de Ligação,** onde tenta atingir o tempo de execução de renderização naquele servidor. Quando bem sucedida, a amostra transite para o estado **Ligado.** Neste momento, começará a descarregar o modelo para renderização. Devido ao tamanho do modelo, o download pode demorar mais alguns minutos. Em seguida, o modelo renderizado remotamente aparecerá.

![Saída da amostra](media/arr-sample-output.png)

Parabéns! Está agora a ver um modelo remotamente renderizado!

## <a name="inspecting-the-scene"></a>Inspecionando o local

Uma vez que a ligação de renderização remota está em execução, o painel do inspetor atualiza com informações adicionais sobre o estado: ![ Amostra de unidade reproduzindo](./media/arr-sample-configure-session-running.png)

Agora pode explorar o gráfico de cena selecionando o novo nó e clicando em **mostrar as crianças** no Inspetor.

![Hierarquia da Unidade](./media/unity-hierarchy.png)

Há um objeto [de avião cortado](../overview/features/cut-planes.md) no local. Tente capacitá-lo nas suas propriedades e movê-lo em torno de:

![Mudar o avião de corte](media/arr-sample-unity-cutplane.png)

Para sincronizar as transformações, clique **em Sync agora** ou verifique a opção Sync every **frame.** Para propriedades de componentes, apenas mudá-las é suficiente.

## <a name="next-steps"></a>Passos seguintes

No próximo quickstart, colocaremos a amostra num HoloLens para ver o modelo remotamente renderizado no seu tamanho original.

> [!div class="nextstepaction"]
> [Início Rápido: Implementar exemplo do Unity no HoloLens](deploy-to-hololens.md)

Em alternativa, a amostra também pode ser implantada num PC de secretária.

> [!div class="nextstepaction"]
> [Quickstart: Implementar a amostra de Unidade para desktop](deploy-to-desktop.md)